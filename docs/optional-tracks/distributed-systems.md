# Distributed Systems Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core curriculum builds individual services. But production
> systems are made of many services talking to each other, and that's where the
> hard problems live. A request that touches 5 services can fail in ways that
> a single service never can. Distributed tracing tells you where it failed.
> Circuit breakers stop failures from cascading. Distributed locks prevent
> two services from corrupting shared state. These aren't theoretical concepts —
> they're the tools you'll reach for during your first production incident
> involving multiple services.

---

### Dist-A — Distributed Tracing with OpenTelemetry ★

**Companion to**: Lab 06 (Prometheus/Grafana), Lab 07 (Structured Logging), Lab 16 (API Gateway)
**Time box**: 8 hours
**Language**: TypeScript + Go (trace across services in different languages)
**What**: Build a 3-service system (API gateway → order service in TypeScript →
inventory service in Go) fully instrumented with OpenTelemetry. Traces flow
across service boundaries via W3C Trace Context headers. Deploy Jaeger for
trace visualization. Correlate traces with logs using trace IDs.

**Why OpenTelemetry**: It's the CNCF standard for observability. It replaces
Jaeger client, Zipkin client, DataDog agent, and every vendor-specific SDK
with one unified API. Companies are consolidating on OTel because it means
no vendor lock-in — instrument once, export to any backend (Jaeger, Grafana
Tempo, DataDog, Honeycomb).

**What you'll learn**:
- Spans and traces: the building blocks of distributed tracing
- Context propagation: W3C Trace Context header (`traceparent`) across service boundaries
- Automatic vs manual instrumentation: what the SDK instruments for free vs what you add
- Span attributes and events: enriching traces with business context (user_id, order_id)
- Sampling strategies: head-based (decide at trace start) vs tail-based (decide after trace completes)
- Trace-to-log correlation: inject `trace_id` into structured logs so you can jump from trace to logs
- Baggage: propagate arbitrary key-value pairs across all services in a trace (e.g., user_id)
- OpenTelemetry Collector: receive spans from services, process them, export to Jaeger

**Done when**:
- [ ] 3-service system: gateway (TS) → order service (TS) → inventory service (Go)
- [ ] OpenTelemetry SDK configured in both TypeScript and Go services
- [ ] Traces flow across all 3 services (one trace, multiple spans, correct parent-child relationships)
- [ ] Automatic instrumentation: HTTP requests and database queries traced automatically
- [ ] Manual spans: at least 2 custom spans for business logic (e.g., "validate-order", "check-stock")
- [ ] Span attributes: `user.id`, `order.id`, `http.status_code` on relevant spans
- [ ] Trace-to-log: `trace_id` injected into structured logs in both services
- [ ] OpenTelemetry Collector: receives spans from all services, exports to Jaeger
- [ ] Jaeger UI: visualize traces, find slow spans, identify bottlenecks
- [ ] Docker Compose: 3 services + OTel Collector + Jaeger + PostgreSQL + Redis

**Testing requirement**: Integration test that triggers a multi-service request and
verifies the trace has 3+ spans with correct parent-child relationships (query Jaeger API).
Test trace-to-log correlation: verify `trace_id` appears in service logs for a traced request.
**CI**: `tsc --noEmit` → `eslint` → `vitest` (TS) + `go vet` → `go test` (Go).

**AI usage note**: OpenTelemetry has many configuration options. Use AI to navigate:
*"What's the difference between the OTel SDK and the OTel Collector? Do I need both?"*
The instrumentation decisions (what to trace, what attributes to add) should be yours —
they depend on what questions you need to answer during an incident.

**Key insight**: "Without distributed tracing, debugging a production issue across 5
services is like debugging a program with only print statements and no stack traces."

---

### Dist-B — Circuit Breakers, Bulkheads & the Saga Pattern ★★

**Companion to**: Lab 03 (REST API), Lab 11 (RabbitMQ), Lab 12 (Kafka)
**Time box**: 9 hours
**Language**: TypeScript
**What**: Build a microservices system (order → payment → inventory → notification)
that handles failures gracefully. Implement circuit breakers (closed → open →
half-open), bulkhead isolation (one slow service doesn't exhaust the connection
pool), timeouts with retry budgets, and the saga pattern for distributed
transactions (order creation spans 3 services with compensating actions on failure).

**Why this matters**: "The service is down" is easy — you get an error, you show
an error. "The service is slow" is what actually kills you. A slow dependency
without a circuit breaker makes YOUR service slow, which makes YOUR callers
slow, which cascades until everything is down. This is the #1 cause of
distributed system outages.

**What you'll learn**:
- Circuit breaker states: closed (normal) → open (fast-fail) → half-open (testing recovery)
- Circuit breaker thresholds: failure count, failure rate, timeout, half-open max requests
- Bulkhead pattern: isolate service calls into separate pools (one slow service can't starve others)
- Retry with exponential backoff + jitter: why jitter prevents thundering herd on recovery
- Timeout cascades: why service A's timeout must be shorter than the caller's timeout
- Saga pattern (choreography): services publish events, each service reacts independently
- Saga pattern (orchestration): a central coordinator tells each service what to do
- Compensating transactions: "undo" partial work when step 3 of 5 fails (e.g., refund payment)
- Idempotency keys: make operations safe to retry (if the network failed, was the payment taken?)

**Done when**:
- [ ] 4 services: order, payment, inventory, notification — communicating via HTTP and RabbitMQ
- [ ] Circuit breaker on payment service call: opens after 5 failures in 30 seconds
- [ ] Circuit half-open: after 10 seconds, allows 1 request through to test recovery
- [ ] Bulkhead: payment calls isolated from inventory calls (separate connection pools)
- [ ] Timeout: payment call times out after 3 seconds (returns fallback or error)
- [ ] Retry with backoff + jitter: transient failures retried up to 3 times
- [ ] Saga: order creation spans payment + inventory + notification with compensating transactions
- [ ] Saga rollback: if inventory check fails after payment succeeds, payment is refunded
- [ ] Idempotency: duplicate order submission produces the same result (not a double charge)
- [ ] Observability: circuit breaker state changes logged and visible in metrics
- [ ] Docker Compose: 4 services + RabbitMQ + Redis (circuit breaker state) + PostgreSQL

**Testing requirement**: Circuit breaker: simulate payment failure, verify circuit opens
after threshold, verify fast-fail while open, verify half-open after timeout. Saga: simulate
inventory failure after payment success, verify compensating refund fires. Idempotency:
submit same order twice with same idempotency key, verify single charge.
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: The patterns are well-documented (Microsoft's cloud design patterns,
Michael Nygard's "Release It!" book). Use AI to understand tradeoffs: *"When should I
use saga choreography vs orchestration?"* Build the state machines yourself — the
circuit breaker state transitions are the core education.

---

### Dist-C — Distributed Locks & Leader Election ★★

**Companion to**: Lab 09 (Redis), Lab 12 (Kafka)
**Time box**: 8 hours
**Language**: Go
**What**: Build a system where multiple worker instances coordinate using
distributed locks. Implement Redis-based locking (the Redlock algorithm),
understand why naive `SETNX` is insufficient, implement leader election
(only one worker runs a scheduled task), and handle lock expiry, split-brain
scenarios, and fencing tokens.

**Why Go**: Distributed coordination tools are typically written in Go — etcd,
Consul, Kubernetes controllers, CockroachDB. Go's goroutines make simulating
multiple concurrent workers trivial. You'll spawn 5 goroutines each trying
to acquire a lock, and watch the coordination happen (or fail).

**Why this is hard**: Distributed locks seem simple: `SETNX lock owner EX 30`.
But what happens when the process holding the lock crashes? When the lock
expires while the process is still working? When the Redis server you locked
against is different from the one the other process checks? Each edge case
reveals a fundamental truth about distributed systems.

**What you'll learn**:
- Distributed mutual exclusion: why local mutexes don't work across processes
- Redis `SETNX` + `EX`: the naive approach and its failure modes
- Redlock algorithm: lock across N Redis instances, require majority agreement
- The Redlock controversy: Martin Kleppmann's critique and Salvatore Sanfilippo's response
- Lock expiry and renewal: lease-based locking with heartbeat extension
- Fencing tokens: why a lock alone doesn't prevent data corruption (the token monotonically increases)
- Leader election: exactly one worker runs the scheduled task, others stand by
- Split-brain: what happens when the leader can't reach Redis but is still running
- Clock skew: why TTL-based locks have fundamental limitations in distributed systems
- etcd as an alternative: Raft consensus (CP) vs Redis's (AP) model

**Done when**:
- [ ] Redis-based distributed lock: acquire, release, auto-expire
- [ ] Redlock: lock across 3 Redis instances, require 2/3 majority
- [ ] Lock renewal: worker extends lock TTL while still processing (heartbeat)
- [ ] Fencing token: lock acquisition returns a monotonically increasing token, writes include it
- [ ] Leader election: 3 worker instances, exactly 1 is leader, others detect and wait
- [ ] Leader failover: kill the leader, verify another worker becomes leader within timeout
- [ ] Split-brain simulation: network-partition the leader from Redis, verify new leader elected
- [ ] Data safety test: fencing token prevents stale leader from writing after lock expires
- [ ] Docker Compose: 3+ worker instances + 3 Redis instances (for Redlock)

**Testing requirement**: Two workers can't hold the same lock simultaneously. Lock expires
and another worker acquires it. Fencing token prevents stale writes. Leader election
elects exactly one leader. Leader failover completes within timeout.
**CI**: `go vet` → `golangci-lint` → `go test -cover` (≥70% coverage).

**AI usage note**: This is one area where reading the original arguments is worth it.
Ask AI to explain: *"What is Martin Kleppmann's argument against Redlock and why does
it matter?"* The implementation should be yours — debugging lock contention between
goroutines teaches you concurrency at a level no tutorial can.

**Key insight**: "Distributed locks don't make distributed systems simple. They make
one specific failure mode manageable. You still need idempotency, fencing, and
timeout handling around every lock."

---
