# Lab 10 — MongoDB Document Model

**Goal**: Build a flexible CMS-style document store that would be genuinely awkward
in relational SQL. Understand when the document model is the right choice.

**Time box**: 6 hours

**Language**: TypeScript

**Done when**:
- [ ] Articles with flexible block types (text, image, code, embed) stored as documents
- [ ] Aggregation pipeline: top authors by views, monthly publish counts
- [ ] Text search index used for article search
- [ ] A written comparison: which queries were easier/harder than PostgreSQL equivalents
- [ ] TTL index used to auto-expire draft articles after 30 days

**Key concepts**:
- When to denormalize (the document model's core tradeoff)
- Aggregation pipeline stages: `$match`, `$group`, `$lookup`, `$project`, `$sort`
- Index types in MongoDB: single field, compound, text, TTL
- BSON types and their gotchas (ObjectId is not a string)
- The N+1 problem in MongoDB and how `$lookup` solves it

**Local environment**: MongoDB in `docker-compose.yml` for development. `docker compose up -d` gives you a running instance for manual exploration and Compass access.

**Testing requirement**: Integration tests with `testcontainers-node` + MongoDB (CI uses testcontainers, not the dev compose). Test the aggregation pipeline with known data and assert expected output. Test TTL index behaviour by manipulating `createdAt` dates. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: Aggregation pipeline syntax is verbose. Use AI freely for syntax help. The concepts — what each stage does — should be your own understanding.

---

## Phase 5 — Async Messaging

---
