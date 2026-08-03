# Example prompts

Ask the way you would ask someone who runs your accounts. The assistant picks the tools; a few starting points, and what happens underneath.

---

## Publish across networks

> "Post this to LinkedIn, X and Bluesky: <text>. Check it fits first."

`preview_post` reports the per-network character counts and the quota cost, then `publish_post` returns a receipt per network. The preview is free, and it is the only preview there is.

> "Which accounts do I have connected, and how much quota is left?"

`list_accounts` — also the source of the channel ids every other tool needs.

---

## Schedule

> "Schedule this for Friday 9am on Instagram and Threads." *(with an image attached)*

The attachment goes in `media`. Instagram would have refused the post without one. The scheduling window is 30 days.

> "Move Friday's post to Monday, and shorten the X version."

`update_post`. The networks have no edit endpoint, so it withdraws the scheduled post and replaces it — and says so if some networks kept the old version.

> "Cancel the post scheduled for tomorrow."

`cancel_post` — the reserved quota comes back.

---

## Threads and long posts

> "Turn these three paragraphs into a thread on X and Bluesky."

`preview_post` says which networks will publish it as a native chain and which will join the parts into one post. Worth checking before publishing, because the two read very differently.

---

## Read the receipt

> "Did yesterday's post make it everywhere?"

`list_posts` returns each network separately: delivered, failed with a reason, cancelled, or `unknown`. Stored engagement metrics come along where the network reports any.

> "Show me everything that failed this week."

`list_posts` with a status filter.

---

## Repair

> "LinkedIn failed. Retry just LinkedIn."

`retry_delivery` touches one delivery and leaves the successful networks alone. It refuses permanent failures — a revoked credential has to be fixed in the browser first.

> "I checked X and the post is there."

`confirm_delivery` settles an `unknown` delivery with your answer. The assistant is not allowed to answer this one for you.

---

## Delete

> "Take yesterday's post down everywhere you can."

`delete_post` removes it from 1Social and from X, LinkedIn, Bluesky and Facebook. Instagram, TikTok and Threads have no working delete API — those posts stay live, and the tool result says so rather than claiming a clean sweep.

---

## What the assistant will not do for you

- **Connect a social account.** Needs a browser and your signature on the network's consent screen.
- **Buy a plan.** A quota error comes back with a link; checkout is yours.
- **Decide whether an `unknown` delivery is live.** You look, you tell it, it records.
