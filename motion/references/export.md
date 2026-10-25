# Motion export

The last mile is a loop with no seam and a file the platform will not transcode into mush.

British English throughout (colour, organised, towards, artefact).

Capture and master encode flags live in `references/render.md`. Sound mux and loudness live in `references/sound.md`.

## Seamless loops

Render frames **0 to N−1**, never 0 to N. Frame N is identical to frame 0. Including it adds a one-frame stall. Six seconds at 60 fps is frames 0–359.

Build every motion from normalised loop time:

```js
const p = (t % DURATION) / DURATION;
```

Rotations, sines, and orbits must complete a whole number of cycles per loop:

```js
Math.sin(p * 2 * Math.PI * k)  // k an integer
```

Eased moves must return. Anything that leaves frame 0's pose must be back by frame N−1, or designed as a hand-off into a still end card (in which case it is not a loop; do not advertise it as one).

Verify by putting frames 0, 1, N−2, and N−1 on a sheet. If N−1 does not flow into 0, fix motion before you re-encode.

## Export for X

Limits change. Check current help before a paid delivery. The working defaults:

| Format | Settings |
| --- | --- |
| Video (prefer this) | MP4, H.264 High profile, yuv420p, AAC audio, up to 60 fps. 1920×1080 for 16:9, 1080×1080 for square, 1080×1350 for 4:5, 1080×1920 for 9:16. Length up to 140 seconds without Premium. Even width and height. |
| GIF | 15 MB limit. About 1200 px wide at most; 800 px is often enough. One global palette. **25 or 50 fps, not 60.** |

Master encode (picture only shown; add audio as in `references/sound.md`):

```bash
ffmpeg -y -framerate 60 -i frames/%05d.png \
  -c:v libx264 -profile:v high -pix_fmt yuv420p -crf 18 -preset slow \
  -movflags +faststart out/scene.mp4
```

Client reels usually need **both** a 16:9 master and a 9:16 cut. Design the hero and CTA inside the centre 1080 px so the crop does not clip type. Do not letterbox a 16:9 into 9:16 as the delivery.

### Why not a 60 fps GIF

GIF frame delays are stored in hundredths of a second. 60 fps needs 1.67 hundredths, which cannot be stored, so players round and the speed goes wrong. 50 fps (2 hundredths) and 25 fps (4 hundredths) divide evenly. Some browsers treat very short delays as slow ones, so 25 fps is the safer GIF. If the GIF is still over 15 MB, reduce width before frame rate. Prefer posting the MP4: smaller, sharper, real 60 fps.

```bash
ffmpeg -y -i out/scene.mp4 -vf "fps=25,scale=1200:-1:flags=lanczos,\
split[a][b];[a]palettegen=stats_mode=diff[p];[b][p]paletteuse=dither=bayer:bayer_scale=3" \
  -loop 0 out/scene.gif
```

Banding means the palette cannot hold the gradient. Reduce gradients in the design, or post MP4. Per-frame palettes look worse than one `palettegen` over the whole clip.

## Product-reel package

When the job is a paid 15-second promo (see `references/pricing.md`):

- `published/master-16x9.mp4`
- `published/cut-9x16.mp4`
- optional `published/loop.gif` only if it stays under 15 MB at 25 fps
- source scene or Remotion project in `published/src/`

Name files so a client can tell aspect ratio without opening them.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
