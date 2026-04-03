# Real-Time & Networking Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core curriculum covers WebSockets (Lab 04) and HTTP
> (Lab 03), but the internet runs on more than TCP. UDP powers video calls,
> game servers, DNS, and real-time streaming. WebRTC is how every video
> conferencing tool works — from Google Meet to Discord. Understanding these
> protocols at the code level separates "web developer" from "software engineer."
>
> This track also connects to the Networking & Security track (Net-A for DNS/UDP
> packet inspection) and the IoT labs (Lab 24 for MQTT over UDP).

---

### RT-A — UDP Fundamentals: Build a Game Server ★★

**Companion to**: Lab 04 (WebSocket Chat), Net-A (DNS + Networking)
**Time box**: 8 hours
**Language**: Go
**What**: Build a real-time multiplayer game server using raw UDP sockets.
The game: a simple 2D arena where players move and see each other's positions
in real time. No game engine — raw sockets, binary protocol, and tick-based
state synchronization.

**Why Go**: Go's `net` package gives you clean, low-level UDP access without
C's footguns. Goroutines map naturally to "one per connected player." It's also
what game server infrastructure (Agones, Nakama) is written in.

**Why UDP instead of TCP**: TCP guarantees delivery and ordering — but that
guarantee costs latency. In a game, you'd rather have the latest position now
(even if you missed one) than wait for a retransmission. This tradeoff is the
entire lesson.

**What you'll learn**:
- UDP vs TCP: connectionless, unreliable, unordered — and why that's sometimes better
- Binary protocol design: pack player state into minimal bytes (not JSON)
- Game loop architecture: fixed tick rate (e.g., 20 ticks/sec), interpolation on client
- Packet loss handling: sequence numbers, client-side prediction, server reconciliation
- NAT traversal basics: why UDP through home routers requires hole punching or a relay
- `net.UDPConn` in Go: `ReadFromUDP`, `WriteToUDP`, deadline management

**Done when**:
- [ ] UDP server accepts connections, tracks player state at 20 ticks/second
- [ ] Binary protocol: player position encoded in <32 bytes per update
- [ ] At least 2 clients connect and see each other's positions update in real time
- [ ] Sequence numbers: client detects and handles out-of-order packets
- [ ] Server gracefully handles: player disconnect (timeout), malformed packets, duplicate packets
- [ ] Simple client: terminal-based or browser-based (WebSocket-to-UDP bridge is fine)
- [ ] Benchmark: measure latency and packet loss at 10, 50, 100 simulated players
- [ ] Written comparison: UDP vs TCP vs WebSocket — when to use each

**Testing requirement**: Unit tests for binary protocol serialization/deserialization.
Integration test: 2 clients connect, one moves, the other receives the update within
100ms. Load test: simulate 100 players, measure server tick consistency.
**CI**: `go vet` → `golangci-lint` → `go test -cover` (≥70% coverage).

**AI usage note**: Binary protocol design is where AI can help explain tradeoffs:
*"How many bytes do I need to represent position + velocity for 100 players?"*
The implementation should be yours — debugging UDP packet handling teaches you
networking at a level that no tutorial can.

---

### RT-B — WebRTC: Peer-to-Peer Video Calls ★★★

**Companion to**: Lab 04 (WebSocket Chat), RT-A (UDP Game Server)
**Time box**: 12 hours
**Language**: TypeScript (signaling server + web client)
**What**: Build a video calling application from the protocol level up.
Signaling server, STUN/TURN integration, SDP exchange, media streams,
and a working 1-on-1 video call between two browsers.

**Why this is ★★★**: WebRTC is one of the most complex web APIs. It involves
ICE candidates, STUN servers, TURN relays, SDP offer/answer, media codecs,
and NAT traversal — all happening before a single video frame is transmitted.
The complexity is the learning: you'll understand how Google Meet, Discord,
and Zoom work at the protocol level.

**What you'll learn**:
- WebRTC architecture: the entire connection lifecycle from offer to media flow
- Signaling: the server's only job is introducing peers (WebSocket for signaling)
- SDP (Session Description Protocol): what the offer/answer contains and why
- ICE (Interactive Connectivity Establishment): how peers find each other through NATs
- STUN: discovering your public IP from behind a NAT
- TURN: relay server for when direct connection is impossible (symmetric NATs)
- Media streams: `getUserMedia`, video/audio tracks, codec negotiation
- Connection quality: monitoring bitrate, packet loss, and resolution adaptation

**Done when**:
- [ ] Signaling server: WebSocket server that exchanges SDP offers/answers and ICE candidates
- [ ] 1-on-1 video call works between two browser tabs (same machine, loopback)
- [ ] 1-on-1 video call works between two different machines on the same LAN
- [ ] STUN configured: connection works when peers are behind different NATs
- [ ] TURN fallback: connection works even through symmetric NATs (using a TURN server)
- [ ] Mute/unmute audio and video during an active call
- [ ] Connection quality displayed: current bitrate, resolution, packet loss
- [ ] Graceful disconnect: the other peer is notified when one hangs up
- [ ] README explains the full WebRTC connection lifecycle with a sequence diagram

**Local environment**: Docker Compose with signaling server. Use a public STUN
server (Google's `stun:stun.l.google.com:19302`) for development. For TURN,
use `coturn` in Docker.

**Testing requirement**: Unit tests for signaling server logic (SDP relay, room management).
Integration test: two headless browser instances (Playwright) establish a connection
and exchange a video frame. Test TURN fallback: block direct UDP, verify call
still connects via TURN. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage`
(≥75% coverage). Playwright test for the connection lifecycle.

**AI usage note**: WebRTC has many moving parts. Use AI to debug specific steps:
*"ICE gathering is stuck in 'checking' state — what does that mean?"* But build
the signaling server and SDP exchange yourself — understanding the handshake is
the entire point of this lab.

**Stretch**: Add a simple screen sharing feature using `getDisplayMedia()`.

---
