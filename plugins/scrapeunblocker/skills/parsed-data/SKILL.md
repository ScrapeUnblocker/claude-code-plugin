---
name: parsed-data
description: "Extract structured JSON from any web page with ScrapeUnblocker instead of parsing HTML by hand. Returns AI-parsed fields (products, articles, listings) using Schema.org, __NEXT_DATA__, or AI-generated rules. Use when you need clean data fields from a page rather than raw markup."
user-invocable: false
---

# AI-parsed structured data with ScrapeUnblocker

Instead of fetching HTML and writing brittle per-site parsers, ask ScrapeUnblocker to return structured JSON directly with `parsed_data=true`.

With this plugin installed, the quickest route is the MCP tool `fetch_parsed`. The HTTP and SDK forms below are for code you write into the user's project.

## Request

```bash
curl -X POST "https://api.scrapeunblocker.com/getPageSource?url=<ENCODED_URL>&parsed_data=true" \
  -H "X-ScrapeUnblocker-Key: YOUR_API_KEY"
```

## With the SDK

```python
from scrapeunblocker import ScrapeUnblockerClient

su = ScrapeUnblockerClient()
product = su.get_parsed("https://www.amazon.com/dp/B08N5WRWNW")
print(product.page_type)  # e.g. "product"
print(product.data)       # the extracted fields
```

```ts
const product = await su.getParsed("https://www.amazon.com/dp/B08N5WRWNW");
console.log(product.pageType, product.data);
```

## Response shape

- `pageType` - what the page was detected as (e.g. `product`, `article`).
- `source` - how it was extracted (Schema.org, `__NEXT_DATA__`, AI rules).
- `data` - the extracted fields.

## When there is nothing to parse

If the page loads but holds no structured data, the API still answers **HTTP 200**: `data.data` is empty, `"data_extracted": false` says nothing was extracted, `detail` explains it, and `html` carries the rendered page. The call is **billed** like a plain `getPageSource`, so use that HTML - do not call again without `parsed_data` and do not retry with it. The `fetch_parsed` MCP tool returns that HTML with a note. A successful parse has no `data_extracted` field.

A URL that does not exist answers **404/410** with an `X-Origin-Status` header and `{"data": {"page_type": "not_found", "data": {}}}` - billed, and a retry returns the same result.

## When a parse looks wrong

Force a fresh set of extraction rules and optionally hint what is missing:

```ts
const fresh = await su.getParsed(url, { refreshRules: true, rulesHint: "price is missing" });
```

## Tips

- Use parsed data for product, listing, and article pages; use raw HTML (see the `page-source` skill) when you need the full markup or a non-standard structure.
- Combine with `proxy_country` for localized pricing/currency.

Docs: https://docs.scrapeunblocker.com/guides/parsed-data?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
