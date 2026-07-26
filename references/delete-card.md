---
name: delete-card
description: Soft delete, restore, or permanently purge RebirthNote cards. Use when user wants to delete or recover notes.
version: 1.1.0
tags: [rebirthnote, delete, restore, cards]
---

## Instructions

### CLI

```bash
rebirth card delete <card-id>    # Soft delete (delFlag=1, recoverable)
rebirth card restore <card-id>   # Restore (delFlag=0)
rebirth card purge <card-id>     # Permanent delete (irreversible)
```

### MCP Tools

- `card_delete` — Parameters: `cardId`. Sets delFlag=1.
- `card_restore` — Parameters: `cardId`. Sets delFlag=0.
- `card_purge` — Parameters: `cardId`, `confirm: true`. Permanently deletes the card.

### Behavior

- **Soft delete**: Sets `delFlag=1`, data stays in database, card hidden from normal queries
- **Restore**: Sets `delFlag=0`, card reappears in queries
- **Purge**: Permanently removes card from all tables:
  - `sys_card_base` (main record)
  - `sys_card_rich_text` / `sys_card_mind_map` / `sys_card_mermaid` / `sys_card_drawboard` / `sys_card_multi_table` / `sys_card_file` (content sub-tables)
  - `sys_relate_id` (card relationships)
  - `card_derived_text` (search index)

### Warning

Purge is irreversible. Always confirm with the user before executing; MCP
requires the literal `confirm: true`.
