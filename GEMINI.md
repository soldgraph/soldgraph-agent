# Soldgraph

The `soldgraph` MCP server returns resale marketplace data. Use it when the user asks what something is worth or sold for, wants comps for a resale item or trading card, or wants to price a list of items.

- `*_sold` tools (`ebay_sold`, `poshmark_sold`, `mercari_sold`) return what items actually sold for. `*_listings` tools return current asking prices.
- Lead with `result.summary`: the median and the typical range (`p25` to `p75`). Then give three to five example sales with their `link`, so the user can check them.
- On eBay sold, rows with `best_offer_accepted: true` show the asking price, because eBay hides the accepted offer. Use `summary.median_excluding_best_offer` when it differs.
- A result is one page of recent listings, not a full sales history. Lots and bundles can pull prices up.
- Each completed search costs 1 request. Check `account_usage` before a large run. If a search is still collecting, call `get_result` with its `request_id`. That is free.
- If a tool says the key is missing or invalid, tell the user to get a free key at https://soldgraph.com/signup and run `gemini extensions config soldgraph`.

To write code against the HTTP API, read https://soldgraph.com/llms.txt first.
