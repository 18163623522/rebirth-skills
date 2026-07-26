---
name: update-card
description: Update metadata and typed content for all nine user-creatable RebirthNote card types.
version: 2.0.0
tags: [rebirthnote, update, edit, cards]
---

# 更新卡片

## CLI

```bash
rebirth card update <card-id> --name "新标题 😀"
rebirth card update <card-id> --content "新的 Markdown"
rebirth card update <card-id> --file ./replacement.html --format html
rebirth card update <card-id> --table-json ./table.json
rebirth card update <card-id> --date 2026-07-26
rebirth card update <card-id> --start-date 2026-07-26 --end-date 2026-07-27
rebirth card update <card-id> --add-tags "工作,学习" --remove-tags "旧标签"
rebirth card update <card-id> --box <box-id> --collect
```

文件型更新使用活动存储根目录，可通过全局 `--storage-path` 或 `REBIRTHNOTE_STORAGE_PATH` 指定；实际文件位于其 `files` 子目录。

## MCP `card_update`

支持 `cardId`、`name`、`content`、`filePath`、`contentFormat`、`tableData`、`date`、`startDate`、`endDate`、`description`、`addTagIds`、`removeTagIds`、`boxId`、`isCollect`。`multi-table` 可通过 `tableData` 或 `filePath` 输入完整 JSON。成功返回更新后的完整规范化卡片。

## 类型化更新

- `card`、`diary`、`task`：Markdown 转 TipTap；日记和任务同步日期语义。
- `html`：原始 HTML 重新托管，更新 URL 与可搜索纯文本。
- `mermaid`：更新页面实际读取的 `sys_card_base.text`。
- `mind-map`：接受 `--content` 或 `--file` 提供的 Markdown/页面原生 JSON；更新时保留卡片 ID，并将根 `id`、节点/连线 `mapId` 统一为该卡片 ID，同时校验唯一根节点、父子关系、循环和连线引用。
- `draw-board`：接受页面原生 JSON，并将兼容简写规范化为页面读取的 `cardType/content` 元素。
- `multi-table`：使用 `tableData` 更新完整配置，并校验 `currentViewId`。
- `attachment`：`filePath` 替换文件，按 `MD5_原文件名` 重新托管，计算大小、URL、标准 `subType` 与索引；图片使用 `img`。

内容、基础字段和派生索引在同一事务内更新。HTML 仍是 `html` 卡片，不创建额外附件卡或 `sys_card_file`；HTML/附件先写新托管文件，事务成功后才清理无引用旧文件，失败时恢复原文件。附件内容解析失败不会回滚卡片，CLI/MCP 会通过 `warnings` 返回原因。标签可传完整 UUID 或唯一标签名。

历史 `rich-text`、旧 Mermaid 子表等仍可读取；内部类型不因此成为可创建类型。
