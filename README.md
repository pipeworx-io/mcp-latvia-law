# @pipeworx/latvia-law

Latvian national legislation from Likumi.lv, the legal-acts portal of the
official publisher Latvijas Vēstnesis: search laws, Cabinet regulations,
orders and other acts by title, then read an act's record and its current
consolidated text, whole or one article (pants) at a time. Latvian text, with
status, issuer, type, adoption and in-force dates and the Latvijas Vēstnesis
publication reference.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2018+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `lv_law_search(query, include_repealed?, page?)` — find acts by Latvian title
  words (for example `datu apstrādes`). Returns id, title, status (in force,
  not yet in force, or lost force), issuer, type, adoption and in-force dates,
  publication and a link. Acts that have lost force are left out unless
  `include_repealed=true`. 20 per page; `next_page` says whether to continue.
- `lv_law_get(id, article?, max_chars?)` — one act by id (for example `300099`):
  the record fields and the consolidated text. `article` returns one article
  (superscripts as `16_1`); otherwise the text is capped at `max_chars` with a
  chapter list. Cabinet regulations are divided into points, which `article`
  addresses by number.

## Auth

Keyless.

## Data sources

- Latvia, Latvijas Vēstnesis, Likumi.lv — consolidated legal acts, status and
  publication references.

The consolidated text is informative: the official publication prevails, and
each response says so. `data_as_of` is the date of the live read. Read live on
each call; nothing is copied. The portal serves web pages, so a change to its
page layout makes the tools fail with an error that says so, never with a
guessed answer. A search or lookup that finds nothing returns `empty_reason`,
not an empty success. EU law is not in this pack (see the EUR-Lex pack).

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "latvia-law": {
      "url": "https://gateway.pipeworx.io/latvia-law/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/latvia-law/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 2018+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/lv_law_search \
  -H 'Content-Type: application/json' \
  -d '{"query":"Fizisko personu datu apstrādes likums"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/lv_law_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-latvia-law` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/latvia-law/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "latvia-law": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-latvia-law"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Latvia Law data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
