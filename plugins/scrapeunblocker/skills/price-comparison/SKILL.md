---
name: price-comparison
description: "Shopping price comparison with ScrapeUnblocker's live web data - find where a product is sold, for how much, and whether it's in stock across Amazon, Walmart, eBay, Best Buy, Google Shopping and any retailer, then rank the offers into one buy recommendation. Use when the user wants to compare prices, find the cheapest place to buy something, do a price check, or track an item's price. Region-aware. For analyzing a competitor's pricing strategy, use competitive-intel instead."
---

# Price comparison with ScrapeUnblocker

Answer "where is this cheapest, in stock, right now?" by pulling live prices from multiple retailers - including the ones that block scrapers.

Use the plugin's MCP tools (`google_search`, `fetch_parsed`) or the HTTP endpoints / plugins.

## Workflow

1. **Find the listings.** `google_search` the product (`"<product name>" price` or an exact model/ASIN), or use Google Shopping. Take the retailer URLs.
2. **Pull each offer as structured data:**
   - **eBay** - use the `ebay-search` plugin (`POST /marketplace/ebay-search`) for clean listings.
   - **Temu** - `temu-product` / `temu-search` plugins.
   - **Amazon / Walmart / Best Buy / any shop** - `fetch_parsed` (or `getPageSource?parsed_data=true`) returns price, title, availability without custom parsers.
3. **Normalize** - same currency, note shipping and stock status, drop irrelevant variants.
4. **Rank** into a single table: retailer | price | shipping | in stock | link, cheapest in-stock first, with a one-line recommendation.

## Region matters

Price, availability and which retailers apply are country-specific. Route through the buyer's country with `proxy_country` on `getPageSource`/`serpApi`, and say which region the comparison is for.

## Example

```python
from scrapeunblocker import ScrapeUnblockerClient
su = ScrapeUnblockerClient()  # reads SCRAPEUNBLOCKER_KEY

offer = su.get_parsed("https://www.walmart.com/ip/...")   # price, title, availability
# repeat per retailer, then rank the offers yourself
```

## Rules

- **Only quote prices the page actually shows** - never invent a number. Include the link so it's verifiable.
- **In-stock beats cheapest-but-unavailable** - rank on real availability.
- **Timestamp it** - prices move.

Docs: https://docs.scrapeunblocker.com/plugins/overview?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
