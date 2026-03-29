# Lab README Template

> Copy this template for every lab. Fill it in as you go, not after.
> The best time to write what you learned is while you're still confused —
> the confusion is the evidence that you actually engaged with the material.

---

```markdown
# Lab NN — [Lab Name]

![CI](https://github.com/YOUR_USERNAME/REPO/actions/workflows/ci.yml/badge.svg)
![Coverage](https://img.shields.io/badge/coverage-XX%25-brightgreen)

> **One sentence**: what this lab is and what it demonstrates.
> Example: "A multi-room WebSocket chat server in Go demonstrating goroutine-based
> fan-out and heartbeat-driven connection lifecycle management."

---

## What I Built

[2–4 sentences describing the running system. What does it do? What can someone
do with it? Avoid listing technologies — describe behaviour.]

Example:
> A server that accepts WebSocket connections, assigns clients to named rooms,
> and broadcasts messages to all members of a room in real time. When a client
> disconnects abnormally (no close frame), the server detects it within 30 seconds
> via a ping/pong heartbeat and removes the client from the room.

---

## Architecture

[A simple ASCII diagram showing how components connect. Even 5 lines is enough.
This forces you to understand the system well enough to draw it.]

```
Browser (HTML/JS)
    │ ws://
    ▼
  Go HTTP Server :8080
    │
    ▼
  Hub (goroutine)          ← single goroutine manages all room state
  ├── Room "general"
  │   ├── Client A  ──── chan []byte ──── writePump goroutine
  │   └── Client B  ──── chan []byte ──── writePump goroutine
  └── Room "dev"
      └── Client C  ──── chan []byte ──── writePump goroutine
```

---

## Key Concepts Learned

[This is the most important section. Write it in your own words.
If you can't explain it without jargon, you don't understand it yet.
Each concept should be 2–5 sentences.]

### [Concept 1]
[Explanation in plain language]

### [Concept 2]
[Explanation in plain language]

[... 3–5 concepts per lab ...]

**Example for WebSocket lab:**

### Why the Hub runs in a single goroutine

The Hub holds a map of rooms and their clients. If multiple goroutines
modified that map concurrently, we'd have a race condition — two goroutines
could try to write to the map at the same time and corrupt it. By running
the Hub in one goroutine and having all other goroutines send *messages* to
it via channels, we serialize all state mutations. The map is always accessed
by exactly one goroutine. No mutex needed.

### The difference between buffered and unbuffered channels

An unbuffered channel blocks the sender until the receiver is ready.
If I used an unbuffered channel for the client's `send` queue, a slow
client would block the Hub — no other client in the room could receive
messages while the slow client was processing. A buffered channel (size 256)
lets the Hub keep going. If the buffer fills up (client is too slow),
we disconnect that client instead of letting it drag down everyone else.

---

## What I Would Do Differently

[Honest reflection. What would you change if you did it again?
This shows maturity and that you're actually thinking, not just executing.]

Example:
> I initially stored message history in the Room struct without any locking,
> assuming only the Hub goroutine would access it. That was correct, but I only
> realized *why* it was safe after writing the Hub logic — I should have thought
> about concurrency first. Next time I'll draw the goroutine/channel diagram
> before writing any code.

---

## AWS Equivalent

[From the AWS mapping reference — what would this be in production on AWS?
One paragraph connecting the lab tool to the managed service.]

Example:
> In AWS, the WebSocket server would be replaced by **API Gateway WebSocket APIs**,
> which manages connection state and routes messages to a Lambda function or HTTP
> backend. The Hub's room state (currently in-memory) would move to **DynamoDB**
> or **ElastiCache**, since serverless functions are stateless. The key difference:
> in this lab I manage the connection lifecycle; with API Gateway, AWS handles
> the connection management and I only write the business logic.

---

## Running Locally

```bash
# Prerequisites
docker compose up -d

# Run the server
go run ./cmd/server

# Run tests
go test ./...

# Run with race detector (catches concurrency bugs)
go test -race ./...
```

---

## Tests

[Describe what you tested and what testing decisions you made.
Not a list of test names — explain the *strategy*.]

Example:
> The Hub logic is tested with unit tests that inject clients via the Hub's
> channels directly, without any real WebSocket connections. This makes tests
> fast and deterministic. The ping/pong heartbeat is tested with a fake clock
> (time injection) so the test doesn't have to wait 30 real seconds.
> The HTML client is not tested — manual verification is sufficient for a lab.

---

## Open API Integration

[If the lab uses a real API, document it here.]

**API used**: [API name and URL]
**Why this API**: [Why it's a good fit for this lab]
**How it's used**: [Where in the code it's called and what for]
**Mocked in tests**: [Yes/No and how]

---

## Stretch Goal Completed

[If you did the stretch goal, describe what you built and what additional
thing you learned from it.]

---

## Time Spent

| Phase | Time |
|---|---|
| Setup + reading docs | Xh |
| Implementation | Xh |
| Writing tests | Xh |
| Debugging | Xh |
| README + reflection | Xh |
| **Total** | **Xh** |

[Be honest about this. It helps you plan future labs better.]
```

---

## Notes on Writing Good Concept Explanations

This section is for the person filling in the template, not for the README itself.

**The rubber duck test**: If you can't explain a concept to someone who has never
heard of it, you don't understand it well enough to write it down. Keep rewriting
until you could explain it to your wife over dinner.

**Concrete over abstract**: "Goroutines are lightweight threads" is abstract.
"I ran 10,000 goroutines and the process used 40MB of memory; 10,000 OS threads
would have used 80GB" is concrete. Use numbers, comparisons, analogies.

**The "so what" test**: After every concept explanation, ask "so what?" If you
can't answer why this matters or when you'd use it, the explanation is incomplete.

**Wrong explanations are better than none**: Write your current understanding,
even if it's incomplete. Future you will see it and correct it. A blank README
teaches nothing.

---

## CI Badge Setup

After pushing to GitHub:

1. Go to your repo → Actions → your workflow
2. Click `...` → "Create status badge"
3. Copy the markdown and paste into your README

This is a 2-minute task that makes every repo look maintained and professional.
