---
name: motion-studio
description: Route an animation or motion-design job to the right specialist skills. Use when the user wants a product promo reel, UI or interface demo, 3D or three.js loop, character or mascot animation, synthesised sound, export for X, a critique-and-fix loop, a budget for a long agent session, or a client quote for motion work. Load this first, then the skills it names.
license: MIT
---

# Motion studio

Direct a code-first animation job. The model writes a deterministic program. A headless browser paints each frame. ffmpeg encodes the master. Do not ask a video model to invent pixels for type, UI, or product geometry: letters melt and grids warp.

British English throughout (colour, organised, towards, artefact).

Load this skill first. Then load only the specialist skills the job needs. Do not invent a parallel pipeline.

## Studio sequence

1. Write a directed brief (`motion-prompt`).
2. Pick the lightest engine that can make the shot (`motion-engines`).
3. Drive every pixel from time `t`, with shared easing and spring tokens (`motion-maths`). If a character or mascot is on screen, also load `motion-principles`.
4. Capture frames with `seek(t)`. Never screen-record (`motion-render`).
5. If the piece needs sound, synthesise it on the same frame grid (`motion-sound`).
6. Inspect real frames, then patch with numbers (`motion-critique`).
7. Close the loop seam and encode a file X will play (`motion-export`).
8. For a long unattended run, fence directories and the token budget (`motion-harness`).
9. For paid work, quote a business outcome with a hard scope (`motion-pricing`).

The first render is a sketch. Quality comes from the critique loop, not from a one-line prompt. Viral "single prompt" posts usually hide a long brief, examples, and a render contract.

## Job routing

For each job: load the skills in the order given. The "why" is the reason that set is sufficient; do not skip the named skills because the brief looks small.

### Product promo reel

**Load:** `motion-prompt` → `motion-engines` → `motion-maths` → `motion-render` → `motion-sound` → `motion-critique` → `motion-export`. Add `motion-pricing` if this is a quote, `motion-harness` if the session will run unattended.

**Why.** A 15-second SaaS or product reel is the default paid unit: one core interaction, a four-beat storyboard, pixel-true UI, a 120 BPM grid, and a 16:9 master plus a 9:16 cut. Remotion or a seekable HTML scene both work; diffusion does not, because type and layout must stay sharp. Sound has to be authored on the same timestamps as the cards, not slapped on in an editor.

Default storyboard if the user has not supplied beats:

- 0–3 s: three disconnected pieces of the product (scattered state).
- 3–7 s: a cursor or focal move gathers them.
- 7–12 s: they resolve into one clear view.
- 12–15 s: a still, readable CTA.

### UI or interface demo

**Load:** `motion-prompt` → `motion-maths` → `motion-engines` → `motion-render` → `motion-critique` → `motion-export`. Add `motion-sound` for tactile clicks on a beat grid.

**Why.** Interface motion is a pure function of time: every card, cursor, and overlay is `f(t)`. Feed a high-resolution screenshot of the real app so the model copies hex, radii, and type scales instead of inventing a generic dark dashboard. Use closed-form springs, not CSS ease-in on everything. Rank secondary motion so the demo does not animate twelve things at once.

### 3D or three.js loop

**Load:** `motion-engines` → `motion-prompt` → `motion-maths` → `motion-render` → `motion-export`. Add `motion-sound` if it needs hits; `motion-critique` after the first encode.

**Why.** Materials, lights, and cameras belong in three.js, pinned to an exact version. The render script must keep SwiftShader fallback and wait for environment maps before `__ready`. Loops fail here on whole-cycle orbits and on `TextGeometry` option names across versions. Export cares about even dimensions and the frame-N stall.

### Character or mascot animation

**Load:** `motion-principles` → `motion-maths` → `motion-prompt` → `motion-engines` → `motion-render` → `motion-critique` → `motion-export`.

**Why.** Naming "squash and stretch" is not enough. Each principle needs frames, percentages, and a pivot. Springs for mascots sit around damping ratio 0.4–0.6; text and UI should not. Protect volume and silhouette explicitly or the model will squash the design into mush. Staging still applies: one action at a time.

### Adding sound

**Load:** `motion-sound`, plus `motion-render` if frames are not captured yet, plus `motion-export` to mux and normalise.

**Why.** Hits live on the same JSON timeline as the visuals. At 60 fps and 120 BPM a beat is 30 frames. Synthesise with `OfflineAudioContext` so length and phase are exact. Place each hit one frame before the picture. Do not license stock whooshes unless the user insists.

### Exporting for X

**Load:** `motion-export`. Load `motion-render` only if the PNG sequence or master MP4 does not exist.

**Why.** X wants H.264 High, yuv420p, AAC, even sizes. GIFs cannot store 60 fps delays, so drop to 25 or 50 fps with one global palette, or post the MP4 instead. Loop seams are an export bug as often as a motion bug.

### Critique and fix loop

**Load:** `motion-critique`. Keep `motion-maths` and `motion-prompt` in context so follow-ups stay in numbers and banned defaults stay banned.

**Why.** No first pass is the film. Watch the MP4 at full speed, read a contact sheet, freeze the ugly moments with `?t=`, then send one theme per follow-up. Automated scoring of checkpoint frames is a gate, not a substitute for looking.

### Budgeting a long agent session

**Load:** `motion-harness`. Keep `motion-critique` if the run will iterate overnight.

**Why.** Unbounded retry loops spend the budget. Isolate standing rules from the current brief, fence `sources/`, `drafts/`, and `published/`, refuse to code before the storyboard is approved, and stop when `render.mp4` exists, duration matches, and the seam loops. Cached context dominates the bill on long sessions; fewer layout iterations beat a cheaper sticker price.

### Quoting a client

**Load:** `motion-pricing`. Pull duration, aspect ratios, and revision cap from `motion-export` and the product-reel routing above.

**Why.** Pitch a 15-second interactive product demo with clean type and a vertical cut, not "AI video". Lock one interaction, two revision rounds, and two masters. Treat published price anecdotes as inconsistent snapshots, not a rate card.

## Standing rules

These apply to every job. Specialists repeat the details.

- Animation is `render(t)` with `t` in seconds. Ban `setTimeout`, `setInterval`, and any use of the wall clock or `requestAnimationFrame` timestamps to decide what is on screen. Live preview may use rAF only to call `render`.
- One self-contained HTML file (or one Remotion composition) per piece. Pin CDN libraries to an exact version.
- One hero per shot. Rank secondary motion 1, 2, 3.
- Ban the defaults: purple or blue gradients, glow, bloom, lens flares, generic particle fields, fade-and-slide-up on every element, everything centred, text that moves while it is being read.
- Springs are closed-form. Do not Euler-step. See the formula caveat in `motion-maths`.
- Source spring presets were read from an image; prefer damping-ratio targets. Source client prices disagree with each other; see `motion-pricing`.
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

Standing rules (seekability, no timers, 120 BPM grid, this layout) live in a root project-instructions file. Ephemeral brief details stay out of that file. Full capture and encode commands are in `motion-render`.

## Quick path for a first piece

1. Prove the pipeline with the minimal seekable scene in `motion-render`.
2. Fill the eight-field brief in `motion-prompt`, including the banned list and the render contract.
3. Render, contact-sheet, and run two critique rounds before calling it done.
4. Encode for the platform in `motion-export`.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
