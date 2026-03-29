# Lab 09 — Redis: Data Structures + Caching

**Goal**: Use Redis for five different use cases, each requiring a different data
structure. Understand why each structure exists, not just how to use it.

**Time box**: 6 hours

**Language**: TypeScript

**Done when**:
- [ ] **Strings**: Cache-aside pattern for a user profile with TTL and invalidation on update
- [ ] **Sorted sets**: Leaderboard with score updates, rank queries, top-10 retrieval
- [ ] **Lists**: Activity feed — append events, retrieve latest N, trim older ones
- [ ] **Sets**: "Who liked this post" — union, intersection, difference
- [ ] **Streams**: Simple event log with consumer group acknowledgement
- [ ] One Lua script for an atomic rate limiter (increment + expire in one round trip)
- [ ] Exchange rate from Frankfurter API cached with 55-minute TTL

**Key concepts**:
- Why each data structure exists (not just how to use it)
- TTL strategies (expire vs evict — understand both)
- The cache invalidation problem (why it's hard)
- Atomic operations with Lua (why multi-command sequences aren't safe)
- Redis Streams vs Kafka (when the simpler option is sufficient)

**Testing requirement**: Integration tests using `testcontainers-node` to spin up a real Redis instance. This is the correct pattern for testing code that depends on external services.

**AI usage note**: When choosing between data structures, decide yourself first. Then ask AI: *"I chose sorted set for [use case]. What are the tradeoffs of using a hash instead?"*

---
