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
