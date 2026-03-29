# Lab 04 — WebSocket Chat (Go)

**Goal**: Build a multi-room WebSocket chat server in Go. Your first real Go program.

**Time box**: 8 hours (new language — budget extra time)

**Language**: Go

**Done when**:
- [ ] Server handles multiple named rooms (`/ws?room=general&username=filipe`)
- [ ] All clients in a room receive broadcasted messages
- [ ] Presence: joining/leaving triggers a user list update to everyone in the room
- [ ] Typing indicator: debounced notification to room when a user is typing
- [ ] Dead connections cleaned up via ping/pong heartbeat
- [ ] Simple HTML client works in browser for manual testing
- [ ] Go tests for the Hub (room management) using `testing` package

**Key concepts**:
- Go syntax (structs, interfaces, error returns, defer)
- Goroutines + channels (Go's concurrency model)
- Why the Hub runs in a single goroutine (avoids mutex on the map)
- WebSocket protocol: frames, ping/pong, close handshake
- `sync.RWMutex` — when you actually need a mutex vs when a channel is better

**Testing requirement**: Unit tests for the Hub using Go's `testing` package. Test: adding/removing clients, broadcasting to a room, rooms being cleaned up when empty. No need to test the WebSocket protocol itself — focus on your application logic.

**AI usage note**: Go will feel strange after TypeScript. When confused by a compiler error, paste it to AI and ask what it means — not how to fix it. Learn Go's type system by fighting it, not bypassing it.

**What to put in README**: "Multi-room WebSocket chat server in Go demonstrating goroutine-based concurrency, channel-driven fan-out, and ping/pong connection lifecycle management."

**Stretch**: Add in-memory message history (last 20 per room). New clients receive history on join. Test the boundary conditions.

---
