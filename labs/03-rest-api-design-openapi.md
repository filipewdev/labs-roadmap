# Lab 03 — REST API Design + OpenAPI

**Goal**: Build a small but complete REST API for a book collection, with OpenAPI
documentation generated from the code — not written by hand.

**Time box**: 7 hours

**Language**: TypeScript (Fastify + Zod + @fastify/swagger)

**Done when**:
- [ ] CRUD endpoints for books (`/books`, `/books/:id`)
- [ ] Request/response schemas defined with Zod — shared between validation and TypeScript types
- [ ] OpenAPI 3.0 spec auto-generated at `/docs` (Swagger UI)
- [ ] Pagination, filtering, and sorting on the list endpoint
- [ ] Error responses are typed (not just `{ message: string }`)
- [ ] Integration tests for every endpoint using Fastify's inject method
- [ ] One endpoint enriched with Open Library API data (book details on create)

**Key concepts**:
- Designing resources (nouns, not verbs in URLs)
- HTTP status code semantics (201 vs 200, 404 vs 400, 422 vs 400)
- Pagination patterns (cursor vs offset — understand both)
- OpenAPI spec structure (paths, components, schemas, responses)
- Why code-first API docs beat hand-written docs (they stay in sync)

**Testing requirement**: Use `fastify.inject()` for integration tests — no real HTTP server needed. Test: all status codes, validation rejection, pagination edge cases (empty result, last page, invalid cursor). Mock the Open Library API call. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥85% coverage).

**AI usage note**: Ask AI to review your URL design: *"I'm designing a REST API for a book collection with authors and reviews. Here's my URL structure. What REST conventions am I violating or following well?"*

**What to put in README**: "REST API with auto-generated OpenAPI 3.0 documentation, schema validation via Zod, and integration test coverage for all endpoints. Book creation enriched with Open Library metadata."

**Stretch**: Add a `/books/search` endpoint with full-text search using a simple in-memory index.

---

## Phase 2 — Real-Time & Networking

---
