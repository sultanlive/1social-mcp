# Changelog

All notable changes to the 1Social MCP listing are recorded here. Versions match the records published to `registry.modelcontextprotocol.io`.

## 1.0.5 — 2026-11-26

- Twelve networks. Discord joins: the 1Social bot posts to the text, announcement and forum channels you pick (a forum post is titled with the first line; required forum tags via `discord.tags`; the parts of a thread go into one Discord thread), with its own registry entry on its own mirror: `dev.1social/discord` → `https://mcp.1social.dev/mcp/discord`. One tag publishes all thirteen.
- Discord posts can be removed any time, thread included.

## 1.0.4 — 2026-11-26

- Eleven networks. Tumblr joins (posts to the blogs you pick: text, up to 30 images or one video, a title, a link card, a source and tags), with its own registry entry on its own mirror: `dev.1social/tumblr` → `https://mcp.1social.dev/mcp/tumblr`. One tag publishes all twelve.
- Tumblr posts can be removed any time.
- README: current prices (Solo $29, Pro $49, 7-day free trial).

## 1.0.3 — 2026-11-22

- Ten networks. Pinterest (image pins and carousels on a board) and Telegram channels (through the 1Social bot) join the list, each with its own registry entry on its own mirror: `dev.1social/pinterest` → `https://mcp.1social.dev/mcp/pinterest`, `dev.1social/telegram` → `https://mcp.1social.dev/mcp/telegram`. One tag publishes all eleven.
- Telegram posts can be removed for 48 hours after publishing; Pinterest pins can be removed any time.

## 1.0.1 — 2026-10-27

- Each per-network entry now has its own remote, the `/mcp/<network>` mirror of the same server (`https://mcp.1social.dev/mcp/instagram`, …). The registry accepts a remote URL in one record only, so 1.0.0 published `dev.1social/1social` and refused the other eight.

## 1.0.0 — 2026-10-27

- Eight per-network registry entries alongside `dev.1social/1social`, all on the same remote: `dev.1social/instagram`, `tiktok`, `x-twitter`, `threads`, `linkedin`, `facebook`, `youtube-shorts`, `bluesky` (`servers/<network>/server.json`). One tag publishes all nine.
- YouTube Shorts joins the network list (eight networks).
- `llms-install.md` for agents that install servers from the README (Cline).

## 0.1.0 — 2026-08-03

- Initial registry listing under the `dev.1social/1social` namespace.
- Streamable-HTTP remote at `https://mcp.1social.dev/mcp`.
- OAuth 2.1 with PKCE (`S256`) and Dynamic Client Registration; authorization server `https://1social.dev`, scopes `openid profile email offline_access`.
- Ten tools: `list_accounts`, `preview_post`, `publish_post`, `list_posts`, `update_post`, `cancel_post`, `delete_post`, `retry_delivery`, `upload_media`, `confirm_delivery`.
- Seven networks: Instagram, TikTok, X, LinkedIn, Bluesky, Threads, Facebook.
