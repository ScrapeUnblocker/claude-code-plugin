---
name: competitive-intel
description: "Real-time competitive and market intelligence using ScrapeUnblocker's live web data - pull a competitor's pricing, features, reviews, hiring signals and positioning straight from their site and public sources, even when those pages sit behind anti-bot protection. Use when the user wants to analyze competitors, compare products, monitor pricing, build a battlecard, research a market landscape, or track what rivals are doing. For a single price check across retailers, use the price-comparison skill instead."
---

# Competitive intelligence with ScrapeUnblocker

Turn public web pages into a competitor picture. ScrapeUnblocker handles the hard part - reaching pages that block scrapers (Cloudflare, DataDome, PerimeterX, Akamai) - so you can focus on what to collect and how to read it.

Use the plugin's MCP tools (`google_search`, `fetch_html`, `fetch_parsed`) or the HTTP endpoints.

## Workflow

1. **Find the pages.** `google_search` the competitor for the surfaces you need: `"<Competitor> pricing"`, `site:<competitor>.com`, `"<Competitor>" reviews`, `"<Competitor>" careers`.
2. **Pull each page as clean data.** For pricing/feature pages, `fetch_parsed` (or `getPageSource?parsed_data=true`) returns structured JSON; for freeform pages use `fetch_html`.
3. **Extract the signals:**
   - **Pricing & packaging** - tiers, prices, what's gated behind which plan.
   - **Features** - what they ship, what they're missing vs the user's product.
   - **Reviews & sentiment** - G2/Trustpilot/Reddit; recurring praise and complaints.
   - **Hiring signals** - open roles reveal roadmap and scale (e.g. lots of data-eng roles = data push).
   - **Positioning** - homepage headline, who they say it's for.
4. **Synthesize** into a battlecard: where we win, where they win, and the exact lines to counter their pitch.

## Example

```python
from scrapeunblocker import Client
su = Client()  # reads SCRAPEUNBLOCKER_KEY

pricing = su.get_parsed("https://competitor.com/pricing")   # structured tiers + prices
about   = su.get_page_source("https://competitor.com/")      # positioning / headline
```

## Rules for honest intel

- **Only report what the pages actually say.** Never invent a competitor price, feature or headcount - if a field isn't on the page, mark it unknown and note where to confirm.
- **Date every snapshot.** Pricing and positioning change; say when it was pulled.
- **Public data only** - marketing, pricing, docs, reviews, job posts. Do not attempt login-walled content.

Docs: https://docs.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
