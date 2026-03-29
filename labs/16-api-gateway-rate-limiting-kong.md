# Lab 16 — API Gateway + Rate Limiting (Kong)

**Goal**: Put Kong in front of two services and configure rate limiting, JWT
verification, and request logging at the gateway level.

**Time box**: 6 hours

**Language**: YAML (Kong declarative config)

**Done when**:
- [ ] Kong routes `/api/v1/*` to TypeScript API and `/api/v2/*` to Go API
- [ ] JWT plugin verifies tokens at gateway level (services don't handle auth)
- [ ] Rate limiting: 100 req/hour per consumer, Redis-backed
- [ ] Request/response logging to stdout in JSON
- [ ] Kong Admin API used to inspect running configuration
- [ ] Circuit breaker behaviour demonstrated (downstream down → 503)

**Key concepts**:
- API gateways as cross-cutting concern handlers (auth, rate limiting, logging extracted from services)
- Consumer model (API keys tied to consumers, rate limits per consumer)
- Declarative config vs Admin API (infrastructure-as-code for gateways)
- Circuit breaking: fail fast when downstream is slow
- Why putting auth in the gateway is both powerful and risky

**Local environment**: `docker-compose.yml` with Kong (DB-less/declarative mode), Redis (rate limit backend), and both upstream APIs. `docker compose up -d` gives you the full gateway stack.

**Testing requirement**: `test.sh` that: gets rejected without a token (401), gets rate-limited after 100 requests (429), gets 503 when downstream is killed. Automated assertions. **CI**: run `test.sh` in GitHub Actions after `docker compose up -d`.

**AI usage note**: Kong's plugin ecosystem is large. Use AI to understand plugin configuration: *"What does the `config.fault_tolerant` option do in the rate-limiting plugin?"* Don't ask AI to generate your declarative config — write it from the docs.

---

## Phase 8 — Shell & Linux

---
