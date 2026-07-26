---
name: manage-boxes
description: List, create boxes and view cards in a box in RebirthNote. Use when user wants to organize notes into collections/folders.
version: 1.1.0
tags: [rebirthnote, boxes, organize, collections]
---

## Instructions

### CLI

```bash
rebirth box list
rebirth box create "盒子名" --description "描述" --color "#FF5733"
rebirth box cards <box-id> --type card --limit 20
rebirth box cards <box-id> --type mind-map --limit 20
rebirth box stats      # Card count per box
```

### MCP Tools

- `box_list` — No parameters
- `box_create` — Parameters: `name`, `description?`, `color?`
- `box_cards` — Parameters: `boxId`, `cardType?`, `limit?`
  - `cardType` 与搜索一致：九种可创建类型为 `card` / `diary` / `task` / `html` / `mermaid` / `mind-map` / `draw-board` / `multi-table` / `attachment`；过滤旧库时仍可传实际存在的历史类型。
- `box_stats` — No parameters; returns card count per box

### Data Model

- Table: `sys_card_box`
- Cards link via `sys_card_base.boxId` — each card belongs to at most one box
- Boxes are scoped to a space (`spaceId`)
