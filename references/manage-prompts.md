---
name: manage-prompts
description: Manage prompt templates stored locally for RebirthNote CLI. Use when user wants to create, view, or manage reusable prompt templates.
version: 1.1.0
tags: [rebirthnote, prompts, templates]
---

## Instructions

### CLI

```bash
rebirth prompt list
rebirth prompt create --name "周报助手" --cmd "请基于本周笔记输出一份简洁周报"
rebirth prompt get <name>
rebirth prompt update <name> --cmd "新内容"
rebirth prompt delete <name>
```

### MCP Tools

- `prompt_list` — No parameters
- `prompt_get` — Parameters: `name`
- `prompt_create` — Parameters: `name`, `cmd`
- `prompt_update` — Parameters: `name`, `cmd`
- `prompt_delete` — Parameters: `name`

### Storage

- Prompts are stored locally in `~/.rebirthnote-cli/prompts.json`
- Not written to the RebirthNote database
- Each prompt has: `name`, `cmd` (content), `createdAt`, `updatedAt`
