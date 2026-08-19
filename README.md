# skills

Agent skills for Cursor and Grok Bot.

## unslop

Standalone extract of Cursor's official pstack `unslop` skill. The 31 rewrite rules are unchanged. The rest of pstack is not included.

Source: [cursor/plugins pstack unslop](https://github.com/cursor/plugins/blob/main/pstack/skills/unslop/SKILL.md), MIT.

### Install

```bash
npx skills add arkaydeus/skills --skill unslop
```

Or copy `unslop/SKILL.md` into your skills folder.

### Use

Enable it on the bots you want. The skill says it must always apply: once enabled, the agent should follow these rules on every reply, not only when you type `/unslop`.
