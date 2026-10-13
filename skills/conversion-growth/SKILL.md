---
name: conversion-growth
description: Find the core action, the drop-off point, and the shareable/viral loop for a product — used when designing a new flow or reviewing why a product isn't gaining traction. Use when building a new user-facing flow, or when asked "why isn't this growing" / "how do we get more users".
---

# Conversion & Growth

For every product or flow, answer these before (or right after) building, per CLAUDE.md §11 (metrics hierarchy) and the growth sections.

## Questions to answer

1. **Primary action** — the one thing a new visitor should do. If there's more than one candidate, that's the problem.
2. **Entry point** — where do users actually land (direct link, search, a shared post)? Design the first screen for that entry, not a generic homepage.
3. **Drop-off** — at which step do users most likely bail? (Reason first from the flow's friction, then check real analytics if connected — don't only guess.)
4. **Shareable artifact** — what does a user produce or see that's worth showing someone else (a score, a certificate, a result, a before/after)? If there isn't one, that's a gap worth naming.
5. **Referral/viral loop** — concretely: artifact → share → new visitor → does the same thing → produces their own artifact. Name each arrow; if one is missing, say so.
6. **Retention hook** — why would this user come back tomorrow, not just today?
7. **Content opportunity** — what from this feature/launch is worth a build-in-public post (CLAUDE.md §8/§36)?

## Output format

```
PRODUCT/FLOW: ...
PRIMARY ACTION: ...
ENTRY POINT(S): ...
LIKELY DROP-OFF: ... (why)
SHAREABLE ARTIFACT: ... / none — gap
VIRAL LOOP: artifact → share → new visitor → repeat action → new artifact
  (mark any broken/missing arrow)
RETENTION HOOK: ... / none — gap
CONTENT ANGLE: ...

RECOMMENDATION: <one concrete change, ranked by leverage>
```

## Rules

- Don't optimize for followers/impressions in isolation — tie every growth idea back to the metrics hierarchy (users experiencing core value → returning → recommending → paying, CLAUDE.md §39).
- If a product has no plausible viral loop or shareable artifact, say so directly rather than inventing one that doesn't fit — that's real signal about the product, not a skill failure.
- For BestVibeCoder specifically: the loop is test → score → certificate → share → new visitor → new test, and later certificate → sponsor → outbid → share. Treat that as the reference loop when evaluating new features for it — does the feature strengthen an arrow in that loop or just add unrelated surface area?
