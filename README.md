# skills

Agent skills for Cursor and Grok Bot.

## bun-testing

Guidance for deterministic `bun:test` suites, with stable module mocks, explicit cleanup, file isolation, and a workflow for diagnosing order-dependent failures.

### Install

```bash
npx skills add arkaydeus/skills --skill bun-testing
```

Or copy `bun-testing/SKILL.md` into your skills folder.

### Use

The skill applies when writing, modifying, debugging, or reviewing Bun tests. Invoke it explicitly with `/bun-testing` or `@bun-testing` when your client supports skill commands.

## unslop

Extract of Cursor's official pstack `unslop` skill, plus fact-preservation guards, an audit-only mode, and extra tells.

Source: [cursor/plugins pstack unslop](https://github.com/cursor/plugins/blob/main/pstack/skills/unslop/SKILL.md) (Lauren Tan, MIT).

### Install

```bash
npx skills add arkaydeus/skills --skill unslop
```

Or copy `unslop/SKILL.md` into your skills folder.

### Use

Invoke only: `/unslop`, `@unslop`, or an explicit ask to unslop / humanise a draft. It does not run on ordinary replies. Say "just flag it" for an audit and no rewrite.

## website-audit

Audit a live website from a URL. Automate robots.txt, sitemap.xml, meta/OG tags, headers, and status codes; run Lighthouse when available; inspect the rest in the browser. Returns a prioritised pass/warn/fail report with a concrete fix for every fail or warning.

### Install

```bash
npx skills add arkaydeus/skills --skill website-audit
```

Or copy `website-audit/SKILL.md` into your skills folder.

### Use

The skill applies when you ask for a website audit, launch checklist, or SEO/UX health check, or share a URL to review. Invoke it explicitly with `/website-audit` or `@website-audit` when your client supports skill commands.

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
