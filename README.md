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
