---
name: best-practices
description: "Build production-ready ScrapeUnblocker integrations. Reference for developers using coding assistants (Claude Code, Cursor, etc.) to fetch any public URL in a real browser and extract its content. Covers the getPageSource endpoint (raw HTML / Markdown / AI-parsed JSON), serpApi (Google results), getImage, country targeting, render waits, and retries."
user-invocable: false
---

# ScrapeUnblocker API

ScrapeUnblocker renders web pages in a real browser and returns the HTML, clean Markdown, AI-parsed structured JSON, Google search results, or images. One API key, a handful of endpoints. Get a key at https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin and read the docs at https://docs.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin.

## Prefer the MCP tools when the MCP server is connected

If the ScrapeUnblocker MCP server is connected (the Claude Code and Cursor plugins bundle it; for other clients see the `mcp` skill), the fastest path is usually a tool call rather than a hand-written HTTP request:

| Tool | Returns |
|------|---------|
| `fetch_html` | Fully rendered HTML of a page |
| `fetch_parsed` | AI-parsed structured JSON |
| `google_search` | Organic Google results |
| `fetch_markdown` | A page as clean Markdown |
| `search_and_read` | A Google search plus its top result pages as Markdown |
| `capture_xhr` | The JSON a page's own API calls (XHR/fetch) received, optionally after browser `steps` |

Use the HTTP details below when writing code for the user's own project, or when the MCP server is not available.

## Choosing the right endpoint

| Use case | Endpoint | Notes |
|----------|----------|-------|
| Fetch a page's fully rendered HTML | `POST /getPageSource` | Runs a real browser, returns the rendered page |
| A page for an LLM to read (summaries, RAG, agents) | `POST /getPageSource?markdown=true` | Clean Markdown, main content only by default |
| Structured data (product, article, listing) without writing parsers | `POST /getPageSource?parsed_data=true` | Returns AI-parsed JSON (Schema.org / `__NEXT_DATA__` / AI rules) |
| Data a page loads from its own JSON API (which refuses direct requests) | `POST /getPageSource?capture_xhr=/api/...` | The page's XHR/fetch responses as JSON; add `steps` to trigger the call |
| Google search results | `POST /serpApi` | Structured organic results |
| Download an image behind protection | `POST /getImage` | Returns raw bytes |

Always pick the most specific endpoint: use `parsed_data=true` before writing your own HTML parsing, `markdown=true` when a model reads the page, and `serpApi` before scraping Google HTML by hand.

## Authentication

Every request is authenticated with your API key in a header:

```bash
curl -X POST "https://api.scrapeunblocker.com/getPageSource?url=https%3A%2F%2Fexample.com" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

The URL must be URL-encoded and passed as the `url` query parameter.

## Useful parameters (getPageSource)

| Parameter | Purpose |
|-----------|---------|
| `parsed_data=true` | Return AI-parsed JSON instead of raw HTML |
| `markdown=true` | Return the page as clean Markdown (`text/markdown`) instead of HTML |
| `main_content_only=false` | With `markdown=true`: convert the whole page, not only the main content (default `true`) |
| `capture_xhr=/api/search` | Return the page's own XHR/fetch responses whose URL contains the fragment (JSON: `html`, `xhr`, `xhr_truncated`), up to 20 |
| `proxy_country=US` | Route through a specific country (ISO alpha-2) |
| `method=css` + `value=%23price` | Wait for a CSS selector (`#price`) before capturing |
| `method=xPath` + `value=//div[@id='price']` | Wait for an XPath match (`className` and `tagName` also work) |
| `method_timeout=15` | Cap (seconds) for the render-wait method |
| `time_sleep=3` | Extra seconds to wait after load |

## Best practices

- **Encode the target URL.** Pass it URL-encoded in the `url` query parameter, not raw. Encode `value` too: a raw `#` in a selector starts the URL fragment and never reaches the API.
- **Prefer parsed data.** For product/article/listing pages, `parsed_data=true` returns clean JSON and saves you brittle per-site parsers.
- **Pick the format for the consumer.** Markdown (`markdown=true`) for text an LLM reads: summaries, RAG, agents. Parsed JSON for fields that go into a database. HTML for your own parser or selectors. `markdown` cannot be combined with `parsed_data`, `get_cookies` or `list_elements` (HTTP 422).
- **Target the right country.** If a page is geo-restricted or localized, set `proxy_country` to match.
- **Use render waits for JS-heavy pages.** If content loads late, wait on a CSS selector (`method=css`) or an XPath (`method=xPath`) instead of a fixed sleep.
- **Retry transient failures.** Some pages occasionally need a second attempt; retry with backoff on 5xx or empty results. The official SDKs retry automatically.
- **Do not retry a missing page.** A 404 or 410 carrying `X-Origin-Status` is the target site's own answer (the page does not exist), billed like any fetched page. Retrying returns the same result; drop or fix the URL.
- **Validate the response.** Occasionally a returned page can itself be an error or interstitial page; check for expected content before trusting it.

## Official SDKs and MCP

Rather than call the HTTP API directly, use an official client:

- Python: `pip install scrapeunblocker`
- Node.js: `npm install scrapeunblocker`
- Ruby: `gem install scrapeunblocker`
- PHP: `composer require scrapeunblocker/client`
- MCP server (Claude and other assistants): `npx -y scrapeunblocker-mcp`

Each reads the API key from the `SCRAPEUNBLOCKER_KEY` environment variable.
