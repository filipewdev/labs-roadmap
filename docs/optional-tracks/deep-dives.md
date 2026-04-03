# Deep Dives Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core labs teach you to build things correctly. These labs
> teach you to build things that survive production. Each one takes a core lab
> and asks: "what happens when this needs to handle 10x traffic, 100x data,
> or real-world failure modes?" These are the gaps between "it works on my
> machine" and "it works at 3am on a Saturday when the on-call gets paged."

---

### Deep-A — PostgreSQL Performance: EXPLAIN ANALYZE, Partitioning & Replication ★★

**Companion to**: Lab 08 (PostgreSQL), Lab 28 (System Design)
**Time box**: 9 hours
**Language**: SQL + TypeScript
**What**: Build a realistic dataset (10M+ rows), write queries that are slow,
and fix them. Master EXPLAIN ANALYZE output, create proper indexes (B-tree,
GIN, GiST, partial), implement table partitioning by date range, set up
read replicas with streaming replication, and understand connection pooling
with PgBouncer.

**Why this matters**: "The database is slow" is the #1 production incident you'll
face. Most developers respond with "add an index" or "increase the instance size."
This lab gives you the actual toolkit: read the query plan, understand why the
planner chose a sequential scan, and fix the root cause — not the symptom.

**What you'll learn**:
- Query planner internals: sequential scan vs index scan vs bitmap heap scan vs index-only scan
- EXPLAIN ANALYZE: reading actual time, rows, loops, buffers — not just the estimated plan
- Index types: B-tree (default), GIN (full-text, JSONB), GiST (geometric, range), partial indexes
- `pg_stat_statements`: finding the slowest queries in production without guessing
- Table partitioning: range partitioning by date, partition pruning, partition-wise joins
- Streaming replication: primary + read replica, replication lag monitoring
- PgBouncer: connection pooling (why 500 application connections to 50 database connections)
- VACUUM and bloat: what happens when you never vacuum, and how to fix it

**Done when**:
- [ ] Dataset: 10M+ rows in at least 2 tables with realistic distribution (not uniform random)
- [ ] 5 slow queries identified and profiled with EXPLAIN ANALYZE (full output documented)
- [ ] Each query fixed: index created OR query rewritten OR schema changed — with before/after timing
- [ ] At least 3 index types used: B-tree, GIN (for JSONB or text), and partial index
- [ ] Table partitioning: one table partitioned by date range, queries use partition pruning
- [ ] Streaming replication: primary + 1 replica, read queries routed to replica
- [ ] PgBouncer: connection pooling configured, benchmark with and without pooling under load
- [ ] `pg_stat_statements` enabled: top 10 slowest queries identified from the extension
- [ ] Performance journal: each slow query documented with EXPLAIN output, diagnosis, fix, and improvement
- [ ] Docker Compose: PostgreSQL primary + replica + PgBouncer + pgAdmin

**Testing requirement**: Integration tests for the TypeScript query layer. Shell script
benchmarks: each optimized query must be at least 10x faster than the unoptimized version.
Test replication: write to primary, read from replica, verify data appears (with acceptable lag).
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: EXPLAIN ANALYZE output is dense. Use AI to interpret: *"This query
shows a Bitmap Heap Scan with 45000 rows — is that expected for a table with 10M rows?"*
The optimization decisions (which index, whether to partition, how to rewrite) should
be yours — there's no single right answer, and the tradeoffs are the education.

---

### Deep-B — Production Docker: Multi-Stage, Distroless & Supply Chain Security ★

**Companion to**: Lab 13 (Container Internals), Lab 14 (Docker Compose)
**Time box**: 6 hours
**Language**: Dockerfile + TypeScript + Go (multi-language builds)
**What**: Take a previous lab's Docker setup and make it production-grade.
Multi-stage builds that produce minimal images, distroless base images,
BuildKit cache mounts, Trivy vulnerability scanning in CI, image signing
with cosign, and SBOM generation.

**Why this matters**: A Docker image that "works" and a Docker image that's
production-ready are very different things. Your Lab 14 images are probably
800MB-1.2GB, running as root, with known CVEs in the base image. This lab
makes them <100MB, non-root, scanned, signed, and auditable.

**What you'll learn**:
- Multi-stage builds: why your dev image is 1.2GB but production should be <100MB
- Distroless images: no shell, no package manager = smaller attack surface
- BuildKit features: cache mounts (`--mount=type=cache`), secrets, SSH forwarding
- Layer optimization: why instruction order matters for Docker build caching
- Trivy scanning: CVE detection in base images and application dependencies
- cosign: cryptographically sign your images (prove they weren't tampered with)
- SBOM (Software Bill of Materials): know exactly what's inside your image
- Docker Scout / Snyk: ongoing vulnerability monitoring for deployed images
- Non-root containers: why running as root in Docker is a security risk even with isolation

**Done when**:
- [ ] Multi-stage Dockerfile for TypeScript app: build stage (Node) → production stage (distroless)
- [ ] Multi-stage Dockerfile for Go app: build stage → `scratch` or distroless (static binary)
- [ ] Image size comparison: before (base node:20) vs after (distroless) — document the difference
- [ ] BuildKit cache mount: `node_modules` cached between builds (faster rebuilds)
- [ ] Non-root: production container runs as non-root user
- [ ] Trivy scan: zero critical/high CVEs in production image (or documented exceptions)
- [ ] cosign: image signed and signature verified before deployment
- [ ] SBOM generated: `syft` or Docker SBOM plugin produces a package list
- [ ] CI pipeline: `docker build` → `trivy scan --severity HIGH,CRITICAL` → `cosign sign`
- [ ] Written guide: "Docker Production Checklist" — the 10 things every Dockerfile should have

**Testing requirement**: Shell script that asserts: image size < 100MB (TS) / < 20MB (Go),
Trivy scan passes (no high/critical), container runs as non-root (`whoami` inside container),
cosign verification succeeds. **CI**: build → scan → sign → push.

**AI usage note**: Dockerfile optimization has many tricks. Use AI to explain:
*"Why does changing the order of COPY instructions affect build cache?"* The
security decisions (what base image, what to exclude, what CVEs to accept) should
be yours.

---

### Deep-C — Build an OAuth2 Authorization Server ★★★

**Companion to**: Lab 15 (Auth), Lab 03 (REST API)
**Time box**: 12 hours
**Language**: TypeScript
**What**: In Lab 15 you consumed OAuth2 — you were the client talking to Google
or GitHub. Now build the server. Implement the Authorization Code flow with PKCE,
token introspection, refresh token rotation, scope-based permissions, client
registration, and the consent screen. This is what Auth0, Keycloak, and
Google's identity platform do under the hood.

**Why this is ★★★**: OAuth2 is deceptively complex. The happy path looks simple:
redirect, exchange code, get token. But the security edge cases — token theft
detection, redirect URI validation, PKCE challenge verification, refresh token
rotation — are where implementations break. Building the server teaches you
what every OAuth2 security recommendation actually protects against.

**What you'll learn**:
- The full OAuth2 spec (RFC 6749) + PKCE extension (RFC 7636)
- Authorization endpoint vs token endpoint: different responsibilities, different security models
- Authorization codes: why they exist, why they're one-time-use, why they expire in 60 seconds
- PKCE (Proof Key for Code Exchange): what specific attack it prevents (authorization code interception)
- Refresh token rotation: how to detect token theft (if a rotated-out token is used, revoke everything)
- Scope design: granular (`user:read`, `user:write`) vs coarse (`user`) — tradeoffs
- Client types: public (SPA, mobile) vs confidential (server) — different security requirements
- Consent management: user grants and revokes access to specific scopes
- Token storage: encryption at rest, secure comparison, token family tracking

**Done when**:
- [ ] Authorization endpoint: renders consent screen, issues one-time authorization code
- [ ] Token endpoint: exchanges authorization code for access + refresh tokens
- [ ] PKCE: code_challenge and code_verifier validation (S256 method)
- [ ] Refresh token rotation: new refresh token on each use, old one invalidated
- [ ] Token theft detection: using a rotated-out refresh token revokes the entire token family
- [ ] Scope-based access: access token contains granted scopes, API enforces them
- [ ] Client registration: API to register OAuth clients with redirect URIs
- [ ] Redirect URI validation: exact match, no open redirects
- [ ] Token introspection endpoint: verify token validity without decoding (RFC 7662)
- [ ] A sample client app that authenticates against your OAuth2 server (prove it works end-to-end)
- [ ] Docker Compose: OAuth2 server + PostgreSQL + sample client app

**Testing requirement**: Security-focused tests are mandatory:
- PKCE: wrong code_verifier is rejected
- Authorization code replay: using a code twice fails
- Refresh token rotation: old refresh token is rejected after rotation
- Invalid redirect_uri: request with wrong redirect is rejected with error (not redirect)
- Expired tokens: access token rejected after expiry
- Scope enforcement: request to protected resource without required scope returns 403
**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥85% coverage).

**AI usage note**: The OAuth2 RFCs are dense but precise. Use AI to clarify specific
sections: *"What specific attack does PKCE prevent and how?"* Build the implementation
yourself — each security check exists for a reason you need to internalize.

---

### Deep-D — API Versioning & Backward Compatibility ★

**Companion to**: Lab 03 (REST API), Lab 27 (GraphQL)
**Time box**: 6 hours
**Language**: TypeScript
**What**: Build an API that evolves without breaking consumers. Implement three
versioning strategies (URL path `/v1/`, header-based, content negotiation) and
compare them. Then practice the hard part: making breaking changes safely using
the expand-contract pattern, deprecation headers, and consumer migration.

**Why this matters**: Building an API is Lab 03. Keeping it alive for 3 years
while 50 consumers depend on it — that's this lab. Most developers have never
had to evolve an API without breaking clients. This skill is rare and extremely
valuable in any company with public or internal APIs.

**What you'll learn**:
- Versioning strategies: URL path (`/v1/users`) vs custom header (`API-Version: 2`) vs content negotiation (`Accept: application/vnd.api.v2+json`) — tradeoffs of each
- The expand-contract pattern: add new field → migrate consumers → remove old field
- Deprecation headers: `Sunset` header (RFC 8594), `Deprecation` header
- API changelog: generate diffs from OpenAPI specs automatically
- Consumer compatibility: can the old client still work after you deploy the new version?
- Breaking vs non-breaking changes: adding a field is safe, removing or renaming is not
- Semantic versioning for APIs: when to bump major vs minor
- Feature flags for APIs: roll out new behavior gradually, not all-at-once

**Done when**:
- [ ] API with 3 versioning strategies implemented side-by-side (same API, 3 routing approaches)
- [ ] Written comparison: URL vs header vs content negotiation — when to use each
- [ ] Expand-contract demo: rename a response field safely across 3 deployments
- [ ] Deprecation headers: `Sunset` and `Deprecation` headers on deprecated endpoints
- [ ] OpenAPI diff: automated detection of breaking changes between two OpenAPI specs
- [ ] Consumer compatibility test: v1 client still works after v2 deployment
- [ ] API changelog: auto-generated from OpenAPI spec differences
- [ ] CI gate: PR fails if it introduces a breaking API change without a major version bump
- [ ] Docker Compose: API + PostgreSQL

**Testing requirement**: Key test: old client (v1) still works after v2 is deployed.
OpenAPI diff test: intentionally introduce a breaking change, verify CI catches it.
Integration tests for all 3 versioning strategies. Test deprecation: verify Sunset
header is present on deprecated endpoints.
**CI**: `tsc --noEmit` → `eslint` → `openapi-diff --fail-on-breaking` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: API evolution has clear patterns but subtle edge cases. Use AI to
explore: *"Is adding a required field to a request body a breaking change?"* (Yes.)
*"Is adding an optional field to a response body a breaking change?"* (No, usually.)
The versioning strategy decision should be yours — it depends on your consumers.

---
