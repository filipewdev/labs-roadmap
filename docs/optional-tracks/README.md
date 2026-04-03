# Advanced Optional Labs

> These are extra labs for when a core lab feels too easy, or when you want
> to go deeper into a specific domain. None of them are prerequisites
> for anything else. Do them because they interest you, not because you feel obligated.
>
> Each one has a **difficulty signal**: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult).

---

## Tracks

| Track | Labs | Focus |
|-------|------|-------|
| [Rust](rust.md) | 5 labs (Rust-A to Rust-E) | CLI, HTTP server, CSV parser, WebAssembly, embedded GPIO |
| [Lua](lua.md) | 4 labs (Lua-A to Lua-D) | OpenResty, Redis scripting, ESP8266 IoT, Love2D game |
| [Other Languages](other-languages.md) | 3 labs | Elixir chat server, Python FastAPI, Zig allocator |
| [Database Deep Dive](databases.md) | 4 labs (DB-A to DB-D) | Neo4j graph, TimescaleDB time-series, ClickHouse analytics, SQLite internals |
| [Deep Dives](deep-dives.md) | 4 labs (Deep-A to Deep-D) | PostgreSQL performance, production Docker, OAuth2 provider, API versioning |
| [Advanced Testing](advanced-testing.md) | 2 labs (Test-A to Test-B) | Playwright E2E, contract/smoke/load testing |
| [Web & Data](web-data.md) | 2 labs (Data-A to Data-B) | Web scraping with Scrapy, full-text search with Meilisearch |
| [Real-Time & Networking](realtime-networking.md) | 2 labs (RT-A to RT-B) | UDP game server, WebRTC video calls |
| [Architecture Patterns](architecture-patterns.md) | 2 labs (Arch-A to Arch-B) | Event sourcing & CQRS, background jobs with BullMQ |
| [Distributed Systems](distributed-systems.md) | 3 labs (Dist-A to Dist-C) | OpenTelemetry tracing, circuit breakers & sagas, distributed locks |
| [DevOps Patterns](devops-patterns.md) | 3 labs (DevOps-A to DevOps-C) | Canary/blue-green deploys, zero-downtime migrations, multi-layer caching |
| [Language Internals](language-internals.md) | 1 lab (Lang-A) | Build a tiny compiler in Go |
| [Homelab Infrastructure](homelab-infrastructure.md) | 6 labs (Infra-A to Infra-F) | Proxmox, WireGuard, Traefik, Harbor, Portainer, home services |
| [Networking & Security](networking-security.md) | 3 labs (Net-A to Net-C) | DNS/networking, OWASP hands-on, Vault secrets management |
| [Career & Differentiator](career-differentiator.md) | 3 labs (Career-A to Career-C) | Kubernetes, Terraform, MCP server |

**Total: 47 optional labs across 15 tracks.**

---

## How to Pick Your First Advanced Lab

Answer these questions:

**"I want to understand why Rust is hyped"** → Start with [Rust-A](rust.md) (CLI tool).
It's achievable in a weekend and the borrow checker will teach you more about
memory than any book.

**"I use Nginx every day and want to master it"** → [Lua-A](lua.md) (OpenResty).
You'll understand every Kong plugin after this.

**"I want to do IoT without an Arduino"** → [Lua-C](lua.md) (ESP8266 NodeMCU).
One chip, WiFi built in, Lua scripting, real MQTT. Cheaper than Arduino + WiFi shield.

**"I want to impress Go developers"** → [Rust-B](rust.md) (Axum server).
Rewriting Lab 03 in Rust and documenting what the type system caught
that TypeScript couldn't is a genuinely impressive portfolio piece.

**"I'm curious about different concurrency models"** → [Elixir-A](other-languages.md).
The comparison between Go's CSP model and Erlang's actor model is one of the most
interesting architectural discussions in systems programming.

**"I want to self-host everything"** → Start with [Infra-A](homelab-infrastructure.md) (Proxmox), then
[Infra-B](homelab-infrastructure.md) (WireGuard). Once you have a hypervisor and VPN, every other infra lab
has a place to run. See [homelab-architecture.md](../homelab-architecture.md)
for the full vision.

**"I want to understand networking and security at a deeper level"** → Start
with [Net-A](networking-security.md) (DNS + networking). Then [Net-B](networking-security.md) (OWASP) if you want the offensive
security angle, or [Net-C](networking-security.md) (Vault) if you're more interested in the ops side.

**"I'm preparing for senior engineering role interviews"** → [Career-A](career-differentiator.md) (Kubernetes) and
[Career-B](career-differentiator.md) (Terraform) are expected knowledge. [Career-C](career-differentiator.md) (MCP) is a differentiator
that very few candidates can demonstrate today.

**"I need to build data pipelines or extract data from websites"** → [Data-A](web-data.md)
(Web Scraping). Then [Data-B](web-data.md) (Search) if you want to make that data searchable.
Together they form a complete data acquisition + search stack.

**"My tests are all unit tests and I know that's not enough"** → [Test-A](advanced-testing.md) (Playwright E2E).
Then [Test-B](advanced-testing.md) (Contract + Smoke + Load) to build the full testing pyramid. These two
labs teach you to think about testing strategy, not just test writing.

**"I want to understand how video calls and real-time multiplayer work"** → [RT-A](realtime-networking.md)
(UDP Game Server) first — it teaches the protocol foundations. Then [RT-B](realtime-networking.md) (WebRTC)
for the full video calling stack. RT-A is manageable in a weekend; RT-B is a
genuine challenge.

**"I want to think like a senior architect, not just a feature builder"** → [Arch-A](architecture-patterns.md)
(Event Sourcing) changes how you model state. [Arch-B](architecture-patterns.md) (Background Jobs) is more
immediately practical and pairs with Labs 09 and 11.

**"I want to understand how programming languages actually work"** → [Lang-A](language-internals.md)
(Tiny Compiler). This is the hardest lab in the curriculum and the most rewarding.
You'll never look at a syntax error the same way again.

**"The database is always the bottleneck and I don't know how to fix it"** → [Deep-A](deep-dives.md)
(PostgreSQL Performance). Then explore [DB-B](databases.md) (TimescaleDB) or [DB-C](databases.md) (ClickHouse)
if your data has a time-series or analytics shape.

**"I know SQL but I've never used anything else"** → [DB-A](databases.md) (Neo4j) for graph data,
[DB-D](databases.md) (SQLite) for embedded/edge. Each teaches you to think about data differently.

**"My services work individually but break when they talk to each other"** → [Dist-A](distributed-systems.md)
(OpenTelemetry) to see what's happening, then [Dist-B](distributed-systems.md) (Circuit Breakers) to
make it resilient.

**"I deploy by SSHing into the server and running git pull"** → [DevOps-A](devops-patterns.md)
(Advanced CI/CD) for safe deployments, [DevOps-B](devops-patterns.md) (Zero-Downtime Migrations)
for safe schema changes.
