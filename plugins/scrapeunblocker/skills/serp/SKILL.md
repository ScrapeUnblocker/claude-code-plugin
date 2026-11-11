---
name: serp
description: "Get structured Google search results through ScrapeUnblocker's serpApi - organic listings with real URLs, ads, the AI overview, People also ask, related searches, the knowledge panel, news, videos, local and shopping results as clean JSON, without scraping Google HTML or hitting CAPTCHAs. Use when the user wants search results, needs URLs to feed into scraping, wants to check rankings, or asks to 'search Google', 'find pages about', or 'look up' something. Supports country targeting and multi-page depth."
---

# Google search results with ScrapeUnblocker

Use ScrapeUnblocker's `serpApi` endpoint to get Google results as structured JSON. Google is a hard target - do **not** scrape `google.com` HTML by hand; this endpoint handles the rendering, CAPTCHAs and parsing for you.

With this plugin installed, the quickest route is the MCP tool `google_search`. The HTTP form below is for code you write into the user's project.

## Basic request

```bash
curl -X POST "https://api.scrapeunblocker.com/serpApi?keyword=web+scraping+api" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

The query goes in `keyword` (URL-encoded). The response is JSON with:

- `organic` - `title`, real `url`, `description` (the snippet), `position`, `displayedUrl` (the breadcrumb), `domain`, `source` (the site's name), `date` when Google shows one, `favicon`, `sitelinks` (`title`, `url`, `description`), `rating` (`value`, `max`, `reviews`), `richSnippet` (the facts line under the snippet) and `thumbnail` - each `[]` or `null` when absent.
- `topAds` / `bottomAds` - `title`, `url`, `description`, `position`, `displayedUrl`, `domain`, `advertiser`, `favicon`, `sitelinks`.
- `aiOverview` - Google's AI summary (`text`, `markdown`, `sources`, `references`), or `null`.
- The first page's other blocks, each `[]` or `null` when absent: `peopleAlsoAsk` (`question`), `relatedSearches` (strings) and `relatedSearchLinks` (`query`, `url`), `knowledgeGraph` (`title`, `subtitle`, `description`, `source`, `attributes`), `topStories` (`title`, `url`, `source`, `date`, `publishedAt`), `videos` (`title`, `url`, `platform`, `channel`, `date`, `duration`), `localResults` (the map pack: `name`, `rating`, `reviews`, `address`, `phone`, `website`, ...), `shoppingResults` (`title`, `price`, `currency`, `merchant`, `rating`, ...) and `spellingCorrection` (`type`, `query`).
- `totalResults`, the counts and `pagesFetched` (pages billed).

## Parameters

| Param | What it does |
|---|---|
| `keyword` | The search query (required, URL-encoded) |
| `proxy_country` | Search as if from this country, e.g. `US`, `DE` - changes the exit and the Google locale/domain |
| `pages_to_check` | How many result pages to pull (1-10; default 1) |
| `full_urls` | `true` waits until Google gives out every result's real URL. Google releases a results page's links a few at a time, the last ones up to about 40 seconds after the search; without it a result whose link has not come yet has the site's home page as its `url` (or a Google redirect link). With it the call takes up to about 40 seconds longer (about 35-50 s in all). Billed the same |

## Country matters

Rankings, ads and even which results appear are geo-specific. Pass `proxy_country` whenever the user cares about a market:

```bash
curl -X POST "https://api.scrapeunblocker.com/serpApi?keyword=best+running+shoes&proxy_country=DE" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

## Common patterns

- **Find URLs to scrape:** search first, take the `organic` URLs, then hand them to the `page-source` or `parsed-data` skill.
- **Targeted discovery (dorks):** `keyword=site:linkedin.com/in "Company" (CTO OR "VP Engineering")` surfaces specific people/pages from Google's index without touching login-walled sites.
- **Rank tracking:** read `position` and `domain` for your site across `pages_to_check`.
- **Content and keyword research:** `peopleAlsoAsk` and `relatedSearches` give the questions and searches around a topic; `knowledgeGraph.attributes` gives an entity's facts.

## Tips

- Read `organic[].url` for the real destination; check `title`/`description` for context. When you need the exact page URL of every result (to cite, open or store them), pass `full_urls=true` (MCP: `full_urls: true` on `google_search`); leave it off for quick answers.
- Empty `organic` with a present `aiOverview` usually means an informational query - refine the keyword.

Docs: https://docs.scrapeunblocker.com/guides/serp-scraping?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
