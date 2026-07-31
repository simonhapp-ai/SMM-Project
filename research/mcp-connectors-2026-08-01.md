# Research: Free/Open-Source MCP Connectors for SMM (2026-08-01)

**Question:** Which real, currently-available, free/open-source MCP servers could materially help SMM across payments, browser automation, deployment, database, email, social distribution, and analytics/SEO — and which of the 8 pre-registered predictions hold up against live 2026 data?

**Method:** Live web search + GitHub README fetches on 2026-08-01. Every finding below is dated; nothing here is from training memory. Where a repo's exact last-commit date wasn't visible in a fetched page, I report the proxy signal that was visible (commit count, archive banner, release cadence claims from secondary sources) and flag the confidence level.

---

## 1. Payments / monetization

**Stripe — official, recommended once a venture charges money.**
- Project: [`stripe/agent-toolkit`](https://github.com/stripe/agent-toolkit) (MCP server + SDKs). Official docs: [docs.stripe.com/mcp](https://docs.stripe.com/mcp).
- License: MIT. ~1.7k GitHub stars, 377 commits — actively maintained by Stripe itself.
- Account/API key: **Yes** — requires a real Stripe account (Chairman-only: bank details, KYC, ToS acceptance per CLAUDE.md's role split).
- Install:
  - Local: `npx -y @stripe/mcp --tools=all --api-key=YOUR_STRIPE_SECRET_KEY` (use a Restricted API Key so the server only exposes permitted tools).
  - Remote/OAuth (simpler): `npx -y mcp-remote https://mcp.stripe.com`.
- Cost: the MCP itself is free; Stripe takes its normal per-transaction fee only once money actually moves — no fee for installing or idling.
- Sources: [Official Stripe MCP Server — mcpservers.org](https://mcpservers.org/servers/stripe/agent-toolkit), [Stripe MCP docs](https://docs.stripe.com/mcp), [stripe/ai GitHub](https://github.com/stripe/ai) (fetched 2026-08-01).

**Lemon Squeezy — usable but strategically muddied; skip the MCP layer.**
- Lemon Squeezy was acquired by Stripe in 2024 and remains operational, but Stripe is folding it into "Stripe Managed Payments" — some indie-SaaS founders are already migrating away because "you're no longer with a scrappy indie-focused company... its MoR product is a side product" ([Fungies.io, 2026](https://fungies.io/lemon-squeezy-stripe-acquisition-saas-founders-2026/); [Lemon Squeezy's own 2026 update post](https://www.lemonsqueezy.com/blog/2026-update)).
- No official Lemon Squeezy MCP exists. Community-only wrappers: `atharvagupta2003/mcp-lemonsqueezy`, `IntrepidServicesLLC/lemonsqueezy-mcp-server`, `YawLabs/lemonsqueezy-mcp` — all thin API-key wrappers, unverified maintenance depth.
- **My read:** given Stripe now owns both, and Stripe's own MCP is official/maintained/well-starred, there's no reason to add an unofficial Lemon Squeezy MCP. If a venture wants merchant-of-record simplicity over raw Stripe, that's a Chairman/strategist product decision, not a tooling gap.

**Gumroad — good no-code option for the Chairman, MCP layer optional and unofficial.**
- No official Gumroad MCP. Community options: `keithah/gumroad-mcp` (`npx gumroad-mcp@latest init`), `rmarescu/gumroad-mcp` — both small, unofficial, require a `GUMROAD_ACCESS_TOKEN` from Settings → Advanced.
- **My read:** Gumroad itself (flat ~10% fee, zero monthly cost, Chairman just needs an account) is a fine low-effort payment rail for a first digital product — but its MCP only lets Claude *query* sales data, it isn't needed to actually sell. Not worth installing until there's a concrete "Claude needs to read Gumroad sales automatically" task.

Sources: [GitHub — keithah/gumroad-mcp](https://github.com/keithah/gumroad-mcp), [GitHub — rmarescu/gumroad-mcp](https://github.com/rmarescu/gumroad-mcp) (2026-08-01).

---

## 2. Browser automation

**Playwright MCP — the clear winner, install now.**
- Project: [`microsoft/playwright-mcp`](https://github.com/microsoft/playwright-mcp).
- License: Apache-2.0. ~35.7k stars, 565 commits — Microsoft-maintained, clearly the most active project in this whole research pass.
- Account/API key: **None.** Runs entirely locally.
- Install: `npx @playwright/mcp@latest`
- Why it's strong: uses accessibility-tree snapshots (not screenshots), cross-browser (Chromium/Firefox/WebKit), no vision model needed. Good for competitor research, scraping public pages, and QA-testing SMM's own built sites before calling them shipped (pairs with the `run` skill already in CLAUDE.md).
- Sources: [microsoft/playwright-mcp GitHub](https://github.com/microsoft/playwright-mcp) (fetched 2026-08-01), [mcp.directory comparison, 2026](https://mcp.directory/blog/chrome-devtools-mcp-vs-playwright-mcp-2026).

**Puppeteer MCP — abandoned, do not use.**
- The official `@modelcontextprotocol/server-puppeteer` reference server was moved to the archived `modelcontextprotocol/servers-archived` repo in 2025 and the MCP team explicitly "recommend[s] migrating to Playwright MCP" ([dev.to, 2026 roundup](https://dev.to/jangwook_kim_e31e7291ad98/top-15-mcp-servers-every-developer-should-install-in-2026-n1h)). Flagging per instructions: this looked promising in search results (lots of tutorials still reference it) but is dead.

---

## 3. GitHub / deployment

**GitHub official MCP — real but not npx, and only marginal value over plain git for SMM today.**
- Project: [`github/github-mcp-server`](https://github.com/github/github-mcp-server). License: MIT. ~31.9k stars, 1,024 commits — actively maintained.
- Account: needs GitHub OAuth login or a PAT (SMM already has the `simonhapp-ai/SMM-Project` repo, so this is just re-authenticating, not a new account).
- **Install: no npx method.** Confirmed by reading the README directly — only Docker (`docker run -i --rm -e GITHUB_PERSONAL_ACCESS_TOKEN ghcr.io/github/github-mcp-server`), a compiled binary, building from source, or GitHub's own hosted remote endpoint. The old `@modelcontextprotocol/server-github` npm package that many older tutorials cite is **not** part of this repo and is one of the servers that got archived alongside SQLite/Postgres/Puppeteer (see below) — treat any "`npx -y @modelcontextprotocol/server-github`" instruction you find elsewhere as stale.
- **My read on redundancy (directly answering the prompt's flag):** CLAUDE.md already notes `gh` isn't installed and plain `git` handles push-to-GitHub-Pages fine. The GitHub MCP server's *incremental* value over that baseline is narrow but real: it can toggle repo settings (e.g., enabling Pages), inspect Actions run/build status, and file issues — things plain `git` literally cannot do without either the `gh` CLI or the GitHub web UI. So it's not fully redundant, but it's also not urgent: it only matters once SMM needs to manage Actions/issues/PRs programmatically, which hasn't come up yet per the Progress Log. Low priority, Docker-based (adds a dependency), skip for now.
- Sources: [github/github-mcp-server README](https://github.com/github/github-mcp-server) (fetched 2026-08-01).

**Vercel / Netlify / Cloudflare Pages — redundant for SMM's current static-site baseline; only matters if a venture outgrows static hosting.**
- **Netlify** (most novice-friendly of the three): official [`netlify/netlify-mcp`](https://github.com/netlify/netlify-mcp), npm package `@netlify/mcp`. Install: `npx -y @netlify/mcp` (needs Node 22+, and a Netlify PAT for auth troubleshooting). Netlify account required.
- **Vercel**: official, but remote-only — `claude mcp add --transport http vercel https://mcp.vercel.com` (OAuth) or `npx -y mcp-remote https://mcp.vercel.com`. Vercel account required.
- **Cloudflare**: official remote MCP at `https://mcp.cloudflare.com/mcp` (OAuth, 2,500+ API endpoints via `search()`/`execute()` tools); the npx-based `workers-mcp` is for *building* a new Worker-hosted MCP server, a different use case. Cloudflare account required.
- **My read:** CLAUDE.md is explicit that GitHub Pages is the free-hosting default, and it's already working via plain `git push`. All three of these platforms add genuine value only when a venture needs something GitHub Pages structurally cannot do — serverless functions, edge middleware, server-side rendering, or (for Vercel/Netlify) build-time environment secrets. Until a venture design calls for that, adding any of these three is pure surface area for zero benefit. Recommendation: don't install any of them speculatively; revisit only when `product-designer`/`web-developer` scope a venture that literally cannot be a static site.
- Sources: [netlify/netlify-mcp GitHub](https://github.com/netlify/netlify-mcp), [Vercel MCP docs](https://vercel.com/docs/agent-resources/vercel-mcp), [Cloudflare Agents docs](https://developers.cloudflare.com/agents/model-context-protocol/cloudflare/servers-for-cloudflare/) (2026-08-01).

---

## 4. Database / backend

**Postgres and SQLite official MCP servers are both archived/dead — important correction to the task's framing.**
- Confirmed directly: `modelcontextprotocol/servers-archived` was archived **2025-05-29**, is read-only, and its banner states "no security updates forthcoming." Both the SQLite and Postgres reference servers live only in that dead repo.
- I also checked the *current* `modelcontextprotocol/servers` repo (still active, 4,158 commits, dual MIT/Apache-2.0 license): its live reference-server list is now just **Everything, Fetch, Filesystem, Git, Memory, Sequential Thinking, Time** — SQLite and Postgres are explicitly not among them, confirming they were dropped, not just relocated-and-maintained.
- Community forks exist for Postgres (e.g. `ahmedmustahid/postgres-mcp-server`, `@henkey/postgres-mcp-server`) but I found no evidence of significant, actively-maintained adoption — treat as unverified/low-confidence.
- **Flag per instructions:** SQLite MCP looked like an obvious "simple local DB for an early venture" pick in search results but is genuinely abandoned. Don't rely on it.
- Sources: [modelcontextprotocol/servers-archived](https://github.com/modelcontextprotocol/servers-archived) (fetched 2026-08-01), [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) (fetched 2026-08-01).

**Supabase — the right pick once a venture becomes a real SaaS tool.**
- Project: [`supabase-community` / `supabase/mcp`](https://github.com/supabase/mcp). License: Apache-2.0. ~2.8k stars, 406 commits.
- Account: **Yes** — Supabase account + Personal Access Token (or OAuth for the hosted remote server). Chairman needs to create the account.
- Install: `npx -y @supabase/mcp-server-supabase@latest --access-token <personal-access-token>`, or the hosted remote `https://mcp.supabase.com/mcp` (OAuth, described by Supabase as the modern path, with the npx flow now secondary).
- Capability: full Postgres under the hood (SQL, migrations, Edge Functions, auth, logs) — effectively supersedes the dead official Postgres MCP for any venture that needs a real backend.
- Supabase free tier: 2 free projects, enough to prototype a real SaaS tool at zero cost before any revenue exists.
- Sources: [Supabase MCP announcement](https://supabase.com/blog/mcp-server), [supabase/mcp GitHub](https://github.com/supabase/mcp) (fetched 2026-08-01).

---

## 5. Email sending

**Resend — best current pick: official, maintained, generous free tier, no blocking cost.**
- Project: [`resend/resend-mcp`](https://github.com/resend/resend-mcp). License: MIT. 159 commits — official, Resend-maintained.
- Account: **Yes** — Resend account + API key (and domain verification for production sending beyond your own verified test address).
- Install: `npx -y resend-mcp` (env vars `RESEND_API_KEY`, `SENDER_EMAIL_ADDRESS`), or `npx add-mcp resend-mcp --name resend --env "RESEND_API_KEY=re_xxxxxxxxx"`.
- Free tier: **3,000 emails/month, capped at 100/day, on 1 domain** — solid for a newsletter/lead-capture funnel at launch scale.
- Sources: [resend/resend-mcp GitHub](https://github.com/resend/resend-mcp) (fetched 2026-08-01), [Resend pricing breakdown, 2026](https://coldletter.com/blog/resend-pricing/).

**Mailgun — official MCP exists but less clearly free; SendGrid's free tier is reportedly gone.**
- Mailgun: official [`mailgun/mailgun-mcp-server`](https://github.com/mailgun/mailgun-mcp-server), `npx -y @mailgun/mcp-server`, 50+ operations exposed. Needs Mailgun account/API key; historically Mailgun's free trial has been more limited/time-boxed than Resend's perpetual free tier — needs Chairman to check current signup terms before relying on it.
- SendGrid: **no official MCP** — only community/unofficial ones (e.g. a Flask-based one by `garethcull`), and a 2026 comparison piece is literally titled "SendGrid Killed Its Free Tier, Now What?" ([dev.to, 2026](https://dev.to/thiago_alvarez_a7561753aa/resend-vs-sendgrid-2026-sendgrid-killed-its-free-tier-now-what-2gh4)) — deprioritize SendGrid entirely for a zero-budget operation.
- **My read:** Resend is simply the better choice on every axis (official, maintained, free tier, MIT) — no reason to reach for Mailgun or SendGrid unless Resend's limits are hit.

---

## 6. Social media / marketing distribution

This is where the picture diverges sharply from what a naive search would suggest — **the platform-level legal/ToS terrain changed materially in early 2026** and directly affects what's actually usable.

**X (Twitter) — no longer free for new developers as of Feb 2026. Major finding.**
- On 2026-02-06, X discontinued its free developer tier entirely and moved to pay-per-use by default for all new accounts: **$0.015 per post created ($0.20 if it contains a link), $0.005 per post read** — new signups get a small one-time credit voucher, not ongoing free usage ([xpoz.ai, 2026](https://www.xpoz.ai/blog/guides/understanding-twitter-api-pricing-tiers-and-alternatives/), [opentweet.io, 2026](https://opentweet.io/how-to/x-api-pay-per-use-explained)).
- Community MCPs exist (`Infatoshi/x-mcp` MIT but thin — only 5 commits/51 stars at last check; `rafaljanicki/x-twitter-mcp-server`; `JohannesHoppe/x-autonomous-mcp`), but they're all just wrappers around an API that now costs money from the first call for a fresh SMM developer account.
- **Implication:** X is off the table as a "free" distribution channel for a brand-new venture unless the Chairman already holds a legacy free-tier developer credential (unlikely for a fresh Aug-2026 setup) or is willing to pay per post.

**Reddit — free tier exists but is explicitly non-commercial; using it to market a revenue-generating venture is a ToS violation. Legal flag.**
- Reddit's free API tier (100 QPM OAuth) is **restricted to non-commercial use**; "monetizing applications built on free tier access violates terms of service." Commercial access requires Reddit's approval plus a paid agreement with a **~$12,000/month minimum spend** ([prowlo.com, citybiz.co, 2026](https://prowlo.com/blog/reddit-api-pricing)).
- Separately, most free/unofficial Reddit scraping-style MCPs broke in May 2026 when Reddit started returning 403s on unauthenticated endpoints ([prowlo.com, 2026](https://prowlo.com/blog/best-reddit-mcp-servers)).
- **This is a genuine legal/compliance flag per SMM's "100% legal only" constraint**, not just a tooling note: any automated Reddit marketing activity for a venture that makes or intends to make money should be treated as commercial use under Reddit's ToS, and the free API tier does not cover that. Manual, human, individual-account posting under Reddit's normal site ToS (not the commercial API terms) is a different legal question and outside this tooling review — flagging for `strategist`/Chairman rather than resolving it here.

**LinkedIn — unofficial MCP relies on scraping via logged-in session cookies. Legal/ToS flag, do not automate.**
- The most-referenced option, `stickerdaniel/linkedin-mcp-server`, works by reading LinkedIn through **your own logged-in browser session** — it is explicitly "not affiliated with, authorized by, endorsed by, or sponsored by LinkedIn," and sources warn "picking the wrong one is how people get their LinkedIn account flagged," shipping with a "no guarantee of account safety" disclaimer ([Postbeam, 2026](https://www.postbeam.ai/blog/linkedin-mcp-server); [Taplio, 2026](https://taplio.com/blog/linkedin-mcp-github)).
- **My read:** this is a ToS-violation-risk tool, not a legitimate free distribution channel. Recommend against using any unofficial LinkedIn MCP for SMM.

**Bluesky — the standout: genuinely free, open, no gatekeeping, install now.**
- Bluesky/AT Protocol API access is free with **no developer-portal approval process** — "you create an account and start calling the AT Protocol API," 5,000 points/hour rate cap (~1,666 posts/hour theoretical), no special commercial tier required ([docs.bsky.app rate limits](https://docs.bsky.app/docs/advanced-guides/rate-limits), [Blotato, 2026](https://www.blotato.com/blog/bluesky-api-pricing)).
- Community MCP: `morinokami/mcp-server-bluesky` — `npx -y mcp-server-bluesky` with `BLUESKY_USERNAME`/`BLUESKY_PASSWORD` env vars (an app password, not a full API application/review process). Also `semioz/bluesky-mcp`.
- **My read:** given X is now pay-per-use and Reddit's free tier is ToS-restricted to non-commercial use, Bluesky is currently the cleanest genuinely-free, ToS-compliant, API-based distribution channel for a monetized venture. Its reach is smaller than X's, but it's the one platform here with zero legal ambiguity and zero cost.

**Buffer — a real "generic marketing MCP" now exists, first-party, and is a legitimate answer to prediction #3.**
- Buffer shipped an **official first-party MCP server in public beta in March 2026**, built on their GraphQL API, supporting Claude, Claude Code, Cursor, Zapier, n8n, and more — schedules posts, browses the content queue, checks connected channels ([Buffer, cited via tinkeringwithideas.io, 2026](https://tinkeringwithideas.io/zapier-mcp-social-posts/)).
- Free tier: **3 channels, 10 scheduled posts each** — thin, but genuinely free and avoids per-platform ToS/scraping risk since Buffer itself holds the official platform integrations.
- **My read:** this is worth knowing about even though its free tier is too small to be SMM's primary channel — it's the legitimate middle ground between "build a direct per-platform integration" and "risk a ToS violation with a scraping-based MCP."

Sources per claim are inlined above; all fetched/searched 2026-08-01.

---

## 7. Analytics / SEO

**Google Search Console (GSC) — free, no official MCP, several viable community options.**
- No single "official Google" GSC MCP found. Community options: `ahonn/mcp-server-gsc` (`npx -y mcp-server-gsc`), `sofianbettayeb/gsc-mcp-server` (`npx -y gsc-mcp-server`). Needs a Google account with a verified GSC property + OAuth — GSC itself is a free Google product, so no blocking cost.
- Source: [mcpservers.org GSC listing](https://mcpservers.org/servers/ahonn/mcp-server-gsc) (2026-08-01).

**GA4 — official server exists but is Python/pipx, not npx; community npx alternatives available.**
- Official: Google Analytics team's own MCP server, Apache-2.0, labeled "experimental," installed via `pipx` (Python 3.10+) — not npx.
- Community npx alternatives: `ruchernchong/mcp-server-google-analytics` (`npx -y mcp-server-google-analytics`), `surendranb/google-analytics-mcp` (`npx -y google-analytics-mcp`).
- Needs a Google account + GA4 property + Admin/Data API access (OAuth or service account). GA4 itself is free.

**PostHog — official, but the repo you'd find first is archived; the live one is inside PostHog's monorepo.**
- `PostHog/mcp` was **archived 2026-01-19**; the maintained version now lives inside PostHog's monorepo (`services/mcp`). Install via `npx @posthog/wizard@latest mcp add`, or manually via the remote `https://mcp.posthog.com/mcp` with a personal API key.
- Needs a PostHog account + API key. PostHog's free tier is generous for product analytics (event-based) — a good fit once a venture is a real product with user behavior worth instrumenting, not for a static marketing page.
- **Flag per instructions:** the top-hit repo for "PostHog MCP" is technically dead (archived) even though the product is actively maintained elsewhere — anyone following the first GitHub link they find would hit a stale pointer.
- Sources: [PostHog/mcp GitHub](https://github.com/PostHog/mcp) (fetched 2026-08-01, confirmed archived).

**Plausible — MCP tooling is fine, but the underlying product costs money for SMM's use case.**
- Multiple community MCPs exist (`AVIMBU/plausible-mcp-server` MIT, `getsentry/plausible-mcp` — notably maintained by Sentry, `ickas/plausible-mcp`).
- **Flag:** Plausible-the-service is a paid SaaS (from ~$9/mo after trial) unless self-hosted, and self-hosting means running your own server — a real cost/ops burden GH Pages-only SMM doesn't currently have. The MCP itself is free, but pointless without a paid or self-hosted Plausible instance.
- **My read:** for a near-zero-budget setup, GA4 (free, official-ish) or GSC (free, no official MCP but several maintained community ones) beat Plausible on pure cost grounds, even though Plausible is nicer/more privacy-friendly as a product.

---

## 8. Anything else genuinely useful

**Image/asset generation MCPs — they exist, but confirm rather than need.** Multiple community options: `mcpimg` (`npx -y mcpimg`, routes to OpenRouter/Together AI/Replicate/fal.ai), Replicate's own MCP, `tadasant/mcp-server-stability-ai` (Stability AI), `shinpr/mcp-image` (Gemini/OpenAI/BytePlus). Every single one is a thin wrapper that still needs a paid third-party image-generation API key (Replicate, Stability, OpenAI, Google) — none offer a perpetually-free generation tier at any real volume. **This confirms prediction #9**: there's no reason to install a dedicated image-gen MCP right now, since it would just add a paid dependency for a need that isn't currently blocking any venture (branding/mockups can be done in plain HTML/CSS/SVG per the existing `artifact-design` workflow).

**The already-active reference servers (Filesystem, Git, Fetch, Memory, Sequential Thinking, Time) in `modelcontextprotocol/servers`** are genuinely maintained (4,158 commits, dual MIT/Apache-2.0) but largely duplicate capabilities Claude Code already grants natively (file read/write, git, WebFetch). Not worth adding.

**Google Drive MCP** is already connected per CLAUDE.md — no action needed, just a reminder it's there for anything Simon wants to review outside the repo.

---

## Predictions: confirmed / refuted / refined

1. **"A low-friction payment connector will be needed once any venture charges money."** — **Confirmed.** Stripe's official MCP (`stripe/agent-toolkit`, MIT, active) is exactly this, and it's genuinely low-friction to install (`npx -y @stripe/mcp ...`). The friction is entirely on the Chairman's side (real Stripe account/KYC), not the tooling side.

2. **"A browser-automation MCP will be the single highest-leverage addition."** — **Confirmed, and it's the clearest "yes" of this whole research pass.** Playwright MCP (Apache-2.0, 35.7k stars, actively maintained by Microsoft, zero account needed, one-line npx install) is free with no blocking prerequisites, unlike almost everything else in this report which needs an account, has a legal caveat, or duplicates an existing capability. It should be installed immediately.

3. **"Free distribution will lean on direct social-platform APIs more than any generic 'marketing MCP.'"** — **Refuted/refined, and this is the biggest surprise of the research.** Direct-API access to the two platforms with the most reach — **X (no free tier for new developers since Feb 2026) and Reddit (free tier is ToS-restricted to non-commercial use)** — is no longer viable for a free, legal, revenue-generating venture. The one platform where direct-API access is still genuinely free and unrestricted is **Bluesky** (smaller reach). Meanwhile a real first-party "generic marketing MCP," **Buffer**, launched in March 2026 and is a legitimate (if thin-free-tier) alternative. Net: distribution in 2026 is more constrained than the prediction assumed — it's not "direct APIs win," it's "most direct APIs got closed off or restricted, leaving Bluesky + Buffer + (implicitly) manual/human posting as the realistic free, compliant options."

4. **"A GitHub MCP server will matter less than expected since plain git/gh already covers most needs."** — **Confirmed.** The official server (`github/github-mcp-server`) adds real but narrow value (Actions status, repo settings, issue management) that plain `git` can't do without `gh` (which isn't installed per CLAUDE.md) — but nothing in SMM's current workflow is actually blocked on this yet. Low priority, and it's Docker/remote-based, not the simple npx one-liner many tutorials imply (that npx form belongs to the archived old package).

5. **"An email-sending connector will be needed for newsletter/lead-capture ventures."** — **Confirmed.** Resend's official MCP (MIT, active, 3,000 free emails/month) is a clean fit and the best of the three email options checked (Mailgun's free terms are murkier, SendGrid's free tier is reportedly gone).

7. **"A real database/backend is only needed once a venture becomes an actual SaaS tool, not for early static-site ventures."** — **Confirmed, with a twist the prediction didn't anticipate.** Even if an early venture *wanted* a lightweight local DB, the obvious lightweight option (official SQLite MCP) is dead — archived since May 2025. So the practical path for SMM is exactly what the prediction says: stay static/no-DB early, and reach for Supabase (Apache-2.0, active, free tier, real Postgres) only once a venture genuinely needs persistent multi-user data.

8. **"SEO/analytics tooling matters more for measuring what's working than for driving initial traffic."** — **Confirmed.** Nothing found in GSC/GA4/PostHog/Plausible drives traffic by itself — they're all measurement layers. Note PostHog specifically is a *product*-analytics tool (event/behavior tracking) suited to an actual SaaS tool, while GSC/GA4 are the right fit for measuring a marketing site's traffic — both are "measure," not "drive."

9. **"Image/asset generation won't need a dedicated MCP."** — **Confirmed.** Plenty exist, but every one just wraps a paid third-party generation API with no meaningfully free tier — installing one now would add cost/complexity for a need that isn't currently blocking anything.

---

## Ranked shortlist

### Install now — genuinely free, no blocking third-party account needed
1. **Playwright MCP** — `npx @playwright/mcp@latest` — Apache-2.0, Microsoft-maintained, zero account. Highest leverage of everything reviewed: competitor research, scraping, and QA-verifying built sites before calling anything shipped.
2. **Bluesky MCP** (`morinokami/mcp-server-bluesky`) — `npx -y mcp-server-bluesky` — needs only a normal Bluesky account + app password (Chairman creates the account, but it's free and instant, no developer-portal approval). The one clean, ToS-compliant, free social-distribution channel found.
3. *(Conditional — install once Simon has a GSC-verified property, which itself is free)* **Google Search Console MCP** (`ahonn/mcp-server-gsc`) — `npx -y mcp-server-gsc` — free product, community-maintained connector, useful the moment there's a live venture site to measure.

### Needs an account/credentials from the Chairman first (tooling itself is free; the account isn't a blocker, just a prerequisite)
- **Stripe MCP** (`npx -y @stripe/mcp --tools=all --api-key=...`) — set up when the first venture is ready to actually charge money. Requires real Stripe account/KYC (Chairman-only per CLAUDE.md role split).
- **Resend MCP** (`npx -y resend-mcp`) — set up when the first newsletter/lead-capture funnel is built. Free tier (3,000 emails/mo) should cover early ventures comfortably; needs a Resend account + API key.
- **Supabase MCP** (`npx -y @supabase/mcp-server-supabase@latest --access-token ...`) — hold until a venture genuinely needs persistent multi-user data (i.e., becomes a real SaaS tool, per prediction #7). Free tier (2 projects) is enough to start.
- **GitHub MCP server** (Docker or hosted remote, no npx) — low priority; only worth setting up once Actions/issue automation is actually needed, since plain `git` covers today's needs.

### Not worth it right now
- **Netlify / Vercel / Cloudflare Pages MCPs** — redundant with the working GitHub Pages baseline; revisit only if a venture needs serverless/SSR that static hosting can't provide.
- **SQLite / Postgres official MCPs** — both archived/dead; use Supabase instead when a real DB is needed.
- **Puppeteer MCP** — officially deprecated in favor of Playwright.
- **X (Twitter) MCP** — X killed its free developer tier for new signups in Feb 2026; now pay-per-use from the first call. Not free.
- **Reddit MCP** — free tier is explicitly non-commercial-use-only under Reddit's ToS; commercial tier starts at ~$12k/month. **Legal flag, not just a cost flag** — using the free tier to market a revenue venture would violate Reddit's terms.
- **LinkedIn MCP** — unofficial, works by scraping via your own logged-in session cookies, explicit account-ban risk, not endorsed by LinkedIn. **Legal/ToS flag** — do not automate.
- **Lemon Squeezy / Gumroad MCPs** — unofficial, thin wrappers; use the platforms directly via their web UI instead, and only add the MCP if a specific automation task needs it.
- **Mailgun / SendGrid MCPs** — Resend is simply the better default (official + generous free tier); SendGrid reportedly dropped its free tier entirely in 2026.
- **Plausible MCP** — fine tooling, but Plausible-the-product costs money (or requires self-hosting) for SMM's budget; GA4/GSC are the free alternatives.
- **PostHog MCP** — legitimate and free-tier-friendly, but only relevant once there's a real product with user behavior to instrument — premature for a static-site-stage venture. Note the top GitHub hit for it is archived; the live version is in PostHog's monorepo.
- **Image-generation MCPs** — all wrap paid APIs; no action needed until a venture specifically needs generated imagery beyond what plain HTML/CSS/SVG covers.
- **Buffer MCP** — real and official, but its free tier (3 channels/10 scheduled posts each) is too thin to be a primary channel; worth knowing about, not worth prioritizing over Bluesky today.

---

## Chairman TODO items surfaced by this research

- [ ] **Legal/ToS flag (not a request, a warning):** do not use Reddit's free API tier or the unofficial LinkedIn MCP for any marketing activity tied to a revenue-generating venture — both cross into ToS-restricted/account-risk territory. If Reddit or LinkedIn distribution is wanted, it should be manual, human-operated, individual-account activity under each platform's normal (non-API, non-commercial-tier) terms — a question for `strategist`, not resolved by tooling choice.
- [ ] When the first venture is ready to charge money: Chairman needs to create a Stripe account (KYC/bank details) — Stripe's MCP is ready to connect the moment that account exists.
- [ ] When the first newsletter/lead-capture funnel is built: Chairman needs to create a Resend account (free tier, no cost) for the email-sending MCP.
- [ ] When/if a venture needs persistent backend data: Chairman needs to create a Supabase account (free tier, no cost).
- [ ] For Bluesky-based distribution: Chairman needs to create a Bluesky account + app password (free, instant, no approval process) — the lowest-friction distribution channel found in this research.
