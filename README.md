# ScrapeUnblocker plugin for Claude Code and Cursor

Official [Claude Code](https://claude.com/claude-code) and [Cursor](https://cursor.com) plugin for [ScrapeUnblocker](https://scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin) - fetch any web page from behind anti-bot protection (Cloudflare, DataDome, PerimeterX, Akamai, Shape) without writing a scraper.

## Install

In Claude Code:

```
/plugin marketplace add ScrapeUnblocker/claude-code-plugin
/plugin install scrapeunblocker@scrapeunblocker
```

You will be asked for your ScrapeUnblocker API key when the plugin is enabled. It is stored in your OS keychain, not in a settings file. Get a key at [app.scrapeunblocker.com](https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin).

## Install in Cursor

The same plugin ships a Cursor manifest (`.cursor-plugin/`). Its MCP server is the hosted
one (`https://mcp.scrapeunblocker.com/mcp/oauth`): you sign in with your ScrapeUnblocker
account once, and there is no key to paste.

Until it is listed in the Cursor Marketplace, install it locally:

```bash
git clone https://github.com/ScrapeUnblocker/claude-code-plugin
mkdir -p ~/.cursor/plugins/local
cp -r claude-code-plugin/plugins/scrapeunblocker ~/.cursor/plugins/local/scrapeunblocker
```

Then run **Developer: Reload Window**, open **Customize**, find the `scrapeunblocker` MCP
server and click **Connect** to sign in. The skills load with it; the `/scrape-url`
command is Claude Code only.

## What you get

**MCP tools** - available to Claude automatically:

| Tool | What it does |
|------|--------------|
| `fetch_html` | Fully rendered HTML of any page, anti-bot bypassed |
| `fetch_parsed` | AI-parsed structured JSON (products, articles, listings) instead of raw markup |
| `google_search` | Organic Google results as structured data |
| `fetch_markdown` | A page as clean Markdown, usually 2-25% the size of the HTML - the cheap way to read a page |
| `search_and_read` | A Google search plus its top 1-5 result pages as Markdown, in one call |
| `list_elements` | A page's links, inputs and buttons with ready-to-use selectors |
| `browse_page` | Click, type, select and scroll on a page in a real browser, then return the HTML (not read-only: it acts on the website) |
| `list_plugins` / `run_plugin` | 100+ ready-made site scrapers (Amazon, Zillow, Google Maps, YouTube, App Store...) that return clean JSON |
| `amazon_search`, `amazon_product`, `amazon_reviews`, `walmart_search`, `ebay_search`, `google_shopping`, `google_local`, `maps_place_details`, `google_images`, `youtube_search`, `youtube_transcript`, `zillow_search`, `indeed_jobs`, `trustpilot_reviews`, `booking_hotels` | The most-used site plugins as tools of their own |

**Command:**

```
/scrapeunblocker:scrape-url https://example.com
/scrapeunblocker:scrape-url https://example.com parsed
/scrapeunblocker:scrape-url https://example.com markdown
/scrapeunblocker:scrape-url https://example.com country=US
```

**Skills** - instructions the agent loads when a task matches, for using ScrapeUnblocker well and for writing scraping code into your project:

| Skill | What it teaches the agent |
|-------|---------------------------|
| `best-practices` | Endpoints, parameters, retries, choosing the right call |
| `page-source` | Rendered HTML or Markdown (`markdown=true`), render waits, country targeting |
| `parsed-data` | AI-parsed structured JSON and how to fix a bad parse |
| `serp` | Google results as JSON: organic with real URLs, ads, AI overview, People also ask, local, shopping |
| `structured-data` | Ready-made site plugins (Skyscanner, eBay, Google Local) instead of hand-written parsers |
| `mcp` | Setting up the ScrapeUnblocker MCP server (local and hosted) in any MCP client |
| `competitive-intel` | Competitor pricing, features, reviews and hiring signals from their own sites |
| `price-comparison` | Live prices and stock across retailers, ranked into one recommendation |

## Agent Skills for any agent

The skills are plain [Agent Skills](https://agentskills.io) (`SKILL.md` folders), so they also work without the plugin - in Codex, Gemini CLI, VS Code Copilot, Windsurf, Cursor or Claude Code. Install all eight with the [`skills`](https://github.com/vercel-labs/skills) CLI:

```bash
npx skills add ScrapeUnblocker/claude-code-plugin
```

Or pick one:

```bash
npx skills add ScrapeUnblocker/claude-code-plugin --skill page-source
```

You can also copy a folder from [`plugins/scrapeunblocker/skills/`](plugins/scrapeunblocker/skills) into your agent's skills folder (`.claude/skills/`, `.agents/skills/`, `.cursor/skills/`...).

Without the plugin, the skills call the HTTP API with your key: put it in the `SCRAPEUNBLOCKER_KEY` environment variable (the SDKs read it from there). Get a key at [app.scrapeunblocker.com](https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin). For tool calls instead of HTTP, add the [MCP server](https://www.npmjs.com/package/scrapeunblocker-mcp) too.

## Why

Anything a normal `fetch` or `curl` returns as `403`, a captcha, or an empty JS shell comes back as the real page. Requests go through a real browser and a proxy pool, so Claude can read pages it otherwise cannot: pricing pages, marketplaces, listings, SERPs.

## Links

- Docs: [docs.scrapeunblocker.com](https://docs.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin)
- MCP server (standalone, for other MCP clients): [`scrapeunblocker-mcp`](https://www.npmjs.com/package/scrapeunblocker-mcp)
- SDKs: [Python](https://pypi.org/project/scrapeunblocker/), [Node.js](https://www.npmjs.com/package/scrapeunblocker), [Ruby](https://rubygems.org/gems/scrapeunblocker), [PHP](https://packagist.org/packages/scrapeunblocker/client)

## License

MIT
