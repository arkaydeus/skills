# Motion principles

The twelve principles in Thomas and Johnston, *The Illusion of Life* (1981), still make movement feel alive. The model knows the names. Naming them is not enough. Say where, how much, and for how many frames.

British English throughout (colour, organised, towards, artefact).

Read `references/maths.md` for springs and easings, `references/brief.md` for the brief, and `references/critique.md` after the first render. Damping ratio 0.4–0.6 is the mascot band; do not use that bounce on UI type.

## Prompt as numbers

Each principle becomes a line with a part, a duration, and a magnitude. If you omit volume and pivot, squash will flatten the design.

| Principle | Meaning | Write this sort of line |
| --- | --- | --- |
| Anticipation | A small opposite move before the main action | "Before each jump, crouch for 6 frames: scaleY 0.85, scaleX 1.08, then launch." |
| Squash and stretch | Soft bodies deform and keep volume | "Stretch to scaleY 1.15 at take-off, squash to 0.85 on landing. Preserve volume: scaleX = scaleZ = 1 / √scaleY." |
| Overlapping action | Parts offset in time | "Ears and antenna lag the body by 3 to 4 frames." |
| Follow-through | Loose parts keep going after the body stops | "After landing, the antenna overshoots and settles on a spring with damping ratio 0.4." |
| Arcs | Paths curve | "The jump follows a parabola; the head traces an arc, never a straight diagonal." |
| Slow in, slow out | Ease at both ends | "Body moves use ease-in-out-quart; never linear except constant spins." |
| Timing | Frame count is weight | "Heavy objects take 18 frames to fall 1 unit; light ones 10." |
| Staging | One idea, readable | "One action at a time; nothing else moves while the mascot waves." |
| Exaggeration | Push the pose, then return | "Exaggerate the key pose of each action by 20%, then return." |
| Secondary action | Support, do not compete | "A blink at 3.2 s (4 frames closed) while it lands; never during the jump." |

The remaining three (straight-ahead versus pose-to-pose, solid drawing, appeal) matter more at design time. Still add: "Keep the silhouette readable at every frame."

## Protect the character

Always include a constraint like:

```text
Keep the mascot's proportions and grid fixed. Only squash and stretch may
change its shape, and only around the base pivot. Never scale the eyes
independently. Squash from the feet, not the centre.
```

Squash from the centre reads as floating. Independent eye scale reads as a bug. If the character is voxel or otherwise grid-snapped, say so, or the model will "smooth" it.

Work principles in one at a time across critique rounds: anticipation first, then squash, then overlap, then follow-through, then arcs. Adding all five in one pass usually wrecks staging.

## Staging still wins

One hero action. Rank anything else. A blink during a jump is two ideas. Holds apply to characters too: after a landing, freeze the body long enough that the follow-through can read.

For a looping mascot, every deform must return to the frame-0 pose by frame N−1. See `references/export.md`.

## Credits

- Florence ([@flxrnc](https://x.com/flxrnc))
- damkin ([@damkina7](https://x.com/damkina7))

Article titles, for provenance only:

- Florence, *Opus 5.5 Motion Lab: Make Claude Animate Like a Studio*, https://x.com/flxrnc/article/2105311836190978392
- damkin, *Autonomous Motion Studio on Claude Opus 5.5: From seek(t) Architecture to Your First $500 Reel*, https://x.com/damkina7/article/2107924159397560795
