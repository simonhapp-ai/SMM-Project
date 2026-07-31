# Founding Brief — Simon Money Maker (SMM)

This is the condensed, organized record of the Chairman's (Simon's) original founding brief and draft kickoff prompt, given 2026-08-01. It's the source of truth for *why* this project exists and what it's bound by. [MASTERPROMPT.md](../MASTERPROMPT.md) is the operational, forward-looking expansion of this; [CLAUDE.md](../CLAUDE.md) is the living charter + log.

## The core ask

Build a real, legal, automated AI business — not a simulation — that generates real revenue, under the internal codename **Simon Money Maker (SMM)**. Claude acts as CEO and does essentially all of the work: research, strategy, company/product creation, coding, website design, and marketing. Simon's role is deliberately narrow: high-level approval, account setup (payment processors, domains, third-party services), and unblocking anything Claude can't do itself (e.g. identity-gated signups).

## Non-negotiable constraints

- **Legal only.** No rule-breaking of any kind aside from that — but that one is absolute.
- **Near-zero budget.** Aside from Simon's existing Claude subscription, everything should run on free tiers, open-source tooling, and free hosting (GitHub Pages is the explicit default for hosting). Any real cost needs to be justified and minimal, and cleared with Simon first.
- **Extreme automation.** The default mode is Claude dispatching specialized agents (Research, Design, Web Dev, Marketing, Strategy) rather than doing everything itself serially or asking Simon to do execution work.
- **Multiple ventures, not one bet.** SMM should run several business ideas at different risk/effort levels simultaneously to maximize the odds that *something* generates money — not put everything behind one idea.
- **Separate identities.** Each venture gets its own name, brand, and product identity. "Simon Money Maker" is strictly an internal/holding-company name between Simon and Claude — it is not customer-facing.
- **Don't reinvent the wheel.** Marketing and monetization should reuse and adapt playbooks and copy frameworks that are already known to work, not invent novel strategies from scratch.
- **Currency matters.** Research into "best ways to make money with AI automation" needs to reflect the actual landscape as of July/August 2026, not stale pre-2026 assumptions.

## The "Time Ruler" requirement

Simon explicitly asked for a mechanism (a "button or switch" plus a visual "time ruler") to schedule *when* agents work, so that token/context usage resets in step with when Simon needs to work on other, unrelated projects. This is a real operational requirement, not a nice-to-have — see CLAUDE.md's "Time Ruler" section for how this maps onto actual Claude Code scheduling mechanisms (`/loop`, `/schedule`, cron).

## Tooling directive

Simon asked Claude to research GitHub and other sources for the best *free* Claude Code skills, extensions, and MCP connectors that would meaningfully boost this project, and to ask Simon for help if any of them are hard to install (since Simon, not Claude, holds the accounts/credentials needed for some integrations).

## The mandatory Phase 1.0 deliverable: the Office Visualization

Simon's draft kickoff prompt made one thing a hard requirement before any business planning text: a working, previewable **Office Visualization** ("the SMM Office") — a dashboard living in this workspace, and the real structure of SMM must match it exactly (i.e. it's not decorative, it's the org chart). It must show:

1. **Minion Supervision Hub** — which agents (Research, Dev, Marketing, etc.) are currently deployed, visually, like a floor plan or status board.
2. **Live Agent Status** — exactly what each agent is doing right now, and what it has already completed.
3. **Chairman's Dashboard** — a dynamic TODO list of things that need Simon's action (account creation, approvals, etc.), and a visually distinct HELP-needed panel for anything blocked on human intervention.
4. **Time Ruler / Token Monitor** — a clear indicator of context/token usage and a recommendation for the next good stopping point.

Simon's original spec called for a single, portable, zero-dependency HTML/CSS/JS file (no build step, previewable via e.g. the VS Code Live Server extension), with hardcoded placeholder data for the first version. That's implemented at [SMM_Office_Visualizer/office.html](../SMM_Office_Visualizer/office.html).

Simon's draft also referenced using "`ultracode` settings for heavy building tasks" — there's no literal setting by that name in this version of Claude Code; the real equivalents are: dispatching work through the Agent tool (optionally to background so multiple things run in parallel), and using the specialized department sub-agents defined in `.claude/agents/` for focused, tool-scoped work. CLAUDE.md documents this mapping.

## Immediate execution plan, as originally drafted

Simon's draft prompt sequenced the very first session as: (1) scaffold the `SMM_Office_Visualizer` folder, (2) write the single-file HTML/CSS/JS dashboard with placeholder data, (3) tell Simon the exact command to preview it. That sequence is what this foundation-build pass executed, plus the supporting charter/prompt/agent infrastructure needed to make everything after it self-sustaining across context resets.
