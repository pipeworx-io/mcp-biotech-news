# Biotech News

Aggregated biotech & pharmaceutical industry news. One call fans out to the major trade publications' RSS feeds, merges, dedupes, and returns headlines newest-first — FDA approvals, clinical readouts, M&A, licensing, funding, layoffs.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

| Tool | What it does |
|------|--------------|
| `biotech_news` | Latest biotech/pharma headlines across all sources, filterable by keyword, trailing day window, or a single source. |
| `biotech_news_sources` | List the aggregated publications (ids usable as `source`). |

## Auth

None. All sources are public RSS feeds.

## Data sources

- [Fierce Biotech](https://www.fiercebiotech.com/rss/xml)
- [Endpoints News](https://endpoints.news/feed/)
- [BioPharma Dive](https://www.biopharmadive.com/feeds/news/)
- [STAT Biotech](https://www.statnews.com/category/biotech/feed/)
- [Fierce Pharma](https://www.fiercepharma.com/rss/xml)
- [Genetic Engineering News](https://www.genengnews.com/feed/)

Individual sources occasionally block datacenter egress; the pack falls back to a feed proxy per feed and reports `sources_failed` rather than failing the whole call.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "biotech-news": {
      "url": "https://gateway.pipeworx.io/biotech-news/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/biotech-news/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1679+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/biotech_news \
  -H 'Content-Type: application/json' \
  -d '{"days":7,"limit":10}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/biotech_news`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "biotech-news": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-biotech-news"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-biotech-news
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Biotech News data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
