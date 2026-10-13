---
name: system-design
description: Design and review distributed systems and software architecture — design docs, RFCs, ADRs, capacity estimates, data models, API contracts, failure-mode analysis, and architecture diagrams. Use this skill whenever the user asks how to build, scale, restructure, or evaluate a system, service, backend, pipeline, or platform — including phrasings like "how would you architect X", "will this scale", "should we use Kafka/microservices/a queue here", "review my architecture", "design a URL shortener / feed / chat / rate limiter", "help me write a design doc", or "we're hitting limits at N users". Also use it when the user is preparing for a system design interview. Trigger even when the word "architecture" never appears — a question about splitting a monolith, choosing a database, or handling 10x traffic is a system design question.
---

# System Design

Most bad designs are not bad because someone picked the wrong queue. They are bad because
nobody wrote down the requirements, nobody estimated the load, and nobody asked what happens
when a dependency is down. This skill is mostly a discipline for doing those three things
before reaching for technology names.

## Core principle

**Requirements → estimates → design → failure modes → tradeoffs.** In that order. A design
presented without the numbers that justify it is a guess wearing a diagram. If the user
hands you a solution ("we want to move to microservices"), work backwards to the problem
first — then say plainly whether the solution fits it.

## Workflow

### 1. Pin down requirements and constraints

Never start drawing boxes until these are written down. If the user hasn't given them,
state the assumptions you're making explicitly and visibly, so they can correct you — don't
bury them or silently invent them.

Capture:

- **Functional**: what must the system actually do? 3–7 bullets, verbs not nouns.
- **Scale**: users (DAU/MAU), requests/sec, data volume, growth rate, read:write ratio.
- **Latency budget**: p50 and p99 targets, per user-facing operation. "Fast" is not a target.
- **Consistency needs**: which operations tolerate staleness, and for how long? This is
  usually per-operation, not per-system.
- **Availability target**: 99.9% is ~43 min/month of downtime; 99.99% is ~4 min. Each nine
  costs real money — ask whether they actually need it.
- **Durability**: what is unacceptable to lose? Is there a recovery point objective?
- **Non-negotiables**: compliance/data residency, existing stack, team size, budget,
  deadline. These eliminate more options than any technical argument will.
- **Explicitly out of scope**: name 2–3 things you are *not* solving. This prevents scope
  creep during the design and signals what the design deliberately ignores.

Ask at most 2–3 clarifying questions, and only ones that would change the design. If the
answer wouldn't change anything, assume and move on.

### 2. Do the arithmetic

Back-of-the-envelope estimates decide the design. A system serving 100 rps and one serving
100k rps are not the same system, and the difference is knowable in 60 seconds.

Show the calculation, not just the result. Round aggressively — one significant figure is
usually enough.

```
DAU × actions/user/day = requests/day
requests/day ÷ 86,400 ≈ average rps        (86,400 ≈ 10^5, close enough)
average rps × 3–10 = peak rps               (higher multiple for consumer/diurnal traffic)
rows/day × bytes/row × retention_days × replication × ~1.3 (index overhead) = storage
peak rps × avg payload bytes = bandwidth
```

Then compare against what one machine can do. If the answer fits comfortably on one box
with a replica, say so and stop designing a distributed system.

**Order-of-magnitude reference points** (directional, not benchmarks — verify for the real
workload before committing):

| Operation | Rough cost |
|---|---|
| L1 cache reference | ~1 ns |
| Main memory reference | ~100 ns |
| SSD random read | ~16 µs |
| Round trip inside one datacenter | ~0.5 ms |
| Round trip cross-region (e.g. US↔EU) | ~80–150 ms |
| Disk (HDD) seek | ~5–10 ms |

| Component | Rough ceiling per node |
|---|---|
| Stateless app server, simple handler | 10k–50k rps |
| App server doing real DB work | 1k–5k rps |
| Relational DB (Postgres/MySQL), simple reads | ~10k–50k qps |
| Relational DB, writes | ~1k–10k wps (fsync-bound) |
| Redis / in-memory cache | ~100k ops/s |
| Kafka partition | ~10 MB/s sustained comfortably |

Useful constants: 1 day ≈ 86,400 s. 1 month ≈ 2.6M s. 1 KB/row × 1M rows/day ≈ 1 GB/day
≈ 365 GB/year before replication.

### 3. Sketch the high-level design

Start with the simplest thing that satisfies the numbers, then add only what the numbers
force you to add. In order:

1. **Data model first.** Entities, relationships, access patterns, expected cardinality.
   The access patterns determine the storage choice — not the other way around.
2. **API contract.** The 3–5 core operations, with request/response shape, idempotency
   semantics, and error behavior. Design the contract before the internals.
3. **Component diagram.** Client → edge → services → stores, plus async paths.
4. **Request walkthrough.** Trace one read and one write end to end, naming what is
   synchronous and what is deferred.

**Picking storage** — match to access pattern:

| Pattern | Reach for |
|---|---|
| Relational data, transactions, flexible queries | Postgres/MySQL (the default; assume this until it's disproven) |
| Known key, high volume, simple access | Key-value store (DynamoDB, Redis, Cassandra) |
| Write-heavy time series / events | Time-series or columnar store, or append-only log |
| Full-text / fuzzy search | Search index (Elasticsearch/OpenSearch), fed from the source of truth |
| Large blobs | Object storage, with the metadata in the primary DB |
| Analytics over history | Columnar warehouse, populated by ETL/CDC — never the OLTP DB |

Note explicitly which store is the **source of truth**. Everything else is a derived copy
that can be rebuilt, and saying so out loud prevents a whole class of consistency bugs.

**Reach for async** (queue, log, worker) when work is slow, retryable, spiky, or fan-out —
not by default. Every queue adds delivery semantics, ordering questions, retry logic,
poison-message handling, and a dead-letter path to design. Say what the queue buys.

### 4. Deep dive on the bottlenecks

Pick the 2–3 hardest parts and go deep. Generic depth everywhere is worse than sharp depth
where it matters. The hard part is usually one of:

- **Hot keys / skew** — celebrity user, popular product, one tenant that is 40% of load.
- **The write path at peak** — where the fsync, the lock, or the single primary lives.
- **Cache strategy** — what's cached, TTL, invalidation, stampede protection
  (request coalescing, jittered TTLs), and what happens on a cold cache.
- **Sharding/partitioning key** — and what makes resharding possible later. Choosing a key
  you can't change is one of the most expensive mistakes in this list.
- **Consistency at the seams** — dual writes are a bug; prefer transactional outbox, CDC,
  or a single source of truth with derived reads.
- **Backpressure** — what sheds load when downstream is slow, and in what order.

### 5. Failure modes and operations

A design isn't finished until it says how it breaks. Walk this checklist and keep what's
relevant:

- **Every dependency is down.** For each: fail closed, fail open, degrade, or queue? Say which.
- **Timeouts** on every network call, budgeted so the total stays inside the latency target.
- **Retries** with exponential backoff *and jitter*, with a retry budget. Naive retries turn
  a blip into an outage.
- **Idempotency** for anything retried or replayed — idempotency keys on writes, dedupe on
  consumers. Assume at-least-once delivery unless proven otherwise.
- **Circuit breakers / bulkheads** so one slow dependency doesn't exhaust every thread.
- **Thundering herd** on restart, cache flush, or mass reconnect.
- **Data loss / corruption**: backups, restore *tested*, and the RPO/RTO the design meets.
- **Observability**: the handful of metrics that would catch this failing (RED: rate,
  errors, duration; plus saturation), the SLO, and what pages a human.
- **Rollout**: migration path, feature flag, dual-write/backfill plan, and how to roll back.

For anything stateful, describe the migration explicitly. "Migrate the data" is not a plan;
expand-migrate-contract is.

### 6. State the tradeoffs

Close by naming what the design gives up, what you'd do differently at 10x, and the
alternative you rejected with the reason. A design that claims no downsides hasn't been
examined. Be concrete: "this trades write latency for read simplicity" beats "there are
tradeoffs to consider."

## Output formats

Don't default to a wall of markdown in the chat reply. When asked to *build* a system
design, pick the format that actually fits what's being built and how it'll be used —
the same judgment you'd apply to any other deliverable:

- **A design doc, RFC, or ADR** meant to be read, revised, and shared → a proper document,
  not just chat prose. Use whatever this session's document tooling is (a Claude Doc, or a
  published Markdown/HTML page) rather than leaving a long structured writeup buried in
  the conversation where it can't be edited or shared on its own.
- **Architecture, sequence, or data-flow diagrams** → always render them, never describe a
  diagram in words. Mermaid is the default (it's plain text, portable, and pastes cleanly
  into docs/wikis). Reach for the richer diagram tool available in this session when a
  design has enough moving parts that a hand-labeled, styled diagram would communicate
  faster than a generic flowchart.
- **Capacity estimates, comparison of storage/queue options, or tradeoff tables** → an
  actual table, or a small chart if the estimates are best understood as a trend/comparison
  (e.g. cost or latency across scale scenarios) — not numbers buried in a paragraph.
- **Something with an interactive element** — a capacity/cost calculator, a sharding-key
  simulator, a clickable mockup of the API surface — is worth more built than described.
  If the user would benefit from playing with it, build it as a working page rather than
  narrating what it would do.
- **A quick gut-check** ("Postgres or Mongo for this?", "does this hold at 10x?") → answer
  directly in chat. Don't manufacture a doc or diagram for a two-sentence answer.

The point is to match effort and format to what's actually being asked for — a full design
review earns a real document with a real diagram; a one-line sanity check doesn't.

### Design doc (default for "design X" / "write a design doc")

```markdown
# [System name]
## Problem & goals
## Non-goals
## Requirements (functional, scale, latency, consistency, availability)
## Estimates  ← show the arithmetic
## Proposed design
### Data model
### API
### Architecture  ← diagram
### Request flow
## Deep dives  ← the 2–3 hard parts
## Failure modes & operations
## Alternatives considered  ← with reasons rejected
## Rollout plan
## Open questions
```

### ADR (for a single decision: "should we use X or Y")

```markdown
# ADR-NNN: [Decision]
Status: proposed | accepted | superseded
## Context   ← forces at play, constraints, what we know
## Decision  ← what we're doing, in one sentence
## Consequences  ← good and bad, explicitly
## Alternatives considered
```

Keep ADRs to one page. Their value is the record of *why*, not exhaustive detail.

### Architecture review (for "review my design")

Lead with the 2–3 things that will actually hurt, then lesser issues. For each: the risk,
why it matters at their stated scale, and a concrete fix. Say what's already good and
should be left alone — a review that only criticizes gets discounted wholesale. Be
direct about severity; don't pad a serious flaw with hedging.

### Diagrams

Use Mermaid for architecture and sequence diagrams — it's readable as text and renders in
most places these are pasted.

```
graph LR
  C[Client] --> LB[Load balancer]
  LB --> API[API service]
  API --> Cache[(Redis)]
  API --> DB[(Postgres primary)]
  DB -.replication.-> R[(Read replica)]
  API --> Q[[Event queue]]
  Q --> W[Worker]
```

Keep one diagram to one level of abstraction — mixing a class diagram into a deployment
diagram makes both useless. Label edges with protocol or purpose when it isn't obvious.
Show sync calls as solid, async/replication as dashed.

## Interview mode

If the user is preparing for a system design interview, the structure above still holds,
but the emphasis shifts: they're being scored on process, not on the final box diagram.
Coach them to spend the first 5 minutes on requirements and estimates, narrate tradeoffs
out loud, and drive the conversation rather than waiting to be asked. Push back if they
jump straight to naming technologies — that's the most common failure mode. Offer to play
interviewer and probe their design with follow-ups if that would help.

## Anti-patterns to catch (in your own designs and in reviews)

- **Resume-driven architecture** — Kubernetes, Kafka, and a service mesh for 500 rps.
- **Microservices before the domain boundaries are known.** A modular monolith is the right
  default until team size or deploy contention actually forces the split. Distributed
  systems convert compile-time errors into 3am pages.
- **Premature sharding.** Vertical scaling plus read replicas carries most products
  further than people expect, at a fraction of the complexity.
- **Cache as a load-bearing wall** — a design that collapses on a cold cache is a design
  with an undeclared single point of failure.
- **Dual writes to two stores** with no outbox or CDC. These drift. Always.
- **Unbounded anything** — queues, retries, result sets, in-memory collections.
- **Exactly-once delivery** assumed rather than built (idempotency + dedupe).
- **No migration story** for the state that already exists.
- **Numbers that appear in the conclusion but never in the reasoning.**

## Calibration

Match depth to the question. "Should I use Postgres or Mongo for a side project" deserves a
direct answer with a short reason, not a design doc. A platform serving millions of users
deserves the full treatment. When the right answer is "your current setup is fine, don't
change it," say that — the most valuable architectural advice is often to not build the
thing.
