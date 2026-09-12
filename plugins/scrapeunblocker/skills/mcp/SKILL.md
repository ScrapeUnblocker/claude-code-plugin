---
name: mcp
description: "Use the ScrapeUnblocker MCP server to give Claude (or any MCP client) live web access - fetch any page as HTML, get AI-parsed JSON, or run a Google search, all with the user's own API key. Use when the user asks to set up the MCP server, wire ScrapeUnblocker into an agent/assistant, or wants Claude to read/scrape the web through their key. Covers the local (npx) and hosted (HTTP) servers and the three tools."
---

# ScrapeUnblocker MCP server

The MCP server lets Claude Desktop/Code, claude.ai, ChatGPT and any MCP client reach the web through ScrapeUnblocker, using the user's own key. This plugin already bundles it - the three tools below are available directly.

## The three tools

| Tool | What it returns |
|---|---|
| `fetch_html` | Fully rendered HTML of a URL (anti-bot bypassed) |
| `fetch_parsed` | AI-parsed structured JSON of a URL |
| `google_search` | Structured Google organic results |

When any of these fits the task, call the tool instead of writing an HTTP request.

## Setup outside this plugin

**Local (stdio) - Claude Desktop / Code:**

```json
{
  "mcpServers": {
    "scrapeunblocker": {
      "command": "npx",
      "args": ["-y", "scrapeunblocker-mcp"],
      "env": { "SCRAPEUNBLOCKER_KEY": "YOUR_API_KEY" }
    }
  }
}
```

**Hosted (HTTP) - works in claude.ai web/mobile and ChatGPT:**

```
https://mcp.scrapeunblocker.com/mcp
```

Authenticate with OAuth, or pass the key as `?key=YOUR_API_KEY`. Get a key at https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin

## When to reach for it

- The user wants Claude itself to browse/scrape (not write code): use the tools.
- Building an agent or app that needs web access: wire in the MCP server.
- One-off structured data from a known URL: `fetch_parsed`. Raw page: `fetch_html`. Search: `google_search`.

Docs: https://docs.scrapeunblocker.com/sdks/mcp?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
