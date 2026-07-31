# Claude Code Skills & Plugin Ecosystem — Live Research

**Date:** 2026-08-01 (all sources fetched live today unless otherwise noted)
**Author:** Research department (researcher sub-agent)
**Question:** What real, currently-available open-source Claude Code Skills and plugins could help SMM (web dev/GitHub Pages, SEO/copywriting, e-commerce, social content, market research, legal boilerplate, PDF/doc gen, image gen)? How does the plugin/marketplace system actually work? Is a live, click-through "office" dashboard actually buildable with Artifacts?

Method note: everything below comes from live WebSearch/WebFetch calls made today, including direct GitHub API pulls for star counts, license, and `pushed_at` timestamps where noted. Secondary blog sources are flagged as such — where they conflicted with Anthropic's own docs, the official docs win and the conflict is noted.

---

## 1. Curated "awesome" lists

Several competing lists exist; none is a single canonical source. As of today:

| List | What it is | Scale (as fetched today) |
|---|---|---|
| [anthropics/skills](https://github.com/anthropics/skills) | **Anthropic's own official repo** — not community, this is the source | 165k★, 46 commits, last push `2026-07-24` |
| [subinium/awesome-claude-code](https://github.com/subinium/awesome-claude-code) | Curated tools/skills/plugins/MCP list | community-curated index |
| [BehiSecc/awesome-claude-skills](https://github.com/BehiSecc/awesome-claude-skills) | Skills-only curated list | ~13k★ per search result |
| [karanb192/awesome-claude-skills](https://github.com/karanb192/awesome-claude-skills) | "50+ verified" skills, cross-tool (Claude Code, Claude.ai, API) | actively maintained per listing |
| [Chat2AnyLLM/awesome-claude-skills](https://chat2anyllm.github.io/awesome-claude-skills/) | Auto-generated aggregator site | tracks **2,619 enabled source repos, 112,861 discoverable skills, 2,544 "healthy" repos** as of `2026-07-31` |
| [jeremylongshore/claude-code-plugins-plus-skills](https://github.com/jeremylongshore/claude-code-plugins-plus-skills) | Large single-repo marketplace + `ccpi` CLI, backing [tonsofskills.com](https://tonsofskills.com) | **471 plugins, 3,069 skills, 347 agents** |

Read on this: the ecosystem is large and fragmented — thousands of nominal "skills" exist, but quality/maintenance varies enormously (the aggregator itself flags only ~2,544 of 2,619 tracked repos as "healthy"). Curated single-purpose repos from named individuals (Corey Haines' marketing repo, Anthropic's own repo) were consistently more useful and better-maintained than mega-aggregators in this research.

Sources: [jqueryscript/awesome-claude-code](https://github.com/jqueryscript/awesome-claude-code), [Chat2AnyLLM aggregator](https://chat2anyllm.github.io/awesome-claude-skills/), [karanb192/awesome-claude-skills](https://github.com/karanb192/awesome-claude-skills), [jeremylongshore/claude-code-plugins-plus-skills](https://github.com/jeremylongshore/claude-code-plugins-plus-skills) (all fetched 2026-08-01).

---

## 2. Anthropic's official skills/plugin system — confirmed from official docs

Fetched directly from **code.claude.com/docs/en/discover-plugins** and **code.claude.com/docs/en/plugin-marketplaces** (both 2026-08-01) — this is primary-source, not a blog summary.

### How it actually works

- Claude Code **auto-adds** the official marketplace (`claude-plugins-official`) on startup. If it can't (network-blocked), add manually:
  ```
  /plugin marketplace add anthropics/claude-plugins-official
  ```
- Install a plugin from it:
  ```
  /plugin install <name>@claude-plugins-official
  ```
  e.g. `/plugin install github@claude-plugins-official`
- Browse interactively: `/plugin` → **Discover** tab. Also viewable at [claude.com/plugins](https://claude.com/plugins).
- **`/plugin marketplace add`** accepts four source types:
  - GitHub shorthand: `owner/repo` (must contain `.claude-plugin/marketplace.json`)
  - Any git URL (GitLab/Bitbucket/self-hosted): `https://gitlab.com/company/plugins.git`
  - Local path: `./my-marketplace` or a direct `marketplace.json` path
  - Remote URL to a hosted `marketplace.json`
- After install, run `/reload-plugins` to activate without restarting.
- Manage: `/plugin list`, `/plugin disable name@marketplace`, `/plugin marketplace update <name>`, `/plugin marketplace remove <name>`.
- **Second tier — "Community marketplace":** `anthropics/claude-plugins-community` — third-party plugins that passed Anthropic's *automated validation and safety screening* (each pinned to a commit SHA), added manually:
  ```
  /plugin marketplace add anthropics/claude-plugins-community
  /plugin install <plugin-name>@claude-community
  ```
  Confirmed live via GitHub API today: 331★, Apache-2.0, last push `2026-07-31T19:31:47Z` (i.e., updated the same day as this research) — genuinely active. Its own description: *"Read-only mirror — submit plugins at clau.de/plugin-directory-submission."*
- **Demo marketplace** (`anthropics/claude-code` repo) — example plugins showing what's possible, marketplace name `claude-code-plugins`, e.g. `commit-commands@claude-code-plugins`.
- Security warning straight from the docs: *"Plugins and marketplaces are highly trusted components that can execute arbitrary code on your machine with your user privileges. Only install plugins and add marketplaces from sources you trust."*

**Important for this project:** these `/plugin` commands only work inside an interactive Claude Code terminal session (they're slash commands, not tools this research sub-agent has access to). Simon or the CEO session needs to run them directly — I cannot execute installs from here.

### Anthropic's own skills repo

[anthropics/skills](https://github.com/anthropics/skills) — the **official** reference implementation, not third-party. Confirmed via GitHub API today: **165,466★**, last push `2026-07-24T20:12:36Z`, created `2025-09-22`. Contains:
- `skills/docx`, `skills/pdf`, `skills/pptx`, `skills/xlsx` — document generation/editing (source-available, **not open-source** — reference-only per repo's own README/THIRD_PARTY_NOTICES)
- Example/creative/dev skills — Apache 2.0
- A `template/` for writing your own skills, and the formal Agent Skills `spec/`

Install:
```
/plugin marketplace add anthropics/skills
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```
(Marketplace/plugin names come straight from the repo's own README as fetched today; confirm exact names with `/plugin` → Discover if they've drifted, since Anthropic renames things without notice.)

### Independent (non-Anthropic) marketplace directories

Distinct from the official/community tiers above — these are third-party web directories layered on top of the same `/plugin` mechanism:
- **[aitmpl.com/plugins](https://www.aitmpl.com/plugins/)** — open-source catalog+CLI (`davila7/claude-code-templates`), indexes **425 plugins, 2,810 skills, 200 agents**.
- **[claudepluginhub.com](https://www.claudepluginhub.com/)** — independent browse/rate/submit directory, not Anthropic-affiliated.
- **[claude-plugins.dev](https://claude-plugins.dev/)** — CLI-based community registry.
- **[tonsofskills.com](https://tonsofskills.com)** — backs the `jeremylongshore` mega-repo above, has its own `ccpi` package-manager CLI.

Read on this: treat these as browsing/discovery front-ends, not trust signals — actual vetting only happens at the Anthropic official/community tier (automated safety screening) or by reading a specific repo's code yourself.

---

## 3. Community skills by category

### Web development / GitHub Pages deployment
- [hoyoboy0726123/claude-skill-github-pages-deployer](https://github.com/hoyoboy0726123/claude-skill-github-pages-deployer) — "git init to live URL, zero manual steps," MIT. Confirmed via API: only **2★**, last push `2026-02-26` (5+ months stale as of today). Thin adoption — treat as unproven, not a priority install.
- [SpillwaveSolutions/publishing-astro-websites-agentic-skill](https://github.com/spillwavesolutions/publishing-astro-websites-agentic-skill) — Astro-framework-specific static site skill (SSG, MDX, Firebase/Netlify/Vercel deploy). Not directly relevant — SMM's charter defaults to **plain HTML/CSS/JS**, no build tooling, so an Astro-specific skill adds a dependency SMM doesn't want.
- [wilwaldon/lets-go](https://github.com/wilwaldon/lets-go) — static site generator skill, explicitly "no build tools, no npm, no frameworks — just clean HTML, CSS, and vanilla JS." Philosophically the closest match to SMM's own constraint, worth a look but not deeply vetted here.

**Read on this category:** genuinely thin. No dominant, well-adopted GitHub Pages skill exists yet. This is lower-value to install — SMM's `web-developer` agent + plain `git push` already covers this need at zero cost, and the one purpose-built skill found has almost no adoption.

### SEO / marketing copywriting / landing pages
- **[coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills)** — by far the strongest find. 70+ skills: `seo-audit`, `ai-seo`, `programmatic-seo`, `copywriting`, `copy-editing`, `cro`, `signup`, `onboarding`, `popups`, `paywalls`, `launch` (product launches/announcements), `marketing-ideas` (140 SaaS marketing ideas), `pricing`, `referrals`, `social`, `ads`, `ab-testing`, `analytics`. Confirmed via GitHub API today: **42,546★**, MIT, last push `2026-07-29T05:41:15Z` (2 days old) — actively maintained. Install:
  ```
  npx skills add coreyhaines31/marketingskills
  ```
  or as a plugin: `/plugin install marketing-skills`, or manual: clone and `cp -r marketingskills/skills/* .agents/skills/`.
- [borghei/Claude-Skills](https://github.com/borghei/Claude-Skills) — landing-page-generator skill (page structures by product type, copywriting frameworks, conversion patterns), plus SEO, legal, e-commerce, PDF, and image-gen skills all in one mega-repo (368 skills / 20 domains). Confirmed via API: 439★, license **not standard MIT** — repo's own README states **"MIT + Commons Clause"** (GitHub API returns `NOASSERTION`, consistent with a non-standard license). Last push `2026-07-21`.
  **Legal flag:** Commons Clause restricts *selling* the software itself or offering it as a paid service — it does not restrict using it internally to produce SMM's own separate commercial output, but confirm this reading before redistributing the skill itself as part of any SMM product. Given `coreyhaines31/marketingskills` covers most of the same ground under plain MIT, prefer that repo when there's overlap.
- [ishwarjha/claude-marketing-research-skill](https://github.com/ishwarjha/claude-marketing-research-skill) — competitor analysis, avatar profiling, positioning, value props. Apache-2.0, 43★, last push `2026-03-07` (~5 months stale).

### Competitive / market research
- Covered above (`ishwarjha`). Also: [zubair-trabzada/ai-marketing-claude](https://github.com/zubair-trabzada/ai-marketing-claude) — 15 marketing skills incl. competitive intelligence + client-ready PDF reports. Not independently verified via API here.
- [Weizhena/Deep-Research-skills](https://github.com/Weizhena/Deep-Research-skills) — structured two-phase (outline → deep-dive) research workflow skill, cross-tool (Claude Code/OpenCode/Codex).

### E-commerce / payment page setup
- Anthropic's own **`stripe`** MCP server (not a "skill") is the most authoritative option: `claude mcp add --transport http stripe https://mcp.stripe.com/`. This requires a real Stripe account — **out of scope until the Chairman sets one up**, per CLAUDE.md's existing "not yet connected" list.
- [wrsmith108/stripe-mcp-skill](https://github.com/wrsmith108/stripe-mcp-skill) and [IncomeStreamSurfer/claude-code-skills-stripe](https://github.com/IncomeStreamSurfer/claude-code-skills-stripe) — community Stripe-focused skills, wrapping the same underlying need for real credentials.
- [finsilabs/awesome-ecommerce-skills](https://github.com/finsilabs/awesome-ecommerce-skills) — curated list, not independently verified.

**Read on this:** every e-commerce/payment option converges on needing a real Stripe (or similar) account — this is a Chairman-approval item, not a research/tooling gap. Nothing here changes CLAUDE.md's existing stance.

### Social media content generation
- [coreyhaines31/marketingskills`social`](https://github.com/coreyhaines31/marketingskills) skill (see above) — content generation only, no posting.
- [OpenClaudia/openclaudia-skills](https://github.com/OpenClaudia/openclaudia-skills) — 34 open-source marketing skills incl. `social-content`, `thread-writer`, `content-calendar`.
- [stevenflanagan1/social-ai-team](https://github.com/stevenflanagan1/social-ai-team) — brand setup → content calendar → captions → creative → performance review, positioned as "a complete AI social team for SMBs."
- Posting/scheduling skills ([STGime/posta-skill](https://github.com/STGime/posta-skill), [guyaga/claude-code-social-media-skill](https://github.com/guyaga/claude-code-social-media-skill)) all wrap **paid third-party APIs** (Upload Post API, Posta) — not free-tier, so lower priority under SMM's budget constraint unless a free tier is confirmed.

### Legal boilerplate (privacy policy / ToS)
- [zubair-trabzada/ai-legal-claude](https://github.com/zubair-trabzada/ai-legal-claude) — "AI Legal Assistant... Contract review, risk analysis, NDA generation, compliance auditing, negotiation strategy, PDF reports — 14 skills." Confirmed via API: **1,602★**, last push `2026-03-27` (~4 months stale but has real traction), license not machine-readable (`null` — check repo LICENSE file directly before relying on it).
- [kimlawtech/korean-privacy-terms](https://github.com/kimlawtech/korean-privacy-terms) — Korea-specific, not relevant to SMM.
- Multiple `mcpmarket.com`-listed "Privacy Policy Generator" / "Legal Pages Generator" skills claim GDPR/CCPA-aware output by scanning a site's actual data practices.

**Legal flag (per researcher operating rules — flag prominently):** every one of these generates *boilerplate*, not legal advice. A generated privacy policy/ToS from any of these skills should be treated as a first draft only. Given SMM's "100% legal only" constraint and that it will handle real user data/payments eventually, do **not** ship a generated legal page for a real venture without at minimum sanity-checking it against the specific jurisdictions/platforms involved (Stripe's own ToS requirements, GDPR if EU users, etc.) — this is a real compliance risk if treated as done-for-you legal cover.

### PDF / document generation
- **Anthropic's own `skills/pdf`, `skills/docx`, `skills/pptx`, `skills/xlsx`** in [anthropics/skills](https://github.com/anthropics/skills) (see §2) is the most authoritative option — but note: these specific four are **source-available, not open-source/Apache** (per the repo's own licensing split).
- Several marketing mega-repos (borghei, zubair-trabzada) bundle "client-ready PDF report" generation as a sub-feature rather than a standalone skill.

### Image generation / editing
- Claude Code has **no built-in image model** — every option here shells out to a third-party API (Gemini/nano-banana, OpenAI GPT-Image, xAI Grok Image, OpenRouter, fal.ai/FLUX). Examples: [hex/claude-image-generation](https://github.com/hex/claude-image-generation) (multi-provider: Gemini/GPT-Image/Grok, parallel generation), [kkoppenhaver/cc-nano-banana](https://github.com/kkoppenhaver/cc-nano-banana), [AgriciDaniel/banana-claude](https://github.com/AgriciDaniel/banana-claude).
- **All of these need a paid API key** for the underlying image model (Gemini/OpenAI/xAI/OpenRouter) — none are meaningfully free-tier at production quality. This is a real budget gap for SMM if any venture needs real product imagery/marketing visuals beyond stock or CSS/SVG.

---

## 4. Predictions — confirm / refute / refine

### Prediction 6: "At least one community skill already exists for 'landing page copywriting' or a 'SaaS launch checklist' — directly reusable."

**CONFIRMED — and more strongly than the prediction states.** This isn't a thin, one-off match. Found multiple directly reusable, actively-maintained, permissively-licensed options:
- `coreyhaines31/marketingskills` — dedicated `copywriting`, `launch` ("product launches and announcements"), and `marketing-ideas` ("140 SaaS marketing ideas") skills, all MIT, 42.5k★, last commit 2 days before this research.
- `borghei/Claude-Skills` — a dedicated `landing-page-generator` skill with page structures by product type, copywriting frameworks, an audit checklist, and conversion-element patterns (Commons-Clause-encumbered, see legal flag above — prefer the MIT alternative).

Recommendation: install `coreyhaines31/marketingskills` now (see shortlist below); skip the Commons-Clause repo unless a specific skill in it has no MIT equivalent.

### Prediction 10: "The bigger tooling gap for this project is Skills, not MCP servers, because playbooks compound across every venture rather than serving one integration."

**CONFIRMED, and it cross-checks with what's already written in this project's own CLAUDE.md.** Cost/design comparisons found today (from `skywork.ai`, `layer3labs.io`, `totalum.app` — 2026 guides, secondary/opinion sources, flagged as such) converge on: Skills are cheap, portable, reusable "how we do X here" playbooks (~100 tokens until activated); MCP servers are live, per-account, per-credential integrations to *one specific external system* (a typical 5-server MCP setup was cited at ~55,000 tokens of standing context cost before any conversation starts — treat that specific figure as a rough, secondary-sourced estimate, not verified against Anthropic's own docs).

This maps directly onto SMM's actual situation: CLAUDE.md's own "Not yet connected" list (Stripe, Playwright, Notion/Slack, Figma) is *entirely* MCP-shaped — each one is blocked on an external account SMM doesn't have yet, not on a tooling gap Claude Code itself has. Skills, by contrast, are installable **today**, for free, with no new account, and the ones found in §3 (SEO, copywriting, launch checklists, market research, legal boilerplate) apply to *every* future venture, not just one. My read: the prediction is right, and the project's existing MCP posture ("don't add speculatively — only when a specific piece of work is blocked without it") is the correct complementary policy — keep it as-is rather than proactively wiring up MCP servers now.

---

## 5. Live, interactive, click-through dashboard with Claude — is it real?

Yes — but it is a specific, documented feature set (published **Artifacts** with **connector calls**), fetched today directly from Anthropic's own docs: **code.claude.com/docs/en/artifacts** (accessed 2026-08-01). This is the same system already referenced in this project's CLAUDE.md and available in this environment via the `Artifact` tool + `artifact-capabilities` skill — I don't have access to either as a read-only research sub-agent, so treat this section as confirming *what's possible*, not as implementation instructions; the web-developer/product-designer department should load `artifact-capabilities` directly for the live, versioned capability contract before building.

**What's confirmed, from the official doc:**

- **Click-through / detail-view interactions:** Yes, achievable — an artifact is "one self-contained page" with full inline HTML/CSS/JS, so click handlers, expandable panels, modals, sliders, toggles, and drag-and-drop all work (the docs show a literal example: a Kanban-style triage board with draggable cards). What's **not** supported is multi-page navigation / separate routes — a "click agent → detail view" pattern must be built as an in-page state change (show/hide a panel), not a link to a second page. For a single-page "office dashboard" where clicking a department tile expands its live status, this is exactly the right shape and fully supported.
- **Live / real-time data:** Supported via **MCP connector calls from the published page** — added specifically for this use case per the docs ("Publish a status board that pulls fresh data through MCP connectors each time someone opens it"). Mechanics:
  - Requires Claude Code CLI **v2.1.209+** (connector calls specifically; artifacts themselves need v2.1.183+).
  - The page "fetches data when it loads and can refresh on an interval or when a viewer uses a refresh control" — this is **poll/interval-based**, not a push/WebSocket connection (the CSP blocks raw WebSocket/fetch/XHR entirely; connector calls are the sole exception, proxied through claude.ai itself).
  - Each viewer's connector calls run through **their own** claude.ai account's connections, not the publisher's — since this dashboard is for Simon alone, viewing and publishing under one account, this isn't a practical obstacle.
  - **Hard tradeoff:** an artifact that calls connectors **cannot be shared as a public link, on any plan** — it must stay private-to-author (Pro/Max) or org-internal (Team/Enterprise). Since the office dashboard is for Simon's private use, this is a non-issue, not a blocker.
- **Self-updating / republishing:** An artifact does **not** silently self-update its own content — the docs are explicit that connector calls refresh *data* on each view, but the *page* itself only gets new content when Claude re-publishes it (an editor/session runs the update, e.g. "Add a per-region breakdown and republish"). So: live *data* on a static *layout* is real; a fully autonomous self-rewriting page is not.
- **Requirements to use any of this:** Pro/Max/Team/Enterprise plan, signed in via `/login` (not an API key/gateway session), Anthropic API as model provider (not Bedrock/Vertex/Foundry), and — for connector calls specifically — the account needs the relevant connector (e.g. Google Drive, GitHub) actually connected under Settings → Connectors, with a one-time per-viewer approval prompt.

**Practical design implication for the Office Visualizer:** a genuinely "live" version would need some real data source Claude can reach via an MCP connector (e.g., a status JSON file kept in Google Drive or the GitHub repo itself, updated by the CEO session after each department task) — the artifact would then poll that connector on open/refresh rather than Claude hand-editing HTML. That is a real, buildable upgrade path, consistent with what CLAUDE.md already flags as "worth doing once there's enough real activity to justify it" — this research confirms the mechanism exists and roughly what it requires; it does not yet exist for SMM (no status data source outside the hand-edited HTML file today).

**Caveat on secondary sources:** several 2026 blog posts (`digitalapplied.com`, `stacktr.ee`, `markloop.io`, `eigent.ai`) describe an *additional*, distinct feature called "Live Artifacts" inside **Claude Cowork** on the desktop app — MCP-connected, but explicitly **no sharing at all** (personal/device-local only), a different plan/surface combination than the published-Artifacts-with-connectors feature described above. Do not conflate the two: Cowork "Live Artifacts" (no sharing, desktop-only) is a different feature from the published, shareable, connector-backed Artifacts documented at code.claude.com/docs/en/artifacts. The latter is the relevant one for a shareable/persistent office dashboard.

---

## 6. Ranked shortlist

### Install now (high value, low/no cost, directly applicable to every venture)

1. **`coreyhaines31/marketingskills`** — SEO, copywriting, launch checklist, pricing, CRO, social content, 70+ skills, MIT, actively maintained (last push 2 days ago).
   ```
   npx skills add coreyhaines31/marketingskills
   ```
   or via the plugin system: `/plugin install marketing-skills` (run inside an interactive Claude Code session).

2. **Anthropic's own document skills** (`docx`/`pdf`/`pptx`/`xlsx`) — for client-ready reports, invoices, or any PDF SMM ends up generating for a venture.
   ```
   /plugin marketplace add anthropics/skills
   /plugin install document-skills@anthropic-agent-skills
   ```
   (Note: source-available license, not open-source — fine for internal use, don't redistribute the skill itself.)

3. **`anthropics/claude-plugins-community`** — worth adding as a marketplace even without installing anything yet, since it's Anthropic-vetted (automated safety screening, commit-pinned) and updates daily. Cheap to add, gives ongoing safe discovery.
   ```
   /plugin marketplace add anthropics/claude-plugins-community
   ```

### Worth evaluating per-venture (install when a specific venture needs it, not speculatively)

4. **`zubair-trabzada/ai-legal-claude`** — for privacy policy/ToS first drafts. 1,602★, real traction. **Use with the legal flag above: draft only, not a compliance guarantee.**
5. **`ishwarjha/claude-marketing-research-skill`** — structured competitor/positioning research, useful at the strategist/venture-selection stage. Apache-2.0, lighter adoption (43★), 5 months stale — treat as a reasonable but unproven aid, not a replacement for live WebSearch research.
6. A Stripe MCP connector (`claude mcp add --transport http stripe https://mcp.stripe.com/`) — only once the Chairman has an actual Stripe account (existing CLAUDE.md gate, unchanged by this research).

### Lower priority / skip for now

7. GitHub Pages "deployer" skills (`hoyoboy0726123`) — thin adoption (2★), stale, and SMM's plain `git push` + GitHub Pages settings already covers this at zero marginal cost.
8. Image-generation skills (`hex/claude-image-generation`, `cc-nano-banana`, etc.) — all require a paid third-party image-model API key; not free-tier compatible, hold until a venture specifically needs real imagery and the Chairman clears a small budget line.
9. `borghei/Claude-Skills` mega-repo — broad coverage but Commons-Clause-encumbered and largely redundant with #1's MIT-licensed equivalents; only pull an individual skill from it if nothing else covers that specific need.
10. Social-media *posting/scheduling* skills (Posta, Upload Post API) — all wrap paid APIs; content-generation-only skills from #1/OpenClaudia are the free-tier-compatible subset.

---

## Sources (primary, dated 2026-08-01 fetch)

- [code.claude.com/docs/en/discover-plugins](https://code.claude.com/docs/en/discover-plugins) — official plugin/marketplace mechanics
- [code.claude.com/docs/en/plugin-marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) — marketplace.json / hosting
- [code.claude.com/docs/en/artifacts](https://code.claude.com/docs/en/artifacts) — official Artifacts + connector-calls spec
- [github.com/anthropics/skills](https://github.com/anthropics/skills), [github.com/anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official), [github.com/anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community) (+ live GitHub API pulls for stars/license/last-push)
- [github.com/coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills), [github.com/borghei/Claude-Skills](https://github.com/borghei/Claude-Skills), [github.com/zubair-trabzada/ai-legal-claude](https://github.com/zubair-trabzada/ai-legal-claude), [github.com/ishwarjha/claude-marketing-research-skill](https://github.com/ishwarjha/claude-marketing-research-skill), [github.com/hoyoboy0726123/claude-skill-github-pages-deployer](https://github.com/hoyoboy0726123/claude-skill-github-pages-deployer) (+ live GitHub API pulls)
- Secondary/opinion sources used only for framing, flagged inline where used: skywork.ai, layer3labs.io, totalum.app (Skills-vs-MCP), digitalapplied.com / stacktr.ee / markloop.io / eigent.ai (Artifacts — superseded by the official doc above where they conflicted)

**What I could not verify:** exact last-commit dates for every repo mentioned (only pulled GitHub API data for the shortlist candidates, not every repo named in §3); the precise current membership of the official `claude-plugins-official` marketplace catalog beyond what the docs page listed; whether `borghei/Claude-Skills`'s Commons Clause terms have any edge case that would affect SMM specifically (recommend a quick read of its actual LICENSE file before pulling from it, given GitHub's API returned `NOASSERTION` rather than a clean SPDX id).
