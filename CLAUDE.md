# Simon Money Maker (SMM) — Operating Charter

SMM is an internal holding-company codename. It is not customer-facing — every real venture built under it gets its own separate brand and identity. This file is the persistent charter for whichever Claude Code session is working in this repo: read it fully at the start of every session before doing anything else. It is meant to be updated, not just read — see **Progress & Assignments Log** below.

Full original founding brief: [docs/founding-brief.md](docs/founding-brief.md). Forward-looking kickoff/execution prompt: [MASTERPROMPT.md](MASTERPROMPT.md).

## Mission

Legally and ethically generate real-world revenue through automated digital products, services, and small SaaS tools, starting August 2026 — on a near-zero budget beyond the Chairman's existing Claude subscription. Multiple ventures run in parallel, at different risk/effort levels, to maximize the odds that something actually makes money.

## Roles

- **Chairman — Simon.** Owns accounts, payments, legal identity, and final approval on anything costing money or requiring a human signature (bank/payment processor signup, domain purchases, ToS acceptance). Does not do research, strategy, coding, or marketing execution — that's delegated entirely.
- **CEO — the Claude Code session working in this repo.** Owns 100% of research, strategy, venture selection, product design, coding, deployment, and marketing copy. Operates through the department sub-agents below rather than doing everything inline.

## Constraints (non-negotiable)

1. **Legal only** — no exceptions.
2. **Near-zero budget** — free tiers, open-source, free hosting (GitHub Pages by default) unless a cost is explicitly approved by the Chairman.
3. **Extreme automation** — default to dispatching specialized sub-agents over doing serial work inline.
4. **Don't reinvent the wheel** — reuse proven monetization/marketing playbooks; adapt, don't invent from scratch.
5. **Currency** — any "best ways to make money with AI" research must reflect the real July/August 2026 landscape, gathered live, not assumed from training knowledge.

## Departments (real, dispatchable sub-agents)

The "virtual office" is not just a metaphor — each department is a real Claude Code custom sub-agent defined in `.claude/agents/`, with its own scoped toolset. Dispatch them via the `Agent` tool with `subagent_type` set to the agent name:

| Department | Agent name | Does |
|---|---|---|
| Research | `researcher` | Market/competitor/monetization-method/tooling research, written up in `research/` |
| Strategy | `strategist` | Turns research into venture decisions, prioritization, business plans |
| Product Design | `product-designer` | Product spec, naming, UX, visual identity, dashboards |
| Web Development | `web-developer` | Builds and ships actual sites/tools/products |
| Marketing | `marketer` | Copy, positioning, distribution, launch |

Typical flow for a new venture: `researcher` → `strategist` (go/no-go + spec direction) → `product-designer` (concrete spec) → `web-developer` (build) → `marketer` (launch). Each venture's output lives under `companies/<venture-name>/`. Run independent department tasks in parallel (single message, multiple `Agent` calls) rather than serially when they don't depend on each other.

Simon's original brief mentioned using "`ultracode` settings" for heavy build tasks — there's no literal setting by that name in this Claude Code version. The real equivalents: run heavy/parallel build work through background `Agent` dispatches (so multiple departments work simultaneously without blocking the interactive session), and reserve the higher-effort model tier for genuinely hard design/strategy calls, not routine execution.

## The Office Visualizer

**There are two versions now — both current, different jobs:**

1. **[SMM_Office_Visualizer/office.html](SMM_Office_Visualizer/office.html)** — static, zero-dependency, offline fallback. Open directly in a browser or via VS Code's Live Server. Hand-edited; update its placeholder data whenever department status materially changes so it doesn't silently drift.
2. **[SMM_Office_Visualizer/office-live.html](SMM_Office_Visualizer/office-live.html)** — the real, live, interactive one Simon asked for (2026-08-01): a clickable floor-plan of department "rooms" that expand in place to show current task / last completed / notes, a Chairman's Office room with live TODO + Help-needed panels, and a Time Ruler corridor. Published as a Claude **Artifact** with the `mcp` capability (`artifact-capabilities` skill), polling live data from Google Drive every 30s. **Published URL:** https://claude.ai/code/artifact/194dacb6-ea2c-4c41-bde1-bd75db46bb53 — this is what Simon should actually keep open, not the static file.

Both contain the sections the Chairman requires, and the *real* company structure must match them: department status, current task + last completed, Chairman's TODO + a distinct HELP-needed panel, Time Ruler.

### How the live version actually gets its data

There's no MCP tool to edit a Google Drive file in place — only `create_file` (new file), not update. So "pushing a status update" means:

1. Create a **new** Drive file, always titled exactly `SMM-Office-Live-Status` (`mcp__claude_ai_Google_Drive__create_file`, `contentMimeType: "application/json"`, `disableConversionToGoogleType: true`), containing the full status JSON (see the shape already in Drive — departments array with `id`/`name`/`agent`/`room`/`status`/`current_task`/`last_completed`/`notes`, plus `chairman_todo`, `help_needed`, `legal_flags`, `time_ruler`, `phase`).
2. The published artifact finds the latest one itself (`search_files` by title, sorted by `modifiedTime` client-side, then `download_file_content` on the newest match) — so you never need to tell it anything, just create the new file.
3. Old status files aren't cleaned up (no delete tool available) — they'll accumulate in Drive over time. Harmless, but Simon can manually clear old ones from Drive if it bothers him.
4. **Update the status file as part of any work that changes department state** — same discipline as the static file, just via `create_file` instead of an edit.

Only `search_files` and `download_file_content` are in the published manifest — if a future update needs a different Drive tool live in the page, the artifact has to be republished with an updated `capabilities` manifest first.

## MCP & Tooling Guide

Verified 2026-08-01 by live research (`researcher`/general-purpose department agents) — full detail and sourcing in [research/mcp-connectors-2026-08-01.md](research/mcp-connectors-2026-08-01.md) and [research/claude-skills-plugins-2026-08-01.md](research/claude-skills-plugins-2026-08-01.md). Don't trust an MCP/skill claim you find elsewhere (a blog post, an old tutorial) without checking against these — the landscape moved a lot in early 2026 (see legal flags below).

**Available right now, already working:**
- **Agent tool** — dispatch the department sub-agents in `.claude/agents/`. **Note:** these didn't register in the session that first created them — Claude Code only watches `.claude/agents/` directories that existed when the session started. **A session restart (or a fresh `claude` session opened in this folder) is required once** before they show up.
- **WebSearch / WebFetch** — backbone of the Research department.
- **Artifact tool** (+ `artifact-design`, `artifact-capabilities` skills) — confirmed capable of a live, click-through, connector-backed dashboard (see Office Visualizer section).
- **`dataviz` skill** — load before designing any chart, KPI tile, or dashboard.
- **`run` skill** — launch and verify a built site/app actually works before calling it shipped.
- **`review` / `security-review` skills** — run before shipping anything customer-facing.
- **`claude-api` skill** — load before building any feature that itself calls the Claude/Anthropic API.
- **Google Drive MCP** (`mcp__claude_ai_Google_Drive__*`) — already connected.
- **`update-config` / `fewer-permission-prompts` skills** — Claude Code hygiene.
- **gh / git** — `gh` CLI is *not* installed on this machine; plain `git` is (`origin` → `simonhapp-ai/SMM-Project`).
- **Playwright MCP** — added 2026-08-01 (`.mcp.json`, project scope, `npx @playwright/mcp@latest`, Apache-2.0, Microsoft-maintained, zero account needed). Highest-leverage pick from the research: competitor research, scraping, automated QA screenshots. **Shows "⏸ Pending approval" until someone runs a fresh `claude` session in this project and approves it once** — same restart requirement as the custom agents above.
- **Skill plugins installed** (user scope, so available to Simon across all his Claude Code projects, not just this one): `document-skills@anthropic-agent-skills` and `example-skills@anthropic-agent-skills` (Anthropic's own PDF/DOCX/PPTX/XLSX generation — source-available, not open-source, internal use only) and `marketing-skills@marketingskills` (Corey Haines' 49-skill marketing pack — CRO, copywriting, SEO, pricing, launch checklists, growth; MIT, 42.5k★, actively maintained). Two marketplaces now registered for future discovery: `claude-community` (Anthropic-vetted third-party plugins) and `anthropic-agent-skills`.

**Ready to add the moment the Chairman has the account (commands below are pre-verified, not speculative):**
- **Stripe MCP** — `claude mcp add --scope project --transport http stripe https://mcp.stripe.com/` (or `npx -y @stripe/mcp --tools=all --api-key=...` locally). Needs a real Stripe account (KYC/bank details) — Chairman-only. Install the moment a venture is ready to charge money.
- **Resend MCP** (email) — `claude mcp add --scope project resend -e RESEND_API_KEY=... -- npx -y resend-mcp`. Free tier: 3,000 emails/month. Needs a Resend account + API key.
- **Supabase MCP** (backend/DB) — `claude mcp add --scope project supabase -- npx -y @supabase/mcp-server-supabase@latest --access-token <token>`. Free tier: 2 projects. Only needed once a venture becomes a real SaaS tool, not for static-site ventures.
- **Bluesky MCP** (social distribution) — `claude mcp add --scope project bluesky -e BLUESKY_USERNAME=... -e BLUESKY_PASSWORD=... -- npx -y mcp-server-bluesky`. Free, instant account, no developer-portal approval. **The one genuinely free, ToS-compliant, API-based distribution channel found** — see legal flags below on why X/Reddit aren't viable alternatives.
- **Google Search Console MCP** — `claude mcp add --scope project gsc -- npx -y mcp-server-gsc`. Free, once Simon has a verified GSC property for a live venture site.

**Explicitly not worth adding (verified dead, redundant, or not free) — don't reach for these even if a tutorial mentions them:**
- SQLite/Postgres official MCPs and Puppeteer MCP — all archived/deprecated (superseded by Supabase and Playwright respectively).
- Netlify/Vercel/Cloudflare Pages MCPs — redundant while GitHub Pages (free, already working) covers every venture's hosting needs.
- Plausible, SendGrid MCPs — cost money or lost their free tier; GA4/GSC and Resend are the free equivalents.
- GitHub official MCP — real and maintained, but Docker/remote-only (no npx), and nothing in SMM's workflow is blocked on it yet (plain git covers today's needs). Revisit only once Actions/issue automation is actually needed.

**Legal/ToS flags (binding on the Marketing department, not just tooling notes):**
- **X (Twitter)** killed its free developer API tier for new accounts in Feb 2026 (pay-per-use from the first call) — not a free channel anymore.
- **Reddit's** free API tier is explicitly non-commercial-use-only under its ToS; using it to market a revenue-generating venture would be a ToS violation (commercial tier starts ~$12k/month). Manual, human, individual-account Reddit posting under normal site ToS is a separate question for `strategist`/Chairman, not resolved by tooling.
- **Unofficial LinkedIn MCPs** scrape via a logged-in session cookie — real account-ban risk, not endorsed by LinkedIn. Do not automate.
- Generated privacy-policy/ToS output (from any legal-boilerplate skill) is a first draft only, never compliance cover — sanity-check against real jurisdictions/platforms before shipping to a real venture.

Don't add anything above speculatively beyond what's already installed — only when a specific piece of work is blocked without it, per the existing policy.

## The Time Ruler (token/context budget management)

This is the real mechanism behind Simon's requested "button/switch and time ruler so my tokens reset when I work on other projects":

- **`/schedule` skill (backed by `CronCreate`/`CronList`/`CronDelete`)** — schedule a department agent to run on a cron, independent of Simon's interactive session. This is the actual "switch": SMM work can run on its own schedule without consuming the context/tokens of whatever else Simon is doing.
- **`/loop` skill (+ `ScheduleWakeup`)** — for a self-paced work stretch within one thread (e.g. "keep working through the venture backlog, checking back every 30 min").
- **Session checkpoint convention** — every work session ends with a dated entry in the Progress Log below *before* stopping, so a brand-new session (fresh context) can resume instantly by reading this file, without re-deriving anything.

The Office Visualizer's Time Ruler panel is a static visual mirror of this — it shows the current recommended stopping point but isn't wired to live token counts (a static HTML file can't read that). Treat the actual context-usage indicator in the Claude Code UI as the source of truth.

## Chairman TODO / Approvals Needed

- [ ] **Restart Claude Code once** (close and reopen, or start a fresh `claude` session) in this project folder — required for the 5 department sub-agents and the new Playwright MCP server to activate. When prompted, approve the `playwright` MCP server.
- [ ] Create a Stripe account (KYC/bank details) — only needed once the first venture is ready to charge money. MCP command is pre-verified and ready in CLAUDE.md's Tooling Guide.
- [ ] Create a Resend account (free) — only needed once the first venture needs a newsletter/lead-capture funnel.
- [ ] Create a Bluesky account + app password (free, instant) — the best free/legal distribution channel found; needed whenever Marketing wants to start posting.
- [ ] Create a Supabase account (free) — only once a venture needs a real backend/database, not for early static-site ventures.
- [ ] **Noted, not an action item:** do not ask Marketing to use Reddit's free API or an unofficial LinkedIn scraper for any revenue venture — both are ToS/legal risks. See Tooling Guide.

## Progress & Assignments Log

Newest entry first. Every real work session appends here — this is the "real assignments and progress," not a verbatim copy of the founding brief.

### 2026-08-01 — Live Office Visualizer
- Simon asked for the Office to be genuinely live and interactive: clickable agents, expandable detail, real-time-ish updates — not a hand-edited static file.
- Built `SMM_Office_Visualizer/office-live.html`: a floor-plan-style dashboard, published as a Claude Artifact with the `mcp` capability, polling a status JSON from Google Drive (`search_files` + `download_file_content`, 30s refresh floor) via `window.claude.mcp.watchTool`. Verified rendering (loading/degraded/live/expanded states, light+dark) via headless-browser screenshots with mocked data before publishing, since the live MCP path only works inside the actual claude.ai artifact runtime.
- Created the live data source itself: a `SMM-Office-Live-Status` JSON file in Google Drive (no update-in-place tool exists for Drive files, so "pushing an update" = creating a new same-titled file each time — see Office Visualizer section for the exact mechanism).
- Published at https://claude.ai/code/artifact/194dacb6-ea2c-4c41-bde1-bd75db46bb53.
- Kept the static `office.html` as an offline fallback rather than replacing it.

### 2026-08-01 — Tooling research + installation pass
- Dispatched parallel research on (a) free/open-source MCP connectors and (b) the Claude Code Skills/plugin ecosystem — see `research/mcp-connectors-2026-08-01.md` and `research/claude-skills-plugins-2026-08-01.md` for full sourced detail.
- Discovered the custom `.claude/agents/` sub-agents didn't register in the already-running session (directory watcher only covers dirs that existed at session start) — confirmed via claude-code-guide, added the restart requirement to the Chairman TODO.
- Installed **Playwright MCP** (project-scoped, `.mcp.json`) — pending one-time approval on next session start.
- Installed 3 skill plugins (user-scoped): `document-skills`, `example-skills` (both Anthropic official), `marketing-skills` (Corey Haines, 49 skills, MIT). Registered 2 marketplaces (`claude-community`, `anthropic-agent-skills`) for future discovery.
- Surfaced real legal/ToS flags for marketing distribution (X's free API tier gone, Reddit's free tier is non-commercial-only, unofficial LinkedIn MCPs carry ban risk) — see Tooling Guide.
- CLAUDE.md's Tooling Guide and Chairman TODO rewritten to reflect verified (not speculative) findings.
- User requested the Office Visualizer become a genuinely live, interactive, click-through dashboard (not a hand-edited static file) — next up, see Progress Log's next entry / MASTERPROMPT.md.

### 2026-08-01 — Foundation build
- Cloned `simonhapp-ai/SMM-Project` (was an empty placeholder repo) into this workspace.
- Wrote this charter, `MASTERPROMPT.md`, and `docs/founding-brief.md`.
- Created the five department sub-agents in `.claude/agents/`.
- Built the first working `SMM_Office_Visualizer/office.html` with placeholder data reflecting this session's own work.
- Created `research/` and `companies/` as landing zones for Phase 2+ output.
- **Not yet started:** live market research (Phase 2), venture selection (Phase 3), any actual building/marketing for a named venture. Next session should open with `MASTERPROMPT.md`'s Phase 2.

## Legal/Ethical guardrail

If any proposed venture, growth tactic, or piece of copy is borderline on legality, platform ToS, or honesty (fake reviews, dark patterns, scraping in violation of ToS, spam, etc.), stop and flag it in the Chairman TODO list instead of proceeding. "No rules aside from legality" cuts both ways — legality is a hard floor, not a target to graze.
