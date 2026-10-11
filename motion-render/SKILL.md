---
name: motion-render
description: Build and run a deterministic animation render pipeline. Use when capturing HTML, canvas, SVG, or three.js animation to PNG frames, WAV, or MP4; when a scene must freeze at an exact time t; or when headless Chromium, Playwright, or ffmpeg is part of a motion job. Prefer seek(t) over screen recording.
license: MIT
---

# Motion render

Capture animation by seeking a pure function of time, then encoding the PNG sequence. Do not screen-record a live preview.

British English throughout (colour, organised, towards, artefact).

Pair with `motion-studio` for routing, `motion-maths` for timing tokens, `motion-export` for platform files, and `motion-sound` if `window.renderAudio` is involved.

## Render contract

Paste this under every motion prompt. Without it, the model will drive the scene from `requestAnimationFrame` and the wall clock, which looks fine live and cannot be captured cleanly.

```text
RENDER CONTRACT (follow exactly)
1. One self-contained HTML file. No external files. Libraries only from
   cdn.jsdelivr.net, pinned to an exact version (for example three@0.160.0).
   Fonts embedded or loaded from a pinned URL, and awaited before ready.
2. Animation is a pure function of time: render(t), t in seconds. Never use
   Date.now(), performance.now() or the requestAnimationFrame timestamp to
   decide what is on screen. Any randomness uses a seeded PRNG.
3. Expose window.seek = async (t) => { ...draw exactly the frame at t... }
   and set window.__ready = true once every asset has loaded.
4. URL flags: ?t=1.25 freezes that moment; ?render=1 disables the live loop.
   With no flags, play a live preview loop.
5. Fixed size: WIDTH x HEIGHT CSS pixels, pixel ratio 1, no scrollbars,
   no responsive layout.
6. FPS and DURATION are constants at the top. Frame i is t = i / FPS for
   i = 0 .. N-1, where N = FPS * DURATION.
7. No state that accumulates between frames. If you need simulation,
   precompute it at load or recompute from t = 0 inside render(t).
8. If there is sound, expose window.renderAudio() that returns a base64 WAV
   made with OfflineAudioContext at 48000 Hz, exactly DURATION long.
```

Ban `setTimeout`, `setInterval`, and unconstrained rAF as the source of motion. Live preview may use rAF only as a clock that calls `render((now - start) / 1000)`.

`seek(t)` is the whole engine: jump to frame 812 without playing 0–811, capture after paint, and stay deterministic if the host CPU throttles.

## Lab setup

Node 18+ (the capture script uses `node:util` `parseArgs`), ffmpeg, Playwright Chromium.

```bash
# macOS
brew install node ffmpeg

# Windows 11 (PowerShell)
winget install OpenJS.NodeJS.LTS
winget install Gyan.FFmpeg
# reopen the shell so node and ffmpeg are on PATH

mkdir motion-lab && cd motion-lab
npm init -y
npm pkg set type=module
npm install -D playwright
npx playwright install chromium
```

```text
motion-lab/
  render.mjs      # frame capture
  motion.js       # timing tokens (see motion-maths)
  scene.html      # current piece
  frames/         # PNGs, wiped every render
  out/            # MP4, GIF, contact sheets
  sources/        # client assets
  drafts/         # experiments
  published/      # finals
```

Confirm:

```bash
node -v
ffmpeg -version
npx playwright --version
```

## Minimal seekable scene

A 4-second, 1080×1080 loop: a disc eases across and back with a little squash. Use it to prove the pipeline before any ambitious brief. Save as `scene.html`.

```html
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<style>
  html, body { margin: 0; width: 1080px; height: 1080px; overflow: hidden; background: #f2efe9; }
  #dot { position: absolute; left: 0; top: 0; width: 160px; height: 160px;
         border-radius: 50%; background: #e4572e; will-change: transform; }
</style>
</head>
<body>
<div id="dot"></div>
<script>
  const DURATION = 4;
  const dot = document.getElementById('dot');
  const easeInOutQuart = x => x < 0.5 ? 8*x**4 : 1 - (-2*x + 2)**4 / 2;

  function render(t) {
    const p = (t % DURATION) / DURATION;
    const half = p < 0.5 ? p * 2 : (1 - p) * 2;
    const x = 140 + easeInOutQuart(half) * 640;
    const squash = 1 + 0.15 * Math.sin(p * Math.PI * 4);
    dot.style.transform =
      `translate(${x}px, 460px) scale(${squash}, ${2 - squash})`;
  }

  window.seek = (t) => render(t);
  const q = new URLSearchParams(location.search);
  if (q.has('t')) {
    render(Number(q.get('t')));
  } else if (!q.has('render')) {
    const start = performance.now();
    const tick = now => { render((now - start) / 1000); requestAnimationFrame(tick); };
    requestAnimationFrame(tick);
  }
  render(0);
  window.__ready = true;
</script>
</body>
</html>
```

Freeze one moment in a browser: `scene.html?t=1.5`. Capture mode adds `?render=1` so the live loop stays off.

## Capture script

Save as `render.mjs`. It launches Chromium at the exact pixel size, waits for `window.seek` and `window.__ready` plus fonts, then for every frame calls `seek(t)`, waits two animation frames so the browser has painted, and writes a numbered PNG. If the scene exposes `window.renderAudio()`, it also writes `audio.wav`.

`--enable-unsafe-swiftshader` lets WebGL fall back to software rendering when the machine has no GPU. Frames stay identical; they just take longer. As a guide, a 6-second 30 fps 1080×1080 three.js scene took about 30 seconds on a GPU-less Linux box. Expect roughly double that at 60 fps, and less on a GPU.

```js
// render.mjs: freeze a seekable HTML scene frame by frame and save PNGs.
// Usage: node render.mjs scene.html --fps 60 --duration 6 --width 1080 --height 1080
import { chromium } from 'playwright';
import { mkdir, rm, writeFile } from 'node:fs/promises';
import { pathToFileURL } from 'node:url';
import path from 'node:path';
import { parseArgs } from 'node:util';

const { values: o, positionals } = parseArgs({
  allowPositionals: true,
  options: {
    fps:      { type: 'string', default: '60' },
    duration: { type: 'string', default: '6' },
    width:    { type: 'string', default: '1080' },
    height:   { type: 'string', default: '1080' },
    out:      { type: 'string', default: 'frames' },
  },
});

const file = positionals[0] ?? 'scene.html';
const fps = Number(o.fps), width = Number(o.width), height = Number(o.height);
const total = Math.round(fps * Number(o.duration));

await rm(o.out, { recursive: true, force: true });
await mkdir(o.out, { recursive: true });

const browser = await chromium.launch({
  args: ['--enable-unsafe-swiftshader', '--hide-scrollbars'],
});
const page = await browser.newPage({ viewport: { width, height }, deviceScaleFactor: 1 });
page.on('console', m => { if (!m.text().includes('GL Driver')) console.log('[page]', m.text()); });
page.on('pageerror', e => { console.error('[page error]', e.message); process.exit(1); });

const url = pathToFileURL(path.resolve(file)).href + '?render=1';
await page.goto(url, { waitUntil: 'load' });
await page.waitForFunction(() => typeof window.seek === 'function' && window.__ready === true,
  null, { timeout: 30000 });
await page.evaluate(() => document.fonts.ready);

for (let i = 0; i < total; i++) {
  const t = i / fps;
  await page.evaluate(async (t) => {
    await window.seek(t);
    await new Promise(r => requestAnimationFrame(() => requestAnimationFrame(r)));
  }, t);
  const name = path.join(o.out, String(i).padStart(5, '0') + '.png');
  await page.screenshot({ path: name, type: 'png' });
  if (i % fps === 0) console.log(`frame ${i}/${total}`);
}

const hasAudio = await page.evaluate(() => typeof window.renderAudio === 'function');
if (hasAudio) {
  const b64 = await page.evaluate(() => window.renderAudio());
  await writeFile('audio.wav', Buffer.from(b64, 'base64'));
  console.log('wrote audio.wav');
}

await browser.close();
console.log(`done: ${total} frames in ./${o.out}`);
```

Remotion compositions skip this script and use Remotion's own renderer. They still need the same idea: frame = f(t), no wall clock. See `motion-engines`.

## Encode with ffmpeg

Render frames 0 to N−1. Six seconds at 60 fps is `00000.png`–`00359.png`. Frame N would duplicate frame 0 and stall the loop. Full platform limits live in `motion-export`.

```bash
node render.mjs scene.html --fps 60 --duration 6 --width 1080 --height 1080

ffmpeg -y -framerate 60 -i frames/%05d.png \
  -c:v libx264 -profile:v high -pix_fmt yuv420p -crf 18 -preset slow \
  -movflags +faststart out/scene.mp4

ffmpeg -y -framerate 60 -i frames/%05d.png -i audio.wav \
  -c:v libx264 -profile:v high -pix_fmt yuv420p -crf 18 -preset slow \
  -c:a aac -b:a 192k -shortest -movflags +faststart out/scene.mp4
```

| Flag | Why it is there |
| --- | --- |
| `-framerate 60` before `-i` | Declares the PNG sequence's rate. Must match `--fps` or picture and sound drift. |
| `-profile:v high -pix_fmt yuv420p` | The combination phones, browsers, and social apps play. Without `yuv420p`, ffmpeg may keep 4:4:4 from the PNGs, which many players reject. Width and height must be even. |
| `-crf 18 -preset slow` | Near-lossless. Use 20–23 if the file is too large. |
| `-movflags +faststart` | Puts the index at the front so playback can start before the download finishes. |
| `-shortest` | Ends when the shorter of video and audio ends. |

## Inspect without guessing

```bash
# One still from the live scene
npx playwright screenshot --viewport-size="1080,1080" --wait-for-timeout=500 \
  "file://$PWD/scene.html?t=2.5" still.png

# One still from the encoded file
ffmpeg -y -ss 2.5 -i out/scene.mp4 -frames:v 1 out/frame.png

# Contact sheet: every 30th frame (0.5 s at 60 fps), 6×2
ffmpeg -y -i out/scene.mp4 -vf "select='not(mod(n\,30))',scale=270:-1,tile=6x2" \
  -fps_mode vfr -frames:v 1 out/sheet.png

# Onion skin: 8 frames from 2.0 s blended
ffmpeg -y -ss 2 -i out/scene.mp4 -vf "tmix=frames=8" -frames:v 1 out/onion.png
```

Contact sheets show composition and pacing. Onion skins show spacing: bunched ghosts are slow, even spacing at the ends of a move means there is no easing. Translate what you see into numbered follow-ups in `motion-critique`.

## Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Flicker or jitter | State carried between frames, wall clock leaking into render mode, unseeded randomness, or z-fighting | Make `render(t)` pure; seed the PRNG; separate coplanar surfaces; raise the camera near plane |
| Wrong or fallback font | Screenshot before the face loaded, or a dead URL | Embed or pin the font; await `document.fonts.load()` and `document.fonts.ready`; then set `__ready` |
| Hiccup at the loop seam | Frame N included, or periodic motion not completing whole cycles | Render 0…N−1; `p = (t % D) / D` with integer cycles; check N−1 into 0 |
| Audio drifts | `-framerate` ≠ capture fps, or WAV length ≠ `DURATION` | Same fps everywhere; `OfflineAudioContext` exactly `DURATION`; `-shortest` |
| Dropped or repeated frames | Screen-recording, or screenshot before paint | Use this script; two rAF waits; frame count = fps × duration |
| Black frames in three.js | No WebGL, or `__ready` before textures | Keep SwiftShader; set `__ready` only after maps, fonts, and the environment |
| Flat 3D text | three.js r160 extrusion option is `height`, not `depth` | Pin `three@0.160.0` and use `height` |
| `width not divisible by 2` | yuv420p needs even sizes | Even WIDTH/HEIGHT, or `scale=trunc(iw/2)*2:trunc(ih/2)*2` |
| Blurry text | Fractional positions, scaled canvas, or pixel-ratio mismatch | Round text to whole pixels, or capture at `deviceScaleFactor: 2` and downscale with lanczos |

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
