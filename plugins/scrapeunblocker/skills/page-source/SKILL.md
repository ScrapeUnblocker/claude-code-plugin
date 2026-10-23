---
name: page-source
description: "Fetch the fully rendered HTML of any web page through ScrapeUnblocker, or the page as clean Markdown for an LLM to read, bypassing anti-bot protection (Cloudflare, DataDome, PerimeterX, Akamai, Shape). Use when a normal request is blocked (403/429, captcha, 'access denied') or when a page needs a real browser to render. Covers the getPageSource endpoint, Markdown output (markdown=true), render waits, and country targeting."
user-invocable: false
---

# Fetching a page with ScrapeUnblocker

Use ScrapeUnblocker's `getPageSource` endpoint to get the fully rendered HTML, or clean Markdown, of a page that would otherwise block you.

With this plugin installed, the quickest route is the MCP tool `fetch_html` (or `fetch_markdown` to read the page). The HTTP and SDK forms below are for code you write into the user's project.

## Basic request

```bash
curl -X POST "https://api.scrapeunblocker.com/getPageSource?url=https%3A%2F%2Fexample.com" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

The `url` must be URL-encoded. The response body is the page HTML.

## With the SDK

```python
from scrapeunblocker import Client

su = Client()  # reads SCRAPEUNBLOCKER_KEY
html = su.get_page_source("https://example.com")
```

```ts
import { ScrapeUnblockerClient } from "scrapeunblocker";

const su = new ScrapeUnblockerClient(); // reads SCRAPEUNBLOCKER_KEY
const html = await su.getPageSource("https://example.com");
```

## Markdown instead of HTML

Add `markdown=true` and the body is the rendered page as clean Markdown (`Content-Type: text/markdown; charset=utf-8`): headings, lists, GFM tables, absolute links, images with alt text and fenced code, without scripts, styles, navigation, footer, buttons, forms or hidden elements.

```bash
curl -X POST "https://api.scrapeunblocker.com/getPageSource?url=https%3A%2F%2Fexample.com&markdown=true" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

- `main_content_only` (default `true`, only with `markdown=true`) keeps the main content and drops navigation, the site header, footer, sidebars and menus. `main_content_only=false` converts the whole page.
- Works with `proxy_country`, the render waits below and browser `steps`. Combined with `parsed_data`, `get_cookies` or `list_elements` the API answers 422, since each sets its own response format.
- Billed like an HTML call. A target's own 404/410 comes back with that status, `X-Origin-Status` and the not-found page as Markdown.
- The MCP tool `fetch_markdown` returns this Markdown (`main_content_only` is its argument too).

## Which format to ask for

- **Markdown** (`markdown=true`): reading, summarising, RAG, or handing a page to an LLM. A fraction of the HTML's tokens.
- **Parsed JSON** (`parsed_data=true`, see the `parsed-data` skill): named fields (price, title, rating) going into a database.
- **HTML** (the default): your own parser, CSS/XPath selectors, or anything that needs the markup.

## Waiting for dynamic content

If the content you need loads via JavaScript after the initial HTML:

- Wait for a CSS selector: `method=css` and `value=%23product-price` (the selector `#product-price`)
- Wait for an XPath match: `method=xPath` and `value=//div[contains(@class,'item')]`
- URL-encode `value` just like `url`: a raw `#` starts the URL fragment, so the API would never receive the selector. The SDKs encode it for you.
- Cap the wait with `method_timeout=15` (seconds), or add a fixed `time_sleep=3`.

## Country targeting

Route through a specific country when a page is geo-restricted or localized:

```bash
curl -X POST "https://api.scrapeunblocker.com/getPageSource?url=<ENCODED_URL>&proxy_country=US" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

## Tips

- Retry on 5xx or empty results with a short backoff; the official SDKs do this automatically.
- A **404 or 410 with an `X-Origin-Status` header** is the target site's own answer: the page does not exist. The body is the site's not-found page, the call is billed, and a retry returns the same result - fix the URL instead of retrying.
- Check the returned HTML actually contains what you expect before parsing it.
- For structured fields (price, title, etc.), prefer `parsed_data=true` (see the `parsed-data` skill) over parsing HTML yourself; to read or summarise a page, prefer `markdown=true`.

Docs: https://docs.scrapeunblocker.com/guides/page-source?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
