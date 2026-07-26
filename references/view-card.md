---
name: view-card
description: Read normalized metadata and typed content for RebirthNote cards.
version: 2.0.0
tags: [rebirthnote, view, read, cards]
---

# 查看卡片

```bash
rebirth card get <card-id>
rebirth card get <card-id> --content-only
rebirth card get <card-id> --meta-only
rebirth card get <card-id> --raw
rebirth card batch-get <id1> <id2>
```

MCP 使用 `card_get({ cardId, raw? })` 或 `card_batch_get({ cardIds })`，返回 JSON 格式的规范化卡片；`raw: true` 对应 CLI `--raw`。

| 类型 | 读取结果 |
|---|---|
| `card` / `diary` / `task` / 历史 `rich-text` | TipTap 转 Markdown |
| `html` | 原始 HTML，同时返回 `url` 与 `localPath` |
| `mermaid` | 优先读取页面字段 `sys_card_base.text`，兼容旧 `sys_card_mermaid` |
| `mind-map` / `draw-board` | 页面原生 JSON |
| `multi-table` | `content` 及完整 `tableData`：`data/attrList/viewList/currentViewId/relationTableId` |
| `attachment` | 文件名、URL、本地路径、MD5、大小及提取文本 |

九种可创建类型为 `card`、`diary`、`task`、`html`、`mermaid`、`mind-map`、`draw-board`、`multi-table`、`attachment`。内部和历史类型仍按已有数据尽量兼容读取。
