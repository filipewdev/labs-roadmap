# Cost Guide

> Everything needed to complete the curriculum, with exact costs.
> The core curriculum (Labs 00-28) is almost entirely free. Hardware
> and hosting costs only appear in the IoT/deployment labs and capstones.
>
> Prices in BRL and USD/EUR where applicable. Last updated: March 2026.

---

## Summary

| Category | Cost |
|---|---|
| Core labs (00-A through 20, 27, 28) | **Free** |
| Flutter labs (21, 22) | **Free** |
| IoT labs (23, 24, 25) | ~R$350-600 hardware (one-time) |
| Capstone C1 (therapy platform) | ~R$30-50/month hosting + services |
| Capstone C2 (HomeHub) | Uses IoT hardware above + no extra cost |
| Open Source track (OS-A, OS-B, OS-C) | **Free** |
| Optional tracks | Mostly free (some need the same IoT hardware or a cheap VPS) |

---

## Core Labs — Phase by Phase

### Phase 0 — Foundations (Labs 00-A, 00-B, 00-C)

| Resource | Cost | Notes |
|---|---|---|
| Node.js, pnpm, TypeScript | Free | |
| Vitest | Free | |
| GitHub account | Free | Public repos, Actions included |
| GitHub Actions CI | Free | 2,000 min/month on free tier (public repos unlimited) |
| BrasilAPI | Free | No key, no rate limit concerns at lab scale |
| ESLint, eslint-plugin-security | Free | |
| **Total** | **Free** | |

### Phase 1 — TypeScript Depth (Labs 01, 02, 03)

| Resource | Cost | Notes |
|---|---|---|
| Fastify, Zod, @fastify/swagger | Free | npm packages |
| Open Library API | Free | No key needed |
| **Total** | **Free** | |

### Phase 2 — Real-Time & Networking (Labs 04, 05)

| Resource | Cost | Notes |
|---|---|---|
| Go compiler | Free | |
| Nginx | Free | Open source |
| OpenSSL (self-signed cert) | Free | |
| Docker (for Lab 05 compose) | Free | Docker Desktop free for personal/education use |
| **Total** | **Free** | |

### Phase 3 — Observability (Labs 06, 07)

| Resource | Cost | Notes |
|---|---|---|
| Prometheus | Free | Open source, Docker image |
| Grafana | Free | OSS version, Docker image |
| Loki + Promtail | Free | Open source, Docker image |
| Node Exporter | Free | Open source |
| **Total** | **Free** | |

### Phase 4 — Databases (Labs 08, 09, 10)

| Resource | Cost | Notes |
|---|---|---|
| PostgreSQL | Free | Docker image |
| Redis | Free | Docker image |
| MongoDB | Free | Docker image (Community Edition) |
| testcontainers-node | Free | npm package, uses Docker |
| IBGE dataset | Free | Public API |
| Frankfurter API | Free | No key needed |
| Drizzle ORM | Free | |
| **Total** | **Free** | |

### Phase 5 — Async Messaging (Labs 11, 12)

| Resource | Cost | Notes |
|---|---|---|
| RabbitMQ | Free | Docker image (with management plugin) |
| Kafka | Free | Docker image (KRaft mode) |
| Kafka UI | Free | Open source, Docker image |
| **Total** | **Free** | |

### Phase 6 — Docker Deep Dive (Labs 13, 14)

| Resource | Cost | Notes |
|---|---|---|
| Linux environment for Lab 13 | Free | WSL2 on Windows, or a Linux VM. Lab 13 requires root access for namespaces/cgroups. |
| Docker, Docker Compose | Free | |
| **Total** | **Free** | |

### Phase 7 — Security (Labs 15, 16)

| Resource | Cost | Notes |
|---|---|---|
| GitHub OAuth app | Free | Register at github.com/settings/developers |
| Kong Gateway (OSS) | Free | Docker image |
| TOTP library | Free | |
| **Total** | **Free** | |

### Phase 8 — Shell & Linux (Labs 17, 18)

| Resource | Cost | Notes |
|---|---|---|
| bats (Bash testing) | Free | |
| Backblaze B2 (Lab 17 backup) | **Free tier** | 10GB storage, 1GB/day download — more than enough |
| VPS or Raspberry Pi for Lab 18 | **See note** | Can use a local VM (free) instead of a VPS. If using a VPS: Hetzner CX22 ~€4.5/month, destroy after the lab. |
| Let's Encrypt | Free | Requires a domain for real cert (optional — self-signed is fine for learning) |
| Domain name (optional) | ~R$40-60/year | Only needed if you want a real SSL cert. Not required. |
| **Total** | **Free** (with local VM) or **~€4.5 one-time** (VPS for one weekend) | |

### Phase 9 — Automation & AI (Labs 19, 20)

| Resource | Cost | Notes |
|---|---|---|
| n8n | Free | Self-hosted in Docker (open source) |
| Anthropic API (Lab 20) | **Paid** | ~$5-10 for the lab at typical usage. No free tier, but new accounts often get initial credits. You can also use a local model (Ollama) to avoid costs, though the lab is designed around the Anthropic streaming API. |
| **Total** | **~$5-10** (Anthropic API) | |

### Phase 10 — Mobile Depth (Labs 21, 22)

| Resource | Cost | Notes |
|---|---|---|
| Flutter SDK | Free | |
| Android Studio / Xcode | Free | Xcode requires a Mac |
| flutter_bloc (Lab 21) | Free | pub.dev package |
| flutter_riverpod (Lab 22) | Free | pub.dev package |
| flutter_secure_storage | Free | |
| Drift (SQLite ORM) | Free | |
| **Total** | **Free** | |

### Phase 11 — Hardware & IoT (Labs 23, 24, 25)

This is where hardware costs appear.

| Resource | Cost | Notes |
|---|---|---|
| **Raspberry Pi 5 (4GB)** | **~R$450-550** | Used for Labs 23, 25, and Capstone C2. One-time purchase. 8GB model (~R$650) is better but not required. |
| USB SSD (120GB+) | ~R$80-120 | For Lab 23 — Pi boots from SSD, not SD card |
| **Arduino Uno/Nano** | **~R$30-50** | For Lab 24 |
| DHT22 sensor | ~R$15-25 | Temperature + humidity |
| Breadboard + jumper wires | ~R$15-20 | |
| **Pi Camera v3** | **~R$150-200** | For Lab 25 only. Can skip Lab 25 if budget is tight. |
| Heatsink + fan for Pi | ~R$20-30 | Recommended for ML inference |
| DuckDNS | Free | Dynamic DNS service |
| Backblaze B2 (backup) | Free tier | |
| **Total** | **~R$350-600** | Depends on whether you do Lab 25 (camera). All hardware is reused in Capstone C2. |

### Phase 12 — ML (Lab 26)

| Resource | Cost | Notes |
|---|---|---|
| Python, pip, venv | Free | |
| NumPy, Pandas, PyTorch | Free | |
| IBGE CSV dataset | Free | |
| Jupyter Notebook | Free | |
| **Total** | **Free** | |

### Phase 13 — API Paradigms & Architecture (Labs 27, 28)

| Resource | Cost | Notes |
|---|---|---|
| Mercurius, Pothos (GraphQL) | Free | npm packages |
| PostgreSQL (Lab 27 docker-compose) | Free | Docker image |
| Excalidraw / Mermaid (diagrams) | Free | |
| **Total** | **Free** | |

---

## Open Source Track

| Resource | Cost | Notes |
|---|---|---|
| npm registry (OS-A) | Free | Publishing is free |
| GitHub Pages (TypeDoc) | Free | |
| pub.dev registry (OS-C) | Free | Publishing is free |
| **Total** | **Free** | |

---

## Capstones

### Capstone C1 — Therapy Scheduling Platform

| Resource | Cost | Period | Notes |
|---|---|---|---|
| Hetzner CX22 VPS | ~€4.5/month (~R$25) | Ongoing | Cheapest option for production. DigitalOcean ~$6/month is an alternative. |
| Domain name | ~R$40-60/year | Annual | Needed for SSL + professional appearance |
| Let's Encrypt SSL | Free | | Auto-renews |
| Resend (email) | Free tier | | 100 emails/day — enough for a small therapy practice |
| Twilio (SMS) | ~$1/month | Ongoing | Pay per message. ~R$0.40 per SMS in Brazil. Low volume = low cost. |
| Z-API (WhatsApp) | ~R$50-80/month | Ongoing | Or use Evolution API (self-hosted, free) |
| Stripe | Free (test mode) | | 2.9% + R$0.39 per transaction in production. No monthly fee. |
| Anthropic API (optional AI assistant) | ~$5-15/month | Optional | Only if you build the session note AI assistant stretch goal |
| **Total (minimum)** | **~R$70-100/month** | | Using Evolution (free WhatsApp) + Resend free tier |
| **Total (comfortable)** | **~R$150-200/month** | | Using Z-API + Twilio + domain |

### Capstone C2 — HomeHub

| Resource | Cost | Notes |
|---|---|---|
| Raspberry Pi + accessories | Already purchased | From Labs 23-25 |
| Arduino Nano (2nd node) | ~R$30-50 | M7 milestone adds a second room |
| Additional sensors (PIR, light) | ~R$15-25 | For room 2 |
| **Total (additional)** | **~R$50-75** | Most hardware already owned from IoT labs |

---

## Optional Tracks

### Rust / Lua / Elixir / Python-A / Zig

| Resource | Cost |
|---|---|
| All compilers and runtimes | Free |
| ESP8266 NodeMCU board (Lua-C) | ~R$20-30 |
| Love2D framework (Lua-D) | Free |
| **Total** | **Free** (except Lua-C hardware) |

### Homelab Infrastructure Track

| Resource | Cost | Notes |
|---|---|---|
| Proxmox VE (Infra-A) | Free | But needs bare-metal hardware. A used mini-PC (Dell Optiplex, Lenovo ThinkCentre) with 32GB RAM: ~R$800-1500 used. |
| WireGuard (Infra-B) | Free | Runs on existing hardware |
| Traefik (Infra-C) | Free | Docker image |
| Harbor (Infra-D) | Free | Self-hosted, Docker Compose |
| Portainer (Infra-E) | Free | Community Edition |
| Jellyfin (Infra-F) | Free | Open source media server |
| External hard drive (for media/backups) | ~R$200-400 | Optional but recommended |
| **Total** | **Free** if you already have hardware, **~R$800-1500** for a dedicated mini-PC |

### Networking & Security Track

| Resource | Cost |
|---|---|
| Pi-hole + Unbound (Net-A) | Free (Docker) |
| OWASP Juice Shop (Net-B) | Free (Docker) |
| OWASP ZAP (Net-B) | Free |
| HashiCorp Vault (Net-C) | Free (open source) |
| **Total** | **Free** |

### Career & Differentiator Track

| Resource | Cost | Notes |
|---|---|---|
| kind — Kubernetes in Docker (Career-A) | Free | |
| Terraform CLI (Career-B) | Free | But provisioning a VPS costs ~€4.5 for the lab (destroy after) |
| MCP SDK (Career-C) | Free | |
| **Total** | **Free** (+ ~€4.5 one-time for Career-B VPS) |

---

## External APIs — Cost Summary

| API / Service | Auth | Free tier | Paid tier | Used in |
|---|---|---|---|---|
| BrasilAPI | None | Unlimited | N/A | 00-A, 03, 08, C1 |
| BACEN | None | Unlimited | N/A | 12, 19, 20 |
| IBGE | None | Unlimited | N/A | 08, 26 |
| ViaCEP | None | Unlimited | N/A | 00-A, 03 |
| Open Library | None | Unlimited | N/A | 03, 10 |
| Frankfurter | None | Unlimited | N/A | 09 |
| Open-Meteo | None | Unlimited (non-commercial) | N/A | 24, C2 |
| REST Countries | None | Unlimited | N/A | 01, 03 |
| NASA APIs | Free key | 1000 req/hour | N/A | 12 |
| GitHub API | Optional token | 60 req/hour (unauth), 5000 (auth) | N/A | 15, 19 |
| JSONPlaceholder | None | Unlimited | N/A | 00-A, 02 |
| OpenFDA | Optional key | 500 req/day (no key), 120k (with key) | N/A | 08, 10 |
| Open Exchange Rates | Free key | 1000 req/month | $12/month | 09, 12 |
| Backblaze B2 | Key | 10GB storage, 1GB/day download | $0.006/GB/month | 17, 23 |
| Cloudflare R2 | Token | 10GB storage, no egress fees | $0.015/GB/month | 17, 23 |
| Stripe | Test keys | Test mode: unlimited | 2.9% + fee per transaction | C1 |
| Resend | API key | 100 emails/day, 3000/month | $20/month (50k emails) | C1 |
| Twilio | SID + token | $15 trial credit | ~$0.05/SMS | C1 |
| Z-API | Token | Free trial | ~R$50-80/month | C1 |
| Evolution API | Self-hosted | Free (open source) | N/A | C1 |
| Anthropic API | API key | No free tier (credits for new accounts sometimes) | ~$3/M input tokens (Haiku) | 20, C1 (optional) |

---

## Total Budget Planner

### Minimum viable (core labs only, no IoT, no capstone hosting)

```
Total: ~$5-10 (Anthropic API for Lab 20)
```

Everything else is free. You can complete 30 of 31 core labs with zero cost,
and Lab 20 can use a local model via Ollama if you want to avoid the API cost entirely.

### Full core curriculum + one capstone (C1)

```
Anthropic API (Lab 20):     ~$5-10     (one-time)
Domain name:                ~R$50/year
VPS (Hetzner):              ~€4.5/month (~R$25/month)
WhatsApp (Evolution):       Free (self-hosted)
Email (Resend free tier):   Free
Total first year:           ~R$350-400
```

### Full curriculum including IoT + both capstones

```
Anthropic API:              ~$5-10     (one-time)
Raspberry Pi 5 (4GB):      ~R$500     (one-time)
SSD + heatsink:             ~R$120     (one-time)
Arduino + sensors:          ~R$80      (one-time)
Pi Camera v3:               ~R$180     (one-time, for Lab 25)
Domain name:                ~R$50/year
VPS:                        ~R$25/month
2nd Arduino (C2):           ~R$50      (one-time)
Total first year:           ~R$1,300-1,500
```

Most of this is hardware you keep forever and reuse in the homelab.
