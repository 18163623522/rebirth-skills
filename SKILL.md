---
name: rebirthnote
description: Operate RebirthNote notes via CLI and MCP — search, create, view, update, delete cards; manage tags, boxes, spaces, todos, prompts; setup CLI/MCP. One skill entry point; see references/ for per-operation details (progressive disclosure).
version: 2.1.1
tags: [rebirthnote, notes, cli, mcp, zettelkasten]
---

# RebirthNote — 统一技能入口

## 概述

- **skill_id**: `rebirthnote`
- **名称**: RebirthNote 笔记操作
- **能力**: 通过 CLI 命令与 MCP 工具对 RebirthNote 本地笔记进行检索、创建、查看、更新、删除，以及管理标签、盒子、空间、待办和提示词模板；支持结构化 JSON 与自然语言两种输出。

## 适用场景

- 用户要求搜索、整理、创建、修改、删除笔记
- 需要按标签/盒子/空间/类型/日期筛选卡片
- 需要管理待办、提示词模板或 CLI/MCP 配置

## 不适用场景

- 修改 RebirthNote 主应用源码（`src/`）或数据库表结构
- 需要云端同步、登录认证（CLI/MCP 仅操作本地库）
- 需要修改 RebirthNote 页面布局、渲染器源码或数据库结构

---

## 操作索引（渐进披露：先读本表，再按需打开 references 下对应 md）

| 操作 | 说明 | 何时读 | 详细说明位置 |
|------|------|--------|--------------|
| **搜索卡片** | 按关键词、标签、盒子、类型、日期搜索 | 用户要查找/筛选笔记 | `skills/references/search-cards.md` |
| **创建卡片** | 创建九种用户卡片，支持 Markdown、HTML、Mermaid、结构化 JSON 与附件 | 用户要新建笔记 | `skills/references/create-card.md` |
| **查看卡片** | 查看单张或批量卡片，内容转为 Markdown | 用户要读某张卡片全文或元数据 | `skills/references/view-card.md` |
| **更新卡片** | 修改标题、内容、标签、盒子、收藏状态 | 用户要改已有笔记 | `skills/references/update-card.md` |
| **删除/恢复卡片** | 软删除、恢复、永久删除 | 用户要删或恢复笔记 | `skills/references/delete-card.md` |
| **标签管理** | 列出/创建/删除/重命名标签，查看标签下卡片 | 用户要管标签 | `skills/references/manage-tags.md` |
| **盒子管理** | 列出/创建盒子，查看盒子下卡片与统计 | 用户要管盒子 | `skills/references/manage-boxes.md` |
| **空间管理** | 列出空间、切换当前空间 | 用户要切换空间 | `skills/references/manage-spaces.md` |
| **待办管理** | 搜索待办、按状态筛选、统计 | 用户要管任务列表 | `skills/references/manage-todo.md` |
| **提示词模板** | 列出/创建/查看/更新/删除本地提示词模板 | 用户要管 CLI 本地 prompts | `skills/references/manage-prompts.md` |
| **CLI/MCP 配置** | 构建、数据库路径、MCP 启动与配置生成 | 用户要搭建或排错 CLI/MCP | `skills/references/setup-cli.md` |

**使用方式**：只把本 `SKILL.md` 放进上下文即可起步；**仅当**上表中「何时读」匹配当前任务时，再打开对应 `skills/references/<名>.md`，避免一次性加载全部细节。

**目录约定**（与 skill-name 标准对齐，无额外 rebirthnote 包一层）：

- `skills/SKILL.md` — 本文件，唯一入口
- `skills/references/*.md` — 按操作拆分的详细说明（上表）
- `skills/scripts/` — 可执行脚本（Python/Bash 等），按需添加
- `skills/assets/` — 模板与静态资源，按需添加

---

## 工具总览

### CLI 命令（全局选项：`--db-path`, `--storage-path`, `--space-id`, `--json`）

- `rebirth card search` / `create` / `get` / `batch-get` / `update` / `delete` / `restore` / `purge`
- `rebirth tag list` / `create` / `delete` / `rename` / `cards`
- `rebirth box list` / `create` / `cards` / `stats`
- `rebirth space list` / `use` / `current`
- `rebirth todo search` / `stats`
- `rebirth prompt list` / `create` / `get` / `update` / `delete`
- `rebirth mcp stdio` / `mcp serve` / `mcp config --cursor` / `mcp config --claude-code`
- `rebirth info` / `rebirth stats`

### MCP Tools（29 个）

`card_create`, `card_search`, `card_get`, `card_batch_get`, `card_update`, `card_delete`, `card_restore`, `card_purge`, `tag_list`, `tag_create`, `tag_delete`, `tag_rename`, `tag_cards`, `box_list`, `box_create`, `box_cards`, `box_stats`, `space_list`, `space_use`, `space_current`, `todo_search`, `todo_stats`, `prompt_list`, `prompt_create`, `prompt_get`, `prompt_update`, `prompt_delete`, `info`, `stats`

---

## 策略与边界

- **输出**：下游自动化优先用结构化 JSON（CLI 加 `--json`，全部 MCP 工具返回可解析的 JSON 文本）；直接给人看时可再转为自然语言或表格。
- **能力对齐**：除 MCP 服务自身的 `stdio/serve/config` 启停配置命令外，CLI 的 29 项业务命令均有同名语义的 MCP 工具；永久删除仍必须先向用户确认。
- **卡片类型**：开放 `card`、`diary`、`task`、`html`、`mermaid`、`mind-map`、`draw-board`、`multi-table`、`attachment` 九种用户卡片。
- **思维导图契约**：CLI/MCP 创建或更新 `mind-map` 时统一根 `id`、节点/连线 `mapId` 与卡片 ID，保证嵌入画布后的整树拖拽和布局同步；原生 JSON 还会校验父子及连线引用。
- **内容格式**：普通卡片、日记、任务使用 Markdown；HTML、Mermaid、画板、多维表等使用各自页面原生格式。
- **不做**：不修改 `src/`、不执行 `synchronize: true`、不绕过纯文本提取与分词写入索引。
- **权限**：仅访问本地配置与数据库，无网络、无认证；数据库路径由 `--db-path` / 环境变量 / `~/.rebirthnote-cli/config.json` 解析。文件存储根目录由 `--storage-path`、`REBIRTHNOTE_STORAGE_PATH`、CLI 配置或 Rebirth 的 Electron `config.json` 依次解析，实际文件进入其 `files` 子目录。

---

## 示例（高层）

1. **「帮我查最近 10 条笔记」** → 再读 `skills/references/search-cards.md`；CLI: `rebirth card search --limit 10` 或 MCP: `card_search`。
2. **「新建一篇标题为 X 的 Markdown 笔记」** → 再读 `skills/references/create-card.md`；CLI: `rebirth card create --name X --content "..."` 或 MCP: `card_create`。
3. **「把这张卡片移入某盒子」** → 再读 `skills/references/update-card.md`；CLI: `rebirth card update <id> --box <boxId>` 或 MCP: `card_update`。

---

## 版本

- 2.1.1：修复 npm 发布包运行时依赖遗漏，CLI/MCP 双入口版本统一，并增加干净安装启动门禁。
- 2.1.0：补齐 CLI 的全部 29 项业务能力到 MCP，并增加命令面、参数和 Skill 文档的防漂移校验。
- 2.0.0：统一九种用户卡片的 CLI/MCP/Skill 契约，补齐结构化内容、附件与 Unicode Emoji 往返。
- 1.1.0：入口仍为根 `SKILL.md`；按操作细节迁至 `references/*.md`，并约定 `scripts/`、`assets/`。
- 1.0.0：统一入口 + 11 个子目录各一 `SKILL.md`（已废弃该布局）。
