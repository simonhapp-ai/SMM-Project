# Duesy — Decision Log

## 2026-08-01 — Venture selected

**Decision:** Build "Duesy," a freelancer invoice payment-reminder tool, as SMM's first shipped venture.

**Context:** Phase 2 research (3 parallel angles — `research/phase2-*-2026-08-01.md`) converged on hand-built micro-SaaS as the best-evidenced category for SMM's toolchain. A follow-up strategy pass identified 5 specific, evidenced gaps: local-service review-request tools, solo-tradesperson invoicing, HoneyBook-lite for creative freelancers, a brand-deal tracker for creators, and a freelancer payment-nudge tool.

**Why this one over the other 4:**
- Review-request and tradesperson-invoicing tools had the strongest direct evidence (sourced G2/Reddit complaints about specific competitors), but their buyer audience (local service businesses — barbers, cleaners, contractors) isn't reachable through any of SMM's actual free/legal marketing channels (Product Hunt, Indie Hackers, Bluesky — see CLAUDE.md's legal flags on X/Reddit/LinkedIn). A well-evidenced idea with no reachable distribution isn't a venture.
- HoneyBook-lite has real evidence (a documented 89% price hike backlash) but competes directly with funded incumbents (Dubsado, Bonsai, HoneyBook itself) and carries real e-signature/legal scope risk for a v1.
- The brand-deal tracker's evidence was the weakest-sourced of the five (secondary sources only).
- The payment-reminder tool has the *weakest single quote* as direct evidence, but wins on the factors that actually matter for a zero-budget, one-operator company: fastest realistic build (days), zero new account dependencies for a v1, and its audience (freelancers) is exactly who's reachable via SMM's legal channels.

**Scope discipline:** v1 ships with zero new third-party accounts — no Stripe, no Supabase, no Resend. It's a static, client-side-only tool (data in browser localStorage). Full automation (accounts, cross-device sync, auto-sent scheduled emails) is explicitly deferred to v2, gated on the Chairman creating Supabase + Resend accounts (already on the CLAUDE.md TODO list for unrelated reasons). This keeps time-to-ship at "today" instead of blocking on account setup.

**What would have to be true for this to work:** Freelancers must find enough value in the reminder-message templates alone (even without automation) to use the tool and eventually pay for the automated version. If v1 gets zero organic pickup within its first couple of weeks on Indie Hackers/Product Hunt/Bluesky, that's a signal to revisit — either the wedge is wrong or distribution needs a different approach — not a reason to keep building deeper into v2 blind.

**Next reviewed:** after v1 ships and gets its first real distribution attempt (`marketer` department, Phase 5).
