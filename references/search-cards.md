---
name: search-cards
description: Search RebirthNote cards by keyword, tag, box, type, date range. Use when user wants to find or query notes.
version: 1.0.0
tags: [rebirthnote, search, cards, query]
---

## Instructions

When the user wants to search or find notes/cards, use the RebirthNote CLI or MCP tools.

### CLI

```bash
rebirth card search "关键词"
rebirth card search --tag "标签名"
rebirth card search --tag-id "标签UUID"
rebirth card search --box "盒子ID" --type card
rebirth card search --type diary --limit 20
rebirth card search --collect --from "2026-01-01" --to "2026-03-08"
rebirth card search --sort updateTime --order desc --limit 20 --offset 0
```

### MCP Tool: `card_search`

Parameters:
- `keyword` — 搜索关键词
- `tagId` / `tagName` — 标签筛选
- `boxId` — 盒子筛选
- `cardType` — 按类型筛选。九种可创建类型为 `card`、`diary`、`task`、`html`、`mermaid`、`mind-map`、`draw-board`、`multi-table`、`attachment`；筛选历史数据时也可遇到 `rich-text`、`card-date`、`mark` 等内部类型。
- 标题、正文、HTML、Mermaid 与结构化文本节点中的 Unicode Emoji 会保留在派生搜索文本中，可直接用 Emoji 关键词搜索。
- `subType` — `pdf | audio | video | img | web-clip` 等
- `isCollect` — 仅收藏
- `from` / `to` — 日期范围（ISO 8601）
- `limit` / `offset` — 分页
- `sort` / `order` — 排序

### Notes

- Search scope: card name, plain text content, annotations, description, code strings
- Default: sorted by update time DESC, only non-deleted cards (delFlag=0)
- Content returned as Markdown where applicable
