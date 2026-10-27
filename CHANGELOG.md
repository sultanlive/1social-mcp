# Changelog

All notable changes to the 1Social MCP listing are recorded here. Versions match the records published to `registry.modelcontextprotocol.io`.

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
