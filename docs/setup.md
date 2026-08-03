# Setup — per-client install paths

The hosted page at [1social.dev/mcp](https://1social.dev/mcp) is the canonical setup entry point — overview, one-click links, and copyable commands live there. This document mirrors those instructions for reference.

**Server URL** (used by every client): `https://mcp.1social.dev/mcp`

Adding the server only authorizes your assistant to talk to 1Social. Before it can post anything, **connect your social accounts in the browser** at [1social.dev/app/channels](https://1social.dev/app/channels) — each network's consent screen has to be signed for by you, and no tool can do it from a chat.

---

## Claude (web & desktop)

**One-click:** [claude.ai/settings/connectors](https://claude.ai/settings/connectors) → Add custom connector → paste the server URL.

**Manual** (Claude Desktop) — edit `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "1social": {
      "url": "https://mcp.1social.dev/mcp"
    }
  }
}
```

Claude opens a browser window the first time it connects, to complete OAuth.

---

## ChatGPT

Custom MCP connector with OAuth.

1. **Settings → Apps & Connectors → Advanced settings** — turn on Developer mode.
2. **Settings → Connectors → Create**, or **Apps → Create**.
3. Name: `1social`. MCP Server URL: `https://mcp.1social.dev/mcp`. Authentication: OAuth.
4. Confirm, then **Create**.

---

## Cursor

**One-click:** [install in Cursor](https://cursor.com/en/install-mcp?name=1social&config=eyJuYW1lIjoiMXNvY2lhbCIsInR5cGUiOiJodHRwIiwidXJsIjoiaHR0cHM6Ly9tY3AuMXNvY2lhbC5kZXYvbWNwIn0=), or open the [1Social MCP page](https://1social.dev/mcp) and click "Install in Cursor".

**Manual** — `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "1social": {
      "type": "http",
      "url": "https://mcp.1social.dev/mcp"
    }
  }
}
```

---

## VS Code

**One-click:** [install in VS Code](https://insiders.vscode.dev/redirect/mcp/install?name=1social&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.1social.dev%2Fmcp%22%7D), or open the [1Social MCP page](https://1social.dev/mcp) and click "Install in VS Code".

**Manual** — `.vscode/mcp.json`:

```json
{
  "servers": {
    "1social": {
      "type": "http",
      "url": "https://mcp.1social.dev/mcp"
    }
  }
}
```

---

## Claude Code (CLI)

```bash
claude mcp add --transport http 1social https://mcp.1social.dev/mcp
```

Then run `/mcp` inside Claude Code and authorize in the browser.

---

## Gemini CLI

```bash
gemini mcp add --transport http 1social https://mcp.1social.dev/mcp
```

**Manual** — `~/.gemini/settings.json` (uses `httpUrl`, not `url`):

```json
{
  "mcpServers": {
    "1social": {
      "httpUrl": "https://mcp.1social.dev/mcp"
    }
  }
}
```

---

## Codex CLI

```bash
codex mcp add 1social --url https://mcp.1social.dev/mcp
```

**Manual** — `~/.codex/config.toml`:

```toml
[mcp_servers.1social]
url = "https://mcp.1social.dev/mcp"
```

---

## Generic HTTP MCP client

Point any compatible client at `https://mcp.1social.dev/mcp`. The first call returns HTTP 401 with `WWW-Authenticate: Bearer resource_metadata="https://mcp.1social.dev/.well-known/oauth-protected-resource"`, which is enough for standard MCP OAuth discovery (OAuth 2.1 with PKCE and Dynamic Client Registration).

See [oauth.md](oauth.md) for the full flow.

---

## First call, in order

1. `list_accounts` — the channel ids every other tool needs, plus your plan and remaining quota.
2. `preview_post` — free, and the only preview that exists.
3. `publish_post` — with an `idempotencyKey`, so a retried call cannot post twice.
