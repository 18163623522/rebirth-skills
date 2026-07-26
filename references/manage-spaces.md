---
name: manage-spaces
description: List and switch spaces in RebirthNote. Use when user needs to work with different note spaces.
version: 1.1.0
tags: [rebirthnote, spaces, workspace]
---

## Instructions

### CLI

```bash
rebirth space list          # 每行输出 id,name（完整 UUID，便于复制到 space use）
rebirth space use <id>      # Set default space (saved to ~/.rebirthnote-cli/config.json)
rebirth space current       # Show current space
rebirth space list --json   # JSON 数组 [{ "id", "name" }, ...]
```

### MCP Tools

- `space_list` — No parameters
- `space_use` — Parameters: `spaceId`; updates the default space in CLI config
- `space_current` — No parameters; returns the currently resolved space

### Space Resolution Priority

CLI determines the current space in this order:
1. `--space-id` command-line argument
2. `defaultSpaceId` in `~/.rebirthnote-cli/config.json`
3. First enabled space in database

### Data Model

- Table: `sys_space`, type: `0` = personal, `1` = team
- All cards, tags, and boxes are scoped by `spaceId`
