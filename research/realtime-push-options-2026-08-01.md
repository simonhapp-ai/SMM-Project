# Research: Free/Cheap Push-Based Realtime Options for the Office Visualizer (2026-08-01)

**Question:** Simon wants the Office Visualizer to be genuinely *live* — a status update pushed from a Claude Code session (via Bash/curl/Node, no persistent server we control) should reach an open browser tab (the GitHub Pages static webapp) in sub-second-to-a-few-seconds time, not on any polling interval (8s or 30s alike). What real, currently-available, free-or-cheap push mechanisms fit "one publisher via REST call, many browser subscribers via a small JS snippet"?

**Method:** Live web search + doc fetches on 2026-08-01. Every limit/claim below is sourced; nothing is from training memory (pricing/free-tier terms move fast).

**Scoped-out by design, not re-litigated:** `office-live.html` (the Claude Artifact using `window.claude.mcp.watchTool`) is confirmed hard-capped at a ~30s `refetchInterval` floor by the official `artifact-capabilities` runtime contract (0.1.15) — it is poll-only by construction, since the Artifact CSP blocks raw WebSocket/fetch/XHR and only allows MCP connector calls (themselves poll-based). That's why this research targets the **other** live view — `SMM_Office_Visualizer/live/index.html`, a plain static site with no CSP restriction — as the one that can actually go push-based.

---

## Comparison table

| Option | Account needed | Publish mechanism | Subscribe mechanism | Free tier (2026) | Setup complexity |
|---|---|---|---|---|---|
| **ntfy.sh** | **No** (public instance; optional free account only for extras) | `curl -d "msg" ntfy.sh/topic` | `EventSource` (SSE) in browser | Generous, unspecified hard cap on public server; anti-abuse burst limit ~60 req then 1/10s | **Lowest** — zero signup, one curl line |
| **Ably** | Yes | REST POST, HTTP Basic Auth (`key:secret`) | JS SDK (`Ably.Realtime`) or raw SSE | 200 concurrent connections, 6M msgs/month, 500 msg/s, no card required | Low — simple auth, no request signing |
| **Supabase Realtime (Broadcast)** | Yes | REST POST to `/realtime/v1/api/broadcast/{topic}/events/{event}` with `apikey` header | JS SDK `.channel().subscribe()` | 200 concurrent connections, 100 msgs/sec, 256KB payload | Low-medium — needs a project first |
| **Pusher Channels** | Yes | REST POST to `/apps/{app_id}/events`, but needs **HMAC-SHA256 request signing** | JS SDK (`Pusher` client) | Sandbox: 100 connections, 200k msgs/day, no card required | Medium — signature math is real friction for a bare curl call |
| **PubNub** | Yes | Plain **GET** request, keys in the URL path, no signing | JS SDK (`PubNub` client) | 200 MAU, 1M transactions/month, 1GB storage (7-day retention) | Low — simplest URL scheme, no signing |
| **Firebase Realtime Database** | Yes (Google account) | REST `PUT`/`POST` to `https://<db>.firebaseio.com/path.json` | JS SDK `onValue()` listener | Spark plan: 100 simultaneous connections, 1GB stored, 10GB downloaded/month | Medium — REST writes need either open (insecure) rules or an auth token; Google's legacy database-secret auth is being phased toward OAuth2, adding real friction for a bare curl call |
| **Cloudflare (Durable Objects/Workers)** | Yes | No REST shortcut — you must **write and deploy a Worker + Durable Object** to fan out WebSocket messages | Raw `WebSocket` in browser | Workers Free: ~3M requests/mo, SQLite-backed Durable Objects now included free (since the Apr 2025 free-tier change), 5GB storage | **High** — this is a coding project, not a curl call. Also: Cloudflare's dedicated **Pub/Sub (MQTT) broker product was retired Aug 20, 2025** — no longer an option at all |
| **PartyKit** | Yes (Cloudflare account, via `wrangler`-style deploy) | N/A — still a dev framework, not a hosted publish endpoint | WebSocket via PartyKit client lib | Free in the sense that it deploys onto your own Cloudflare account's free Workers/Durable Objects quota | **High** — same as raw Cloudflare; PartyKit is now folded into Cloudflare as `cloudflare/partykit`/`partyserver`, "Powered by Durable Objects, Inspired by PartyKit," not a simpler standalone hosted service anymore |

---

## 1. Supabase Realtime — Broadcast (and Postgres Changes)

- **Broadcast is REST-publishable without a persistent connection or the JS SDK.** Confirmed: `POST https://<PROJECT_REF>.supabase.co/realtime/v1/api/broadcast/<topic>/events/<event>` with an `apikey` header. A new `http_send()` client-library method now exists specifically to force REST delivery regardless of WebSocket state — this is an explicitly supported, documented path, not a hack. ([Supabase Broadcast docs](https://supabase.com/docs/guides/realtime/broadcast))
- Example:
  ```bash
  curl -X POST \
    -H "apikey: <SUPABASE_ANON_OR_SERVICE_KEY>" \
    -H "Content-Type: application/json" \
    --data-raw '{"payload": {"department": "research", "status": "active"}}' \
    "https://<PROJECT_REF>.supabase.co/realtime/v1/api/broadcast/office-status/events/update"
  ```
- Subscribe (browser JS, `@supabase/supabase-js`):
  ```js
  const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);
  supabase.channel('office-status')
    .on('broadcast', { event: 'update' }, (msg) => render(msg.payload))
    .subscribe();
  ```
- **Postgres Changes** (the other mode) requires an actual table + row writes, triggering change events to subscribers — heavier than Broadcast for a status-blob use case; skip it here, Broadcast is the fit.
- **Free tier:** 200 concurrent connections, 2,000,000 realtime messages/month (from pricing page), 100 messages/sec, 256KB max broadcast payload. Plenty for "a handful of updates per hour, a few tabs open."
- **Account:** Yes, new Supabase project (already on the SMM roadmap per CLAUDE.md's Tooling Guide, just not activated yet).
- Sources: [Supabase Realtime Limits](https://supabase.com/docs/guides/realtime/limits), [Supabase Broadcast docs](https://supabase.com/docs/guides/realtime/broadcast), [Realtime Broadcast feature page](https://supabase.com/features/realtime-broadcast) (fetched 2026-08-01).

## 2. Pusher Channels

- **Confirmed: classic server-triggers-event-via-REST model still works** — `POST /apps/{app_id}/events` with `name`, `channels`, `data` in the body. But every request needs `auth_key`, `auth_timestamp`, `auth_version`, `body_md5`, and an HMAC-SHA256 `auth_signature` computed over the request — not something you can hand-type in a curl line without a helper script (their own docs assume you use a server SDK, e.g. `pusher-http-node`, to build the signed request for you). ([Pusher HTTP API docs](https://pusher.com/docs/channels/server_api/http-api/))
- Subscribe (browser JS):
  ```js
  const pusher = new Pusher('APP_KEY', { cluster: 'eu' });
  const channel = pusher.subscribe('office-status');
  channel.bind('update', (data) => render(data));
  ```
- **Free tier (Sandbox plan):** 100 concurrent connections, 200,000 messages/day, no credit card required to sign up.
- **Account:** Yes.
- **Verdict:** Workable, but the HMAC signing step is the one piece of friction that makes it worse than Ably/PubNub/ntfy for a bare Bash/curl publisher — you'd need a small wrapper script (Node, using their SDK) rather than a single curl line.
- Sources: [Pusher Channels Pricing](https://pusher.com/channels/pricing/), [Pusher HTTP API Reference](https://pusher.com/docs/channels/library_auth_reference/rest-api/) (fetched 2026-08-01).

## 3. Ably

- **Confirmed: plain HTTP POST with HTTP Basic Auth publishes instantly** — no signing, no SDK required to publish.
  ```bash
  curl -X POST "https://main.realtime.ably.net/channels/office-status/messages" \
    -u "<ABLY_API_KEY_ID>:<ABLY_API_KEY_SECRET>" \
    -H "Content-Type: application/json" \
    --data '{"name": "update", "data": {"department": "research", "status": "active"}}'
  ```
- Subscribe (browser JS, `ably` npm/CDN package):
  ```js
  const ably = new Ably.Realtime({ key: 'ABLY_API_KEY' }); // or token auth for a public page
  const channel = ably.channels.get('office-status');
  channel.subscribe('update', (msg) => render(msg.data));
  ```
  (For a public GitHub Pages page, don't embed the full API key client-side — use Ably's token-auth flow or a read-only/subscribe-only key restricted to that channel.)
- **Free tier:** 200 concurrent connections, 200 concurrent channels, 6,000,000 messages/month, 500 messages/sec rate limit, no credit card required. Messages retained 1 day (irrelevant here — the page just wants the latest state).
- **Account:** Yes.
- **Verdict:** The REST-publish story is the cleanest of the "real pub/sub services" — no signature math, generous free tier, purpose-built for exactly "server publishes, many browsers subscribe."
- Sources: [Ably Pricing/Limits](https://ably.com/docs/platform/pricing/limits), [Ably REST API Reference](https://ably.com/docs/api/rest-api) (fetched 2026-08-01).

## 4. Firebase Realtime Database / Firestore (Spark free plan)

- REST writes work: `curl -X PUT -d '{"status":"active"}' 'https://<project>.firebaseio.com/office/research.json'` — any write to the RTDB via REST is instantly pushed to browser SDK listeners (`onValue()`), this is RTDB's whole design.
- **The catch:** an unauthenticated REST write only works if your database security rules are wide open (`".write": true`) — fine for a low-stakes personal dashboard but a real, if minor, exposure (anyone could overwrite Simon's status page). Doing it properly needs either a Google OAuth2 access token or Firebase's legacy database-secret `?auth=` param (which Google has been steering people away from). That's more moving parts than Ably/ntfy for the same outcome.
- **Free tier (Spark):** 100 simultaneous connections, 1GB stored, 10GB downloaded/month. Comfortably enough for this use case.
- **Account:** Yes (Google/Firebase project).
- **Verdict:** Works, but the auth story is the worst tradeoff of the mainstream options here — either insecure-open rules or noticeably more setup than Ably.
- Sources: [Firebase RTDB REST Save Data docs](https://firebase.google.com/docs/database/rest/save-data), [Firebase RTDB Limits](https://firebase.google.com/docs/database/usage/limits) (fetched 2026-08-01).

## 5. Cloudflare (Workers / Durable Objects)

- **No REST-publish shortcut exists.** To fan a message out to many connected browsers you need a Durable Object acting as the broadcast hub, which means writing actual Worker code and deploying it with `wrangler` — a real (if small) coding project, not a curl call.
- **Cloudflare's dedicated Pub/Sub product (MQTT-based messaging) was retired** — its private beta ended August 20, 2025; Cloudflare now points people at Durable Objects/the `@cloudflare/actors` library or Queues instead, confirming there's no simpler managed pub/sub primitive on Cloudflare anymore.
- **Free tier:** SQLite-backed Durable Objects are now included on the Workers Free plan (added ~April 2025) — ~3M requests/month, 5GB Durable Object storage, WebSocket messages billed at a favorable 20:1 ratio. Genuinely free and genuinely capable of a real WebSocket relay — just requires building it.
- **Account:** Yes (Cloudflare).
- **Verdict:** Best raw capability and cost ceiling of anything researched, but disproportionate setup effort for "publish a JSON blob a few times an hour." Worth revisiting only if SMM later wants a general-purpose realtime backend for an actual venture, not just the Office Visualizer.
- Sources: [Cloudflare Durable Objects Free Tier changelog](https://developers.cloudflare.com/changelog/post/2025-04-07-durable-objects-free-tier/), [Durable Objects Limits](https://developers.cloudflare.com/durable-objects/platform/limits/), [Pub/Sub retirement note](https://www.answeroverflow.com/m/1389361809856790701) (fetched 2026-08-01).

## 6. PartyKit

- **Still exists, but as a library, not a simpler hosted alternative.** Acquired by Cloudflare in April 2024; the current `cloudflare/partykit` repo describes itself as "Powered by Durable Objects, Inspired by PartyKit" — it's now essentially a nicer developer API (`partyserver`) on top of the same Durable Objects primitive above, deployed to your own Cloudflare account. Docs remain live at docs.partykit.io.
- **Verdict:** Same setup burden as raw Cloudflare Durable Objects (write a server, deploy with a Wrangler-style CLI) for no meaningful free-tier or simplicity advantage over Ably/ntfy for this specific low-volume use case. Not the pick.
- Sources: [PartyKit "joining Cloudflare" post](https://blog.partykit.io/posts/partykit-is-joining-cloudflare/), [cloudflare/partykit GitHub](https://github.com/cloudflare/partykit), [partyserver README](https://github.com/cloudflare/partykit/blob/main/packages/partyserver/README.md) (fetched 2026-08-01).

## 7. Others found

**ntfy.sh — the simplest option found, full stop.**
- Open-source, and the public `ntfy.sh` instance is usable **with no account at all** — a "topic" is just a URL path you make up (treat it like a hard-to-guess slug/password since anyone who knows the topic name can publish or read it).
- Publish (literally this simple):
  ```bash
  curl -d '{"department":"research","status":"active"}' https://ntfy.sh/smm-office-status-<random-suffix>
  ```
- Subscribe (browser JS, no library needed — native `EventSource`):
  ```js
  const es = new EventSource('https://ntfy.sh/smm-office-status-<random-suffix>/sse');
  es.onmessage = (e) => render(JSON.parse(e.data));
  ```
- **Free tier:** No published hard cap on the public server beyond generic anti-abuse throttling (~60-request burst, then 1 request/10s per visitor/IP) — "as long as you don't abuse it, it's free." Paid tiers only add things like reserved topic names, higher limits, and email/priority extras SMM doesn't need.
- **Account:** No — this is the one option on this whole list that needs zero signup at all. (Self-hosting is also possible later if the public instance ever becomes a concern, since it's open source — not needed now.)
- Sources: [ntfy.sh homepage](https://ntfy.sh/), [ntfy docs — Subscribing via API](https://docs.ntfy.sh/subscribe/api/), [ntfy FAQ](https://docs.ntfy.sh/faq/) (fetched 2026-08-01).

**PubNub — a reasonable second-simplest, mentioned for completeness.**
- Publish is a plain **GET** with keys in the URL path, no signing: `GET https://ps.pndsn.com/publish/<pub_key>/<sub_key>/0/<channel>/0/<url-encoded-json>`.
- Subscribe needs the PubNub JS SDK (`PubNub` client, `.subscribe({channels:[...]})` + listener).
- Free tier: 200 MAU, 1,000,000 transactions/month, 1GB storage (7-day retention) — more than enough, but requires an account and is a bigger/older SDK than needed here.
- Sources: [PubNub REST API docs](https://www.pubnub.com/docs/sdks/rest-api), [PubNub pricing](https://www.pubnub.com/pricing/) (fetched 2026-08-01).

---

## Recommendation

**Pick ntfy.sh.**

Reasoning against the specific constraints (near-zero budget, Claude Code as publisher via a bare Bash/curl call — no Node dependency install, no signed requests, no persistent process we control — a static GitHub Pages page as the only subscriber, low volume):

- **Zero account creation.** Every other option on this list needs Simon (or Claude, but per the Charter only the Chairman owns accounts) to sign up for a new service. ntfy.sh needs nothing — publish today, from the next Bash tool call, with no setup step blocking on the Chairman TODO list at all.
- **Publish is one curl line**, no HMAC signing (unlike Pusher), no OAuth/legacy-secret juggling (unlike Firebase), no SDK/project bootstrapping (unlike Supabase/Ably/PubNub), and no code to write and deploy (unlike Cloudflare/PartyKit).
- **Subscribe is ~4 lines of vanilla JS** (`EventSource`) with no third-party script tag to load into the GitHub Pages page at all — smaller footprint than pulling in any vendor's JS SDK.
- **Sub-second delivery** — SSE push, not polling, and open-source/self-hostable later if the public instance's soft limits ever actually bite (they won't at "a handful of updates per hour, a few tabs").

Runner-up, if Simon ever wants message history/retention, presence, or a "real" pub/sub product with an account and dashboard: **Ably** — cleanest REST-publish story of the account-based options (Basic Auth, no signing) and the most generous free tier (6M msgs/month, 200 connections) of anything requiring signup.

### Concrete next steps

1. Pick a hard-to-guess topic name, e.g. `smm-office-status-h7k2p9` (treat it as a shared secret — don't reuse a guessable word).
2. Add an `EventSource` listener to `SMM_Office_Visualizer/live/index.html` that renders on message receipt, falling back to (or running alongside) the existing 8s `status.json` fetch as a safety net for tabs that were open before a network hiccup or an ntfy.sh restart.
3. Whenever department state changes, in addition to the existing `status.json` commit+push (keep that as the durable source of truth / cold-load state for a freshly opened tab), also fire:
   ```bash
   curl -d "$(cat SMM_Office_Visualizer/live/status.json)" https://ntfy.sh/smm-office-status-h7k2p9
   ```
   so open tabs update instantly while `status.json` stays the ground truth for anyone loading the page fresh.
4. No Chairman action item needed — this doesn't require a new account, unlike everything else in the Chairman TODO list.
