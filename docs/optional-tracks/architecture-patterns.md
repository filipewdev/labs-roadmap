# Architecture Patterns Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core curriculum teaches you how to build services. But
> at scale, how you coordinate between services matters more than how you build
> any individual one. Event sourcing changes how you think about state. Task
> queues change how you think about reliability. These patterns appear in every
> system that handles money, compliance, or high-volume background work.
>
> These labs are especially relevant for senior/staff engineering roles where
> you're expected to choose architecture patterns, not just implement them.

---

### Arch-A — Event Sourcing & CQRS ★★

**Companion to**: Lab 08 (PostgreSQL), Lab 11 (RabbitMQ), Lab 12 (Kafka)
**Time box**: 10 hours
**Language**: TypeScript
**What**: Build an order management system where every state change is stored
as an immutable event. The current state is derived by replaying events.
Separate the write model (commands that produce events) from the read model
(projections optimized for queries).

**Why event sourcing matters**: Traditional CRUD overwrites state — you know
what it is now, but not how it got there. Event sourcing stores every change
as a fact. This enables: audit trails (who changed what, when), temporal queries
("what was the state last Tuesday?"), and rebuilding read models from scratch.
It's how banks, trading platforms, and compliance-heavy systems work.

**What you'll learn**:
- Events as the source of truth: append-only, immutable, ordered
- Aggregates: the consistency boundary where commands are validated
- Projections: read-optimized views derived from events (eventually consistent)
- Snapshots: how to avoid replaying 10 million events on every request
- CQRS (Command Query Responsibility Segregation): different models for reads and writes
- Eventual consistency: what it means and how to design UIs that handle it
- Event versioning: what happens when your event schema needs to change

**Done when**:
- [ ] Event store: PostgreSQL table storing events (aggregate_id, type, payload, version, timestamp)
- [ ] Aggregate: `Order` with commands: `create`, `addItem`, `removeItem`, `submit`, `cancel`
- [ ] Each command validates business rules and produces events (e.g., `OrderCreated`, `ItemAdded`)
- [ ] Current state derived by replaying events (not stored separately)
- [ ] Projection: denormalized read model in a separate PostgreSQL table (e.g., order summary list)
- [ ] Projection rebuilt from scratch by replaying all events (proves events are the source of truth)
- [ ] Snapshot: after 50 events, store a snapshot to speed up aggregate loading
- [ ] Temporal query: "what was this order's state at timestamp X?"
- [ ] Event versioning: one event schema change handled with an upcaster
- [ ] Docker Compose: API + PostgreSQL

**Testing requirement**: Unit tests for aggregate command validation (invalid transitions
rejected). Integration test: create order → add items → submit → verify projection
matches. Test snapshot: create 100 events, load aggregate, verify snapshot is used.
Test temporal query: verify state at a past timestamp.
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: Event sourcing has subtle tradeoffs. Use AI to understand patterns:
*"When should I use event sourcing vs traditional CRUD?"* and *"What are the downsides
of eventual consistency?"* The domain modeling (what events to define, what invariants
to enforce) should be your own work — that's the architecture skill.

**Key insight**: Event sourcing is not about technology — it's about modeling your
domain as a series of facts. The technical implementation follows from that mental
model shift. If you find yourself storing "current state" alongside events, you've
missed the point.

---

### Arch-B — Background Jobs & Task Queues with BullMQ ★

**Companion to**: Lab 09 (Redis), Lab 11 (RabbitMQ), Lab 19 (n8n)
**Time box**: 7 hours
**Language**: TypeScript (BullMQ + Redis)
**What**: Build a job processing system that handles email sending, image
resizing, report generation, and scheduled tasks. Implement retries with
exponential backoff, dead-letter queues, priority queues, rate limiting,
and job progress tracking.

**Why BullMQ**: Lab 11 teaches message queues at the infrastructure level
(RabbitMQ). BullMQ is the application-level pattern — how your TypeScript
code actually consumes and processes background work. It's built on Redis
(Lab 09) and is what most Node.js production systems use for job processing.
Understanding both levels is what separates "I use a job queue" from
"I designed the job processing architecture."

**What you'll learn**:
- Job lifecycle: waiting → active → completed/failed → delayed → dead-letter
- Retry strategies: exponential backoff, max attempts, custom backoff functions
- Priority queues: urgent jobs processed before bulk operations
- Rate limiting: process at most N jobs per time window (respect external API limits)
- Cron jobs: scheduled recurring tasks (daily reports, cache warming)
- Job progress: real-time progress updates for long-running tasks (WebSocket to UI)
- Concurrency control: how many jobs to process simultaneously per worker
- Graceful shutdown: finish current jobs before worker exits (SIGTERM handling)
- Dead-letter queue: when retries are exhausted, preserve the failed job for inspection

**Done when**:
- [ ] BullMQ queues: `email`, `image-processing`, `reports`, `scheduled`
- [ ] Email queue: simulated email sending with retry on failure (exponential backoff, max 3)
- [ ] Image queue: resize images with concurrency limit (2 simultaneous workers)
- [ ] Report queue: long-running job with progress tracking (0% → 100%)
- [ ] Scheduled queue: cron job runs daily, produces a report
- [ ] Priority: urgent emails processed before bulk newsletter sends
- [ ] Rate limiting: external API calls limited to 10/minute
- [ ] Dead-letter queue: failed jobs after max retries moved to DLQ with full context
- [ ] Dashboard: Bull Board UI showing queue status, job details, and metrics
- [ ] Graceful shutdown: worker finishes current job on SIGTERM (not mid-process kill)
- [ ] Docker Compose: API + workers + Redis + Bull Board

**Testing requirement**: Unit tests for job processors. Integration tests: enqueue a job,
verify it completes. Test retry: simulate failure, verify exponential backoff timing.
Test DLQ: fail a job past max retries, verify it appears in dead-letter queue.
Test cron: advance time, verify scheduled job executes.
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: BullMQ's API is well-designed but the concurrency and retry
configuration has subtle interactions. Use AI to clarify: *"What happens if my worker
crashes mid-job — does BullMQ retry or is the job lost?"* The queue architecture
decisions (how many queues, what retry strategy, what concurrency) should be yours.

---
