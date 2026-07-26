# RebirthNote AI Skills

**只有一个对外 skill**：根目录的 `SKILL.md` 是统一入口；按操作拆分的细节在 `references/`，实现渐进式披露。

## 使用方式

1. **先读** `skills/SKILL.md`（本仓库唯一对外的 skill 入口）。
2. 根据用户意图在「操作索引」表里找到对应操作与「何时读」。
3. **仅当**需要该操作的参数/示例/边界时，再打开 `skills/references/<操作>.md`：
   - 例如搜索 → `skills/references/search-cards.md`
   - 例如创建 → `skills/references/create-card.md`
   - 其余见 `SKILL.md` 内表格。

## 目录结构（方案 B，无额外 skill 根目录）

```
skills/
├── SKILL.md              ← 唯一入口：概述 + 操作索引 + 工具总览 + 边界（先读）
├── README.md             ← 本说明
├── references/           ← 按操作拆分的详细说明（按需阅读）
│   ├── search-cards.md
│   ├── create-card.md
│   ├── view-card.md
│   ├── update-card.md
│   ├── delete-card.md
│   ├── manage-tags.md
│   ├── manage-boxes.md
│   ├── manage-spaces.md
│   ├── manage-todo.md
│   ├── manage-prompts.md
│   └── setup-cli.md
├── scripts/              ← 可执行脚本（Python/Bash 等），按需添加
└── assets/               ← 模板与资源文件，按需添加
```

## 与各工具的配合

| 工具       | 建议用法 |
|------------|----------|
| Cursor     | 将 `skills/SKILL.md` 或 `AGENTS.md` 纳入规则/上下文；按需引用 `skills/references/*.md`。 |
| Claude Code| 使用根目录 `CLAUDE.md` / `AGENTS.md`；需要细节时再打开 `skills/references/<操作>.md`。 |
| Trae 等    | 使用根目录 `AGENTS.md`；技能细节以 `skills/SKILL.md` 为索引，按操作读 references。 |

**结论**：对外只暴露一个 skill（根 `SKILL.md`），通过「操作索引」+「何时读」控制上下文体积。

CLI 与 MCP 的业务能力保持一一覆盖：除 MCP 服务自身的启停和配置命令外，
CLI 的 29 项业务命令均有对应 MCP 工具。完整机器校验由
`scripts/check-card-contract.mjs` 执行。

## cardType 约定（CLI/MCP 创建）

- 九种用户卡片：`card`、`diary`、`task`、`html`、`mermaid`、`mind-map`、`draw-board`、`multi-table`、`attachment`。
- 思维导图通过 CLI/MCP 创建或更新时，根 `id`、节点/连线 `mapId` 会统一为卡片 ID，保证嵌入画板后的整树移动与布局同步。
- `rich-text`、`mark`、`card-date`、`local-directory` 是内部或历史类型，不开放直接创建。
- 附件使用顶级类型 `attachment`，图片等媒体类型写入 `subType`，其中图片统一为 `img`。
- 所有文本字段支持 Unicode Emoji 原样往返。详细输入和返回契约见 `references/create-card.md`、`references/update-card.md`、`references/view-card.md`。
