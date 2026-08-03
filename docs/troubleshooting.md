# Troubleshooting

Common errors when connecting to or calling 1Social MCP, and what to do about them.

Tool failures come back as normal MCP results with `isError: true` and a readable message — not as protocol errors — so your assistant can usually tell you the fix itself.

---

## Connection / OAuth

### `WWW-Authenticate: Bearer resource_metadata=...` on the first call

Expected. The first request to `mcp.1social.dev/mcp` returns HTTP 401 with that header so a compatible client can run OAuth discovery. Authorize in the browser when prompted and the client retries with a token.

### `invalid_target` at the token endpoint

The `resource` parameter must be the full endpoint URL including the path — `https://mcp.1social.dev/mcp`, not `https://mcp.1social.dev`. It has to match the `resource` field in `/.well-known/oauth-protected-resource` byte for byte.

### The consent screen never appears

Some clients open a tab rather than a popup — check tab focus, and allow popups for `1social.dev`. After consent the tab closes and the client resumes on its own.

### It asks me to authorize on every session

Your client is not persisting the refresh token. 1Social issues short-lived access tokens plus a refresh token (`offline_access`); a client that stores only the access token will re-prompt.

### 401 out of nowhere, mid-session

The token was revoked. Check **Connected apps** at [1social.dev/app/agent](https://1social.dev/app/agent) — if the client is not listed, it was disconnected and has to authorize again.

---

## Tool calls

### "One or more of those account ids does not exist on this account"

Channel ids came from an earlier turn and one of the accounts has since been disconnected. Call `list_accounts` again and use the current ids — ids are not worth caching across a conversation.

### "The post was refused because it exceeds what one or more networks accept"

A limit was hit: caption length, segment count, or a network that requires media. Call `preview_post` — it names the network and the amount it is over. Instagram and TikTok reject any post without an image or video.

### The attached image never reaches the tool

Only `media` (a file attached in the chat) and `mediaUrls` (a public `https` link) are accepted, and they are top-level parameters. A file on your machine that the client cannot attach needs `upload_media`: call it, `PUT` the bytes to the `uploadUrl` with the headers it returns, then pass the returned `publicUrl` in `mediaUrls`. The upload target is short-lived, so do the `PUT` straight away. A link to a local web server does not work.

### "That post can no longer be edited"

`update_post` only works while a post is still scheduled and untouched. Once it is published, in flight, or cancelled, there is nothing to edit — a published post can only be deleted.

### "Posts can only be scheduled up to 30 days ahead"

That is the whole window. Pass a nearer time, or `null` to publish now.

### A retry was refused

`retry_delivery` refuses for a reason, and the reason matters:

| Reason | What to do |
|---|---|
| the failure is permanent (revoked credential, policy rejection) | fix the cause first — usually reconnect the account at [1social.dev/app/channels](https://1social.dev/app/channels). Retrying would fail again and spend quota. |
| retries exhausted | the delivery has already been retried the maximum number of times. |
| the delivery has not failed | there is nothing to retry. |
| the outcome is unknown | a retry could publish a second copy. Check the network, then call `confirm_delivery` — or pass `force: true` if you accept a possible duplicate. |
| the account was disconnected | reconnect it in the browser, then publish again. |

### A delivery is stuck on `unknown`

`unknown` means 1Social genuinely could not determine whether the post went out, and it will not guess. Look at the network yourself, then have the assistant call `confirm_delivery` with your answer: live commits the quota, not-live marks the delivery failed so it can be retried.

### `delete_post` said the post is still live

Correct, and not a bug. Instagram, TikTok and Threads expose no working delete API — the post stays up and only you can remove it. X, LinkedIn, Bluesky and Facebook do support removal. On a thread only the first post comes down; the rest stay live as orphans.

### "The 1Social API did not respond in time"

The request may or may not have taken effect. Call `list_posts` and look before retrying anything that publishes — an `idempotencyKey` on the original `publish_post` is what makes a safe retry possible.

---

## Quota & billing

### "This account is out of posting quota for the current period"

The message includes how many units the post needed and how many remain. Quota is metered in **deliveries, not posts** — one post to four networks is four — and a delivery to X costs more units than the others because X charges per post.

Upgrade at [1social.dev/pricing](https://1social.dev/pricing). The assistant can hand you the link but cannot buy on your behalf; checkout happens in your browser.

A new account has a small free monthly allowance, so the tools work before you pay for anything. A failed delivery does not count against quota when the failure was ours or the network's. The allowance resets at the start of each month.

---

## Still stuck?

- Email: [support@1social.dev](mailto:support@1social.dev)
- Issue tracker: [github.com/sultanlive/1social-mcp/issues](https://github.com/sultanlive/1social-mcp/issues)
