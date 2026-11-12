---
name: mcp
description: "Use the ScrapeUnblocker MCP server to give Claude (or any MCP client) live web access - fetch any page as HTML or Markdown, get AI-parsed JSON, run a Google search or a ready-made site plugin (Amazon, Walmart, Google Maps, YouTube, Zillow...), all with the user's own API key. Use when the user asks to set up the MCP server, wire ScrapeUnblocker into an agent/assistant, or wants Claude to read/scrape the web through their key. Covers the local (npx) and hosted (HTTP) servers and the tools."
---

# ScrapeUnblocker MCP server

The MCP server lets Claude Desktop/Code, claude.ai, ChatGPT and any MCP client reach the web through ScrapeUnblocker, using the user's own key. The ScrapeUnblocker plugins for Claude Code and Cursor already bundle it, so there the tools below are available directly; anywhere else, set it up as shown under Setup.

## The tools

| Tool | What it returns |
|---|---|
| `fetch_html` | Fully rendered HTML of a URL (anti-bot bypassed) |
| `fetch_parsed` | AI-parsed structured JSON of a URL |
| `google_search` | Structured Google organic results |
| `fetch_markdown` | A page as clean Markdown (main content, absolute links; `main_content_only: false` for the whole page), usually 2-25% the size of the HTML |
| `search_and_read` | A Google search plus its top 1-5 result pages as Markdown, in one call |
| `list_elements` | A page's links, inputs, buttons and selects, each with a ready-to-use `selector` |
| `browse_page` | Runs browser `steps` (click, type, select, scroll, wait) on a page, then returns the HTML. The one tool not marked read-only: steps act on the website |
| `list_plugins` / `run_plugin` | 100+ ready-made site scrapers that return clean JSON: find one with `list_plugins`, run it with `run_plugin` |
| `amazon_search`, `amazon_product`, `amazon_reviews`, `walmart_search`, `ebay_search`, `google_shopping`, `google_local`, `maps_place_details`, `google_images`, `youtube_search`, `youtube_transcript`, `zillow_search`, `indeed_jobs`, `trustpilot_reviews`, `booking_hotels` | The most-used site plugins as tools of their own |

When any of these fits the task, call the tool instead of writing an HTTP request.

## Setup

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
- A site with a plugin (check `list_plugins`): the plugin. One-off structured data from any other URL: `fetch_parsed`. Reading or summarising a page: `fetch_markdown` (in code: `getPageSource` with `markdown=true`, see the `page-source` skill). Markup or selectors: `fetch_html`. Clicking or typing first: `list_elements`, then `browse_page`. Answering a question from the web: `search_and_read`. Just the links: `google_search`.

Docs: https://docs.scrapeunblocker.com/sdks/mcp?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
