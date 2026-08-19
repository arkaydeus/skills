# skills

Agent skills for Cursor and Grok Bot.

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
