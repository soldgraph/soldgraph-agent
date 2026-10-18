# Soldgraph for AI agents

Let your AI agent check what things actually sold for. Soldgraph returns sold listings from **eBay** (8 countries), **Poshmark** and **Mercari**, plus live listings from **Depop**, **Whatnot**, **OfferUp**, **Facebook Marketplace**, **Vinted** and **TCGplayer**. Every result comes with the median price worked out and a link to each listing.

This repo packages Soldgraph for Claude Code, Cursor, Gemini CLI and any agent that reads skills. Each package bundles:

- the **hosted Soldgraph MCP server** (`https://api.soldgraph.com/mcp`), with 19 read-only tools
- the **`soldgraph` skill**, which teaches the agent when to use Soldgraph and how to read a result

You need a Soldgraph API key. [Get one free](https://soldgraph.com/signup?utm_source=github&utm_campaign=soldgraph-agent): 100 free requests, no credit card required.

## Install

### Claude Code

```text
/plugin marketplace add soldgraph/soldgraph-agent
/plugin install soldgraph@soldgraph
```

Then set your API key when Claude Code asks, or run `/plugin configure soldgraph@soldgraph`. You can also connect only the MCP server with one command. See [soldgraph.com/claude-code](https://soldgraph.com/claude-code?utm_source=github&utm_campaign=soldgraph-agent).

### Gemini CLI

```bash
gemini extensions install https://github.com/soldgraph/soldgraph-agent
```

Gemini CLI asks for your API key during install. Change it later with `gemini extensions config soldgraph`.

### Cursor

Copy this repo into `~/.cursor/plugins/local/soldgraph`, then run **Developer: Reload Window**. Set your API key as `SOLDGRAPH_KEY` in the plugin's settings. You can also add only the MCP server to `~/.cursor/mcp.json`. See [soldgraph.com/cursor](https://soldgraph.com/cursor?utm_source=github&utm_campaign=soldgraph-agent).

### Any agent that reads skills

Codex, OpenClaw, Hermes, Cursor and others:

```bash
npx skills add soldgraph/soldgraph-agent
```

Set `SOLDGRAPH_KEY` in your environment. The skill calls the HTTP API with `curl` when no MCP tools are connected.

### Claude Desktop, VS Code and other MCP clients

Point the client at `https://api.soldgraph.com/mcp` and send the header `Authorization: Bearer <your key>`. See the [MCP guide](https://soldgraph.com/docs/guides/mcp?utm_source=github&utm_campaign=soldgraph-agent) for each client's config.

## Try asking

- "What has a Nintendo Switch OLED sold for on eBay this month? Link three examples."
- "Price every item in inventory.csv from eBay sold listings and add a median column."
- "What do Lululemon Align leggings sell for on Poshmark vs Mercari?"
- "What's Umbreon VMAX alt art worth, and what did it last sell for?"
- "Find road bikes under $400 near 94103 on OfferUp."

## Tools

| Tool | Returns |
|---|---|
| `ebay_sold`, `poshmark_sold`, `mercari_sold` | Sold listings: what items actually sold for |
| `ebay_listings`, `poshmark_listings`, `mercari_listings`, `depop_listings`, `whatnot_listings`, `vinted_listings` | Active listings and asking prices |
| `offerup_listings`, `facebook_listings` | Local listings near a ZIP code |
| `offerup_item`, `facebook_item`, `vinted_item` | One listing's details |
| `tcgplayer_search`, `tcgplayer_sales` | Card products with market price, and recent sales |
| `account_usage`, `api_status`, `get_result` | Your remaining requests, live marketplace health, and finishing a slow search. Free |

Every tool is read-only. Soldgraph never asks for a marketplace login.

## Cost

Each completed search costs 1 request, the same as the [API](https://soldgraph.com/docs?utm_source=github&utm_campaign=soldgraph-agent). Failed searches and the last three tools are free. Plans start at $9 a month for 2,000 requests.

## Keep your key safe

The key lives in your client's config, keychain or environment. Don't paste it into a chat or commit it. If it leaks, revoke it in your [Soldgraph dashboard](https://soldgraph.com/app/keys?utm_source=github&utm_campaign=soldgraph-agent).

## Links

- [Docs](https://soldgraph.com/docs?utm_source=github&utm_campaign=soldgraph-agent) · [llms.txt](https://soldgraph.com/llms.txt) · [OpenAPI](https://soldgraph.com/openapi.json) · [Status](https://soldgraph.com/status?utm_source=github&utm_campaign=soldgraph-agent)
- Code examples: [soldgraph/soldgraph-examples](https://github.com/soldgraph/soldgraph-examples)
- Support: hello@soldgraph.com

Soldgraph is independent and not affiliated with eBay, Poshmark, Mercari or any other marketplace.

MIT License.
