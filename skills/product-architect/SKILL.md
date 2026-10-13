---
name: product-architect
description: Scope a new product/feature idea before writing code — problem, user, MVP vs nice-to-have vs future, core loop, monetization. Use whenever Naveen says "build this" for a new product or a substantial new feature, before any implementation starts.
---

# Product Architect

Run this before writing code for a new product or a substantial new feature. The goal is to stop scope creep before it starts, per CLAUDE.md's MVP Rule (§17) and Product Decision Framework (§16).

## Steps

1. **Understand the idea in one paragraph.** What is it, in plain language — not features, the actual thing.
2. **User problem.** Who has this problem, how often, how painful is it, what do they do today instead.
3. **Differentiation & distribution.** Why this over the existing alternative; who are the first 10 users and where do they already hang out (CLAUDE.md §24 — if there's no answer here, flag it before building).
4. **Core loop.** The one repeatable action that creates value. Not a feature list — the loop.
5. **Data model.** The 3-6 core entities, nothing speculative.
6. **Monetization** (even if "free for now" — say so explicitly and why).
7. **Scope split — do this explicitly, in writing, before coding:**
   - **MVP** — the smallest build that answers the validation question (CLAUDE.md §17: an MVP answers a question, it doesn't ship a platform).
   - **Nice-to-have** — real but deferred.
   - **Future** — park it, don't build it, don't design around it either.

## Output format

```
PRODUCT: <name>
ONE-LINE: <what it does>

USER PROBLEM: ...
FREQUENCY/PAIN: ...
EXISTING ALTERNATIVE: ...
DIFFERENTIATION: ...
FIRST 10 USERS: ...

CORE LOOP: ...
DATA MODEL: entity list
MONETIZATION: ...

MVP (build now):
- ...
NICE-TO-HAVE (defer):
- ...
FUTURE (park, don't design for it):
- ...

VALIDATION QUESTION THE MVP MUST ANSWER: ...
```

## Rules

- If the MVP list has more than ~5 items, push back — that's not an MVP.
- If "first 10 users" has no concrete answer, say so explicitly rather than proceeding — per CLAUDE.md §24, that's a reason to question whether to build it at all.
- Don't silently expand scope mid-build. If new scope shows up while building, re-run the MVP/nice-to-have/future split rather than just adding it.
- This is a planning pass, not permission to over-plan — keep it to one pass, then move to building (CLAUDE.md's core principle: BUILD → SHIP → MEASURE → LEARN → ITERATE).
