# Installing 1Social MCP

1Social is a **remote** MCP server. There is nothing to clone, build or run locally, and there is no API key: the user signs in with OAuth in the browser.

- Server URL: `https://mcp.1social.dev/mcp`
- Transport: Streamable HTTP
- Auth: OAuth 2.1 with PKCE and Dynamic Client Registration. The first request returns HTTP 401 with `WWW-Authenticate: Bearer resource_metadata="https://mcp.1social.dev/.well-known/oauth-protected-resource"`; the client discovers the authorization server from there.

## Steps

1. **Add the server to the MCP settings.** For Cline, add this entry to `cline_mcp_settings.json` (keep any servers already there):

   ```json
   {
     "mcpServers": {
       "1social": {
         "type": "streamableHttp",
         "url": "https://mcp.1social.dev/mcp",
         "disabled": false
       }
     }
   }
   ```

   Other clients use the same URL; most call the type `http` or `streamable-http`.

2. **Sign in.** The server answers the first request with 401 until the user authorizes. Tell the user to click **Authenticate** next to the 1social server in the MCP Servers panel; a browser window opens on 1social.dev. Do not ask the user for a token or password in chat, and do not try to fetch one yourself.

3. **Connect social accounts.** If the user has no 1Social account or no networks connected yet, they do that at https://1social.dev/app in the browser. It cannot be done from chat because each network's consent screen has to be approved by the user.

4. **Verify.** Call `list_accounts`. It returns the connected channels with their ids, each network's limits and the remaining monthly quota. An empty list means step 3 is not done yet.

## Using it

1. `list_accounts`: channel ids and limits.
2. `preview_post`: a free dry run against every chosen network's rules. Publishes nothing.
3. Show the user the final text and target accounts, and wait for a clear yes. Publishing is public.
4. `publish_post` with an `idempotencyKey`: `scheduledAt: null` publishes now, an ISO-8601 UTC time schedules (up to 30 days ahead).
5. Report the per-network receipt exactly as returned. `unknown` means 1Social could not tell whether the post went out; say so.

Full tool list and troubleshooting: [README.md](README.md), [docs/setup.md](docs/setup.md), [docs/troubleshooting.md](docs/troubleshooting.md).
