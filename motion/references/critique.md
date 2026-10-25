# Motion critique

Render, look, fix. Every round looks at actual frames, writes down what is wrong, converts it to numbers, and sends one focused follow-up.

British English throughout (colour, organised, towards, artefact).

You need frames or an MP4 from `references/render.md`. Timing language lives in `references/maths.md`. Vague-to-numbers translations below assume 60 fps.

## A round

1. Render the full sequence and the MP4.
2. Look three ways: play the MP4 at full speed once, make a contact sheet, freeze the two or three moments that bother you (`scene.html?t=2.5` or a PNG from ffmpeg).
3. Fix with a follow-up that names the time range, the element, and the target values. One theme per follow-up (material, then timing, then camera). A list of ten changes in one message gets half of them ignored.
4. Re-render and check the frames that changed. Open the PNGs. Do not trust a memory of the live preview.

The first pass is a sketch. Directed briefs still take several rounds. A piece that looks "done" in the side-panel preview often fails on the contact sheet: no hero, everything centred, text moving while you are trying to read it.

## Inspection tools

Commands live in `references/render.md`. Use them.

- **Contact sheet:** composition and pacing. Fail if any column has no obvious hero, or if every frame is centred the same way.
- **Onion skin:** spacing. Bunched ghosts = slow. Even spread at the start or end of a move = no easing.
- **Speed graph:** add `?debug=1` that plots the hero's speed, or log position per frame to CSV. A good move is a smooth hump. Spikes are jitter. A spike at the loop point is a seam.
- **Side by side:** frames 0, mid, and N−1 for loops.

## Automated checkpoints

For a 15-second reel, screenshot **1.5 s, 4.0 s, 8.5 s, 14.0 s** (the four-beat storyboard: scatter, gather, resolve, CTA). For other lengths, pick one frame per beat plus the last still.

Inspection prompt to run against those four stills:

```text
For each checkpoint, answer:
1. Contrast: is light type sitting on a light card? Name the pair.
2. Inertia: had the previous move settled before the next headline appeared?
3. Hero: what owns the frame? If two things do, say so.
4. Score polish 1-10. If below 8, name the two worst defects and output a
   minimal patch (constants and the functions to change), not a rewrite.
```

If the score is below 8, patch and re-render. If it is 8 or above, encode the master. Two automated passes should have cleared type collisions, margin clipping, and lopsided transitions before a human watches the export.

Do not treat 8/10 as taste. It is a gate for collisions and settling. Taste still needs a person.

Overnight runs that iterate this loop need the stop conditions in `references/harness.md`. Unbounded "try again" is how the bill explodes.

## Follow-ups that work

Copy the shape, not the subject.

- "Raise roughness from 0.05 to 0.12; the chrome reads as a mirror, not metal."
- "Make the glint a single narrow highlight that crosses in 1.2 s, not a wide wash."
- "Stagger letters 80 ms apart, left to right, instead of all at once."
- "Hold the final composition still for 1.5 s before the loop resets."
- "Move the horizon down to 62% of frame height so the word sits above centre."
- "Replace the linear camera move with ease-in-out-quart, and halve its distance."
- "Secondary motion 3 is competing with the hero; cut its amplitude by 60%."
- "Add a 6-frame anticipation dip before each letter rises."
- "Give it sound: synthesise in Web Audio, a soft low hit for each letter, landing one frame before it breaks the surface."
- "Show frames 0, 90 and 359 side by side and confirm the loop is seamless."
- "Explain what you changed and which constants control it."

## Gut feeling to numbers

| You think | Send this |
| --- | --- |
| "Make it pop." | Scale 1.00 → 1.08 → 1.00 over 6 frames (100 ms), ease-out-back on the way up. |
| "It feels slow." | Cut the hero entrance from 800 ms to 500 ms; keep the hold. |
| "It feels floaty." | Raise spring damping ratio from 0.5 to 0.8, or land on ease-out-expo. |
| "Too busy." | One hero; drop secondary motion 3; stagger 50 → 80 ms. |
| "Looks cheap / plastic." | Environment map, one key plus one rim, roughness 0.10–0.20, no glow. |
| "No energy." | Anticipation: 5 frames, 4% the opposite way; overshoot 3–5% on landing. |
| "Harsh landing." | ease-out-quint, or a spring at damping ratio 0.7. |
| "Cannot read the text." | Hold text still at least 1.5 s; contrast at least 4.5:1. |
| "Camera feels dead." | Dolly 2–4%; background layer at 0.3× the foreground. |
| "The loop jumps." | Frames 0…N−1; whole cycles in `DURATION`; N−1 matches 0. |

Also fail the round (do not mark polish 8) when: white text on light cards; metric cards still moving when the headline arrives; a logo fading to black as the ending; everything entering with the same fade-and-slide-up.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
