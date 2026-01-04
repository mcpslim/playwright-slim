# playwright-slim

> **Playwright MCP server optimized for AI assistants** — Reduce context window tokens by 76.3% while keeping full functionality. Compatible with Claude, ChatGPT, Gemini, Cursor, and all MCP clients.

[![npm version](https://img.shields.io/npm/v/playwright-slim.svg)](https://www.npmjs.com/package/playwright-slim)
[![Test Status](https://img.shields.io/badge/tests-passing-brightgreen)](https://github.com/mcpslim/mcpslim)
[![MCP Compatible](https://img.shields.io/badge/MCP-compatible-blue)](https://modelcontextprotocol.io)

## What is playwright-slim?

A **token-optimized** version of the Playwright [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server.

### The Problem

MCP tool schemas consume significant **context window tokens**. When AI assistants like Claude or ChatGPT load MCP tools, each tool definition takes up valuable context space.

The original `@playwright/mcp` loads **22 tools** consuming approximately **~15,462 tokens** — that's space you could use for actual conversation.

### The Solution

`playwright-slim` intelligently **groups 22 tools into 6 semantic operations**, reducing token usage by **76.3%** — with **zero functionality loss**.

Your AI assistant sees fewer, smarter tools. Every original capability remains available.

## Performance

| Metric | Original | Slim | Reduction |
|--------|----------|------|-----------|
| Tools | 22 | 6 | **-73%** |
| Schema Tokens | 2,922 | 237 | **91.9%** |
| Claude Code (est.) | ~15,462 | ~3,657 | **~76.3%** |

> **Benchmark Info**
> - Original: `@playwright/mcp@0.0.54`
> - Schema tokens measured with [tiktoken](https://github.com/openai/tiktoken) (cl100k_base)
> - Claude Code estimate includes ~570 tokens/tool overhead

## Installation

```bash
npx playwright-slim
```

No additional setup required. The slim server wraps the original MCP transparently.

## Usage

### Claude Desktop

Add to your `claude_desktop_config.json`:

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

### Claude Code (CLI)

```bash
claude mcp add playwright -- npx -y playwright-slim
```

### Gemini CLI

```bash
gemini mcp add playwright -- npx -y playwright-slim
```

### VS Code (Copilot, Cline, Roo Code)

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

MCPSlim acts as a **transparent bridge** between AI models and the original MCP server:

```
┌─────────────────────────────────────────────────────────────────┐
│  Without MCPSlim                                                │
│                                                                 │
│  [AI Model] ──── reads 22 tool schemas ────→ [Original MCP]    │
│             (~15,462 tokens loaded into context)                 │
├─────────────────────────────────────────────────────────────────┤
│  With MCPSlim                                                   │
│                                                                 │
│  [AI Model] ───→ [MCPSlim Bridge] ───→ [Original MCP]           │
│       │                │                      │                 │
│   Sees 6 grouped      Translates to        Executes actual   │
│   tools only         original call       tool & returns    │
│   (~3,657 tokens)                                              │
└─────────────────────────────────────────────────────────────────┘
```

### How Translation Works

1. **AI reads slim schema** — Only 6 grouped tools instead of 22
2. **AI calls grouped tool** — e.g., `interaction({ action: "click", ... })`
3. **MCPSlim translates** — Converts to original: `browser_click({ ... })`
4. **Original MCP executes** — Real server processes the request
5. **Response returned** — Result passes back unchanged

**Zero functionality loss. 76.3% token savings.**

## Available Tool Groups

| Group | Actions |
|-------|---------|
| `capture` | 4 |
| `control` | 5 |
| `interaction` | 8 |
| `navigation` | 3 |

Plus **2 passthrough tools** — tools that don't group well are kept as-is with optimized descriptions.

## Compatibility

- ✅ **Full functionality** — All original `@playwright/mcp` features preserved
- ✅ **All AI assistants** — Works with Claude, ChatGPT, Gemini, Copilot, and any MCP client
- ✅ **Drop-in replacement** — Same capabilities, just use grouped action names
- ✅ **Tested** — Schema compatibility verified via automated tests

## FAQ

### Does this reduce functionality?

**No.** Every original tool is accessible. Tools are grouped semantically (e.g., `click`, `hover`, `drag` → `interaction`), but all actions remain available via the `action` parameter.

### Why do AI assistants need token optimization?

AI models have limited context windows. MCP tool schemas consume tokens that could be used for conversation, code, or documents. Reducing tool schema size means more room for actual work.

### Is this officially supported?

MCPSlim is a community project. It wraps official MCP servers transparently — the original server does all the real work.

## License

MIT

---

<p align="center">
  Powered by <a href="https://github.com/mcpslim/mcpslim"><b>MCPSlim</b></a> — MCP Token Optimizer
  <br>
  <sub>Reduce AI context usage. Keep full functionality.</sub>
</p>
