# skills

Agent skills for Cursor and Grok Bot.

## motion

Code-first animation: the model writes a deterministic program, Playwright captures `seek(t)` frames, ffmpeg encodes. Start with `motion`, which asks what you are doing and reads only the reference that stage needs (product reel, UI demo, three.js loop, mascot, sound, X export, critique loop, session budget, client quote).

Credits: [Florence (@flxrnc)](https://x.com/flxrnc), [damkin (@damkina7)](https://x.com/damkina7).

### Install

```bash
npx skills add arkaydeus/skills --skill motion
```

Or copy `motion/SKILL.md` into your skills folder.

### Use

Invoke `/motion` or `@motion` when your client supports skill commands. Use it first on any animation job; it loads only the reference it needs.

### Renamed

The eleven `motion-*` skills are now one skill, `motion`. Existing installs should remove the old names:

```bash
npx skills remove motion-studio
npx skills remove motion-render
npx skills remove motion-prompt
npx skills remove motion-maths
npx skills remove motion-principles
npx skills remove motion-engines
npx skills remove motion-sound
npx skills remove motion-critique
npx skills remove motion-export
npx skills remove motion-harness
npx skills remove motion-pricing
```

Then add `motion` with the install command above.

| Old skill | Now |
| --- | --- |
| `motion-studio` | `motion` (entry) |
| `motion-render` | `motion/references/render.md` |
| `motion-prompt` | `motion/references/brief.md` |
| `motion-maths` | `motion/references/maths.md` |
| `motion-principles` | `motion/references/principles.md` |
| `motion-engines` | `motion/references/engines.md` |
| `motion-sound` | `motion/references/sound.md` |
| `motion-critique` | `motion/references/critique.md` |
| `motion-export` | `motion/references/export.md` |
| `motion-harness` | `motion/references/harness.md` |
| `motion-pricing` | `motion/references/pricing.md` |

## Moved

`bun-testing`, `unslop` and `website-audit` now live in [arkaydeus/ork-skills](https://github.com/arkaydeus/ork-skills):

```bash
npx skills add arkaydeus/ork-skills --skill bun-testing
npx skills add arkaydeus/ork-skills --skill unslop
npx skills add arkaydeus/ork-skills --skill website-audit
```
