# Lab 11 — RabbitMQ: Queues, Routing + Dead Letters

**Goal**: Build an order processing pipeline where failures go to a Dead Letter
Queue instead of disappearing silently.

**Time box**: 7 hours

**Language**: Go

**Done when**:
- [ ] Producer: HTTP endpoint publishes order events to a topic exchange
- [ ] Three consumers: `inventory`, `payment`, `notification`
- [ ] Consumer failure simulation: 20% random failure rate with controlled retries
- [ ] Failed messages after 3 retries → Dead Letter Queue
- [ ] DLQ consumer logs failed messages with error context
- [ ] RabbitMQ management UI explored (queue depth, bindings, exchange types)

**Key concepts**:
- Exchanges vs queues — the routing layer most people don't understand
- Topic exchanges and routing key patterns (`order.*`, `order.created`)
- Manual ack vs auto-ack (and why auto-ack loses messages)
- Consumer prefetch (`Qos`) — why it prevents one consumer from being flooded
- Dead Letter Exchanges: where messages go when they can't be processed
- Message durability: `amqp.Persistent` (survives broker restart)

**Testing requirement**: Unit tests for message routing logic. Integration test: publish a message designed to fail 3 times and verify it appears in the DLQ.

**AI usage note**: AMQP concepts are genuinely complex. Use AI freely to clarify: "What's the difference between a fanout and topic exchange?" Understanding the concept is the goal.

---
