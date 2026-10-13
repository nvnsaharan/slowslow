---
name: launch-readiness
description: Final pre-launch checklist across functionality, security, SEO, performance, analytics, and legal/ops — inspects only, doesn't change code until findings are reported. Use when Naveen says "is this ready to launch" or before any public announcement/launch post.
---

# Launch Readiness

Run this as the final gate before a public launch or announcement, per CLAUDE.md §22. **Inspect first, report findings ranked P0/P1/P2, then fix P0/P1 and re-test — don't fix silently while inspecting.**

## Pass 1 — Inspect (no code changes yet)

Pull in the other skills for their checklists rather than duplicating them:
- Functionality/UX: run `qa-browser` (real browser, desktop + mobile + edge cases).
- Security: run `security-audit`.
- SEO/sharing: run `seo-launch`.
- Performance: run `performance-audit`.
- Growth loop: run `conversion-growth` (does the launch have a shareable artifact and a story, per CLAUDE.md §23?).

Additionally check:
- **Onboarding** — does a brand-new user understand what to do in the first 10 seconds?
- **Error states** — network failure, invalid input, expired session — do they fail gracefully with a real message, not a blank screen or raw stack trace?
- **Empty states** — first-run/zero-data screens designed, not just blank.
- **Analytics** — is the core action tracked (signup, activation, the core loop's key step)? Per CLAUDE.md §15, at minimum: signup, activation, core action.
- **Environment/secrets** — production env vars set, no secrets in the client bundle or repo.
- **Legal/ops** — privacy policy and terms present if the product collects any user data or payment; a support/contact/feedback path exists.
- **Payment/refund flow** — if applicable, tested end to end including a failure case.

## Pass 2 — Report

```
LAUNCH READINESS: <product>

P0 — BLOCKERS (must fix before launch):
1. ...

P1 — IMPORTANT (fix before or immediately after):
1. ...

P2 — POLISH (can follow post-launch):
1. ...

LAUNCH STORY CHECK (CLAUDE.md §23 — problem → insight → build → result → invitation): present / missing
FIRST 10 USERS PLAN (CLAUDE.md §24): clear / unclear
```

## Pass 3 — Fix & re-verify

- Fix P0s, then P1s.
- Re-run the specific sub-skill(s) affected (don't just eyeball the diff) to confirm the fix actually works.
- Do not silently skip a P0 — if something can't be fixed now, say so explicitly and flag it as a known launch risk rather than letting it pass unmentioned.

## Rules

- This is an audit, not a rewrite — don't refactor unrelated code while running it.
- P2 items should not block or delay the launch; say that explicitly so a long polish list doesn't turn into launch avoidance (CLAUDE.md §40: "when I avoid launching, push toward shipping").
