---
name: create-card
description: Create any of the nine user-creatable RebirthNote card types through CLI or MCP.
version: 2.0.0
tags: [rebirthnote, create, cards]
---

# 创建卡片

CLI 与 MCP 只开放九种用户卡片：

| `cardType` | 输入 | 页面兼容存储 |
|---|---|---|
| `card` | Markdown | TipTap → `sys_card_rich_text` |
| `diary` | Markdown、可选 `date` | TipTap；默认本地当天并维护 `card-date` |
| `task` | Markdown、`startDate/endDate` | TipTap；日期范围与默认 `styleData` |
| `html` | 原始 HTML 或 HTML 文件 | 托管文件，基础表写 `url/localPath/text` |
| `mermaid` | Mermaid 源码 | `sys_card_base.text` |
| `mind-map` | Markdown 或原生 JSON | `sys_card_mind_map`；根与节点/连线统一使用卡片 ID 作为 `mapId` |
| `draw-board` | 原生画板 JSON | `sys_card_drawboard` |
| `multi-table` | 原生表格 JSON | `sys_card_multi_table` 的完整配置 |
| `attachment` | 本地文件路径 | 托管文件及 `sys_card_file` |

`mark`、`card-date`、`local-directory`、`rich-text` 是内部或历史类型，只支持读取/搜索/兼容更新，不允许直接创建。附件的 `img`、`video`、`audio`、`pdf` 等是 `subType`，不是顶级卡片类型。

## CLI

```bash
rebirth card create --name "笔记 😀" --content "# 正文"
rebirth card create --name "日记" --type diary --date 2026-07-26 --content "今天很好 👨‍👩‍👧‍👦"
rebirth card create --name "任务" --type task --start-date 2026-07-26 --end-date 2026-07-27 --content "- [ ] 完成"
rebirth card create --name "网页" --type html --format html --file ./page.html
rebirth card create --name "流程图" --type mermaid --format mermaid --content "graph TD; A[😀]-->B"
rebirth card create --name "脑图" --type mind-map --format markdown --content "# 根节点"
rebirth card create --name "画板" --type draw-board --format json --file ./board.json
rebirth card create --name "数据表" --type multi-table --table-json ./table.json
rebirth card create --name "图片" --type attachment --file ./photo.png
```

参数：

- 通用：`--name`、`--type`、`--content`、`--file`、`--format`、`--tags`、`--box`、`--description`。
- 全局存储：`rebirth --storage-path /path/to/rebirth-storage card create ...`；也可使用 `REBIRTHNOTE_STORAGE_PATH`。参数表示存储根目录，托管文件写入其 `files` 子目录。
- 日期：`--date`、`--start-date`、`--end-date`，格式均为 `YYYY-MM-DD`。
- 多维表：`--table-json` 可接 JSON 字符串或 JSON 文件；省略时创建含默认视图和标题字段的可用空表。
- `--file` 对 HTML、画板、多维表表示输入内容文件，对附件表示要导入的文件。
- `--file` 对思维导图也支持 Markdown 或 JSON 文件，格式由 `--format markdown|json` 指定。

思维导图写入前会执行页面契约规范化：

- 根对象 `id`、所有节点 `mapId`、所有连线 `mapId` 统一改为新卡片 ID。
- 必须且只能有一个 `cardType=mind-map-node` 且 `isRoot=true` 的根节点。
- 非根节点必须引用存在的 `parentId`，不能出现父子循环。
- 连线的 `from/to` 必须引用存在的节点。

这保证脑图嵌入画布后，页面能通过节点 `mapId` 找到画布中的脑图容器，拖动根节点时子节点与连线会一起重新布局。

画板接受页面原生结构，也兼容 `type/text` 简写；写入前统一规范化为 `cardType/content`。文本元素必须提供非空 `id`，未传坐标时从页面默认视口附近自动排列：

```json
{
  "elements": [
    {
      "id": "text-1",
      "type": "text",
      "text": "画板内容 🎨"
    }
  ],
  "lines": [],
  "customDemos": []
}
```

HTML 会作为 `html` 卡片托管到活动文件目录并写入 `user-data://files/...`，不会额外创建一张 `attachment` 卡。附件按 `MD5_原文件名` 导入，图片数据库子类型固定为 `img`。

## MCP `card_create`

字段与 CLI 对应：`name`、`cardType`、`content`、`filePath`、`contentFormat`、`tableData`、`tags`、`boxId`、`description`、`date`、`startDate`、`endDate`。成功返回完整规范化卡片对象，而不是只有 ID。

每种类型会做条件校验；缺失附件、非法 JSON、错误日期、空 Mermaid 等不会创建主表空壳。标题、Markdown、HTML、Mermaid 以及结构化文本节点中的 Unicode Emoji 会原样保存并进入搜索文本。
