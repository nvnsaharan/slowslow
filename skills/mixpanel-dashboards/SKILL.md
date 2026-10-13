---
name: mixpanel-dashboards
description: Builds and updates Mixpanel dashboards end-to-end via the mixpanel MCP tools — from a request like "build me a retention dashboard for X" or "track onboarding for the new feature" to a finished, saved dashboard with a shareable URL. Use this whenever the user wants a recurring/shareable view of metrics (a "dashboard," "board," "report I can check on," or a standing way to watch a funnel/retention/adoption trend), not just a one-off number — even if they don't say the word "dashboard." Also use it when the user wants to add reports to, or reorganize, a dashboard that already exists. Do NOT use it for a single ad-hoc question ("how many X happened last week") — just run the query directly for that.
---

# Mixpanel Dashboards

Turn a request for a dashboard into a real, saved Mixpanel dashboard with a
URL the user can bookmark — built from verified events and properties, not
guessed ones, and reusing what already exists rather than cluttering the
project with near-duplicates.

## Why this order of operations matters

Mixpanel projects accumulate cruft fast: half-named events, dashboards nobody
remembers making, metrics that were one-offs. The steps below exist to avoid
adding to that pile — every step is there because skipping it has a specific
failure mode (a dashboard full of zeros because an event name was guessed
wrong, a duplicate dashboard next to one that already existed, a chart scoped
to the wrong project's data because two products share one Mixpanel project).

### 1. Get oriented before touching anything

Call `Get-Business-Context` first, exactly as the mixpanel MCP server's own
instructions say to. It resolves org vocabulary, project nicknames, and which
project a name like "the US app" or an internal acronym actually refers to —
guessing this from the tool list alone is how you end up building the
dashboard in the wrong project. Follow with `Get-Projects` if the target
project still isn't obvious after that.

### 2. Check whether this dashboard already exists

Run `List-Dashboards` (or `Search-Entities` with `entity_types=['dashboard']`,
which the tool description prefers) scoped to the target project, filtered by
a guess at the title. If something close already exists:

- If the user's request is really "add to" or "extend" that dashboard, use
  `Update-Dashboard` (call `Get-Dashboard` with `include_layout=true` first to
  get real row/cell IDs) instead of creating a new one.
- If it's a genuinely different view, proceed, but mention the existing one
  to the user so they can decide if it should be replaced instead.

Never skip this check to save time — a project with five near-identical
"Activation Dashboard" entries is worse than asking one clarifying question.

### 3. Gather what the dashboard actually needs to answer

Before querying anything, nail down:

- **Audience/purpose** — who checks this and what decision does it inform?
  (A founder's weekly health check needs different framing than an on-call
  engineer's crash dashboard.)
- **Key metrics or events** — the specific things being tracked, in the
  user's words.
- **Time range / recurring window** — a dashboard usually has a default
  `time_filter` (e.g. trailing 30 days), unlike a one-off query.
- **Which pattern this is** — see Common Patterns below; most requests map
  cleanly onto one, which tells you what reports to build without guessing.

If the request is unambiguous and small (e.g. "make a dashboard for
sign-ups and activations this month"), don't interrogate the user over a
2-report dashboard — infer sensibly and confirm briefly. Save the
back-and-forth for genuinely ambiguous or large asks.

### 4. Verify names — never guess an event or property

Once you know roughly what to measure, confirm the exact event and property
names exist before building a single query:

- `Get-Events` with `query="<keyword>"` to find candidates, or
  `event_names=[...]` to check specific ones exist. Prefer `verified=True`
  events when there's a choice between a verified and unverified event that
  both plausibly fit.
- `List-Properties` (with `events=[...]` to scope to the relevant events) to
  confirm the property names and types you plan to filter or break down by.

An unrecognized event or property name in a query silently returns zeros —
it does not error. A dashboard that quietly shows all-zero charts because of
a typo is a worse failure than stopping to double check the name first.

### 5. Check for a multi-product / multi-market split before assuming scope

Some of this user's Mixpanel projects hold events from more than one product
or market in a single project, distinguished by a property (commonly `app`,
but check — it could be `market`, `platform`, `country`, etc.). Before
assuming "one dashboard = the whole project," check whether such a property
exists and has multiple values (`List-Properties` + `Get-Property-Values`).
If it does:

- Ask (or infer from the request) whether the dashboard should be scoped to
  one value (e.g. `app == "compario"`), broken down by it (one line per
  value, for a comparison view), or left unscoped because the user actually
  wants the whole project's aggregate.
- Default to scoping or breaking down rather than silently aggregating
  everything together — an aggregate across products usually isn't what
  anyone meant to look at, and it's a common way these dashboards end up
  useless.

### 6. Reuse or create metrics

Run `List-Metrics` (optionally with `query="<keyword>"`) before building a
report that measures something standard (activation, a core action, a
formula like a ratio). If a verified metric already covers it, reference it
instead of re-deriving the same logic ad hoc — that's exactly the kind of
duplication this process exists to avoid. Only call `Create-Metric` when the
thing being measured is genuinely reusable (will likely be asked for again)
and nothing existing covers it; a metric used in exactly one report on one
dashboard usually doesn't need to be saved separately.

### 7. Build each report

For every chart/table the dashboard needs, call `Run-Query` with
`skip_results=true` — the dashboard only needs the resulting `query_id`, not
the data itself, so don't spend tokens rendering results you're about to
discard. Use `Get-Query-Schema` for the relevant `report_type` (insights,
funnels, retention, flows) if you're unsure of a field. Match the report
type to the pattern (see below) rather than defaulting everything to
`insights`.

Insights queries against `$all_events` are capped at a 93-day range — if the
dashboard needs a longer trend, break it into per-event metrics instead of
one `$all_events` breakdown.

### 8. Assemble the dashboard

Call `Create-Dashboard` with the `query_id`s from step 7, grouped into rows
(max 4 items per row, max 30 rows). Give it a `time_filter` matching the
window from step 3, and a real `title`/`description` — not a generic
placeholder. Use `TextContent` rows sparingly to label sections when the
dashboard mixes patterns (e.g. a header row before the retention block).

### 9. Report back

Give the user the dashboard's URL (or the updated dashboard's, for
`Update-Dashboard`) and a one-line summary of what's on it. If step 5 found a
multi-product split and you had to make a judgment call about scoping, say so
explicitly — that's the kind of assumption that should be visible, not
silent.

## Common dashboard patterns

Most requests map onto one of these. Use them to decide report types and
breakdowns quickly instead of designing from scratch each time.

- **Activation / onboarding funnel** — a `funnels` report from first-open (or
  install) through the events that define "activated" for this product.
  Usually one report; add a line/table beneath it for daily volume of the
  first step so drop-off and top-of-funnel trend are both visible.
- **Retention** — a `retention` report: initial action → return action, with
  the unit (day/week) matching how often the product expects people back.
  Pair with a cohort-size trend so retention % isn't read without knowing if
  the cohort is shrinking.
- **Feature adoption** — `insights`, `total`/`unique` math on the feature's
  key event, trended over time (`line`), plus a breakdown by a relevant
  segment (platform, market, user cohort) if one exists. Compare against a
  baseline event (e.g. app opens) so adoption reads as a rate, not a raw
  count that just tracks overall growth.
- **Per-market / per-app breakdown** — same underlying metric(s), broken down
  by the product-distinguishing property from step 5, usually as a
  `stacked-bar` or one line per value on a `line` chart, so the products are
  visibly compared rather than blended.
- **Health / error monitoring** — `total` counts of failure-style events
  (`*_failed`, crash events), trended daily, usually without a breakdown
  unless the user specifically wants it split by cause.

If a request doesn't fit any of these, ask what decision the dashboard needs
to support rather than picking a pattern that doesn't match.
