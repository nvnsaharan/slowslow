---
name: product-audit
description: Full product-manager-style audit of a startup, SaaS, app, or side project — not a design review. Evaluates the actual product experience (problem, onboarding, core loop, retention, monetization, differentiation) by using it like a first-time user, then gives a ranked keep/fix/remove/build call. Use when auditing a competitor, researching an opportunity (CLAUDE.md §37/§11), or reviewing one of Naveen's own products before a launch/pricing/roadmap decision.
---

# Product Audit

Audit like a PM who just joined the company as the first product hire — not like a design critic. The product experience (does it solve the problem, fast, and make you want to come back) matters more than visual polish. If the target is a live web product, drive it for real with the `chrome-devtools` MCP ([qa-browser](../qa-browser/SKILL.md) style) rather than describing it from the landing page.

Ban generic notes ("improve the UX", "clearer CTA", "add more features"). Every point needs a *why it matters* and a concrete alternative.

Mark anything you can't determine as **"Unclear from the product experience"** — never invent details (problem, target user, business model, pricing logic).

## Process

1. **Understand the product** — name, category, target customer, core problem, primary use case, business model, likely alternatives.
2. **Use it** — landing page → signup/login → onboarding → core feature → secondary features → pricing/upgrade flow → settings/account → empty states → error states → mobile. Behave like a real first-time user, not a code reviewer.
3. Work through the sections below in order, then give the ranked call in **What I'd Do**.

Throughout, separate **OBSERVED** (what actually happened) from **INFERENCE** (what you think it implies) — don't blur them.

## Output format

```
# 5-SECOND TEST
What I think this does: ...
Who I think it's for: ...
What action I'm supposed to take: ...
Clarity: /10
Initial appeal: /10

# CORE PRODUCT
Problem: real? how frequent/painful?
Solution: does it actually solve it, meaningfully better than the obvious alternative?
Time to value: steps before first real value; name the unnecessary ones
Core loop: Input → Processing → Result → Reason to return — is it strong?
Retention: why come back tomorrow/next week? if no mechanism, say so plainly

# USER JOURNEY
Discovery → Signup → Onboarding → First action → First value → Repeat usage → Payment/upgrade
For each stage hit: friction / confusion / unnecessary steps / missing info / opportunity
(prioritize by user impact, not visual severity)

# PRODUCT QUALITY
OBSERVED bugs/broken flows/inconsistent behavior/slow interactions/missing feedback/unfinished features
INFERENCE of consequence for each

# DIFFERENTIATION
Why does this need to exist? What would users use instead?
Strongest differentiator — is it obvious? Could a competitor copy it easily?
(if it's just better execution of an existing idea, say that — don't force a differentiator)

# COMPETITOR / ALTERNATIVE ANALYSIS
Alternatives a real user would actually consider — compare experience, functionality, ease of use, pricing, differentiation, switching cost

# MONETIZATION
If paid: would I pay, why/why not, is pricing understandable, is paid value obvious, does price match problem value
If free: plausible monetization paths (not "just add ads")

# WHAT I'D DO
Keep: what must not change
Fix: what currently hurts the product
Remove: what's unnecessary/distracting
Build: highest-value thing to build next
Experiment: one experiment to run (per CLAUDE.md §38 — cheapest validation first)

# PRODUCT SCORE
Problem: /10   Solution: /10   UX: /10   Value: /10
Differentiation: /10   Retention potential: /10   Monetization: /10   Execution: /10
Overall: /10 — explain in 2-3 sentences

# BIGGEST INSIGHT
"The single biggest thing I'd tell the founder is..."

## Human Reviewer Notes
(leave blank — for the human to fill in after reading; never fabricate this)
```

## Rules

- Majority of the audit is product experience, not visual design — if you catch yourself writing about colors/fonts more than flows, stop and redirect (that's [frontend-design](../frontend-design/SKILL.md)/[impeccable](../../../../.claude/skills/impeccable) territory, not this one).
- Ruthless prioritization in "What I'd Do" — this is the section that matters, not a wall of observations.
- If auditing one of Naveen's own products, tie findings back to CLAUDE.md's Metrics Hierarchy (§39: activation → retention → recommend → pay → profitable acquisition) and flag whether this is a distraction from the current priority (§3) before recommending build work.
- If auditing a competitor, this feeds CLAUDE.md's Research Mode (§37/§11) — surface complaints (signal) over one-off preferences (noise), per §12.
- Never fabricate the Human Reviewer Notes section or pretend a live test happened if the MCP/browser wasn't actually available — say so (§31: Observation vs Recommendation vs Action).
