# playwright-slim

> 🚀 Playwright MCP with **91.9% token reduction** for AI models

[![npm version](https://img.shields.io/npm/v/playwright-slim.svg)](https://www.npmjs.com/package/playwright-slim)
[![Test Status](https://img.shields.io/badge/tests-passing-brightgreen)](https://github.com/palan-k/mcpslim)

## Performance

| Metric | Original | Slim | Improvement |
|--------|----------|------|-------------|
| Tools | 22 | 6 | **-73%** |
| Tokens | 2,922 | 237 | **91.9%** |

> **Version Info**
> - Original: `@playwright/mcp@0.0.54`
> - Slim version synced with original
> - Tokens measured with [tiktoken](https://github.com/openai/tiktoken) v1.0.21 (cl100k_base)

## Installation

```bash
npx playwright-slim
```

## Usage

### Claude Desktop

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "playwright-slim"]
    }
  }
}
```

### Claude Code CLI

```bash
claude mcp add playwright -- npx -y playwright-slim
```

### Gemini CLI

```bash
gemini mcp add playwright -- npx -y playwright-slim
```

### VS Code (Copilot, Cline, etc.)

```bash
code --add-mcp '{"name":"playwright","command":"npx","args":["-y","playwright-slim"]}'
```

### Cursor

Add to `.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "playwright-slim"]
    }
  }
}
```

## How It Works

MCPSlim acts as a **transparent bridge** between AI models and the original MCP server.

```
┌─────────────────────────────────────────────────────────────────┐
│  Without MCPSlim                                                │
│                                                                 │
│  [AI Model] ──── reads 22 tool schemas ────→ [Original MCP]    │
│             (2,922 tokens loaded into context)                    │
├─────────────────────────────────────────────────────────────────┤
│  With MCPSlim                                                   │
│                                                                 │
│  [AI Model] ───→ [MCPSlim Bridge] ───→ [Original MCP]           │
│       │                │                      │                 │
│   Sees 6 grouped      Translates to        Executes actual   │
│   tools only         original call       tool & returns    │
│   (237 tokens)                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Translation Flow

1. **AI reads slim schema** - Only 6 grouped tools instead of 22 (saves tokens)
2. **AI calls grouped tool** - e.g., `page({ action: "navigate", url: "..." })`
3. **MCPSlim translates** - Converts to: `navigate_page({ url: "..." })`
4. **Original MCP executes** - Real server processes the request
5. **Response returned** - Result passes back unchanged

**Zero functionality loss. 91.9% token savings.**

### Tool Groups

- `capture`: 4 actions
- `control`: 5 actions
- `interaction`: 8 actions
- `navigation`: 3 actions

- Plus 2 passthrough tools (ungrouped, but description slimmed)

## Compatibility

- ✅ All original `@playwright/mcp` functionality preserved
- ✅ Works with Claude, Gemini, ChatGPT, Qwen, and any MCP-compatible AI
- ✅ Same API - just use grouped action names
- ✅ Schema compatibility verified via automated tests

## License

MIT

---

Powered by [MCPSlim](https://github.com/palan-k/mcpslim) - MCP Token Compression Bridge
