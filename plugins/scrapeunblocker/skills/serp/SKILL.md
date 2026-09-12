---
name: serp
description: "Get structured Google search results through ScrapeUnblocker's serpApi - organic listings, ads and the AI overview as clean JSON, without scraping Google HTML or hitting CAPTCHAs. Use when the user wants search results, needs URLs to feed into scraping, wants to check rankings, or asks to 'search Google', 'find pages about', or 'look up' something. Supports country targeting and multi-page depth."
---

# Google search results with ScrapeUnblocker

Use ScrapeUnblocker's `serpApi` endpoint to get Google results as structured JSON. Google is a hard target - do **not** scrape `google.com` HTML by hand; this endpoint handles the rendering, CAPTCHAs and parsing for you.

With this plugin installed, the quickest route is the MCP tool `google_search`. The HTTP form below is for code you write into the user's project.

## Basic request

```bash
curl -X POST "https://api.scrapeunblocker.com/serpApi?keyword=web+scraping+api" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

The query goes in `keyword` (URL-encoded). The response is JSON with `organic` (title, url, description, position), plus `topAds` / `bottomAds`, `aiOverview` and result counts.

## Parameters

| Param | What it does |
|---|---|
| `keyword` | The search query (required, URL-encoded) |
| `proxy_country` | Search as if from this country, e.g. `US`, `DE` - changes the exit and the Google locale/domain |
| `pages_to_check` | How many result pages to pull (1-10; default 1) |

## Country matters

Rankings, ads and even which results appear are geo-specific. Pass `proxy_country` whenever the user cares about a market:

```bash
curl -X POST "https://api.scrapeunblocker.com/serpApi?keyword=best+running+shoes&proxy_country=DE" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

## Common patterns

- **Find URLs to scrape:** search first, take the `organic` URLs, then hand them to the `page-source` or `parsed-data` skill.
- **Targeted discovery (dorks):** `keyword=site:linkedin.com/in "Company" (CTO OR "VP Engineering")` surfaces specific people/pages from Google's index without touching login-walled sites.
- **Rank tracking:** read `position` for your domain across `pages_to_check`.

## Tips

- Read `organic[].url` for the real destination; check `title`/`description` for context.
- Empty `organic` with a present `aiOverview` usually means an informational query - refine the keyword.

Docs: https://docs.scrapeunblocker.com/guides/serp-api?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
