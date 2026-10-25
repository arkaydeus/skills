# Motion harness

A one-line prompt yields a throwaway clip. A fenced workspace yields the next video without rebuilding the pipeline. This skill is the fence, not the pictures.

British English throughout (colour, organised, towards, artefact).

Read `references/critique.md` for overnight iteration and `references/render.md` for the acceptance artefact. Directed briefs belong in `references/brief.md`; do not dump them into standing instructions.

## The long-brief fact

Posts that claim a film from one short prompt are usually hiding a long brief: thousands of characters, examples, skills, and secrets. Budget for a directed eight-field prompt plus the render contract, not for a sentence. The engineering is the product.

## Seven layers

### 1. Memory isolation

Standing rules live in a root project-instructions file: palette, fps, fonts, this folder layout, seek(t) only, no timers, 120 BPM grid. The current brief, screenshot notes, and experiment log stay out of that file. Mixing them makes every later job drag the last client's copy.

### 2. Directory fencing

```text
sources/     client assets, never overwritten by a draft
drafts/      experiments; safe to delete
published/   finals only
```

Failed iterations must not overwrite the last good `scene.html`. Copy to `drafts/round-03/` before a risky pass.

### 3. Verification gates

No code until the storyboard is approved. Pipeline:

```text
Brief → Storyboard → Wireframe → Spring physics → Audio → Final
```

If the user has not signed off the TIME field in `references/brief.md`, do not open the engine.

### 4. Context throttling

Do not load the whole repo. Pull styling notes only for the components on screen. Read `references/maths.md` when timing changes, not when you are only fixing a hex value.

### 5. Circuit breakers

If the same compile or render error happens twice in a row, stop. Raise reasoning effort and change the approach; do not retry the same patch. Cap critique rounds (two automated passes is the default before a human looks). If the score is still below 8 after those two, escalate rather than burning another ten encodes.

### 6. Structured state, not prose

Replace paragraphs of motion description with a list: timestamp, element, property, from, to, curve. XML, JSON, or a markdown table are all fine. Prose is how two cards start at the same frame by accident.

### 7. Rigid acceptance

The run ends only when all of these are true:

- `published/render.mp4` (or the agreed filename) exists on disk
- Playback duration matches `DURATION` within one frame
- For loops: frame 0 and frame N−1 join without a stall
- For story reels: the end card is still for the agreed hold
- QA frames named in the brief have been described and, if scored, are at least 8/10

Missing MP4 is not "almost done".

## Token budget

Prices change. Treat the figures below as a snapshot from empirical write-ups of long agent sessions, not as an invoice.

On long multi-turn runs (often 150k–300k tokens), cached context reads dominate: on the order of four fifths of the bill, with fresh input and output each around a tenth. A top-tier model may list at roughly twice a mid-tier sticker price, while measured spend on long jobs can sit nearer 1.26× because it spends fewer iterations on layout (around 35% fewer in those write-ups). Cached reads at a small fraction of fresh-input price are why isolation and throttling matter more than which label is on the model.

Practical rules:

- Keep the standing instructions short and stable so they cache.
- Do not paste every PNG into context. Send the four checkpoints, or a contact sheet, not 900 frames.
- Prefer a frontier-class model for layout and critique; do not "save money" with a weaker model that takes twice the rounds.
- Set a hard dollar or token cap before an unattended loop. When it hits, stop and write what failed.

A published example for one 15-second reel claimed on the order of a few dollars of inference and about 40 minutes end to end, against agency quotes in the thousands and a two-week wait. That inference figure is an anecdote, not a quote. Re-estimate from current prices and this job's round cap.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
