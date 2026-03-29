# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A **specs-only curriculum repository** — lab specifications, reference docs, capstone specs, and progress tracking. There is no application code, no build system, no tests, and no dependencies to install. Each lab's implementation lives in its own separate repo.

## Repository Structure

- `labs/` — 28 numbered lab spec files (Markdown), each a standalone weekend project (Labs 01-28)
- `phase-0/` — 3 foundational lab specs (00-A testing, 00-B AI usage, 00-C Git/CI with security gates)
- `capstones/` — 2 capstone project specs (C1: therapy platform, C2: IoT+mobile HomeHub)
- `open-source/` — 3 open-source track specs (OS-A npm, OS-B contribution + code review, OS-C pub.dev) + overview
- `docs/` — reference and guidance docs:
  - `lab-workflow.md` — how to approach each lab from prep to review (the process guide)
  - `all-lab-cards.md` — full spec index with links to every lab
  - `readme-template.md` — template for every implementation repo
  - `optional-advanced-labs.md` — optional tracks: Rust, Lua, Elixir, Zig, Homelab Infrastructure, Networking & Security, Career & Differentiator
  - `homelab-architecture.md` — vision doc for a self-hosted personal cloud
  - `aws-context.md` — AWS managed equivalent for every tool used
  - `open-apis.md` — public APIs mapped to labs
- `.github/ISSUE_TEMPLATE/` — lab journal issue template for tracking progress

## Key Conventions

- **Labs are standalone** — any lab can be done independently, in any order
- **Test-first methodology** — every lab spec defines exactly what tests are required
- **AI as reviewer, not generator** — first attempt always written by hand; AI enters as code reviewer (see `docs/lab-workflow.md` for detailed do/don't examples)
- **Conventional commits** — all implementation repos use conventional commits with CI
- **Dockerized environments** — every lab with external services (databases, brokers, etc.) requires a `docker-compose.yml` as a deliverable. `docker compose up -d` must give a working dev environment.
- **CI per language** — each lab specifies its CI pipeline and coverage threshold:
  - TypeScript: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80-85%)
  - Go: `go vet` → `golangci-lint` → `go test -cover` (≥70%)
  - Python: `ruff` → `pytest --cov` (≥70%)
  - Flutter: `dart analyze` → `flutter test --coverage`
  - Bash: `bats test/`
  - Shell/config labs: `test.sh` in CI after `docker compose up -d`
- **Security gates in CI** — Lab 00-C establishes dependency audit + security linting inherited by all labs
- **ADRs in capstones** — both C1 and C2 require Architecture Decision Records in `docs/adr/`
- **Lab completion criteria**: (1) implementation repo with CI green, (2) README with "Key Concepts Learned" in plain language, (3) GitHub issue closed

## Working in This Repo

Changes here are typically: editing/adding lab specs, updating reference docs, or updating progress tracking in README.md. There are no commands to build, lint, or test — this is pure Markdown documentation.

## Lab Spec Format

Each lab spec includes these fields (preserve this structure when editing):
- Goal, Time box, Language
- **Done when** — checklist of deliverables
- **Key concepts** — what the student should understand, not just implement
- **Local environment** — docker-compose requirements (for labs with external services)
- **Testing requirement** — specific tests required + CI pipeline + coverage threshold
- **AI usage note** — per-lab guidance on when AI helps vs hurts
- **What to put in README** — suggested summary for the implementation repo

## Flutter Labs

Labs 21 and 22 use different state management approaches by design:
- Lab 21 (Biometric Auth): **BLoC** — auth as a finite state machine
- Lab 22 (Offline Sync): **Riverpod** — reactive streams from Drift + async providers
- A written comparison of BLoC vs Riverpod is a deliverable in Lab 22

## Implementation Repos

Implementation repos (created per-lab, not in this repo) follow the template in `docs/readme-template.md` and must include: architecture diagram, "Key Concepts Learned" section, testing strategy description, "What I Would Do Differently" reflection, and time tracking table.

## Stack Context

Home base: **TypeScript** (frontend/backend) + **Flutter/Dart** (mobile, BLoC + Riverpod). Labs introduce **Go**, **Python**, **Bash**, and **C++** (Arduino). Optional tracks cover Rust, Lua, Elixir, Zig, Kubernetes, Terraform, and MCP.

## Optional Tracks (in `docs/optional-advanced-labs.md`)

Seven optional tracks exist beyond the core curriculum. These are a menu, not a checklist:
- **Rust** (5 labs): CLI, HTTP server, CSV parser, WebAssembly, embedded GPIO
- **Lua** (4 labs): OpenResty, Redis scripting, ESP8266 IoT, Love2D game
- **Elixir / Python-A / Zig** (3 labs): alternative language explorations
- **Homelab Infrastructure** (6 labs): Proxmox, WireGuard, Traefik, Harbor, Portainer, home services
- **Networking & Security** (3 labs): DNS/networking, OWASP hands-on, Vault secrets management
- **Career & Differentiator** (3 labs): Kubernetes, Terraform, MCP server
