# skills

Agent skills for Cursor and Grok Bot.

## unslop

Standalone extract of Cursor's official pstack `unslop` skill, with a few additions from [theclaymethod/unslop](https://github.com/theclaymethod/unslop).

Kept from Cursor: the 31 rewrite rules. Invoke-only here, not always-on, so it does not burn tokens on ordinary chat.

Added from claymethod: fact-preservation guards, an audit-only mode, and extra tells (throat-clearing, reasoning leaks, knowledge-cutoff residue, false agency, essay scaffolding).

Left out: Python scanners, voice teach/mimic, eval suite, and anything aimed at detector evasion.

Sources: [cursor/plugins pstack unslop](https://github.com/cursor/plugins/blob/main/pstack/skills/unslop/SKILL.md) (Lauren Tan, MIT); [theclaymethod/unslop](https://github.com/theclaymethod/unslop) (MIT).

### Install

```bash
npx skills add arkaydeus/skills --skill unslop
```

Or copy `unslop/SKILL.md` into your skills folder.

### Use

Invoke only: `/unslop`, `@unslop`, or an explicit ask to unslop / humanise a draft. It does not run on ordinary replies. Say "just flag it" for an audit and no rewrite.
