# Duesy — Product Spec (v1)

## One-sentence value proposition
Track who owes you money and get the perfectly-worded reminder to send them — no accounting software, no signup, nothing leaves your browser.

## Audience
Freelancers and solo operators who invoice clients directly (not through a platform that already handles this) and hate writing "hey, just following up" emails.

## v1 scope (ships today, zero third-party accounts)
- **Add an invoice**: client name, amount, invoice date, due date.
- **Dashboard**, grouped by urgency: Upcoming (due within 7 days), Overdue (1-7 / 8-30 / 30+ days, escalating visual urgency), Paid (archive).
- **Reminder message generator**: per invoice, one click produces a copy-ready message. Tone escalates automatically with lateness:
  - Not yet due / due soon → friendly heads-up
  - 1-7 days overdue → polite follow-up
  - 8-30 days overdue → firmer, direct
  - 30+ days overdue → final-notice tone, still professional (never threatening/legal-sounding — that's a liability line v1 shouldn't cross)
- **Storage**: browser `localStorage` only. Export/import as JSON for backup or moving between devices (manual, not synced).
- **No login, no account, no server.** Explicitly stated on the page so it reads as a feature (privacy) not a limitation.

## Explicitly out of scope for v1 (this is the v2 gate)
- Automatic scheduled email sending (needs Resend account)
- Cross-device sync / multi-user accounts (needs Supabase account)
- Invoice PDF generation, payment collection, accounting features — Duesy is the reminder layer, not an invoicing suite. Don't scope-creep into competing with Bonsai/HoneyBook directly.

## Identity
- **Name:** Duesy (short for "what's due," reads friendly/breezy — freelancer tone, not corporate accounting-software tone).
- **Tone:** warm, slightly informal, competent. Never guilt-trippy or aggressive — the product's whole pitch is taking the awkwardness out of chasing money, so it can't itself feel awkward or naggy.
- **Visual direction:** clean, calm, a single confident accent color, generous whitespace. Avoid generic SaaS-dashboard blue; avoid anything that reads like accounting software (no green ledger aesthetics).

## Monetization path (not built in v1)
v1 is free — it's the distribution/validation layer. v2 (automated sending, sync) becomes a small subscription, priced well under Dubsado/Bonsai's $15-40/mo given it does one thing, not a full client-management suite.

## Success signal for v1
Real, unprompted usage/interest from posting on Indie Hackers, Product Hunt, and Bluesky (SMM's legal free channels) within the first couple of weeks. No signal = revisit the wedge before investing in v2's account-gated automation.
