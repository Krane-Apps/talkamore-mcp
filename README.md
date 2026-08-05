# Talkamore MCP server

A wiki about your life that writes itself — exposed as a remote [MCP](https://modelcontextprotocol.io) server, so the AI you already use can read and write it.

Every day you work things out in ChatGPT, Claude, or Cursor — decisions, plans, things you learned — and lose them when the chat ends. Talkamore is the memory layer that fixes that, built on the pattern from [Karpathy's LLM-wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): instead of machine-written memory fragments, an LLM maintainer files what you say into persistent, cross-linked wiki pages about your people, projects, and themes. Each claim carries a receipt back to the day you said it. You can read your memory like a document, edit it like a document, and the maintainer respects your edits — after you touch a page it only ever appends.

This repo is the public integration reference. The server itself is hosted at `api.talkamore.com` — there is nothing to install or run.

Listed on the official MCP registry as [`com.talkamore/wiki`](https://registry.modelcontextprotocol.io/v0/servers?search=talkamore).

## Tools

| Tool | What it does |
|---|---|
| `save_to_wiki` | File content into the wiki. The maintainer decides which pages it touches and links them. |
| `list_wiki_index` | The wiki's index: every page with its slug and kind (person / project / theme / synthesis / note). |
| `search_wiki` | Hybrid search (keyword-gated, semantically ranked) across all pages. Returns snippets. |
| `read_wiki_page` | One page in full, by slug (for example `people/rohan`, or `index`). |

## Connect

One URL, no token:

```
https://api.talkamore.com/mcp
```

Add it to your client and a browser opens to sign in (or sign up) and approve — OAuth 2.1 with PKCE, handled natively by every client below. Transport is streamable HTTP (stateless JSON-RPC).

### Claude Code

```bash
claude mcp add --transport http talkamore https://api.talkamore.com/mcp
```

Then run `/mcp` and pick **Authenticate**.

### Cursor

Add to `~/.cursor/mcp.json`, then restart Cursor and approve the sign-in prompt:

```json
{
  "mcpServers": {
    "talkamore": {
      "url": "https://api.talkamore.com/mcp"
    }
  }
}
```

### Claude Desktop and claude.ai

Settings → Connectors → Add custom connector, paste the URL, and approve in the browser.

### ChatGPT (Plus, Pro, Business)

ChatGPT supports custom MCP connectors through Developer mode, with full read and write tools:

1. On the web app: Settings → **Security and login** → turn on **Developer mode**
2. Settings → **Plugins** → **+** → create a connector with `https://api.talkamore.com/mcp` (auth: OAuth) — ChatGPT runs the browser sign-in flow (client ID metadata documents; no registration step)
3. In a chat, pick the connector under the composer's **Developer mode** tool — the wiki tools appear in your conversations

On the free tier, use the capsule import at [talkamore.com/wiki](https://talkamore.com/wiki) instead: ask ChatGPT what it knows about you, paste the answer, and the maintainer files it into pages.

### Manual setup (token URL)

For clients that cannot open a sign-in browser, a personal token URL still works: sign in at [talkamore.com/wiki](https://talkamore.com/wiki), open **Connect AIs** → **Manual setup**, and use `https://api.talkamore.com/mcp/YOUR_TOKEN`. The token is the whole auth — treat that URL as a secret.

## Use it

End of a session:

> save this to my talkamore wiki

Start of the next one:

> check my talkamore wiki about the launch plan

The point is the loop: what you work out in one chat is waiting in the next, in any client, and it compounds instead of resetting.

## Security

- OAuth: S256 PKCE only, single-use 60s authorization codes, refresh rotation — reuse of a rotated token revokes the whole grant
- Disconnect any client from talkamore.com/wiki; its next call is refused
- Tokens and codes are stored as SHA-256 digests only
- All wiki content is encrypted at rest with per-user keys (envelope encryption)
- Every credential is scoped to one user's wiki; there is no cross-user surface

## Links

- Product: [talkamore.com](https://talkamore.com)
- The wiki surface: [talkamore.com/wiki](https://talkamore.com/wiki)
- Registry entry: [`com.talkamore/wiki`](https://registry.modelcontextprotocol.io/v0/servers?search=talkamore)
- The idea this implements: [karpathy/llm-wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)

Questions or problems: team@talkamore.com
