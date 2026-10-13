---
name: ux-psychology
description: Apply behavioral-psychology UX principles (smart defaults, goal gradient, reciprocity, IKEA effect, loss aversion, contrast/anchoring, Hick's law, peak-end, Zeigarnik, friction budget, etc.) when building or reviewing forms, onboarding, paywalls, empty states, and core flows — with an ethics guard against dark patterns. Use when designing any user-facing flow, onboarding, pricing/paywall, or when asked to improve conversion, activation, or retention of a screen.
---

# UX Psychology

Role: expert UX/UI engineer. Goal: flows that convert and retain **because they are easier and more honest**, not because they trick people. Per CLAUDE.md §39, the target is users reaching core value, returning, and paying — not clicks.

Apply these proactively, even if the request is just "a login form" or "a pricing page". Add a short inline code comment naming the principle where it's applied (`// UX: smart default — most users pick 7:00`).

## Ethics guard (read first, overrides everything below)

Every principle has a manipulative twin. If the honest version isn't possible, don't apply the principle.

- **No fake progress.** A head start must be real (you genuinely completed something). Never show 20% that the user didn't earn.
- **No confirmshaming.** Dismiss buttons say what they do ("Not now", "Keep free plan"), never guilt ("I don't care about my data").
- **No fake scarcity / urgency / countdowns / inflated anchors.** Anchors must be real prices the product actually offers.
- **No hidden or pre-checked consent, no hard-to-find cancel.** Cancel must be as easy as signup.
- **Loss aversion only for real losses** (unsaved data, a real streak the user chose to keep), never invented.
- **Calm products (alarm, sleep, reflection): no streak-shaming, no guilt notifications, no engagement-bait.** Success = user leaves the phone. Morrow's brand-voice rule already bans toxic positivity — keep it.
- When unsure: would the user thank us if they saw exactly why we designed it this way? If not, cut it.

## Core principles (the original 6, adjusted)

1. **Smart defaults.** Never ship an empty form. Pre-fill input/select/radio with the statistically likeliest choice (time, timezone, sound, length). Make the submit label reflect reality ("See 12 results", "Set alarm for 7:00"), not "Submit". Defaults must be the *user-favorable* choice, never the one that benefits us at their expense.
2. **Endowed progress / goal gradient.** Show real progress early: step 1 already done (e.g. "Account created"), count completed steps, show "2 of 4". Motivation rises as the goal nears. Only credit real accomplishment — see ethics guard.
3. **Reciprocity / delayed auth.** Give value before asking for anything. Preview the result, let them set the first alarm or write the first note, *then* ask for login/permissions. Never put an auth wall on the first interaction.
4. **IKEA effect.** Let users build something first (pick a name, goal, wake time, sound, theme), then ask for email/account at the end, labeled "Save" or "Continue" — they're protecting their creation, not "signing up".
5. **Loss aversion (honest).** For upgrade/cancel/save, frame the primary CTA around protecting something real ("Keep my notes backed up"). Keep the secondary button neutral ("Not now"). Don't write the consequence into the dismiss button.
6. **Contrast / anchoring (honest).** Never show a price in isolation: place it next to a real higher-priced option or the real monthly equivalent ("₹799/yr — ₹67/mo vs ₹149/mo monthly"). One plan visually recommended. Anchors must be genuine offers.

## Added principles

7. **Hick's law — fewer choices, faster decisions.** One primary action per screen. Cap options (≤5), group the rest behind "More". A night ritual screen has one job.
8. **Friction budget.** Every extra tap/field/permission costs completions. Count taps to core value (target: first alarm in ≤3 taps; night note in ≤2). Remove or defer anything not needed *now*.
9. **Progressive disclosure.** Show the simple version first; reveal advanced settings on demand (repeat rules, sound packs, snooze tuning). Don't front-load the settings page.
10. **Recognition over recall.** Show options, recents, and examples instead of asking users to remember or invent. Suggest, don't demand a blank field ("What matters today?" + 3 tappable suggestions).
11. **Peak-end rule.** People remember the peak moment and the ending. Design the end of each flow on purpose: the last screen of the night ritual and the first screen of the morning should be the most considered, calmest moments in the app.
12. **Zeigarnik / open loops.** Unfinished things nag the mind. Use it to *close* loops for the user (the "tomorrow-you has it" promise) or to invite completion (an unfinished setup checklist) — never to manufacture anxiety.
13. **Commitment & consistency.** A small first yes leads to bigger ones. Ask for the tiny action first (set one alarm), then the next (add a wake intention), then notifications/permissions *after* value is felt.
14. **Permission timing (contextual asks).** Ask for notification, mic, calendar, location only at the moment the benefit is obvious, with a one-line reason ("Mic lets you say 'snooze 5 minutes'"). Pre-explain before the OS dialog; a denial is often permanent.
15. **Immediate feedback (Doherty threshold).** Respond to every tap within ~100ms visually; show progress for anything >1s; confirm saves ("Alarm set for 7:00 — rings in 8h 12m"). Silence reads as broken. For an alarm app, the confirmation of *when it will ring* is the trust moment.
16. **Error prevention over error messages.** Constrain inputs, confirm only destructive actions, offer undo instead of "Are you sure?". Error copy: what happened, what to do, no blame.
17. **Empty states are onboarding.** Every empty list teaches the next action with one button and an example. Never a blank screen or bare "No data".
18. **Jakob's law — be familiar.** Users expect your app to work like the ones they already use. Use platform conventions for time pickers, back, switches, and settings; spend novelty only on the core idea.
19. **Social proof & trust signals (real ones).** Real counts, real quotes, privacy statements where sensitive data is asked for ("Voice notes never leave your phone"). No invented testimonials.
20. **Thumb zone & tap targets (mobile).** Primary actions in the bottom third, targets ≥48dp, no tiny controls for one-handed, half-asleep use. Design the night screens for dim light, dark mode by default, large type.
21. **Time-of-day context.** Match the UI to the moment: morning = clear, brisk, high contrast; night = dim, slow, quiet, minimal copy. Reduce brightness, motion and choice as bedtime approaches.
22. **Retention hooks that serve the user.** Variable or scheduled value is fine when it's genuinely useful (a new reflection, a readback of your own words). Avoid slot-machine rewards, streak guilt, or notifications whose only job is reopening the app.

## How to use

**Build mode** (new flow/screen): before coding, list which principles apply and the concrete decision for each (one line each). Then implement with inline `// UX:` comments. Keep it to the 3–5 that matter for this screen — don't apply all 22.

**Review mode** (existing screen/flow): walk the screen and output:

```
SCREEN/FLOW: ...
PRIMARY ACTION: ... (one, or flag if several)
TAPS TO CORE VALUE: N (target: ...)
FINDINGS (ranked by impact):
1. [principle] what's wrong → concrete fix
2. ...
ETHICS CHECK: pass / flags
TOP FIX: the one change with the most leverage
```

## Rules
- Tie each change to a metric (activation, completion rate, D7 retention, conversion) — name it so it can be measured later (CLAUDE.md §15, §39).
- Prefer the smallest change that removes friction over adding persuasion.
- Observation vs recommendation vs action (CLAUDE.md §31): say which you did.
- Pair with `frontend-design` for visuals, `conversion-growth` for loops and drop-offs, and `qa-browser` to verify the flow really works on a phone-width viewport.
