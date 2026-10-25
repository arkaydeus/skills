# Motion prompt

A vague line gets the model's defaults. A directed brief gets a film you can light, time, and ban. Fill every field, even briefly. Paste the render contract from `references/render.md` underneath.

British English throughout (colour, organised, towards, artefact).

## Eight fields

| Field | What to write |
| --- | --- |
| GOAL | What it is for and how it should feel, in one or two sentences. Name the audience and platform. |
| OUTPUT | File type, engine, pinned version, size, fps, frame count, whether it loops. |
| TIME | A timeline in seconds: what happens when, with easing or spring names. This is the single most useful field. |
| LOOK | Background as hex, lighting, materials with roughness and metalness, typeface, palette. Prefer a screenshot of the real product over adjectives. |
| MOTION | The hero move for each shot, then secondary motion ranked 1, 2, 3 so the model knows what to drop. |
| SHOTS | Lens, height, angle, moves, cuts. "One locked shot" is a valid answer. |
| BANNED | Defaults you do not want. Start from the list below and add this piece's own. |
| QA | What must be checked before the reply: named frames, the loop seam, the render contract, known API gotchas. |

Two rules that fix most drafts:

1. **One hero per shot.** Exactly one thing owns attention. Name it.
2. **Rank secondary motion.** Number 3 should be subtle, and it is the first thing to cut when the shot is busy.

Do not describe styling in paragraphs if you can attach one high-resolution screenshot of the target app. The model should extract hex, radii, padding, and type scale from that picture. Generic "clean SaaS dark mode" prose is how every reel ends up looking the same.

## Default banned list

Always include these, then add the job's own:

- Purple or blue gradients
- Glow, bloom, and lens flares
- Generic floating particles
- Fade-and-slide-up on every element
- Everything centred
- Text that moves while it is being read
- Unseeded `Math.random`
- Wall-clock or `requestAnimationFrame` timestamps inside `render(t)`
- Video-diffusion frames for UI, type, or logos

## Pin versions, then name the gotchas

The model has seen many API vintages. If you pin `three@0.160.0` but it writes `depth` on `TextGeometry`, letters come out flat with no error: in r160 that option is `height`. Point font JSON at the same pinned package, for example `https://cdn.jsdelivr.net/npm/three@0.160.0/examples/fonts/helvetiker_bold.typeface.json`.

## Filled brief (15-second product reel)

Swap the product, colours, and timings. This is a dashboard-consolidation reel, not a chrome title. Paste the render contract after it.

```text
GOAL: A 15-second product demo for a B2B analytics SaaS, posted on X and
used as a landing-page hero. Calm, expensive, "scattered to one view".
Not a screensaver and not a feature tour.

OUTPUT: One self-contained scene.html. GSAP 3 from jsdelivr, pinned, or
plain canvas if you can keep it under two files. 1920x1080 master, also
think 1080x1920 crop. 60 fps, 900 frames (0 to 899), not a seamless
loop: it ends on a still CTA.

TIME (seconds):
  0.0-3.0   Three metric cards (Ads, Social, Web) sit apart on a dark
            field. Headline "Scattered data." Cards enter with a spring,
            damping ratio 0.7, staggered 80 ms.
  3.0-7.0   A magnetic cursor selects them and draws them to one focal
            point. Cursor moves ease-in-out-quart. Cards use the same
            spring as entry.
  7.0-12.0  Cards morph into one responsive dashboard. Headline "One
            clear view." Morph on a closed-form spring, damping ratio 0.8.
  12.0-15.0 Still CTA card, copy readable, nothing else moving.
            Hold the end card fully still.

LOOK: Pull tokens from the attached screenshot of the live app (hex,
radii, type). If none is attached, wait and ask. Fallback only:
background #0E0D0C, cards #161513, text #F2EEE8, accent #E4572E.
No stock illustrations.

MOTION: Hero = the three cards becoming one dashboard. Secondary, ranked:
  1. Cursor path that gathers the cards.
  2. Headline swap, still while on screen.
  3. A 2% camera dolly in from 7 s to 12 s, ease-in-out-quart.

SHOTS: One continuous shot. 16:9, slight top-down on the cards, no cuts.
Safe crop for 9:16: keep hero and CTA inside the centre 1080 px.

BANNED: purple or blue gradients, bloom, glow, lens flares, particle
fields, fade-and-slide-up, unseeded Math.random, text moving during
read time, logo fading to black as an ending.

QA: Before replying, describe frames at 1.5 s, 4.0 s, 8.5 s and 14.0 s.
Confirm the CTA is still from 12 s. Confirm render mode does not read
the clock. Confirm springs are closed-form, not Euler.
```

A 6-second seamless three.js loop uses the same eight fields with different OUTPUT and TIME: one word or product, hold long enough to read, sink or orbit back so frame N−1 meets frame 0. Put material numbers in LOOK (metalness 1, roughness 0.10, named lights). See `references/engines.md`.

## TIME field, written well

- Give seconds, not "then it appears".
- Name the curve (`ease-out-quint`, `ease-in-out-quart`, `ease-in-expo`) or the spring (stiffness and damping ratio). Tokens live in `references/maths.md`.
- Stagger in milliseconds (30–80 ms). Keep the whole stagger under about 500 ms.
- After a hero move, hold at least 0.5 s. Hold text still for at least 1.5 s. Hold an end card 1–2 s.
- Put hero hits on the beat grid when sound is planned: at 60 fps and 120 BPM a beat is 30 frames (0.5 s). See `references/sound.md`.

## QA field, written well

Ask for a description of named frames, not "looks good". For a 15-second reel, 1.5 s, 4 s, 8.5 s and 14 s match the critique checkpoints in `references/critique.md`. For a 6-second loop, include 0, mid, and N−1.

A directed brief still needs the follow-up loop. Directing means that loop starts from a usable draft, not that one prompt ships the film.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
