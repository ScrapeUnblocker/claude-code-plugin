# ScrapeUnblocker plugin

Scrape any web page from behind anti-bot protection (Cloudflare, DataDome, PerimeterX, Akamai, Shape) directly from Claude Code.

Install from the marketplace in this repository:

```
/plugin marketplace add ScrapeUnblocker/claude-code-plugin
/plugin install scrapeunblocker@scrapeunblocker
```

## Components

| Type | Name | Purpose |
|------|------|---------|
| MCP server | `scrapeunblocker` | `fetch_html`, `fetch_parsed`, `google_search` |
| Command | `/scrapeunblocker:scrape-url` | One-shot scrape of a URL, HTML or parsed JSON |
| Skill | `best-practices` | API reference for writing integrations |
| Skill | `page-source` | Rendered HTML, render waits, country targeting |
| Skill | `parsed-data` | Structured extraction, fixing a bad parse |

## Configuration

The plugin asks for your API key at enable time (`api_key`, stored as a sensitive value in the OS keychain) and passes it to the MCP server as `SCRAPEUNBLOCKER_KEY`. Get a key at [app.scrapeunblocker.com](https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin).

The `/scrape-url` fallback path (used only when the MCP server is unavailable) reads `SCRAPEUNBLOCKER_KEY` from your shell environment instead.
