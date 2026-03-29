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

**Testing requirement**: `test.sh` that: gets rejected without a token (401), gets rate-limited after 100 requests (429), gets 503 when downstream is killed. Automated assertions.

---

## Phase 8 — Shell & Linux

---
