# Lab 12 — Kafka: Topics, Partitions + Consumer Groups

**Goal**: Build a user analytics event pipeline. Understand why Kafka is
fundamentally different from a queue.

**Time box**: 8 hours

**Language**: Go (producer) + Python (consumer)

**Done when**:
- [ ] Go producer publishes `page_view` and `click` events with user ID as partition key
- [ ] Consumer group A (Go): persists raw events to PostgreSQL
- [ ] Consumer group B (Python): aggregates events in-memory, writes stats to Redis every 30s
- [ ] Multiple instances of Consumer A running — Kafka rebalances partitions
- [ ] Consumer offset reset demonstrated: replay historical events with a new group
- [ ] Kafka UI shows topic/partition/consumer lag

**Key concepts**:
- Topics are logs, not queues (the fundamental difference from RabbitMQ)
- Partitions determine parallelism (more partitions = more consumer instances)
- Consumer groups — each group has its own offset, reads independently
- Partition key — why all events for a user must go to the same partition
- Offset management — at-least-once vs at-most-once delivery
- Replay: why you can re-read from offset 0 (impossible with RabbitMQ)

**Local environment**: `docker-compose.yml` with Kafka (KRaft mode, no ZooKeeper), PostgreSQL, Redis, and Kafka UI. `docker compose up -d` gives you the full pipeline infrastructure.

**Testing requirement**: For the Python aggregator, write pytest tests with a mock Kafka consumer (no real broker needed). Test the aggregation logic in isolation from infrastructure. Go producer: unit tests for event serialization and partition key logic. **CI (Go)**: `go vet` → `golangci-lint` → `go test -cover`. **CI (Python)**: `ruff` → `pytest --cov` (≥70% coverage).

**AI usage note**: Draw the partition/offset model on paper first. Then ask AI: *"Check my understanding of how consumer group rebalancing works"* and explain your model to it.

---

## Phase 6 — Docker Deep Dive

---
