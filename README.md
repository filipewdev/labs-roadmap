# Labs Roadmap

> A structured curriculum of 31 weekend engineering labs covering backend systems,
> infrastructure, databases, mobile, IoT, ML, and system design — each one
> standalone, test-first, and documented.

[![Labs completed](https://img.shields.io/github/issues-closed/filipewdev/labs-roadmap?label=labs%20completed&color=22c55e)](https://github.com/YOURNAME/labs-roadmap/issues?q=is%3Aissue+is%3Aclosed)
[![In progress](https://img.shields.io/github/issues/filipewdev/labs-roadmap?label=in%20progress&color=3b82f6)](https://github.com/YOURNAME/labs-roadmap/issues?q=is%3Aissue+is%3Aopen)

---

## What This Is

A self-directed engineering curriculum designed around one constraint: every lab
must be completable in a single weekend, produce a tested implementation, and leave
behind documentation explaining what was actually learned — not just what was built.

The scope covers the tooling used in production engineering teams at scale:
distributed messaging, observability, container internals, security protocols,
edge ML, and more. Labs are fully standalone — you can do Lab 12 without having
done Lab 07.

Each implementation lives in its own repo. This repo holds the specs, reference
docs, and progress tracking.

---

## Methodology

**Every lab ships with tests.** The testing requirement is specified per lab —
not "add some tests" but exactly what kind, against what behaviour, and with what
framework. Testing is part of the definition of done.

**Conventional commits and CI from day one.** Lab 00-C establishes the Git
discipline and GitHub Actions pipeline that all subsequent labs inherit.
Every implementation repo runs: typecheck → lint → test → coverage threshold.

**AI as a reviewer, not a generator.** Each lab card includes a specific AI usage
note. The default rule: first attempt always written by hand. AI enters as a
code reviewer or rubber duck — not a code writer.

**A lab is done when:**
1. Implementation repo exists with CI green
2. Lab README has *Key Concepts Learned* written in plain language
3. Issue is closed and board card moved to Done ✓

---

## Labs

```
Phase 0 — Foundations
  00-A  Testing Fundamentals                TypeScript · 8h
  00-B  AI as a Coding Tool                 Any · 4h
  00-C  Git Discipline + GitHub Actions     Shell/YAML · 4h

Phase 1 — TypeScript Depth
  01    Advanced TypeScript Types           TypeScript · 6h
  02    Error Handling Patterns             TypeScript · 5h
  03    REST API Design + OpenAPI           TypeScript · 7h

Phase 2 — Real-Time & Networking
  04    WebSocket Chat                      Go · 8h
  05    Nginx as a Reverse Proxy            Nginx config · 5h

Phase 3 — Observability
  06    Prometheus + Grafana                Go + YAML · 7h
  07    Structured Logging + Loki           Go + YAML · 5h

Phase 4 — Databases
  08    PostgreSQL Mastery                  SQL + TypeScript · 8h
  09    Redis: Data Structures + Caching    TypeScript · 6h
  10    MongoDB Document Model             TypeScript · 6h

Phase 5 — Async Messaging
  11    RabbitMQ: Queues, Routing + DLQ     Go · 7h
  12    Kafka: Topics, Partitions + Groups  Go + Python · 8h

Phase 6 — Docker Deep Dive
  13    Container Internals From Scratch    Go + Shell · 8h
  14    Docker Compose Production Patterns  YAML · 5h

Phase 7 — Security
  15    Auth: JWT, PKCE, RBAC + TOTP        TypeScript · 8h
  16    API Gateway + Rate Limiting         YAML · 6h

Phase 8 — Shell & Linux
  17    Bash Scripting Fundamentals         Bash · 5h
  18    Linux System Administration         Shell · 6h

Phase 9 — Automation & AI
  19    n8n Self-Hosted Automation          YAML + JS · 6h
  20    AI Streaming API                    TypeScript · 7h

Phase 10 — Mobile Depth
  21    Flutter Biometric Auth              Dart · 7h
  22    Flutter Local-First + Offline Sync  Dart + TypeScript · 10h

Phase 11 — Hardware & IoT
  23    Raspberry Pi as Production Server   Shell · 6h
  24    Arduino + MQTT Sensor Pipeline      C++ + Python · 8h
  25    Edge ML: YOLOv8 on Raspberry Pi     Python · 8h

Phase 12 — ML
  26    Python ML Fundamentals              Python · 8h

Phase 13 — API Paradigms & Architecture
  27    GraphQL API                         TypeScript · 7h
  28    System Design Practice              Diagrams + prose · 8h

── Open Source Track ────────────────────────────────────────────────────
  OS-A  Publish npm Library                 (after 00-A)
  OS-B  First OSS Contribution              (after 00-C)
  OS-C  Publish pub.dev Package             (after Lab 21)

── Capstones ────────────────────────────────────────────────────────────
  C1    Therapy Scheduling Platform         (after Labs 03 08 09 11 14 15 19)
  C2    HomeHub: IoT + Mobile               (after Labs 05 06 07 08 18 21–25)
```

---

## Suggested Starting Order

If you're following along or curious about the sequencing:

| Weeks | Lab | Reason for this order |
|---|---|---|
| 1–2 | 00-A Testing | Testing habit before any other code |
| 3 | 00-B + 00-C | AI discipline and Git hygiene set early |
| 4 | OS-A | Publish the 00-A library while it's fresh |
| 5–7 | 01, 02, 03 | TypeScript depth — directly applicable to real work |
| 8–9 | 04 WebSocket (Go) | First Go program — budget extra time |
| 10 | 05 Nginx | Wraps Lab 04 into a production-shaped system |
| 11 | 08 PostgreSQL | High applicability; jump here ahead of sequence |

After that: any order based on what your current work needs or what interests you most.

---

## Reference Docs

| Document | Contents |
|---|---|
| [Lab workflow](./docs/lab-workflow.md) | How to approach each lab from prep to review |
| [Lab index](./docs/all-lab-cards.md) | Full spec index with links to every lab |
| [AWS context](./docs/aws-context.md) | AWS managed equivalent for every tool used |
| [Open APIs](./docs/open-apis.md) | BrasilAPI, BACEN, IBGE, NASA and others — mapped to labs |
| [README template](./docs/readme-template.md) | Template for every implementation repo |
| [Advanced labs](./docs/optional-advanced-labs.md) | Optional tracks: Rust, Lua, Elixir, Zig, Homelab Infra, Networking & Security, Career |
| [Cost guide](./docs/cost-guide.md) | What's free, what costs money, total budget planner |
| [Homelab architecture](./docs/homelab-architecture.md) | Vision doc for a self-hosted personal cloud |
| [Capstone I spec](./capstones/C1-therapy-platform.md) | Therapy scheduling platform — full specification |
| [Capstone II spec](./capstones/C2-homehub.md) | HomeHub IoT + mobile — full specification |

---

## Progress

| Lab | Repo | Notes |
|---|---|---|
| — | — | Starting soon |

*Updated as labs are completed.*

---

## Stack

Home base: **TypeScript** (frontend/backend) · **Flutter** (mobile)

Introduced through labs: **Go** · **Python** · **Bash** · **C++** (Arduino)

Optional tracks: **Rust** · **Lua** · **Elixir** · **Zig**
