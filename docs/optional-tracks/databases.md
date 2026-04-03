# Database Deep Dive Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core curriculum covers PostgreSQL (Lab 08), Redis (Lab 09),
> and MongoDB (Lab 10) — the three databases most developers encounter first.
> But the database landscape is far wider, and choosing the right database for
> the right problem is one of the most impactful architecture decisions you'll
> make. Each database in this track exists because relational databases are the
> wrong tool for its specific use case.

---

### DB-A — Neo4j: Graph Database for Connected Data ★

**Companion to**: Lab 08 (PostgreSQL), Lab 28 (System Design)
**Time box**: 8 hours
**Language**: TypeScript
**What**: Build a social network recommendation engine using Neo4j. Model users,
posts, friendships, and interests as a graph. Implement "people you may know"
(friends-of-friends), content recommendations (people who liked X also liked Y),
and shortest path between two users.

**Why Neo4j**: It's the most mature graph database. Cypher is the SQL of graph
databases — declarative, readable, and powerful. Used at LinkedIn (connection
recommendations), Airbnb (fraud detection), NASA (knowledge graphs), and most
fintech companies for anti-money-laundering.

**Why a graph database and not SQL**: In PostgreSQL, "find friends-of-friends-of-friends"
is a self-referential JOIN 3 levels deep — slow and unreadable. In Neo4j, it's
`MATCH (me)-[:FRIENDS*2..3]-(suggestion)`. When your data IS the relationships,
a graph database isn't an optimization — it's the correct model.

**What you'll learn**:
- Graph data model: nodes, relationships, properties, labels
- Cypher query language: MATCH, WHERE, CREATE, MERGE, RETURN
- Graph traversal patterns: shortest path, variable-length paths, path filtering
- Index-free adjacency: why graph traversals are O(1) per hop regardless of database size
- When to use a graph vs relational: the "3 JOINs" heuristic
- Graph algorithms: PageRank, community detection, centrality (built into Neo4j GDS)
- Data import: loading CSV data into graph relationships

**Done when**:
- [ ] Neo4j running in Docker with a social network dataset (500+ users, 5000+ relationships)
- [ ] TypeScript API with Neo4j driver connecting via Bolt protocol
- [ ] "People you may know": friends-of-friends who aren't already friends, ranked by mutual connections
- [ ] "Recommended content": posts liked by people similar to you (collaborative filtering via graph)
- [ ] Shortest path: find the shortest connection chain between two users
- [ ] At least one graph algorithm from GDS library (PageRank for influence scoring OR community detection)
- [ ] Performance comparison: the same "friends-of-friends" query in PostgreSQL vs Neo4j with timing
- [ ] Docker Compose: Neo4j + TypeScript API

**Testing requirement**: Integration tests for each recommendation query. Test that
the graph model correctly represents relationships (create users → add friendship →
verify traversal). Benchmark: PostgreSQL vs Neo4j for 3-hop traversal on same dataset.
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: Cypher syntax is intuitive but graph thinking is not. Use AI to
translate: *"How would I express 'find all users who are exactly 2 hops away from me
and share at least 3 interests' in Cypher?"* The data model design (what's a node vs
a relationship vs a property) should be your own work — that's the graph modeling skill.

**Key insight**: "If your WHERE clause has more than 3 JOINs and they're all
self-referential, you need a graph database."

---

### DB-B — TimescaleDB: Time-Series Data at Scale ★

**Companion to**: Lab 06 (Prometheus/Grafana), Lab 08 (PostgreSQL), Lab 24 (IoT Sensors)
**Time box**: 7 hours
**Language**: TypeScript
**What**: Build a metrics ingestion and analysis pipeline. Ingest sensor data
(or simulated IoT data), query it with time-bucketed aggregates, set up
continuous aggregates for dashboards, implement retention policies, and
compress old data. Connect to Grafana for real-time visualization.

**Why TimescaleDB**: It's a PostgreSQL extension — not a separate database.
You keep everything you know from Lab 08 (SQL, indexes, joins, transactions)
and gain time-series superpowers. No new database to learn, no new ops to manage.
Just `CREATE EXTENSION timescaledb` and your time-series queries go from seconds
to milliseconds.

**Why not just PostgreSQL**: PostgreSQL CAN store time-series data. But querying
"average temperature per hour for the last 30 days" on 100M rows is painful
with vanilla PostgreSQL. TimescaleDB's hypertables automatically partition by time,
compress old chunks, and pre-compute continuous aggregates. The same query that
takes 12 seconds in PostgreSQL takes 50ms in TimescaleDB.

**What you'll learn**:
- Hypertables: automatic time-based partitioning (chunks)
- `time_bucket()`: the time-series equivalent of GROUP BY for time ranges
- Continuous aggregates: materialized views that update automatically as new data arrives
- Compression: columnar compression for old data (10-20x storage reduction)
- Retention policies: automatically drop data older than N days
- Downsampling: keep 1-minute granularity for 7 days, 1-hour for 90 days, daily forever
- Real-time aggregation vs pre-computed: when each is appropriate

**Done when**:
- [ ] TimescaleDB running in Docker (PostgreSQL + timescaledb extension)
- [ ] Hypertable created for sensor readings (timestamp, sensor_id, value, type)
- [ ] Ingestion service: TypeScript inserts 1000+ readings per second (batch insert)
- [ ] `time_bucket` queries: average, min, max per 1min/1hour/1day buckets
- [ ] Continuous aggregate: hourly summary that auto-updates
- [ ] Compression enabled on chunks older than 7 days
- [ ] Retention policy: drop raw data older than 30 days (continuous aggregates survive)
- [ ] Grafana dashboard: real-time sensor data with time range selector
- [ ] Performance comparison: same aggregation query on vanilla PostgreSQL vs TimescaleDB
- [ ] Docker Compose: TimescaleDB + TypeScript ingestion + Grafana

**Testing requirement**: Integration tests for ingestion (insert batch, verify count),
aggregation queries (known dataset, verify computed averages), continuous aggregate
(insert new data, verify aggregate updates). Benchmark: compare query time for
30-day aggregation on 10M rows — TimescaleDB vs vanilla PostgreSQL.
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: TimescaleDB's SQL extensions are well-documented. Use AI to understand
the internals: *"How does TimescaleDB decide when to create a new chunk?"* and
*"What's the difference between a continuous aggregate and a materialized view?"*
The schema design and retention strategy should be yours.

---

### DB-C — ClickHouse: Analytics on Billions of Rows ★★

**Companion to**: Lab 08 (PostgreSQL), Lab 26 (Python ML), Lab 28 (System Design)
**Time box**: 8 hours
**Language**: Python
**What**: Build an analytics pipeline that ingests millions of rows of event data
(web analytics, e-commerce click events) and queries them in real time. Compare
query performance against PostgreSQL for the same dataset. Implement materialized
views for pre-aggregated dashboards.

**Why ClickHouse**: It's a columnar database designed for analytical queries. Where
PostgreSQL stores data row-by-row (optimized for "get one user's data"), ClickHouse
stores data column-by-column (optimized for "count all users who did X grouped by Y").
The same aggregation query that takes 30 seconds in PostgreSQL takes 200ms in
ClickHouse on 100M rows. That's not an optimization — it's a different paradigm.

**Why Python**: Analytics and data science context — pairs naturally with Lab 26.
The `clickhouse-connect` library provides clean DataFrame integration. You'll likely
use ClickHouse alongside Python data tools in real analytics pipelines.

**What you'll learn**:
- Columnar storage: why `SELECT count(*) GROUP BY category` is instant but `SELECT *` is slow
- MergeTree engine family: MergeTree, ReplacingMergeTree, AggregatingMergeTree — when each is used
- Materialized views: pre-aggregate data on insert (like a real-time ETL pipeline)
- OLAP vs OLTP: why you need PostgreSQL AND ClickHouse, not one or the other
- Batch inserts: ClickHouse hates single-row inserts — why and how to batch properly
- Approximate query processing: `uniq` vs `uniqExact`, `quantile` vs `quantileExact`
- Data types: `LowCardinality(String)` for efficient storage of repeated values
- Partition keys and ordering keys: how data layout determines query performance

**Done when**:
- [ ] ClickHouse running in Docker with 10M+ rows of event data ingested
- [ ] Python ingestion script: batch insert from CSV or generated data (100k rows/sec target)
- [ ] Analytical queries: top pages, conversion funnel, user retention cohorts
- [ ] Materialized view: real-time aggregation of events into hourly summaries
- [ ] Approximate vs exact: demonstrate `uniq` vs `uniqExact` with timing difference
- [ ] Benchmark: same 5 analytical queries on PostgreSQL vs ClickHouse with 10M rows — document the difference
- [ ] Grafana dashboard connected to ClickHouse (ClickHouse data source plugin)
- [ ] Docker Compose: ClickHouse + Python ingestion + optional Grafana

**Testing requirement**: Integration tests for ingestion pipeline (insert batch, verify
count and schema). Query correctness tests (known dataset, verify aggregation results).
Benchmark: documented with actual query times, not just "it's faster."
**CI**: `ruff` → `pytest --cov` (≥70% coverage).

**AI usage note**: ClickHouse's SQL dialect has quirks (no UPDATE, limited DELETE,
different JOIN semantics). Use AI to translate: *"How do I express this PostgreSQL
query in ClickHouse?"* The schema design (partition key, order key, engine choice)
should be your own — it determines whether queries are fast or unusable.

**Key insight**: ClickHouse is not a general-purpose database. It cannot replace
PostgreSQL for transactional workloads. But for analytics — "how many users clicked
X in the last 30 days grouped by country and device?" — it's not even close.
ClickHouse was built for this one thing, and it does it better than anything else.

---

### DB-D — SQLite: The Most Deployed Database in the World ★

**Companion to**: Lab 08 (PostgreSQL), Lab 22 (Flutter Offline Sync)
**Time box**: 6 hours
**Language**: Go
**What**: Build a CLI note-taking application backed by SQLite. Implement
full-text search (FTS5), JSON functions, WAL mode for concurrent access,
and understand why SQLite is not "a toy database" but the most widely deployed
database engine in existence — inside every smartphone, every browser,
and increasingly on production servers (Litestream, LiteFS, Turso).

**Why Go**: SQLite in Go is extremely common for CLI tools and embedded
applications. `modernc.org/sqlite` provides a pure-Go SQLite implementation
(no CGO needed — compiles anywhere Go compiles). Many production Go tools
(Litestream, rclone, Hugo) use SQLite as their storage layer.

**Why SQLite deserves its own lab**: Lab 08 teaches PostgreSQL, which is
server-based — start a server, connect clients, manage users. SQLite is the
opposite: a library linked into your application, storing everything in a
single file. This isn't a limitation — it's a feature. No server means no
network latency, no connection pooling, no authentication overhead. For
read-heavy, single-writer workloads, SQLite is often faster than PostgreSQL.

**What you'll learn**:
- WAL mode (Write-Ahead Logging): concurrent readers with one writer — and why this is usually enough
- PRAGMA tuning: `journal_mode`, `synchronous`, `cache_size`, `mmap_size` — understand each setting
- FTS5 (Full-Text Search): SQLite's built-in search engine — tokenizers, ranking, highlighting
- JSON functions: `json_extract`, `json_each`, `json_group_array` — query JSON columns
- Concurrent access patterns: multiple readers are fine, writes serialize — design for this
- Backup strategies: `.backup` command, Litestream for continuous replication to S3
- When SQLite beats PostgreSQL: embedded apps, CLI tools, edge computing, read-heavy workloads
- When PostgreSQL beats SQLite: multiple writers, complex transactions, multi-user access

**Done when**:
- [ ] CLI note app: `notes add "title" "body"`, `notes search "query"`, `notes list`, `notes export`
- [ ] FTS5: full-text search across note titles and bodies with ranking and highlighting
- [ ] JSON column: notes have a `metadata` JSON column queried with `json_extract`
- [ ] WAL mode enabled: demonstrate concurrent reads while a write is in progress
- [ ] PRAGMA tuning: benchmark with default vs tuned settings on 100k notes
- [ ] Backup: automated backup to a second file (or Litestream to local directory)
- [ ] Unit tests covering all CLI commands and search edge cases
- [ ] Written comparison: SQLite vs PostgreSQL — when each is the right choice

**Testing requirement**: Unit tests for all CLI commands. Integration test for FTS5
(create notes with known content, search, verify ranking). Test concurrent access:
read during write, verify no errors. Benchmark: insert 100k notes with and without
WAL mode + PRAGMA tuning.
**CI**: `go vet` → `golangci-lint` → `go test -cover` (≥70% coverage).

**AI usage note**: SQLite's documentation is legendary — the most thorough of any
database. Use AI to navigate it: *"What's the difference between `synchronous=NORMAL`
and `synchronous=FULL` in SQLite?"* The PRAGMA tuning decisions should be yours —
each setting is a tradeoff between durability and performance.

**Key insight**: "SQLite is not a replacement for PostgreSQL. PostgreSQL is not a
replacement for SQLite. They are different tools for different problems." Understanding
when to use each is a more valuable skill than mastering either one individually.

---
