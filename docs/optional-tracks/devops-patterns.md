# DevOps Patterns Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core curriculum teaches you CI with linting and tests
> (Lab 00-C), Docker Compose (Lab 14), and monitoring (Lab 06). But production
> deployments require more: deploying without downtime, evolving database schemas
> under load, and designing caching layers that prevent your database from
> melting. These are the patterns that separate "I can deploy" from "I can
> deploy safely at 3am without waking anyone up."
>
> These labs are especially relevant if you're targeting DevOps, SRE, or
> platform engineering roles — but every backend engineer benefits from
> understanding them.

---

### DevOps-A — Advanced CI/CD: Canary Deploys, Blue-Green & Feature Flags ★★

**Companion to**: Lab 00-C (Git/CI), Lab 14 (Docker Compose), Career-A (Kubernetes)
**Time box**: 8 hours
**Language**: TypeScript + GitHub Actions YAML
**What**: Build a deployment pipeline that goes beyond "push to main, deploy
everything." Implement three deployment strategies: blue-green (instant
rollback), canary (gradual rollout with automatic rollback on error rate
spike), and feature flags (deploy code without activating it). Use GitHub
Actions for the pipeline and Docker Compose to simulate the infrastructure.

**Why this matters**: "We deploy once a week on Friday afternoon" is a symptom
of a team that doesn't trust their deployment pipeline. Teams that deploy 50
times a day trust theirs because they have canary detection, instant rollback,
and feature flags. This lab builds that trust.

**What you'll learn**:
- Blue-green deployments: two identical environments, instant traffic switch
- Canary releases: route 5% of traffic to the new version, monitor error rates, promote or rollback
- Feature flags: deploy code to production but activate it only for specific users or percentages
- Health check-based promotion: automatic rollback when canary error rate exceeds threshold
- GitHub Actions matrix builds: test across Node versions and OS simultaneously
- Deployment environments in GitHub: approval gates, environment secrets, deployment history
- Rollback strategies: which deployment patterns make rollback instant vs painful

**Done when**:
- [ ] Blue-green: two Docker Compose services behind Nginx, traffic switches instantly via config reload
- [ ] Canary: Nginx weighted upstream routes 5% to canary, 95% to stable
- [ ] Canary auto-rollback: health check script detects elevated error rate (>5%) and reverts traffic
- [ ] Canary promotion: if error rate stays low for 2 minutes, promote canary to 100%
- [ ] Feature flag service: simple TypeScript service backed by Redis, flags toggled via API
- [ ] Feature flags in app: at least 2 features behind flags (one percentage-based, one user-targeted)
- [ ] GitHub Actions pipeline: lint → test → build → deploy canary → monitor → promote/rollback
- [ ] Rollback demonstrated: deploy a broken version, observe automatic canary rollback
- [ ] Written comparison: blue-green vs canary vs feature flags — when to use each

**Local environment**: Docker Compose with Nginx (load balancer), 2+ app instances,
Redis (feature flags), Prometheus (canary metrics).

**Testing requirement**: Integration tests for the feature flag service. Shell script
that simulates a canary deployment and verifies rollback on failure. Test that the
blue-green switch is atomic (no dropped requests during switch).
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: Deployment strategies have subtle failure modes. Use AI to explore:
*"What happens if the canary health check passes but the bug only affects 1% of users?"*
The pipeline configuration and rollback logic should be your own work.

---

### DevOps-B — Zero-Downtime Database Migrations ★★

**Companion to**: Lab 08 (PostgreSQL), Lab 03 (REST API), Deep-A (PostgreSQL Performance)
**Time box**: 7 hours
**Language**: TypeScript + SQL
**What**: Practice the hardest part of production deployments: changing the
database schema while the application is serving traffic. Implement the
expand-contract pattern, online index creation, safe column renames, and
data backfills that don't lock tables. Every migration must be reversible
and safe to run while the old version of the app is still running.

**Why this matters**: The scariest moment in most deployments isn't the code
change — it's the database migration. A bad migration can lock a table for
minutes, cause downtime, or corrupt data. This lab teaches you to make schema
changes boring.

**What you'll learn**:
- The expand-contract pattern: add new → backfill → migrate consumers → remove old
- Online index creation: `CREATE INDEX CONCURRENTLY` (why the default locks writes)
- Safe column operations: which ALTER TABLE operations lock the table and which don't
- Data backfills: batch updates with progress tracking that don't lock the table
- Migration ordering: why the new code must work with both old AND new schema
- Reversible migrations: every migration has a rollback that doesn't lose data
- Advisory locks: prevent two migration runners from executing simultaneously
- Ghost tables: the `gh-ost` / `pgroll` approach to zero-downtime schema changes

**Done when**:
- [ ] Migration tool set up (node-pg-migrate, Prisma migrate, or Knex migrations)
- [ ] Expand-contract: rename a column safely (add new → copy data → update app → drop old)
- [ ] Online index: create index on a 1M+ row table without locking writes (prove with concurrent inserts)
- [ ] Safe NOT NULL: add NOT NULL constraint to existing column without table lock
- [ ] Data backfill: update 1M rows in batches of 1000, with progress logging, no table lock
- [ ] Backward compatibility: old app version works with new schema, new app works with old schema
- [ ] Rollback tested: every migration reversed cleanly
- [ ] Advisory lock: prevent concurrent migration execution
- [ ] Written guide: "Safe vs Dangerous PostgreSQL Operations" reference card

**Local environment**: Docker Compose with PostgreSQL (seeded with 1M+ rows) + TypeScript API
(two versions running simultaneously to prove backward compatibility).

**Testing requirement**: Integration tests that run migrations while the API serves
traffic (concurrent requests during migration). Assert: no 500 errors during migration,
no data loss, rollback restores original schema. Benchmark: migration duration on 1M rows.
**CI**: `tsc --noEmit` → `eslint` → migration dry-run → `vitest run --coverage` (≥80% coverage).

**AI usage note**: PostgreSQL's locking behavior is nuanced. Use AI to check:
*"Does ALTER TABLE ADD COLUMN with a DEFAULT lock the table in PostgreSQL 14+?"*
(Answer: no, since PG 11 — but it did before. Version matters.) The migration
strategy and ordering decisions should be yours.

---

### DevOps-C — Multi-Layer Caching Strategies ★

**Companion to**: Lab 09 (Redis), Lab 05 (Nginx), Lab 16 (API Gateway)
**Time box**: 7 hours
**Language**: TypeScript
**What**: Build a 4-layer caching system: HTTP cache headers (browser),
CDN/reverse proxy cache (Nginx), application cache (in-memory LRU), and
distributed cache (Redis). Understand cache invalidation strategies, cache
stampede prevention, and when each layer is appropriate.

**Why this matters**: Lab 09 teaches you Redis as a data structure. This lab
teaches you caching as an architecture pattern. The difference between a
system that handles 100 req/s and one that handles 10,000 req/s is almost
always caching — but wrong caching causes stale data bugs that are harder
to debug than performance problems.

**What you'll learn**:
- HTTP cache headers: `Cache-Control`, `ETag`, `Last-Modified`, `Vary` — what the browser does before even hitting your server
- Reverse proxy caching: Nginx `proxy_cache` for static and semi-static content
- Application-level cache: in-memory LRU (lru-cache) for hot data, bounded by memory
- Distributed cache: Redis for shared cache across multiple app instances
- Cache invalidation strategies: TTL, event-driven (publish on write), cache-aside vs write-through vs write-behind
- Cache stampede / thundering herd: what happens when a popular cache key expires and 1000 requests hit the database simultaneously — and how to prevent it (locking, probabilistic early expiration)
- Cache warming: pre-populate cache before traffic hits
- Cache observability: hit rate, miss rate, eviction rate — the metrics that tell you if your cache is working

**Done when**:
- [ ] HTTP headers: API returns proper `Cache-Control`, `ETag` headers; browser caches responses
- [ ] Nginx cache: reverse proxy caches API responses, `X-Cache-Status` header shows HIT/MISS
- [ ] In-memory LRU: application caches hot data with configurable max size and TTL
- [ ] Redis cache: distributed cache shared across 2+ app instances
- [ ] Cache-aside pattern: app checks cache → miss → query DB → populate cache → return
- [ ] Cache invalidation: write to DB triggers cache invalidation (event-driven, not TTL-only)
- [ ] Stampede prevention: implement mutex-based cache rebuilding (only one request hits DB)
- [ ] Cache metrics: Prometheus metrics for hit rate, miss rate, latency per cache layer
- [ ] Grafana dashboard: visualize cache performance across all 4 layers
- [ ] Benchmark: measure requests/second with and without each cache layer enabled
- [ ] Docker Compose: Nginx + API (2 instances) + Redis + PostgreSQL + Prometheus + Grafana

**Testing requirement**: Integration tests for each cache layer independently.
Test cache invalidation: write → assert cache updated. Test stampede prevention:
100 concurrent requests for expired key → assert only 1 database query.
Benchmark: compare throughput with 0, 1, 2, 3, 4 cache layers enabled.
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: Cache invalidation is famously one of the two hard problems in
computer science. Use AI to understand tradeoffs: *"When should I use write-through
vs cache-aside?"* The cache architecture decisions (what to cache, what TTL, which
layer) should be yours — they depend on your access patterns, and there's no
universal right answer.

**Key insight**: The best cache is the one you don't need. Before adding a cache
layer, ask: "Can I make the database query faster?" (see Deep-A). Caching hides
performance problems; sometimes fixing the root cause is better.

---
