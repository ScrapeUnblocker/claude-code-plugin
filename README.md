# ScrapeUnblocker plugin for Claude Code

Official [Claude Code](https://claude.com/claude-code) plugin marketplace for [ScrapeUnblocker](https://scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin) - fetch any web page from behind anti-bot protection (Cloudflare, DataDome, PerimeterX, Akamai, Shape) without writing a scraper.

## Install

In Claude Code:

```
/plugin marketplace add ScrapeUnblocker/claude-code-plugin
/plugin install scrapeunblocker@scrapeunblocker
```

You will be asked for your ScrapeUnblocker API key when the plugin is enabled. It is stored in your OS keychain, not in a settings file. Get a key at [app.scrapeunblocker.com](https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin).

## What you get

**MCP tools** - available to Claude automatically:

| Tool | What it does |
|------|--------------|
| `fetch_html` | Fully rendered HTML of any page, anti-bot bypassed |
| `fetch_parsed` | AI-parsed structured JSON (products, articles, listings) instead of raw markup |
| `google_search` | Organic Google results as structured data |

**Command:**

```
/scrapeunblocker:scrape-url https://example.com
/scrapeunblocker:scrape-url https://example.com parsed
/scrapeunblocker:scrape-url https://example.com country=US
```

**Skills** - reference material Claude pulls in when it is writing scraping code for your project:

- `best-practices` - endpoints, parameters, retries, choosing the right call
- `page-source` - fetching rendered HTML, render waits, country targeting
- `parsed-data` - structured extraction and how to fix a bad parse

## Why

Anything a normal `fetch` or `curl` returns as `403`, a captcha, or an empty JS shell comes back as the real page. Requests go through a real browser and a proxy pool, so Claude can read pages it otherwise cannot: pricing pages, marketplaces, listings, SERPs.

## Links

- Docs: [developers.scrapeunblocker.com](https://developers.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin)
- MCP server (standalone, for other MCP clients): [`scrapeunblocker-mcp`](https://www.npmjs.com/package/scrapeunblocker-mcp)
- SDKs: [Python](https://pypi.org/project/scrapeunblocker/), [Node.js](https://www.npmjs.com/package/scrapeunblocker), [Ruby](https://rubygems.org/gems/scrapeunblocker), [PHP](https://packagist.org/packages/scrapeunblocker/client)

## License

MIT
