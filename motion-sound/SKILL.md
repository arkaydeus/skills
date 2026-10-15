---
name: motion-sound
description: Author animation sound on the same frame grid as the picture. Use when adding synthesised clicks, whooshes, or hits; locking a 120 BPM grid at 60 fps; exposing window.renderAudio as a WAV; or normalising loudness for social. Prefer OfflineAudioContext over stock libraries and over live AudioContext.
license: MIT
---

# Motion sound

Write sound as code on the same timestamps as the picture. Do not drop a music bed on a finished MP4 and hope the transients land.

British English throughout (colour, organised, towards, artefact).

Needs `motion-render` for capture and mux, `motion-maths` for the beat grid, `motion-export` for the file X will play.

## Shared timeline

Keep visual cues and audio cues in one JSON list: element, start frame, duration, sound function. If a card transition moves by 12 frames, its click moves by 12 frames. No separate sound edit.

Benchmark tempo for fast SaaS reels: **120 BPM**.

- 2 beats per second
- 1 beat every **30 frames at 60 fps** (0.5 s)
- Hero hits on beats, secondary hits on half-beats (15 frames)

Example grid for a 15-second dashboard reel:

| Frame | Beat | Picture | Sound |
| --- | --- | --- | --- |
| 0 | kick | first card in | low click |
| 30 | snare | second card | higher click |
| 60 | kick | third card | low click |
| 90 | snare | cursor grab | short tick |
| 120–180 | build | morph | rising noise, then a drop when the dashboard resolves |

Place each hit **one frame early** (about 17 ms at 60 fps). The ear leads the eye; a hit on the same frame as the impact feels late.

## Synthesise, do not license

Micro-clicks, paper swooshes, and bass drops are a decaying oscillator plus an envelope. No stock library unless the user supplies music they already own.

Tactile click (decaying sine). Time `t` is seconds since the hit. The envelope time constant is about 11 ms (`exp(−90 t)`), which is tighter than a 20 ms click; keep it. This is a sample, not a file on disk.

```js
function synthesiseClick(t) {
  return Math.sin(2 * Math.PI * 1800 * t) * Math.exp(-t * 90) * 0.4;
}
```

Render the whole bed with `OfflineAudioContext` at 48000 Hz, stereo, length exactly `DURATION` seconds. Live `AudioContext` is not deterministic and will not match the PNG sequence.

Expose `window.renderAudio()` returning a base64 WAV. `render.mjs` writes `audio.wav` when that function exists.

```js
window.renderAudio = async () => {
  const sr = 48000, ctx = new OfflineAudioContext(2, sr * DURATION, sr);
  for (let s = 0; s < DURATION; s++) {
    const t0 = Math.max(0, s - 1 / 60);
    const osc = ctx.createOscillator(), g = ctx.createGain();
    osc.frequency.value = 880;
    g.gain.setValueAtTime(0.0001, t0);
    g.gain.exponentialRampToValueAtTime(0.4, t0 + 0.005);
    g.gain.exponentialRampToValueAtTime(0.0001, t0 + 0.25);
    osc.connect(g).connect(ctx.destination);
    osc.start(t0); osc.stop(t0 + 0.3);
  }
  const buf = await ctx.startRendering();
  return wavBase64(buf);
};

function wavBase64(buf) {
  const ch = buf.numberOfChannels, len = buf.length, sr = buf.sampleRate;
  const dv = new DataView(new ArrayBuffer(44 + len * ch * 2));
  const str = (o, s) => [...s].forEach((c, i) => dv.setUint8(o + i, c.charCodeAt(0)));
  str(0, 'RIFF'); dv.setUint32(4, 36 + len * ch * 2, true); str(8, 'WAVE');
  str(12, 'fmt '); dv.setUint32(16, 16, true); dv.setUint16(20, 1, true);
  dv.setUint16(22, ch, true); dv.setUint32(24, sr, true);
  dv.setUint32(28, sr * ch * 2, true); dv.setUint16(32, ch * 2, true);
  dv.setUint16(34, 16, true); str(36, 'data'); dv.setUint32(40, len * ch * 2, true);
  let off = 44;
  for (let i = 0; i < len; i++) for (let c = 0; c < ch; c++) {
    const v = Math.max(-1, Math.min(1, buf.getChannelData(c)[i]));
    dv.setInt16(off, v < 0 ? v * 0x8000 : v * 0x7fff, true); off += 2;
  }
  let bin = '';
  const bytes = new Uint8Array(dv.buffer);
  for (let i = 0; i < bytes.length; i += 0x8000)
    bin += String.fromCharCode(...bytes.subarray(i, i + 0x8000));
  return btoa(bin);
}
```

`exponentialRampToValueAtTime` cannot take 0. Use `0.0001`. Schedule from `t0 >= 0`.

## Loudness

Aim for about **−14 LUFS** integrated, true peaks under **−1 dBTP**. Common social and streaming target. Check current platform docs if the client has a spec.

```bash
ffmpeg -y -i out/scene.mp4 -i audio.wav -i music.wav -filter_complex \
  "[1:a][2:a]amix=inputs=2:normalize=0,loudnorm=I=-14:TP=-1:LRA=11[a]" \
  -map 0:v -map "[a]" -c:v copy -c:a aac -b:a 192k -ar 48000 -shortest out/final.mp4

ffmpeg -hide_banner -i out/final.mp4 -af ebur128 -f null - 2>&1 | tail -12
```

Single-pass `loudnorm` can land a dB or two off on short, spiky beds. For an exact figure, run it twice: first with `print_format=json` to measure, then again with the measured values.

`-framerate` on the picture must match capture fps or the bed will drift. WAV length must equal `DURATION`. See `motion-render` troubleshooting.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
