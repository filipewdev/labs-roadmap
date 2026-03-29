# Capstone I — Therapy Scheduling Platform

> **Type**: Capstone (multi-month build, not a weekend lab)
> **Purpose**: Losângela's real production app — a landing page + scheduling system for her therapy practice
> **Stack**: TypeScript throughout (Next.js frontend, Fastify backend), with tooling from the labs integrated
> **Build after**: Labs 03, 08, 09, 11, 14, 15, 19

---

## Overview

This is not a toy project — it will be used by real clients and a real therapist.
That constraint forces you to think about security, privacy, reliability, and user
experience in ways that tutorial projects never do.

**Core features:**
1. Public landing page for Losângela's practice
2. Client self-scheduling (book, reschedule, cancel)
3. Automated reminders (WhatsApp + email + SMS) 24h before each session
4. Per-session notes and therapeutic report editor (private, therapist-only)
5. Admin dashboard for Losângela (schedule overview, client list, reports)
6. Full observability (Prometheus + Grafana from Lab 06)
7. Deployed on a VPS behind Nginx (from Lab 05) with SSL

**What makes this portfolio-worthy:**
- It's live, in production, solving a real problem
- It integrates async messaging (reminders via RabbitMQ), observability, Nginx, n8n, and AI (optional session note assistant)
- It handles LGPD compliance — a real constraint that demonstrates professional maturity
- BrasilAPI used for national holiday detection (auto-block scheduling on holidays) and CEP lookup

---

## Architecture

```
Internet
    │
    │ :443 HTTPS
    ▼
┌────────────────────────────────────────────────────────────────┐
│                    NGINX (Lab 05)                               │
│  /              → Next.js (landing + booking UI)               │
│  /api/          → Fastify API                                  │
│  /admin/        → Next.js admin dashboard (auth required)      │
└──────┬──────────────────────────────┬───────────────────────────┘
       │                              │
       ▼                              ▼
┌─────────────────┐           ┌─────────────────┐
│   Next.js App   │           │  Fastify API    │
│   (TypeScript)  │◄──REST────┤  (TypeScript)   │
│                 │           │                 │
│ / landing page  │           │ /appointments   │
│ /book     UI    │           │ /sessions       │
│ /admin    dash  │           │ /clients        │
└─────────────────┘           └────────┬────────┘
                                       │
                      ┌────────────────┼──────────────┐
                      │                │              │
                      ▼                ▼              ▼
              ┌──────────────┐ ┌─────────────┐ ┌──────────────┐
              │  PostgreSQL  │ │    Redis    │ │  RabbitMQ    │
              │  (primary    │ │  (sessions, │ │  (reminder   │
              │   data)      │ │   cache)    │ │   queue)     │
              └──────────────┘ └─────────────┘ └──────┬───────┘
                                                      │
                                            ┌─────────▼─────────┐
                                            │  Reminder Worker  │
                                            │  (TypeScript)     │
                                            └─────────┬─────────┘
                                                      │
                                     ┌────────────────┼───────────────┐
                                     │                │               │
                                     ▼                ▼               ▼
                              ┌───────────┐   ┌──────────┐   ┌────────────┐
                              │ WhatsApp  │   │  Email   │   │    SMS     │
                              │ (Z-API or │   │ (Resend) │   │ (Twilio)   │
                              │ Evolution)│   └──────────┘   └────────────┘
                              └───────────┘
```

---

## Database Schema

```sql
CREATE TABLE clients (
    id               UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name             TEXT NOT NULL,
    email            TEXT UNIQUE,
    phone            TEXT,
    created_at       TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    consent_given_at TIMESTAMPTZ,
    consent_ip       INET
);

CREATE TABLE appointments (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id     UUID NOT NULL REFERENCES clients(id) ON DELETE CASCADE,
    scheduled_at  TIMESTAMPTZ NOT NULL,
    duration_mins INT NOT NULL DEFAULT 50,
    status        TEXT NOT NULL DEFAULT 'scheduled',
    location      TEXT,
    notes         TEXT,
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE session_reports (
    id                 UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    appointment_id     UUID NOT NULL REFERENCES appointments(id) ON DELETE CASCADE,
    content            TEXT,
    mood_rating        INT,
    topics             TEXT[],
    next_session_goals TEXT,
    created_at         TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at         TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE reminders (
    id             UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    appointment_id UUID NOT NULL REFERENCES appointments(id),
    channel        TEXT NOT NULL,
    scheduled_for  TIMESTAMPTZ NOT NULL,
    sent_at        TIMESTAMPTZ,
    status         TEXT NOT NULL DEFAULT 'pending',
    error          TEXT,
    created_at     TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE audit_log (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    action      TEXT NOT NULL,
    resource    TEXT NOT NULL,
    resource_id UUID,
    actor_id    UUID,
    ip_address  INET,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

---

## Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend | Next.js 14 (App Router) | TypeScript, SSR, familiar |
| API | Fastify | TypeScript, fast, built-in schema validation |
| ORM | Drizzle ORM | TypeScript-first, SQL-like, no magic |
| Database | PostgreSQL 16 | Reliable, LGPD-friendly, used in Lab 08 |
| Cache | Redis | Session storage, queue results, from Lab 09 |
| Queue | RabbitMQ | Reliable reminder delivery with DLQ, from Lab 11 |
| WhatsApp | Z-API or Evolution API | Brazil-specific requirement |
| Email | Resend | Developer-friendly, great DX |
| SMS | Twilio | Standard, reliable |
| Reverse proxy | Nginx | From Lab 05 |
| Observability | Prometheus + Grafana + Loki | From Labs 06 + 07 |
| Automation | n8n | From Lab 19 — notification dispatch layer |
| Auth | Lab 15 patterns | JWT, RBAC, no auth shortcuts |
| CI/CD | GitHub Actions | Automated deploy on push to main |
| Hosting | Hetzner CX22 or DigitalOcean | ~€4–6/month |

---

## Reminder System

This is where everything from the labs comes together.

```
1. Client books appointment (API)
2. API creates appointment in PostgreSQL
3. A scheduled job (every 5 minutes) queries:
   SELECT * FROM reminders
   WHERE scheduled_for <= NOW() + INTERVAL '24 hours'
     AND scheduled_for > NOW() + INTERVAL '23 hours'
     AND status = 'pending'
4. For each due reminder, publish to RabbitMQ
5. Reminder Worker consumes queue, sends via WhatsApp/email/SMS
6. Worker updates reminder.status = 'sent' | 'failed'
7. Failed messages → DLQ (same pattern as Lab 11)
```

---

## LGPD Compliance Considerations

LGPD (Lei Geral de Proteção de Dados) is Brazil's GDPR equivalent.
For a therapy app handling sensitive health data, this is legally important.

1. **Consent**: Record explicit consent when clients book (timestamp + IP)
2. **Data minimization**: Only collect what's needed for scheduling
3. **Right to deletion**: `DELETE /api/clients/:id` must cascade and purge all PII
4. **Session reports**: Audit log every access to `session_reports` table
5. **No third-party analytics**: Use your own Grafana — no Google Analytics

---

## Open API Integration

**BrasilAPI** used for:
- `GET /api/holiday/v1/{year}` — auto-block scheduling on national holidays
- `GET /api/cep/v2/{cep}` — client address auto-fill from postal code

---

## Local Environment

All services run via `docker-compose.yml`. `docker compose up -d` must start:
PostgreSQL, Redis, RabbitMQ, Prometheus, Grafana, Loki, and Nginx.
The Next.js and Fastify apps run on the host during development.

A `Makefile` with `make up`, `make down`, `make logs`, `make test` standardizes the workflow (same pattern as Lab 14).

---

## Testing Strategy

This is a production app — testing is non-negotiable.

- **API**: Integration tests for all endpoints using `fastify.inject()` + `testcontainers` (PostgreSQL, Redis, RabbitMQ)
- **Reminder worker**: Unit tests for scheduling logic; integration test verifying a message flows from queue to sent status
- **RBAC**: Tests that therapist-only routes reject client tokens and vice versa
- **LGPD**: Test that `DELETE /api/clients/:id` cascades and purges all PII

**CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage). CI must pass before every merge to `main`.

---

## Development Discipline

- **Conventional commits** throughout — this repo follows the same Git discipline as every lab
- **AI as reviewer, not generator** — first attempt always written by hand; AI reviews security-sensitive code (auth, LGPD, RabbitMQ error handling)
- **Branch workflow**: feature branches → PR → CI green → merge

---

## Build Milestones

- [ ] M1: Database schema + API (appointments CRUD)
- [ ] M2: Booking UI (public-facing page)
- [ ] M3: Admin dashboard (schedule view)
- [ ] M4: Reminder worker (email first, then WhatsApp)
- [ ] M5: Session report editor (Tiptap rich text)
- [ ] M6: Landing page (polished, professional)
- [ ] M7: Observability wired in
- [ ] M8: Deploy to VPS + domain + SSL
- [ ] M9: LGPD compliance review
- [ ] M10: Go live — Losângela uses it with real clients

---

## Portfolio Statement

> "I built a production scheduling platform currently used by an active therapy
> practice, handling appointment management, multi-channel automated reminders
> via WhatsApp/email/SMS, and session documentation. Deployed on a VPS with
> Nginx, full observability via Prometheus and Grafana, async notifications via
> RabbitMQ, and LGPD-compliant data handling."
