# Advanced Optional Labs

> These are extra labs for when a core lab feels too easy, or when you want
> to go deeper into a specific language ecosystem. None of them are prerequisites
> for anything else. Do them because they interest you, not because you feel obligated.
>
> Each one has a **difficulty signal**: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult).

---

## Rust Track

> Why Rust: memory safety without a garbage collector, the fastest compiled language
> for systems work, increasingly used in embedded (RTOS, IoT firmware) and
> infrastructure (Cloudflare workers, Linux kernel modules, AWS Firecracker).
> It has the steepest learning curve here — the borrow checker will fight you.
> That fight is the learning.

---

### Rust-A — CLI Tool: Real-World File Processor ★

**Companion to**: Lab 00-A (Testing), Lab 17 (Bash scripting)  
**Time box**: 8 hours  
**What**: Build a `logparse` CLI tool that reads Nginx JSON access logs,
filters by status code, aggregates by endpoint, and outputs a report.

**Why Rust for this**:
- Rust's `clap` crate makes CLI ergonomics excellent
- File I/O and JSON parsing without a garbage collector forces you to understand
  ownership before you need it for harder Rust
- The tool is genuinely useful (replaces the `jq` pipeline in Lab 17)

**What you'll learn**:
- Rust ownership and borrowing (the borrow checker's rules)
- Error handling with `?` operator and `anyhow` / `thiserror`
- Iterators and closures (Rust's functional-ish style)
- `serde` for JSON deserialization
- `clap` for argument parsing with auto-generated help text

**Done when**:
- [ ] `logparse --file access.log --status 5xx --top 10` works
- [ ] Output: table of top 10 paths by 5xx count with percentage
- [ ] Streams the file (doesn't load entire file into memory)
- [ ] Rust tests (`cargo test`) cover the parsing and aggregation logic
- [ ] Compiles with `--release` and is under 3MB binary

**Key Rust concept to internalize**: Why does this not compile?
```rust
let v = vec![1, 2, 3];
let first = &v[0];  // borrow
v.push(4);          // ERROR: cannot borrow `v` as mutable because it's also borrowed
println!("{}", first);
```
The compiler prevents a use-after-reallocation bug that would be a silent crash in C.

**Testing**: Unit tests for log line parsing, aggregation logic. Integration test
that processes a generated log file and asserts the top endpoint is correct.

---

### Rust-B — HTTP Server with Axum ★★

**Companion to**: Lab 03 (REST API)  
**Time box**: 10 hours  
**What**: Reimplement Lab 03's book collection REST API in Rust using Axum.
Exact same API contract — compare the two implementations.

**Why this matters**: You'll write the same API twice, in TypeScript and Rust.
The comparison is the education. What's easier? What's harder? What does the
type system catch in Rust that TypeScript misses?

**What you'll learn**:
- Axum routing and extractors (`Path`, `Query`, `Json`, `State`)
- `async`/`await` in Rust (Tokio runtime)
- Rust's type system for API design (exhaustive match on error types)
- `sqlx` for PostgreSQL queries (compile-time SQL verification — this is extraordinary)
- Why Rust's error handling via `Result<T, E>` beats TypeScript's `try/catch`

**Done when**:
- [ ] All CRUD endpoints work with identical JSON contract to Lab 03
- [ ] PostgreSQL via `sqlx` with compile-time query checking
- [ ] Proper error responses (not 500 for everything)
- [ ] Integration tests using `axum::test` (no real HTTP server)
- [ ] `cargo clippy` passes with no warnings

**Key moment**: When you use `sqlx::query_as!()`, the macro verifies your SQL
query against the actual database schema at *compile time*. A typo in a column
name is a compile error, not a runtime error at 3am in production. This is
genuinely better than anything TypeScript ORM can offer.

---

### Rust-C — Build a Memory-Safe CSV Parser ★★

**Companion to**: Lab 26 (Python ML), Lab 08 (PostgreSQL)  
**Time box**: 8 hours  
**What**: Write a CSV parser from scratch (no `csv` crate for the core parser).
Then use it to parse the IBGE municipalities dataset and load it into PostgreSQL.

**Why**: Writing a parser in Rust teaches you why memory layout matters,
how string slicing without copying works (zero-copy parsing with lifetimes),
and how Rust's ownership model enables performance patterns impossible in GC languages.

**Done when**:
- [ ] Custom parser handles: quoted fields, escaped quotes, multi-line fields
- [ ] Zero-copy: parsed fields are `&str` slices of the original input, not `String` copies
- [ ] Benchmarked against the `csv` crate using `criterion`
- [ ] Loads IBGE data into PostgreSQL via `sqlx`
- [ ] Property-based tests using `proptest` (generate random CSV and verify round-trip)

---

### Rust-D — WebAssembly Module ★★

**Companion to**: Lab 01 (TypeScript Types), Lab 04 (WebSocket)  
**Time box**: 8 hours  
**What**: Write a high-performance text processing module in Rust, compile it to
WebAssembly, and call it from your TypeScript code. Use case: fast CPF batch
validation for a form that validates 10,000 CPFs server-side in a bulk import.

**Done when**:
- [ ] Rust crate compiles to `.wasm` via `wasm-pack`
- [ ] TypeScript calls the Wasm module (no JS in the hot path)
- [ ] Benchmarked: Wasm vs pure TypeScript for 100k CPF validations
- [ ] Tests in both Rust (`cargo test`) and TypeScript (testing the Wasm interface)

**The result you should see**: Wasm is 5–15x faster for CPU-bound tasks
like this. The benchmark result becomes part of your README and is a
concrete, credible performance claim.

---

### Rust-E — Embedded: GPIO Control on Raspberry Pi ★★★

**Companion to**: Lab 23 (Raspberry Pi), Lab 24 (Arduino)  
**Time box**: 10 hours  
**What**: Control a GPIO pin on the Raspberry Pi using Rust instead of Python.
Turn an LED on and off from a REST API written in Rust (Axum).

**Why this is ★★★**: Cross-compilation (compile on laptop, run on ARM Pi),
`unsafe` Rust for hardware register access, and understanding why embedded
Rust is displacing C in new firmware projects.

**Done when**:
- [ ] Cross-compiled from x86 laptop to ARM64 Pi using `cross` tool
- [ ] Axum server runs on Pi, responds to `POST /led/{on|off}`
- [ ] GPIO controlled via `rppal` crate (safe Rust GPIO)
- [ ] One `unsafe` block — write to a GPIO register directly — with comment explaining why it's safe

---

## Lua Track

> Why Lua: it's embedded in Redis (Lua scripts), Nginx (OpenResty), Neovim,
> World of Warcraft, and many IoT firmwares (NodeMCU for ESP8266/ESP32).
> It's a tiny language (30-page manual) that takes 2 hours to learn syntax-wise.
> The value is in the specific contexts where it lives.

---

### Lua-A — Nginx Scripting with OpenResty ★

**Companion to**: Lab 05 (Nginx), Lab 16 (Kong)  
**Time box**: 6 hours  
**What**: Replace Kong with OpenResty (Nginx + Lua) and implement three
Kong-like features yourself: JWT verification, rate limiting, and request logging.

**Why**: Kong is built on OpenResty. Understanding the substrate makes you
a much better Kong (and Nginx) user. Also, many companies use OpenResty
directly instead of Kong for maximum control.

**What you'll learn**:
- Lua syntax (tables, functions, coroutines in 30 min)
- The `ngx.*` API: `ngx.req`, `ngx.resp`, `ngx.var`, `ngx.log`
- Shared memory (`lua_shared_dict`) for rate limiter state across workers
- Why Lua in Nginx is fast: runs inside Nginx worker processes, no subprocess overhead

**Done when**:
- [ ] OpenResty in Docker, proxying to Lab 03's TypeScript API
- [ ] `access_by_lua_block`: verify JWT signature using `resty.jwt`
- [ ] `access_by_lua_block`: rate limiter using `lua_shared_dict` (atomic increment)
- [ ] `log_by_lua_block`: write structured JSON access log
- [ ] All three features tested with `curl` test script

**Key insight**: Kong's rate limiting plugin is ~150 lines of Lua. After this lab
you could read that source code and understand it completely.

---

### Lua-B — Redis Scripting ★

**Companion to**: Lab 09 (Redis)  
**Time box**: 4 hours  
**What**: Write 5 atomic Redis operations as Lua scripts. These cannot be
implemented safely without Lua (or Lua-equivalent transactions).

**Scripts to implement**:

```lua
-- 1. getset_with_expiry(key, value, ttl)
-- Atomically: get old value, set new value with TTL
-- Needed when: you want the previous value AND to update atomically

-- 2. rate_limit(key, limit, window_seconds) → {allowed: bool, remaining: int}
-- Atomically: increment counter, set expiry on first call, return count

-- 3. pop_and_push(source_list, dest_list, max_items) → moved_count
-- Atomically move up to N items from one list to another
-- Needed when: implementing a work queue with a "processing" list

-- 4. sorted_set_upsert_with_decay(key, member, score, decay_factor)
-- Update score, but apply exponential decay to existing score first
-- Used for: trending algorithms (old high-score items decay over time)

-- 5. conditional_set(key, value, ttl, expected_value) → {updated: bool}
-- Set only if current value matches expected (optimistic locking)
```

**Done when**:
- [ ] All 5 scripts implemented and callable from TypeScript via `redis.eval()`
- [ ] Each script has tests that verify atomicity (call from two processes simultaneously)
- [ ] Scripts benchmarked: Lua vs equivalent multi-command approach (measure RTTs saved)

---

### Lua-C — IoT Firmware for ESP8266 / NodeMCU ★★

**Companion to**: Lab 24 (Arduino + MQTT)  
**Time box**: 8 hours  
**Hardware**: ESP8266 NodeMCU board (~R$20) — cheaper than Arduino + WiFi shield  
**What**: Replace the Arduino + Python bridge with a single ESP8266 running
Lua firmware. The ESP8266 reads the DHT22 sensor AND publishes directly to
MQTT over WiFi. No serial bridge needed.

**Why this is significant**: This is a real production IoT pattern.
The ESP8266 is used in millions of commercial IoT devices. Lua on NodeMCU
gives you scripting speed on hardware with 80KB of RAM.

**What you'll learn**:
- NodeMCU Lua API: `gpio`, `i2c`, `net`, `mqtt`, `tmr`
- The event loop model in Lua (single-threaded, callback-based — familiar from Node.js)
- WiFi connection management (reconnect on drop)
- MQTT client in Lua (directly to Mosquitto, no bridge)
- Flash memory constraints: your entire program must fit in ~256KB

**Done when**:
- [ ] ESP8266 reads DHT22 and publishes to MQTT over WiFi
- [ ] Reconnects to WiFi and MQTT automatically on drop
- [ ] Deep sleep between readings (extends battery life 100x)
- [ ] OTA (over-the-air) firmware update working
- [ ] All data from Lab 24's Grafana dashboard still populates correctly

---

### Lua-D — Game Scripting: Love2D Simple Game ★

**Companion to**: Nothing — this is a fun one  
**Time box**: 6 hours  
**What**: Build a simple 2D game in Love2D (a Lua game framework). The game:
a side-scrolling character that collects items. The twist: game logic and
physics are data-driven from a JSON config file, and you implement the
scripting API that modifies game behaviour at runtime (like WoW addons).

**Why**: Game scripting is one of the places where Lua became dominant.
Understanding why — the sandboxed execution model, the embeddability —
teaches you something about language design that applies to how you think
about scripting and DSLs in general.

**Done when**:
- [ ] Playable game: character moves, collects items, has a score
- [ ] Level data in external JSON file (no hardcoded level design)
- [ ] Modding API: a separate `mod.lua` file can change physics constants
  and add items without modifying game source
- [ ] One automated test: use Love2D's headless mode to simulate 10 frames
  and assert expected game state

---

## Other Language Optional Labs

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

## Homelab Infrastructure Track

> Why this track: the core curriculum teaches you to build systems. This track
> teaches you to host and operate them on your own hardware. The goal is a
> personal cloud — a self-hosted environment where you deploy everything you
> build, plus daily-use services (file storage, media streaming, VPN).
>
> Several core labs are foundations: Lab 05 (Nginx), Lab 14 (Docker Compose),
> Lab 16 (Kong), Lab 18 (Linux Admin), Lab 23 (Raspberry Pi). This track
> builds on top of them.
>
> Full architecture vision: [homelab-architecture.md](homelab-architecture.md)

---

### Infra-A — Proxmox: Virtualization From Bare Metal ★

**Companion to**: Lab 18 (Linux Admin), Lab 23 (Raspberry Pi)
**Time box**: 8 hours
**What**: Install Proxmox VE on bare metal (or a spare machine), create VMs
and LXC containers, and understand the hypervisor layer that sits under
everything else in a homelab.

**Why Proxmox**: It's free, runs KVM and LXC, has ZFS support built in, and
is what most serious homelabs run. Understanding the VM/container boundary
at this level makes every Docker decision clearer.

**What you'll learn**:
- KVM vs LXC containers (when each is appropriate)
- ZFS basics: pools, datasets, snapshots, scrubbing
- Network bridges and VLANs inside Proxmox
- VM templates and cloud-init (spin up a new Ubuntu VM in 30 seconds)
- Backup and restore: Proxmox Backup Server or built-in `vzdump`

**Done when**:
- [ ] Proxmox installed on bare metal (not inside a VM)
- [ ] One VM created from a cloud-init template (Ubuntu Server)
- [ ] One LXC container running a lightweight service (e.g., Pi-hole)
- [ ] ZFS pool configured with at least one snapshot taken and restored
- [ ] Network bridge configured — VMs and containers can reach each other and the LAN
- [ ] Backup of one VM completed and restore verified

---

### Infra-B — WireGuard VPN: Secure Remote Access ★

**Companion to**: Lab 18 (Linux Admin), Infra-A (Proxmox)
**Time box**: 5 hours
**What**: Set up a WireGuard VPN server on your homelab so you can securely
access all services from anywhere — phone, laptop, coffee shop WiFi.

**Why WireGuard**: It's the modern VPN standard — faster, simpler, and more
auditable than OpenVPN. The entire codebase is ~4,000 lines (OpenVPN is
~100,000). Understanding it teaches you real networking: routing tables,
NAT traversal, and key exchange.

**What you'll learn**:
- WireGuard key pairs and the cryptokey routing model
- Split tunneling vs full tunneling (route only homelab traffic vs everything)
- NAT traversal and persistent keepalives
- `iptables` / `nftables` forwarding rules for VPN traffic
- DNS resolution inside the tunnel (Pi-hole or Unbound over VPN)

**Done when**:
- [ ] WireGuard server running on a Proxmox VM or LXC container
- [ ] Phone connects via WireGuard and reaches homelab services
- [ ] Laptop connects from external network and accesses internal IPs
- [ ] Split tunnel configured: only homelab subnet routes through VPN
- [ ] DNS queries from VPN clients resolve internal hostnames
- [ ] `wg show` output understood and documented

---

### Infra-C — Traefik: Docker-Native Reverse Proxy ★

**Companion to**: Lab 05 (Nginx), Lab 14 (Docker Compose)
**Time box**: 6 hours
**What**: Replace static Nginx config with Traefik — a reverse proxy that
auto-discovers Docker services and provisions SSL certificates from
Let's Encrypt automatically.

**Why Traefik over Nginx for a homelab**: In Lab 05 you learned Nginx config
by hand — that's essential knowledge. But in a homelab with 15+ services,
manually updating Nginx config files doesn't scale. Traefik reads Docker
labels and generates routes automatically. One `docker compose up` and the
service is routable with HTTPS.

**What you'll learn**:
- Traefik entrypoints, routers, services, and middleware
- Docker provider: auto-discovery via container labels
- Let's Encrypt ACME with DNS challenge (works behind NAT, no port 80 needed)
- Middleware chains: rate limiting, basic auth, IP allowlisting
- Dashboard: real-time view of all routes and their health

**Done when**:
- [ ] Traefik running in Docker Compose with the Docker provider enabled
- [ ] At least 3 services routed via Docker labels (e.g., Grafana, n8n, a lab API)
- [ ] Let's Encrypt wildcard certificate via DNS challenge (DuckDNS or Cloudflare)
- [ ] HTTP → HTTPS redirect on all routes
- [ ] One middleware chain applied (e.g., basic auth on the Traefik dashboard)
- [ ] A new service becomes routable by adding 3 Docker labels — no config file changes

---

### Infra-D — Harbor: Private Container Registry ★★

**Companion to**: Lab 14 (Docker Compose), Infra-C (Traefik)
**Time box**: 6 hours
**What**: Run your own container registry so you can `docker push` images
built from your labs and deploy them anywhere in your homelab without
touching Docker Hub.

**Why Harbor**: It's the CNCF-graduated registry. It adds vulnerability
scanning (Trivy), RBAC, image signing, and replication — all things you
need when your homelab grows beyond "one Pi running Docker."

**What you'll learn**:
- OCI image spec: what a registry actually stores (manifests, layers, blobs)
- Trivy vulnerability scanning integrated into push workflow
- Image retention policies (auto-delete old tags, keep last N)
- Robot accounts for CI/CD (GitHub Actions pushes to your Harbor)
- Replication: mirror Docker Hub images locally (faster pulls, offline access)

**Done when**:
- [ ] Harbor running behind Traefik with a valid SSL certificate
- [ ] One lab image pushed to Harbor and pulled on a different machine
- [ ] Trivy scan runs automatically on push — vulnerabilities visible in UI
- [ ] Retention policy configured: keep only last 5 tags per repo
- [ ] GitHub Actions workflow pushes images to Harbor on tag
- [ ] Robot account used for CI (not your personal credentials)

---

### Infra-E — Portainer: Docker Fleet Management ★

**Companion to**: Lab 14 (Docker Compose), Infra-A (Proxmox)
**Time box**: 4 hours
**What**: Install Portainer to manage Docker across multiple hosts (your Pi,
a Proxmox VM, your dev machine) from a single UI.

**Why Portainer**: The CLI is great for one machine. When you have 3+ Docker
hosts, Portainer gives you a single pane of glass — deploy stacks, inspect
logs, manage volumes, and control access. Understanding the difference between
CLI-first and UI-first Docker management is a real ops skill.

**What you'll learn**:
- Portainer agent architecture (agent on each host, server on one)
- Stacks: deploying docker-compose via Portainer (GitOps-style from a repo)
- Environment management: grouping and tagging Docker hosts
- Access control: team-based permissions per environment
- App templates: one-click deploys for common services

**Done when**:
- [ ] Portainer server running with at least 2 connected environments
- [ ] One stack deployed via Portainer from a Git repository
- [ ] Container logs viewable and searchable through Portainer UI
- [ ] One app template deployed (e.g., Uptime Kuma for status monitoring)
- [ ] Understand when Portainer helps vs when `docker compose` CLI is better

---

### Infra-F — Home Services: SFTP + Media Streaming ★

**Companion to**: Lab 14 (Docker Compose), Lab 18 (Linux Admin)
**Time box**: 5 hours
**What**: Set up the daily-use services that make a homelab actually useful:
a secure file server (SFTP) and a media streaming server (Jellyfin).

**Why**: A homelab that only runs dev experiments gets neglected. One that
also serves your movies and secures your files gets used every day. Daily
usage is what teaches you operations — you notice when things break because
you actually depend on them.

**What you'll learn**:
- SFTP configuration: chroot jails, key-only auth, per-user directories
- Jellyfin/media server: transcoding, hardware acceleration, library management
- Storage planning: mount points, permissions, disk monitoring
- Backup integration: these services have real data you can't lose
- Docker volumes vs bind mounts for persistent media data

**Done when**:
- [ ] SFTP server accessible externally via VPN (not exposed to internet)
- [ ] Per-user directories with chroot isolation
- [ ] Jellyfin streaming media on LAN and via VPN
- [ ] Storage health monitoring (disk usage alerts in Grafana)
- [ ] Automated backup of SFTP data and Jellyfin config to a separate drive or cloud

---

## Networking & Security Track

> Why this track: the core curriculum teaches you to build secure applications
> (Lab 15 auth, Lab 05 SSL, Lab 18 hardening). But it never makes you *break*
> anything, never makes you look at a packet, and never teaches you how networks
> actually route traffic. These labs fill those gaps. They're especially valuable
> before or alongside the Homelab Infrastructure track — you'll debug networking
> issues constantly in a homelab and need to understand what's happening at the
> wire level.
>
> Prerequisite: Lab 18 (Linux Admin) gives you the baseline.
> Lab 00-C's security gates section introduces CI-level scanning.

---

### Net-A — DNS + Networking Fundamentals ★

**Companion to**: Lab 05 (Nginx), Lab 18 (Linux Admin), Infra-B (WireGuard)
**Time box**: 6 hours
**What**: Set up a local DNS server (Pi-hole + Unbound), understand how DNS
resolution actually works from root servers to your browser, and use
`tcpdump`/Wireshark to watch it happen.

**Why this matters**: Every service you deploy depends on DNS. When something
"doesn't connect," the problem is almost always DNS or routing. This lab gives
you the mental model to diagnose instead of guessing.

**What you'll learn**:
- DNS resolution chain: stub resolver → recursive resolver → root → TLD → authoritative
- A, AAAA, CNAME, MX, TXT, SRV records — when each is used
- `dig`, `nslookup`, `host` — querying DNS from the command line
- `tcpdump` and Wireshark: capturing and reading packets on a network interface
- Pi-hole as a DNS sinkhole: how ad-blocking works at the DNS level
- Unbound as a recursive resolver: why running your own resolver improves privacy
- Split-horizon DNS: different answers for internal vs external clients

**Done when**:
- [ ] Pi-hole running in Docker, resolving DNS for your local network
- [ ] Unbound configured as Pi-hole's upstream (no forwarding to Google/Cloudflare)
- [ ] Custom local DNS record: `mylab.local` resolves to your server's IP
- [ ] `tcpdump` capture showing a full DNS resolution (query → response)
- [ ] Wireshark used to inspect a TCP handshake + HTTP request (understand the layers)
- [ ] Written explanation: what happens between typing a URL and the page loading
- [ ] Bonus: block a domain via Pi-hole and show the DNS sinkhole response in `dig`

**Testing requirement**: Shell script that asserts: `dig mylab.local` returns expected IP, `dig blocked-domain.com` returns Pi-hole's sinkhole IP, Unbound resolves without forwarding (check `dig +trace`).

**AI usage note**: Networking has layers of abstraction. Use AI to clarify: *"What's the difference between a recursive and authoritative DNS server?"* The packet captures should be your own — looking at real traffic is the learning.

---

### Net-B — OWASP Top 10: Break It, Then Fix It ★★

**Companion to**: Lab 15 (Auth), Lab 03 (REST API)
**Time box**: 8 hours
**What**: Build a deliberately vulnerable web app (or use OWASP Juice Shop),
exploit the top vulnerabilities yourself, then write the fixes. You learn
security by breaking things, not by reading about them.

**Why hands-on exploitation**: Lab 15 teaches you to build auth correctly.
This lab teaches you *why* — by showing you what happens when you don't.
Understanding the attacker's perspective is what separates "follows security
best practices" from "understands security."

**What you'll learn**:
- **Injection** (SQL injection, command injection): how unsanitized input becomes code
- **Broken authentication**: session fixation, credential stuffing, token leakage
- **XSS** (Cross-Site Scripting): stored, reflected, DOM-based — and why CSP headers exist
- **IDOR** (Insecure Direct Object Reference): accessing other users' data by changing an ID
- **SSRF** (Server-Side Request Forgery): making the server call internal endpoints
- **Security misconfiguration**: default credentials, verbose errors, open debug endpoints
- OWASP ZAP or Burp Suite Community for automated scanning

**Done when**:
- [ ] Juice Shop (or custom app) running in Docker
- [ ] At least 5 OWASP Top 10 vulnerabilities exploited and documented
- [ ] For each exploit: what the vulnerability is, how you exploited it, and the fix
- [ ] OWASP ZAP scan run against the app — report reviewed and understood
- [ ] Fixes applied and verified (exploit no longer works after fix)
- [ ] `SECURITY.md` written: a security assessment document for the app

**Testing requirement**: For each vulnerability, write a test that proves the fix works — e.g., a SQL injection payload that returned data before the fix now returns a 400. **CI**: run OWASP ZAP baseline scan in GitHub Actions.

**AI usage note**: Use AI to understand attack vectors: *"How does a stored XSS attack work step by step?"* Do the actual exploitation yourself — the muscle memory of crafting a payload is the education.

**Optional tooling**: SonarQube or Snyk can be used for static analysis alongside
manual exploitation. They catch different things — scanners find patterns,
manual testing finds logic flaws. Understanding when each is useful is part
of the lab.

---

### Net-C — Secrets Management with Vault ★★

**Companion to**: Lab 14 (Docker Compose), Lab 15 (Auth), Infra-C (Traefik)
**Time box**: 6 hours
**What**: Replace `.env` files with HashiCorp Vault. Centralize secrets for
a multi-service Docker Compose setup where services fetch secrets at startup
instead of reading environment variables.

**Why this matters**: Every lab in this curriculum uses `.env` files. That's fine
for a single service on your laptop. But in a homelab with 15+ services across
multiple hosts, `.env` files become a liability — they get committed by accident,
they're hard to rotate, and there's no audit trail. Vault solves all three.

**What you'll learn**:
- Vault architecture: sealed/unsealed state, storage backends, auth methods
- KV secrets engine: versioned key-value storage with metadata
- AppRole auth: how services authenticate to Vault without human interaction
- Dynamic secrets: Vault generates short-lived database credentials on demand
- Audit log: every secret access is logged (who, when, what)
- Vault Agent: sidecar that handles token renewal and secret templating

**Done when**:
- [ ] Vault running in Docker (dev mode for learning, then production mode with file storage)
- [ ] KV secrets engine storing credentials for PostgreSQL, Redis, and an API key
- [ ] One service refactored: reads secrets from Vault at startup instead of `.env`
- [ ] AppRole auth configured for the service (no human token in production)
- [ ] Dynamic PostgreSQL credentials: Vault creates a DB user with 1-hour TTL
- [ ] Audit log enabled — you can see who accessed which secret and when
- [ ] Written comparison: `.env` files vs Vault — when each is appropriate

**Testing requirement**: Integration test verifying the service starts correctly with Vault-provided secrets. Test that expired dynamic credentials are rejected by the database. **CI**: Vault runs in dev mode for CI tests.

**AI usage note**: Vault's API is well-documented but the concepts (sealing, lease renewal, policy syntax) are non-obvious. Use AI to clarify: *"What happens when a Vault lease expires — does the service lose access immediately?"* The configuration should be your own work.

---

## How to Pick Your First Advanced Lab

Answer these questions:

**"I want to understand why Rust is hyped"** → Start with Rust-A (CLI tool).
It's achievable in a weekend and the borrow checker will teach you more about
memory than any book.

**"I use Nginx every day and want to master it"** → Lua-A (OpenResty).
You'll understand every Kong plugin after this.

**"I want to do IoT without an Arduino"** → Lua-C (ESP8266 NodeMCU).
One chip, WiFi built in, Lua scripting, real MQTT. Cheaper than Arduino + WiFi shield.

**"I want to impress Go developers"** → Rust-B (Axum server).
Rewriting Lab 03 in Rust and documenting what the type system caught
that TypeScript couldn't is a genuinely impressive portfolio piece.

**"I'm curious about different concurrency models"** → Elixir-A.
The comparison between Go's CSP model and Erlang's actor model is one of the most
interesting architectural discussions in systems programming.

**"I want to self-host everything"** → Start with Infra-A (Proxmox), then
Infra-B (WireGuard). Once you have a hypervisor and VPN, every other infra lab
has a place to run. See [homelab-architecture.md](homelab-architecture.md)
for the full vision.

**"I want to understand networking and security at a deeper level"** → Start
with Net-A (DNS + networking). Then Net-B (OWASP) if you want the offensive
security angle, or Net-C (Vault) if you're more interested in the ops side.
