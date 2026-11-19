# ScrapeUnblocker plugin

Web search and content extraction for Claude Code: Google results, any public web page as Markdown, HTML or AI-parsed JSON, and structured data from 180+ sites.

Install from the marketplace in this repository:

```
/plugin marketplace add ScrapeUnblocker/claude-code-plugin
/plugin install scrapeunblocker@scrapeunblocker
```

## Components

| Type | Name | Purpose |
|------|------|---------|
| MCP server | `scrapeunblocker` | `fetch_html`, `fetch_markdown`, `fetch_parsed`, `google_search`, `search_and_read`, `list_elements`, `browse_page`, site plugins |
| Command | `/scrapeunblocker:scrape-url` | One-shot scrape of a URL: HTML, Markdown or parsed JSON |
| Skill | `best-practices` | API reference for writing integrations |
| Skill | `page-source` | Rendered HTML or Markdown, render waits, country targeting |
| Skill | `parsed-data` | Structured extraction, fixing a bad parse |
| Skill | `serp` | Google search results as JSON (organic, ads, AI overview) |
| Skill | `structured-data` | Plugin data (Skyscanner, eBay, Google Local) + parsed_data |
| Skill | `mcp` | Use the ScrapeUnblocker MCP server (local + hosted) |
| Skill | `competitive-intel` | Competitor pricing, features, reviews, hiring signals |
| Skill | `price-comparison` | Compare prices across retailers, rank offers |

## Configuration

The plugin asks for your API key at enable time (`api_key`, stored as a sensitive value in the OS keychain) and sends it to the hosted MCP server (`https://mcp.scrapeunblocker.com/mcp`) in the `x-scrapeunblocker-key` header. Nothing is installed or run on your machine for the MCP tools. Get a key at [app.scrapeunblocker.com](https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin).

The `/scrape-url` fallback path (used only when the MCP server is unavailable) reads `SCRAPEUNBLOCKER_KEY` from your shell environment instead.

## Where data goes

The MCP tools call the hosted MCP server at `https://mcp.scrapeunblocker.com/mcp`. The `/scrape-url` fallback and the HTTP examples in the skills call the ScrapeUnblocker API at `https://api.scrapeunblocker.com` directly. No other service receives your data. The API keeps a per-key request history (URL and status, no page content) for 7 days so you can see your recent calls in the dashboard.
