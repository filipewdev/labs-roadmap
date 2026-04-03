# Other Language Labs

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

---

### Elixir-A — Concurrent Chat Server (Pattern Match Edition) ★★

**Companion to**: Lab 04 (WebSocket Chat in Go)  
**Time box**: 10 hours  
**What**: Reimplement the WebSocket chat server in Elixir using Phoenix Channels.
Compare with the Go version: same feature set, radically different concurrency model.

**Why**: Elixir's actor model (Erlang/OTP) handles concurrency differently from
Go's goroutines. Each connection is a lightweight process with isolated state.
If one crashes, it restarts without affecting others. Go requires explicit error
handling to achieve this. Elixir makes it structural.

**What you'll learn**:
- Pattern matching (Elixir's core feature, more powerful than switch)
- Processes and message passing (OTP GenServer)
- Phoenix Channels (the Elixir WebSocket framework, used in production at Discord)
- "Let it crash" philosophy: why isolating failure is better than preventing it
- Hot code reloading (upgrade a running server without restarting connections)

**Done when**:
- [ ] Same feature set as Lab 04: rooms, presence, typing indicators, heartbeat
- [ ] Each connection is a supervised process (crashes don't affect other clients)
- [ ] ExUnit tests for the Channel logic
- [ ] README comparison: Go vs Elixir — lines of code, concurrency model, failure isolation

---

### Python-A — Async Python: FastAPI + httpx ★

**Companion to**: Lab 03 (REST API)  
**Time box**: 6 hours  
**What**: Build the book collection API in Python with FastAPI, Pydantic validation,
and async database access with `asyncpg`. Add a background task that enriches
books with Open Library data on insert.

**Why**: Python async (`async`/`await`) works very differently from JS/Go.
Understanding the event loop, `asyncio`, and why you can have async Python
code that's actually slower than sync is a common trap for Python beginners.

**Done when**:
- [ ] FastAPI with Pydantic v2 schema validation
- [ ] `asyncpg` for non-blocking PostgreSQL queries
- [ ] Background task via `asyncio` that calls Open Library API on book creation
- [ ] `pytest-asyncio` tests for all endpoints
- [ ] `hypercorn` ASGI server (or uvicorn) with proper shutdown handling

---

### Zig-A — Memory Allocator From Scratch ★★★

**Companion to**: Lab 13 (Container internals)  
**Time box**: 12 hours  
**What**: Implement a simple arena memory allocator in Zig — a language designed
as a safer C replacement. Then use it to parse a binary file format.

**Why Zig**: It's gaining traction in systems programming as a C replacement
(Bun.js is written in Zig, TigerBeetle DB is written in Zig). It has no hidden
control flow, no hidden allocations, and explicit error handling. It will make
you understand what languages like Rust and Go are actually doing for you.

**This is genuinely hard.** Only do it if you're comfortable with C memory
concepts (pointers, alignment, heap vs stack).

**Done when**:
- [ ] Arena allocator: allocate, reset (free all at once), destroy
- [ ] Uses the allocator to parse a simple binary format (define your own)
- [ ] No dynamic allocation outside the arena (verified by Zig's allocator interface)
- [ ] Zig tests (`zig test`) with AddressSanitizer enabled

---
