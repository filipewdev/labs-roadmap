# Lab 20 — AI Streaming API

**Goal**: Build an API proxy for an AI model that adds streaming, caching,
rate limiting, and usage tracking — the infrastructure every AI product needs.

**Time box**: 7 hours

**Language**: TypeScript (Fastify)

**Done when**:
- [ ] POST `/chat` streams responses using SSE
- [ ] Redis cache: identical prompts return cached response (with `X-Cache-Hit` header)
- [ ] Rate limiting: 20 requests/hour per user, tracked in Redis
- [ ] PostgreSQL logs: every request stores model, tokens, cost estimate, latency
- [ ] GET `/usage` returns current user's usage stats
- [ ] React component renders streaming response with a blinking cursor
- [ ] One AI tool call: user can ask "what's the Selic rate today?" and the AI fetches from BACEN

**Key concepts**:
- SSE vs WebSockets — when each is appropriate
- Streaming from Anthropic API through your server to the browser
- Cache key design (SHA256 hash of prompt)
- Cost estimation from token counts
- Why you want a proxy layer in front of AI APIs in production

**Local environment**: `docker-compose.yml` with Redis and PostgreSQL. `docker compose up -d` gives you both services for local development. The Anthropic API key is loaded from `.env` (never committed).

**Testing requirement**: Unit tests for cache key generation and cost estimation. Integration test that verifies the rate limiter fires at the correct threshold. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: You're building an AI wrapper with AI help. Use it — ask it to review your streaming implementation specifically. Streaming bugs are subtle.

---

## Phase 10 — Mobile Depth

---
