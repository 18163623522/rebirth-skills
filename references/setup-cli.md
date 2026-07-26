---
name: setup-cli
description: Build, configure, and run the RebirthNote CLI and MCP Server. Use when user needs to set up, build, or configure the CLI/MCP integration.
version: 1.1.0
tags: [rebirthnote, cli, mcp, setup, configuration]
---

## Instructions

### Build

```bash
npm run build:cli          # Build → cli-mcp-publish/dist-cli/index.mjs
node cli-mcp-publish/dist-cli/index.mjs   # Run CLI directly
```

### Global Options

```bash
--db-path <path>    # Override database file path
--storage-path <path> # Override Rebirth storage root; managed files use its files/ child
--space-id <id>     # Override space ID
--json              # JSON output mode
--verbose           # Verbose logging
--quiet             # Silent mode
```

### Database Path Discovery

The CLI automatically finds the SQLite database in this order:
1. `--db-path` command argument
2. `REBIRTHNOTE_DB_PATH` environment variable
3. `dbPath` in `~/.rebirthnote-cli/config.json`
4. OS default:
   - macOS: `~/Library/Application Support/Rebirth/rebirth_database/rebirth.db`
   - Windows: `%APPDATA%/Rebirth/rebirth_database/rebirth.db`
   - Linux: `~/.config/Rebirth/rebirth_database/rebirth.db`

### MCP Server

```bash
rebirth mcp stdio                              # stdio mode (for Claude Code)
rebirth mcp serve --port 45137 --host 127.0.0.1  # SSE mode (for Cursor)
```

### Generate MCP Config

```bash
rebirth mcp config --cursor       # → Cursor MCP config JSON
rebirth mcp config --claude-code  # → Claude Code MCP config JSON
```

### Cursor MCP Config

```json
{ "mcpServers": { "rebirthnote": { "url": "http://127.0.0.1:45137/mcp" } } }
```

### Claude Code MCP Config

```json
{ "mcpServers": { "rebirthnote": { "command": "rebirth-mcp", "args": [] } } }
```

### System Info

```bash
rebirth info     # Database path, size, card/space count
rebirth stats    # Card type breakdown, tag/box counts
```

MCP exposes the same queries as `info` and `stats`.
