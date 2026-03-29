# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

This is a **specs-only curriculum repository** — it contains lab specifications, reference docs, capstone specs, and progress tracking. There is no application code, no build system, no tests, and no dependencies to install. Each lab's implementation lives in its own separate repo.

## Repository Structure

- `labs/` — 26 numbered lab spec files (Markdown), each a standalone weekend project
- `phase-0/` — 3 foundational lab specs (testing, AI usage, Git/CI)
- `capstones/` — 2 capstone project specs (C1: therapy platform, C2: IoT+mobile)
- `open-source/` — 3 open-source track specs + overview
- `docs/` — reference docs (lab index, AWS context, open APIs, README template, advanced labs)
- `.github/ISSUE_TEMPLATE/` — lab journal issue template for tracking progress

## Key Conventions

- **Labs are standalone** — any lab can be done independently, in any order
- **Test-first methodology** — every lab spec defines exactly what tests are required
- **AI as reviewer, not generator** — first attempt always written by hand; AI enters as code reviewer
- **Conventional commits** — all implementation repos use conventional commits with CI
- **Lab completion criteria**: (1) implementation repo with CI green, (2) README with "Key Concepts Learned" in plain language, (3) GitHub issue closed

## Working in This Repo

Changes here are typically: editing/adding lab specs, updating reference docs, or updating progress tracking in README.md. There are no commands to build, lint, or test — this is pure Markdown documentation.

## Lab Spec Format

Each lab spec includes: objective, scope, tech stack, time estimate, deliverables, "Done When" checklist, AI usage note, and AWS equivalent reference. When editing specs, preserve this structure.

## Implementation Repos

Implementation repos (created per-lab, not in this repo) follow the template in `docs/readme-template.md` and must include: architecture diagram, "Key Concepts Learned" section, testing strategy description, "What I Would Do Differently" reflection, and time tracking table.

## Stack Context

Home base: **TypeScript** (frontend/backend) + **Flutter** (mobile). Labs introduce **Go**, **Python**, **Bash**, and **C++** (Arduino). Optional tracks cover Rust, Lua, Elixir, and Zig.
