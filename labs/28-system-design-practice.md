# Lab 28 — System Design Practice

**Goal**: Practice the system design interview format used by international
companies. Design three systems under time pressure, with written tradeoff
analysis — the skill that separates senior from mid-level in interviews.

**Time box**: 8 hours (split into three 2-hour design sessions + 2 hours writing)

**Language**: Diagrams + prose (Mermaid, Excalidraw, or paper)

**Done when**:
- [ ] Design 1: URL shortener (2 hours) — hash strategy, redirect latency, analytics
- [ ] Design 2: Real-time chat system (2 hours) — messaging, presence, history, scale
- [ ] Design 3: Rate limiter as a service (2 hours) — algorithms, distributed state, accuracy
- [ ] Each design includes: architecture diagram, data model, API contract, scaling strategy
- [ ] Each design includes: at least 3 explicit tradeoff decisions with reasoning
- [ ] ADR (Architecture Decision Record) written for one key decision per design

**Key concepts**:
- The system design interview framework: requirements → estimation → API → data model → architecture → deep dive → tradeoffs
- Back-of-envelope estimation: QPS, storage, bandwidth (order of magnitude, not precision)
- CAP theorem applied to real decisions (not just the theory)
- Horizontal vs vertical scaling — when each is appropriate
- Caching strategies: where, what, for how long, and invalidation
- Database choice: when SQL, when NoSQL, when both
- Consistency models: strong, eventual, causal — and what each costs

**The format for each design session**:

```
1. Requirements clarification      (10 min)
   Functional + non-functional. What's in scope? What's not?

2. Back-of-envelope estimation     (10 min)
   QPS, storage per year, read/write ratio

3. API design                      (15 min)
   Endpoints or interfaces. What does the caller see?

4. Data model                      (15 min)
   Tables/collections. Access patterns drive the schema.

5. High-level architecture         (20 min)
   Diagram: clients → load balancer → services → data stores

6. Deep dive on one component      (20 min)
   Pick the hardest part. Design it in detail.

7. Tradeoffs and alternatives      (10 min)
   What did you choose? What did you reject? Why?
```

**Design 1 — URL Shortener**:
Focus areas: hash collision strategy (hash vs counter), redirect latency (cache vs DB lookup), analytics pipeline (sync vs async), custom aliases, expiration.

**Design 2 — Real-Time Chat System**:
Focus areas: message delivery guarantees, presence service, message history storage, group chats at scale, read receipts, typing indicators. You've *built* this in Lab 04 — now design it for 10M concurrent users.

**Design 3 — Rate Limiter as a Service**:
Focus areas: token bucket vs sliding window vs fixed window, distributed state (Redis vs local), accuracy vs performance, rule configuration API. You've *used* rate limiters in Labs 05, 16, 20 — now design one as a standalone service.

**ADR format** (use for one key decision per design):

```markdown
# ADR-001: [Title of decision]

## Status
Accepted

## Context
[What is the problem? What constraints exist?]

## Decision
[What did you choose?]

## Alternatives considered
[What else could you have done? Why didn't you?]

## Consequences
[What are the tradeoffs? What becomes easier? What becomes harder?]
```

**Testing requirement**: There's no code to test. The deliverable is the quality of the designs and the clarity of the tradeoff analysis. Have someone else (or AI) challenge your designs — defend your decisions or update them.

**AI usage note**: After completing each design *on your own*, use AI as an interviewer: *"Here's my system design for a URL shortener. What would you challenge about it in an interview?"* Do not use AI to generate the designs — you need the practice of thinking through ambiguity yourself.

**What to put in README**: All three designs with diagrams. The ADRs. A reflection on which design was hardest and why.
