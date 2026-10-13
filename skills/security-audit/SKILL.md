---
name: security-audit
description: Security review before shipping — auth, injection, secrets, client-trusted state, Firebase/Firestore rules. Use before any launch, before merging changes that touch auth/payments/user data, and whenever adding a score/price/bid/role/entitlement that a client could otherwise forge.
---

# Security Audit

Run this before shipping anything that touches auth, payments, user data, or any value the client could tamper with. Per CLAUDE.md §21/§34, this is a required pass, not optional polish.

## Checklist

- **XSS** — is any user input rendered as HTML/inserted into the DOM unescaped?
- **Injection** — any raw string concatenation into a query (SQL, Firestore query construction, shell commands)?
- **Auth** — can an unauthenticated or wrong-role request reach a protected action? Check both UI-level and API/backend-level gating — UI hiding a button is not access control.
- **IDOR** — does any endpoint/doc read use a client-supplied ID without checking the requester owns/can access it?
- **CORS / rate limiting** — are public endpoints open to abuse (scraping, spam, cost-draining calls to paid APIs)?
- **Secrets** — grep the diff and the repo for API keys, service-account JSON, `.env` values, hardcoded tokens. Check `.gitignore` covers them *before* first commit, not after.
- **Firebase/Firestore/Supabase rules** — if security rules changed, or a new collection/table was added, are rules scoped to the owner, not `allow read, write: if true`? Use the `firebase-security-rules-auditor` skill for Firestore specifically.
- **Client-trusted state (critical for this portfolio)** — never trust a score, price, bid, user role, payment state, or entitlement supplied solely by the browser. It must be computed, verified, or re-checked server-side. This is directly relevant to BestVibeCoder's quiz score and any future sponsorship/bid system.
- **Webhooks** — is the signature/secret verified before acting on a webhook payload (Stripe, etc.)?
- **Dependency vulnerabilities** — flag anything egregious (`npm audit`/`pip-audit` if quick to run), but don't rabbit-hole here for a pre-launch MVP.

## Output format

```
SCOPE REVIEWED: <files/flows>

FINDINGS (most severe first):
1. [SEVERITY] <what> — <where> — <concrete abuse scenario>
   FIX: ...

NO ISSUES FOUND IN: <areas checked clean>
```

## Rules

- Findings are Observation/Recommendation, not silently-applied fixes, unless asked to fix — state what you found before changing anything (CLAUDE.md §31).
- A P0 here (auth bypass, exposed secret, client-trusted money/score) blocks launch — say so plainly, don't soften it.
- Don't treat this as a one-time gate — re-run it whenever auth, payments, user data, or a client-supplied value that affects money/score changes.
