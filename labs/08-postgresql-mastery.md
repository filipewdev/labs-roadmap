# Lab 08 — PostgreSQL Mastery

**Goal**: Take a slow query from ~3 seconds to under 50ms using only indexes and
query rewrites. No schema changes. Real dataset from IBGE.

**Time box**: 8 hours

**Language**: SQL + TypeScript

**Done when**:
- [ ] IBGE municipalities dataset imported (~5570 cities with population and state data)
- [ ] 5 slow queries identified and optimized using `EXPLAIN ANALYZE`
- [ ] At least one B-tree, one partial, and one GIN index created and explained
- [ ] At least one window function query written (e.g., rank cities by population within state)
- [ ] Row-Level Security policy implemented (therapist sees only their clients)
- [ ] TypeScript queries using Drizzle ORM for the same operations
- [ ] Backup and restore: `pg_dump` the database, drop it, restore from the dump, verify data integrity. This is your first disaster recovery exercise.

**Key concepts**:
- Reading `EXPLAIN ANALYZE` output (Seq Scan = problem, Index Scan = good)
- B-tree vs GIN vs BRIN index — when each applies
- Partial indexes (only index rows matching a condition — much smaller)
- Window functions: `ROW_NUMBER()`, `RANK()`, `LAG()`, `SUM() OVER (...)`
- Row-Level Security as a multi-tenancy pattern
- Connection pooling with PgBouncer (why 500 app connections → 20 DB connections)
- Backup and restore: `pg_dump` (logical) vs `pg_basebackup` (physical) — know when each applies

**Local environment**: PostgreSQL in `docker-compose.yml`. `docker compose up -d` gives you a working database seeded with IBGE data. PgBouncer optional but recommended.

**Testing requirement**: TypeScript integration tests using Drizzle + the dockerized test database. Use `beforeEach` to reset data. This is the correct pattern for testing database code. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: Paste a query plan to AI and ask it to explain each node. Excellent use of AI. Do not ask AI to write your indexes — figure out what to index from the query plan yourself first.

**What to put in README**: Table of query benchmarks before/after optimization, with the index used and a plain-language explanation of why it helped.

---
