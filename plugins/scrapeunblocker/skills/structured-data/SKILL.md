---
name: structured-data
description: "Get ready-made structured JSON from specific sites through ScrapeUnblocker's plugins, instead of scraping HTML yourself - Skyscanner flights/hotels/car hire, Google Local (Maps), eBay search, Temu product/search, and oopbuy. Use when the user wants clean data from one of these platforms, or asks to 'get flights', 'search eBay', 'Temu product', 'local businesses near', or 'places on Google Maps'. For any other site, use parsed_data=true (see the parsed-data skill)."
---

# Structured data via ScrapeUnblocker plugins

For a handful of high-value platforms, ScrapeUnblocker ships **plugins**: dedicated endpoints that return clean, ready-made JSON so you never write selectors. One call = one request = the whole result set.

## The plugin endpoints

| Data | Endpoint |
|---|---|
| Flights | `POST /flights/skyscanner-quotes` |
| Hotels | `POST /hotels/skyscanner-quotes` |
| Car hire | `POST /carhire/skyscanner-quotes` |
| Local businesses (Google Maps) | `POST /maps/google-local` |
| eBay search | `POST /marketplace/ebay-search` |
| Temu product | `POST /goods/temu-product` |
| Temu search | `POST /goods/temu-search` |
| oopbuy search | `POST /goods/oopbuy-search` |

All take `x-scrapeunblocker-key` and their own query params. Example:

```bash
curl -X POST "https://api.scrapeunblocker.com/marketplace/ebay-search?query=mechanical+keyboard" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

See each plugin's exact fields at https://docs.scrapeunblocker.com/plugins/overview?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin

## For every other site: parsed_data

If the target isn't one of the plugins above, don't hand-write parsers - ask for AI-parsed JSON with `parsed_data=true` (or the MCP tool `fetch_parsed`):

```bash
curl -X POST "https://api.scrapeunblocker.com/getPageSource?url=<ENCODED_URL>&parsed_data=true" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

```python
from scrapeunblocker import Client
su = Client()  # reads SCRAPEUNBLOCKER_KEY
product = su.get_parsed("https://www.example-shop.com/product/123")
```

## Choosing

1. **Is it a plugin platform** (Skyscanner / Google Local / eBay / Temu / oopbuy)? Use the plugin - it's the cleanest.
2. **Any other product / article / listing page?** Use `parsed_data=true`.
3. **Need raw HTML** (custom parsing, non-standard page)? Use the `page-source` skill.

Docs: https://docs.scrapeunblocker.com/plugins/overview?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
