# Lab 27 — GraphQL API

**Goal**: Rebuild Lab 03's book collection API in GraphQL. Same data, same
operations — completely different paradigm. The comparison is the education.

**Time box**: 7 hours

**Language**: TypeScript (Fastify + Mercurius + Pothos)

**Done when**:
- [ ] GraphQL schema with `Book`, `Author`, and `Review` types with relationships
- [ ] Queries: `books` (paginated, filtered), `book(id)`, `author(id)`
- [ ] Mutations: `createBook`, `updateBook`, `deleteBook`
- [ ] N+1 problem identified and solved with DataLoader
- [ ] Subscription: real-time notification when a new book is created
- [ ] Playground accessible at `/graphql` for manual exploration
- [ ] Written comparison: REST (Lab 03) vs GraphQL — when each is the better choice

**Key concepts**:
- Schema-first thinking: types, queries, mutations, subscriptions
- Resolvers and the resolver chain (how nested fields are resolved)
- N+1 problem: why naive resolvers make 100 DB queries instead of 2
- DataLoader: batching and caching at the request level
- Over-fetching and under-fetching: the problem GraphQL was designed to solve
- Fragments, variables, and aliases in client queries
- Why GraphQL is not always better than REST (caching, file uploads, simplicity)

**Local environment**: PostgreSQL in `docker-compose.yml` (same setup as Lab 08).

**Testing requirement**: Integration tests using Mercurius's `inject` method. Test: nested queries return correct data, mutations validate input, DataLoader batches correctly (assert query count). **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: GraphQL schema design has strong conventions. Ask AI to review your schema: *"Is this a well-designed GraphQL schema? What would you change for production use?"* Write the resolvers yourself — that's where the learning is.

**What to put in README**: Side-by-side comparison of the same operations in REST and GraphQL. Include query examples and a table of tradeoffs.
