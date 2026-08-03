# 1Social MCP

**Model Context Protocol server for publishing to social networks — secured by OAuth.**

1Social MCP lets an AI assistant post for you. Connect Claude, ChatGPT, Cursor, VS Code, or a CLI client to 1Social, then publish, schedule, check and fix posts across Instagram, TikTok, X, LinkedIn, Bluesky, Threads and Facebook in natural language.

- **Server URL:** `https://mcp.1social.dev/mcp`
- **Transport:** Streamable HTTP
- **Auth:** OAuth 2.1 with PKCE and Dynamic Client Registration
- **Landing & setup:** [1social.dev/mcp](https://1social.dev/mcp)

## Install

1Social MCP is a **remote, OAuth-secured** server. There is no package to install and no API key to paste. Add this to your client's MCP config:

```json
{
  "mcpServers": {
    "1social": {
      "url": "https://mcp.1social.dev/mcp"
    }
  }
}
```

**One-click install:** [Cursor](https://cursor.com/en/install-mcp?name=1social&config=eyJuYW1lIjoiMXNvY2lhbCIsInR5cGUiOiJodHRwIiwidXJsIjoiaHR0cHM6Ly9tY3AuMXNvY2lhbC5kZXYvbWNwIn0=) · [VS Code](https://insiders.vscode.dev/redirect/mcp/install?name=1social&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.1social.dev%2Fmcp%22%7D) · [Claude connectors](https://claude.ai/settings/connectors) · [ChatGPT connectors](https://chatgpt.com/settings/connectors)

CLI clients (Claude Code, Gemini CLI, Codex CLI) and manual snippets: see [Supported clients](#supported-clients).

## Quick start

1. Create a 1Social account at [1social.dev](https://1social.dev).
2. **Connect your social accounts in the browser.** This step needs a browser and cannot be done from a chat — each network's OAuth consent screen has to be signed for by you.
3. Add the server to your client with the one-click link, the CLI command, or the JSON/TOML snippet above.
4. Authorize with OAuth when your client opens the consent screen.
5. Ask your assistant to post.

## What you can do from chat

- **📤 Publish and schedule** — one message goes to every network you pick, now or up to 30 days ahead.
- **🔍 Check before it goes** — per-network character counts, whether an image is required, whether a multi-part post will thread natively or be joined, and what it costs against your quota.
- **🧾 Read the receipt** — what reached each network, what failed and why, and what genuinely could not be determined.
- **♻️ Fix what broke** — retry one network without touching the ones that already went out.
- **🖼 Attach media** — post an image or video from the chat. Instagram and TikTok refuse posts without one.
- **✏️ Edit, cancel, delete** — change a scheduled post, unschedule it, or delete it from the networks that allow deletion.

## Tool surface

Ten tools, deliberately few. Publish-now and schedule are the same tool (`scheduledAt: null` means now), and reading one post is `list_posts` with a `postId` rather than a second tool — a smaller set is one a model picks from accurately.

| Tool | What it does |
|---|---|
| `list_accounts` | Connected accounts, what each network accepts, current plan, remaining quota. Call it first — channel ids come from here. |
| `preview_post` | Free dry run: per-network limits, media requirements, threading behaviour, quota cost. Nothing is published. |
| `publish_post` | Publishes now, or schedules up to 30 days ahead. Returns a per-network receipt. Takes an `idempotencyKey`. |
| `list_posts` | Posts with their delivery outcome per network — delivered, failed with a reason, cancelled, or `unknown` — plus stored engagement metrics. |
| `update_post` | Edits a post that has not been sent yet. The networks have no edit endpoint, so it withdraws and replaces. |
| `cancel_post` | Unschedules a post and releases the quota it reserved. |
| `delete_post` | Deletes from 1Social and attempts removal from each network. See [What deletion can and cannot do](#what-deletion-can-and-cannot-do). |
| `retry_delivery` | Retries one failed network, leaving the successful ones alone. Refused for permanent failures. |
| `upload_media` | Pre-signed upload target, for clients that cannot attach a file to a tool call. |
| `confirm_delivery` | Records **your** answer about a delivery whose outcome was unknown: is the post live on that network or not? |

The live set is what the server says it is — run `tools/list` against `https://mcp.1social.dev/mcp` for the exact, current shapes.

## It tells you when it doesn't know

A post to four networks can land on three. Every tool result is per network: the failure is named in words you can act on, and a delivery whose outcome genuinely could not be determined comes back as `unknown` rather than as success.

`unknown` is not a dead end. You look at the network, tell the assistant what you see, and `confirm_delivery` settles it — confirming it is live commits the quota, confirming it is not marks the delivery failed so `retry_delivery` can pick it up. The assistant is not allowed to guess this on your behalf.

## What deletion can and cannot do

`delete_post` always removes the post from 1Social. Removal *from the network* depends on the network:

| Network | Remote delete |
|---|---|
| X, LinkedIn, Bluesky, Facebook | ✅ works |
| Instagram, TikTok, Threads | ❌ no working delete API — the post **stays live** and only you can take it down |

On a multi-part thread only the first post is removed; the rest stay live as orphans. The tool reports what actually came down, per network, and the assistant is instructed to tell you that rather than confirm a clean delete.

## Plans & access

Publishing costs us money per delivery, so the quota is metered in **deliveries, not posts** — one post to four networks is four deliveries. A delivery to X costs more units than the others because X charges per post ([pricing](https://1social.dev/pricing) has the current numbers).

| | No subscription | Solo | Pro |
|---|---|---|---|
| **Price** | $0 | $19/mo | $39/mo |
| **Monthly deliveries** | 30 units | 600 units | 1,600 units |
| All seven networks | ✅ | ✅ | ✅ |
| Delivery receipts & retries | ✅ | ✅ | ✅ |

A new account has a small free monthly allowance — enough to publish, watch the receipt come back, retry something, and schedule a post — so the tools work before you pay for anything. A failed delivery does not count against the quota when the failure was ours or the network's.

**You never have to leave the chat to pay.** A tool call that runs out of quota comes back with a checkout link for your account.

## Supported clients

- **Claude** (web & desktop) — Settings → Connectors → Add custom connector
- **ChatGPT** — Settings → Apps & Connectors → Advanced → Developer mode → Add custom connector
- **Cursor** — one-click deeplink, or `~/.cursor/mcp.json`
- **VS Code** — one-click deeplink, or `.vscode/mcp.json`
- **Claude Code (CLI)** — `claude mcp add --transport http 1social https://mcp.1social.dev/mcp`
- **Gemini CLI** — `gemini mcp add --transport http 1social https://mcp.1social.dev/mcp`
- **Codex CLI** — `codex mcp add 1social --url https://mcp.1social.dev/mcp`
- **Generic HTTP MCP client** — point it at the URL and let it complete OAuth discovery

Full per-client instructions: [docs/setup.md](docs/setup.md).

## Cursor plugin

This repository is also a Cursor plugin. It contributes one MCP server (`1social`) and no rules, skills, agents, or hooks.

```
.cursor-plugin/plugin.json   ← plugin manifest
mcp.json                     ← the MCP server it contributes
assets/logo.svg              ← plugin logo
```

There is nothing to configure. The server advertises OAuth 2.1 metadata, Cursor registers itself dynamically, and the consent screen opens on first use.

## Example prompts

**Cross-post with a check first**
> "Draft a post about our new pricing page for LinkedIn, X and Bluesky, check it fits, then publish."

→ `preview_post` reports the per-network counts and cost; `publish_post` returns a receipt per network.

**Schedule with an image**
> "Post this screenshot to Instagram and Threads on Friday at 9am."

→ The attached file goes in `media`; Instagram would have refused the post without it.

**Find out what actually happened**
> "Did yesterday's post make it everywhere?"

→ `list_posts` returns the receipt — including any delivery still marked `unknown`.

**Repair, not repost**
> "LinkedIn failed. Retry just LinkedIn."

→ `retry_delivery` touches one delivery and leaves the successful networks alone.

More: [docs/examples.md](docs/examples.md).

## Authorization

OAuth 2.1 with PKCE, per the MCP authorization spec. The authorization server is `https://1social.dev`; it supports Dynamic Client Registration, so compatible clients set themselves up and you only see the consent screen. Scopes granted are `openid`, `profile`, `email` and `offline_access`.

**The assistant never sees your social credentials.** Your network tokens stay in 1Social; the assistant holds only a token for your 1Social account, and you can revoke it at any time from the web app.

Flow details: [docs/oauth.md](docs/oauth.md).

## Troubleshooting

Common connection, OAuth, and tool-call errors: [docs/troubleshooting.md](docs/troubleshooting.md).

## Project links

- Product: [1social.dev](https://1social.dev)
- MCP page: [1social.dev/mcp](https://1social.dev/mcp)
- MCP server: `https://mcp.1social.dev/mcp`
- OAuth issuer: `https://1social.dev`
- Issues: [github.com/sultanlive/1social-mcp/issues](https://github.com/sultanlive/1social-mcp/issues)

## License

MIT — see [LICENSE](LICENSE).

---

Built by [@sultanlive](https://github.com/sultanlive). 1Social is a hosted publishing service; this repo is documentation for its public MCP server. Server source is not open.
