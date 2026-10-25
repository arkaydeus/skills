---
name: motion
description: "Make code-driven animation and motion design: product reels, UI demos, three.js loops, character or mascot animation, sound, critique, export for X, pricing. Use for any motion or animation job; it asks what you're doing and loads only the reference it needs."
license: MIT
---

# Motion

Direct a code-first animation job. The model writes a deterministic program. A headless browser paints each frame. ffmpeg encodes the master. Do not ask a video model to invent pixels for type, UI, or product geometry: letters melt and grids warp.

British English throughout (colour, organised, towards, artefact).

## Intake

Infer the job type when it is obvious. Otherwise ask one question listing the types:

> What are you doing: a new piece (brief), pick an engine, build or render, timing and easing, character animation, sound, critique a render, export for X, a long or overnight session, or quote a client?

Then read only the 1–2 references in the table. Never load all references up front. Do not invent a parallel pipeline.

## Routing

| Job | Read |
| --- | --- |
| New piece / brief | `references/brief.md` |
| Pick an engine | `references/engines.md` |
| Build or render | `references/render.md` |
| Timing and easing | `references/maths.md` |
| Character or mascot animation | `references/principles.md`, `references/maths.md` |
| Sound | `references/sound.md` |
| Critique a render | `references/critique.md` |
| Export for X | `references/export.md` |
| Long or overnight session | `references/harness.md` |
| Quote a client | `references/pricing.md` |

Add `references/export.md` when quoting a product reel (masters and duration). Add `references/critique.md` when an overnight run will iterate.

## Full-piece pipeline

When making a complete piece, walk the stages in order. Read each reference only when that stage is reached:

1. Brief — `references/brief.md`
2. Engines — `references/engines.md`
3. Maths — `references/maths.md`. If a character or mascot is on screen, also read `references/principles.md`
4. Render — `references/render.md`
5. Sound — `references/sound.md` if the piece needs audio
6. Critique — `references/critique.md`
7. Export — `references/export.md`

Add `references/harness.md` for a long unattended run. Add `references/pricing.md` for paid work.

The first render is a sketch. Quality comes from the critique loop, not from a one-line prompt. Viral "single prompt" posts usually hide a long brief, examples, and a render contract.

## Standing rules

These apply to every job. References repeat the details.

- Animation is `render(t)` with `t` in seconds. Ban `setTimeout`, `setInterval`, and any use of the wall clock or `requestAnimationFrame` timestamps to decide what is on screen. Live preview may use rAF only to call `render`.
- One self-contained HTML file (or one Remotion composition) per piece. Pin CDN libraries to an exact version.
- One hero per shot. Rank secondary motion 1, 2, 3.
- Ban the defaults: purple or blue gradients, glow, bloom, lens flares, generic particle fields, fade-and-slide-up on every element, everything centred, text that moves while it is being read.
- Springs are closed-form. Do not Euler-step. See the formula caveat in `references/maths.md`.
- Source spring presets were read from an image; prefer damping-ratio targets. Source client prices disagree with each other; see `references/pricing.md`.
- Do not use a video-diffusion pass on UI, type, or logos. Abstract background texture is the only place it is allowed.

## Default lab layout

```text
motion-lab/
  render.mjs
  motion.js
  scene.html
  frames/
  out/
  sources/
  drafts/
  published/
```

Standing rules (seekability, no timers, 120 BPM grid, this layout) live in a root project-instructions file. Ephemeral brief details stay out of that file. Full capture and encode commands are in `references/render.md`.

## Quick path for a first piece

1. Prove the pipeline with the minimal seekable scene in `references/render.md`.
2. Fill the eight-field brief in `references/brief.md`, including the banned list and the render contract.
3. Render, contact-sheet, and run two critique rounds before calling it done.
4. Encode for the platform in `references/export.md`.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
