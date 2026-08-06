---
name: scrape-url
allowed-tools: Bash(curl:*), Bash(python3:*), Bash(awk:*), Bash(grep:*), Bash(cut:*), Bash(head:*), Bash(echo:*)
argument-hint: "[url] | [url] parsed | [url] country=US"
description: "Scrape any web page through the ScrapeUnblocker anti-bot API and return its HTML or AI-parsed JSON. Use when a page is blocked (403/429, captcha) or needs a real browser to render."
---

# Scrape a URL with ScrapeUnblocker

Fetch the target page through ScrapeUnblocker, bypassing anti-bot protection (Cloudflare, DataDome, PerimeterX, Akamai, Shape).

Target: $ARGUMENTS

## Steps

1. Parse `$ARGUMENTS`: the **first token is the URL**. Only the tokens **after** the URL are treated as flags - if one of them is `parsed`, return AI-parsed JSON instead of HTML; if one is `country=XX`, route through that country. (Flags are never matched against the URL itself, so a URL containing `parsed` or `country=` does not change the request.)

2. **Preferred path - use this plugin's MCP tools.** They already carry the API key you entered when enabling the plugin, so nothing else is needed:
   - no `parsed` flag -> `fetch_html` with the URL (plus the country if given)
   - `parsed` flag -> `fetch_parsed` with the URL (plus the country if given)

3. **Fallback - direct HTTP.** Only if the MCP server is unavailable, and only when `SCRAPEUNBLOCKER_KEY` is set in the environment, run:

   ```bash
   URL_RAW=$(echo "$ARGUMENTS" | awk '{print $1}')
   ENC=$(python3 -c "import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=''))" "$URL_RAW")
   FLAGS=$(echo "$ARGUMENTS" | cut -s -d' ' -f2-)
   EXTRA=""
   echo "$FLAGS" | grep -qw parsed && EXTRA="$EXTRA&parsed_data=true"
   CC=$(echo "$FLAGS" | grep -oE 'country=[A-Za-z]{2}' | head -1 | cut -d= -f2)
   [ -n "$CC" ] && EXTRA="$EXTRA&proxy_country=$CC"
   curl -s -X POST "https://api.scrapeunblocker.com/getPageSource?url=$ENC$EXTRA" \
     -H "X-ScrapeUnblocker-Key: ${SCRAPEUNBLOCKER_KEY:?set SCRAPEUNBLOCKER_KEY}" | head -c 20000
   ```

   If neither the MCP server nor `SCRAPEUNBLOCKER_KEY` is available, tell the user to run `/plugin` and set the ScrapeUnblocker API key, or get one at https://app.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin.

4. Summarize the result for the user. If they asked for specific fields, extract them; otherwise describe the page.

## Notes

- For structured fields (price, title, etc.), pass `parsed` to get clean JSON instead of HTML.
- If the result looks like a block/captcha page, retry once or add `country=US` (or the relevant country).
- Docs: https://developers.scrapeunblocker.com?utm_source=claude-code&utm_medium=integration&utm_campaign=claude-code-plugin
