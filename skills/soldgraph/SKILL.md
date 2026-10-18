---
name: soldgraph
description: Look up what items actually sold for (sold prices, comps, resale value) on eBay, Poshmark and Mercari, and live listings on Depop, Whatnot, OfferUp, Facebook Marketplace, Vinted and TCGplayer, through the Soldgraph API. Use when the user asks what something is worth or sold for, wants comps for a resale item or trading card, wants to price a list of items, or is writing code that needs resale price data.
---

# Soldgraph

Soldgraph returns resale marketplace data as JSON: sold listings from eBay (8 countries), Poshmark (US, Canada) and Mercari (US), and active listings from those plus Depop, Whatnot, OfferUp, Facebook Marketplace, Vinted and TCGplayer.

## Before the first call

- The API key must be in the `SOLDGRAPH_KEY` environment variable. Never print it, echo it or put it in code, a prompt or a commit.
- If it is not set, tell the user to create a free key at https://soldgraph.com/signup (100 free requests, no credit card) and set `SOLDGRAPH_KEY`. Do not ask them to paste the key into the chat.
- If Soldgraph MCP tools are available (`ebay_sold`, `mercari_sold`, `account_usage` and so on), use them instead of HTTP. They wait for each search and return compact rows.

## Search over HTTP

Base URL `https://api.soldgraph.com/v1`. Every route is `GET` with `Authorization: Bearer $SOLDGRAPH_KEY`.

```bash
curl -sG https://api.soldgraph.com/v1/ebay/sold \
  -H "Authorization: Bearer $SOLDGRAPH_KEY" \
  --data-urlencode "q=nintendo switch oled" \
  --data-urlencode "count=200"
```

- **200** with `status: "complete"`: the result is ready.
- **202** with `status: "pending"` and `poll_url`: poll `https://api.soldgraph.com` + `poll_url` + `?wait=20` with the same header until `status` is `complete` or `failed`. The server holds each poll up to 20 seconds, so do not sleep between polls. `poll_url` is a path like `/v1/jobs/{id}`. Join it to `https://api.soldgraph.com`, not to `/v1`.

```bash
curl -s "https://api.soldgraph.com/v1/jobs/REQUEST_ID?wait=20" -H "Authorization: Bearer $SOLDGRAPH_KEY"
```

## Routes

| Route | Returns |
|---|---|
| `/ebay/sold`, `/poshmark/sold`, `/mercari/sold` | Sold listings: what items actually sold for |
| `/ebay/listings`, `/poshmark/listings`, `/mercari/listings`, `/depop/listings`, `/whatnot/listings`, `/vinted/listings` | Active listings and asking prices |
| `/offerup/listings`, `/facebook/listings` | Local listings near a `zip` (US) |
| `/offerup/item`, `/facebook/item`, `/vinted/item` | One listing's details by ID or URL |
| `/tcgplayer/search`, `/tcgplayer/sales` | Card products with market price, and a product's recent sales sample |

Common inputs: `q` (keywords, be specific: model, size, grade), `page`, `min_price`, `max_price`, `country`. eBay takes `count` of 40, 60, 120 or 200 rows and `country` of `us`, `uk`, `ca`, `au`, `de`, `fr`, `it`, `es`. Some routes paginate with `cursor`: send `next_page` and `next_cursor` from the last result. Full inputs: https://soldgraph.com/docs/api/filtering.md

## Reading a result

- `result.summary` has `median`, `p25`, `p75`, `low`, `high`, `currency` and `priced_count` for the whole page. Lead with the median and the typical range.
- `result.data` holds the rows. Each has `title`, `displayed_price`, `link` and, for sold routes, `sold_date`.
- On eBay sold, rows with `best_offer_accepted: true` show the asking price, because eBay hides the accepted offer. Use `summary.median_excluding_best_offer` when it differs.

## How to answer

- Give the median and range, then three to five example sales with their `link`, so the user can check them.
- Say that a result is one page of recent listings from the marketplace, not a full sales history.
- Lots and bundles can pull prices up. Leave them out or call them out.
- Each completed search costs 1 request, cache hits included. Failed searches and job polls are free. Before a large run, check usage with `GET /v1/usage` or the `account_usage` tool.
- To price many items, send one batch: `POST /v1/batches`. See https://soldgraph.com/docs/api/batches.md

## Writing code against the API

Read https://soldgraph.com/llms.txt first. It lists every route, field and error code. The OpenAPI spec is at https://soldgraph.com/openapi.json. Keep the key on the server, read it from `SOLDGRAPH_KEY`, and handle the 202 then poll flow.

Errors: https://soldgraph.com/docs/api/errors.md. A `401` means the key is missing or wrong. A `429 quota_exceeded` means the month's requests are used up.
