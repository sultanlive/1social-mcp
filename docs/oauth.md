# OAuth — how access works

1Social MCP follows the MCP authorization spec: OAuth 2.1 with PKCE (`S256`) and Dynamic Client Registration. The MCP server at `mcp.1social.dev` is the **resource server**; the 1Social web app at `1social.dev` is the **authorization server**. Compatible clients discover all of this on their own — you only see the consent screen.

## Discovery flow

1. Your client sends its first JSON-RPC request (e.g. `POST /mcp` with `tools/list`).
2. The server answers:
   ```
   HTTP/2 401
   WWW-Authenticate: Bearer resource_metadata="https://mcp.1social.dev/.well-known/oauth-protected-resource"
   ```
3. The client fetches the protected-resource metadata (RFC 9728):
   ```
   GET https://mcp.1social.dev/.well-known/oauth-protected-resource
   ```
   ```json
   {
     "resource": "https://mcp.1social.dev/mcp",
     "authorization_servers": ["https://1social.dev"],
     "scopes_supported": ["openid", "profile", "email", "offline_access"],
     "bearer_methods_supported": ["header"],
     "resource_documentation": "https://1social.dev/legal/privacy",
     "resource_name": "1Social"
   }
   ```
   Note that `resource` is the full endpoint URL, **path included** — it is an identifier, not an origin, and a client that sends only the origin as its `resource` parameter gets `invalid_target` at the token endpoint.
4. The client fetches the authorization-server metadata:
   ```
   GET https://1social.dev/.well-known/oauth-authorization-server
   ```
   This returns `authorization_endpoint`, `token_endpoint`, `registration_endpoint`, `jwks_uri`, the supported grants (`authorization_code`, `refresh_token`) and `code_challenge_methods_supported: ["S256"]`.
5. The client registers itself at the `registration_endpoint` (Dynamic Client Registration) — nothing to create by hand.
6. The client opens the authorization endpoint in a browser. You sign in to 1Social and approve.
7. The client exchanges the code for a token at the token endpoint, with its PKCE verifier.
8. Every later request carries `Authorization: Bearer <token>` and reaches the tools.

## Scopes

`openid`, `profile`, `email` and `offline_access` — identity plus a refresh token. There is no separate tool scope: a token is either good for your account's tool surface or it is not.

Access tokens are **opaque**, not signed JWTs. The MCP server resolves each one against the authorization server, so a token is only ever as valid as 1Social says it is at that moment — which is what makes revocation immediate.

## What the token can and cannot reach

The token authorizes the published MCP tools against **your** 1Social account: your connected channels, your posts, your quota.

**It does not carry your social credentials.** The access tokens for Instagram, TikTok, X, LinkedIn, Bluesky, Threads and Facebook stay inside 1Social and are never returned by a tool. An assistant with a 1Social token can publish through 1Social; it cannot take over your accounts, change their settings, or read your DMs.

Connecting a *new* social account is deliberately not a tool. It needs a browser and your signature on the network's own consent screen.

## Managing access

[1social.dev/app/agent](https://1social.dev/app/agent) lists every client that has completed OAuth under **Connected apps**, with a disconnect button. Disconnecting revokes that client's access immediately — the next tool call gets a 401 and the client has to authorize again.

## Token lifetime

Access tokens are short-lived and renewed with the refresh token (`offline_access`). A client that does not persist the refresh token will re-prompt every session; that is a client-side problem, not a server one.
