# Career & Differentiator Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: these labs won't make or break your ability to build software,
> but they signal to senior engineering hiring teams that you operate at a senior level.
> Kubernetes and Terraform are expected knowledge at most mid-to-senior roles.
> MCP is an emerging differentiator that shows you understand the AI tooling
> ecosystem at the protocol level.

---

### Career-A — Kubernetes: The Developer's Perspective ★

**Companion to**: Lab 14 (Docker Compose), Infra-A (Proxmox)
**Time box**: 8 hours
**What**: Deploy a previous lab to a local Kubernetes cluster using `kind`.
Not cluster management — just the developer-side skills: writing manifests,
debugging pods, understanding services.

**Why this matters for senior engineering roles**: Almost every mid-to-senior job
description lists Kubernetes. You don't need to be a cluster admin, but you
need to read a `kubectl get pods` output, write a Deployment manifest, and
debug why your service isn't reachable. This lab gives you exactly that.

**What you'll learn**:
- Pods, Deployments, Services, ConfigMaps, Secrets — the core resource types
- `kubectl`: the 10 commands you'll use daily (`get`, `describe`, `logs`, `exec`, `apply`, `delete`, `port-forward`, `rollout`, `scale`, `top`)
- Declarative vs imperative: why YAML manifests beat `kubectl create`
- Service discovery: how pods find each other by name
- Health checks: liveness vs readiness probes (and why getting them wrong causes outages)
- Rolling updates and rollbacks: zero-downtime deployments

**Done when**:
- [ ] `kind` cluster running locally with at least 2 nodes
- [ ] One previous lab deployed as a Deployment with 2 replicas
- [ ] Service exposing the app (ClusterIP + `kubectl port-forward` for access)
- [ ] ConfigMap for non-secret config, Secret for credentials
- [ ] Liveness and readiness probes configured and tested (kill the app, watch it restart)
- [ ] Rolling update demonstrated: deploy a new version with zero downtime
- [ ] `kubectl rollout undo` demonstrated: rollback to previous version
- [ ] Written comparison: Docker Compose (Lab 14) vs Kubernetes — when each is appropriate

**Local environment**: `kind` (Kubernetes in Docker). No cloud account needed. `kind create cluster` and you're running.

**Testing requirement**: Shell script that asserts: deployment has 2 ready replicas, service is reachable via port-forward, rolling update completes without downtime (curl during rollout). **CI**: `kind` cluster in GitHub Actions, apply manifests, run assertions.

**AI usage note**: Kubernetes YAML is verbose. Use AI to explain fields: *"What does `terminationGracePeriodSeconds` do and what happens if I set it too low?"* Write your own manifests — copy-pasting from AI teaches you nothing about resource relationships.

---

### Career-B — Terraform: Infrastructure as Code ★★

**Companion to**: Lab 18 (Linux Admin), Infra-A (Proxmox)
**Time box**: 7 hours
**What**: Provision a real VPS from code using Terraform. Destroy it and
recreate it to prove it's reproducible. Then set up the same server you
manually configured in Lab 18 — but this time, entirely from code.

**Why this matters for senior engineering roles**: "Infrastructure as Code" is not
optional at any serious company. Clicking in a cloud console is how you get
an environment that nobody can reproduce and everyone is afraid to touch.
Terraform is the industry standard.

**What you'll learn**:
- HCL syntax: resources, variables, outputs, data sources
- Terraform workflow: `init` → `plan` → `apply` → `destroy`
- State management: what `terraform.tfstate` is and why it matters
- Provider ecosystem: Hetzner, DigitalOcean, or AWS (pick one that has a free/cheap tier)
- Modules: reusable infrastructure components
- `plan` as a safety net: see what will change before it changes

**Done when**:
- [ ] Terraform provisions a VPS with SSH key, firewall rules, and Docker installed
- [ ] `terraform destroy` tears down everything cleanly
- [ ] `terraform apply` recreates the identical environment from scratch
- [ ] At least one variable used (e.g., server size, region)
- [ ] State stored remotely (Terraform Cloud free tier or S3 backend)
- [ ] One module extracted (e.g., "hardened-vps" module reusable across projects)
- [ ] Written comparison: manual setup (Lab 18) vs Terraform — what each teaches, when each is appropriate

**Local environment**: Terraform CLI only. Cloud provider account required (Hetzner CX22 ~€4/month or DigitalOcean $4/month — destroy after the lab to avoid charges).

**Testing requirement**: `terraform plan` must show no changes after a fresh `apply` (idempotency). Shell script that SSHs into the provisioned server and asserts: Docker is running, firewall is configured, SSH is key-only. **CI**: `terraform fmt -check` → `terraform validate` in GitHub Actions (no apply in CI unless you want to pay for it).

**AI usage note**: HCL syntax is straightforward. Use AI for provider-specific questions: *"What's the Hetzner provider resource for a firewall rule?"* The architecture decisions (what to parameterize, how to structure modules) should be your own.

---

### Career-C — Build an MCP Server ★

**Companion to**: Lab 20 (AI Streaming API), Lab 08 (PostgreSQL), Lab 09 (Redis)
**Time box**: 6 hours
**What**: Build a Model Context Protocol (MCP) server that exposes your lab
data as tools for AI assistants. When an AI asks "what are the top 5 cities
by population?", your MCP server queries your Lab 08 PostgreSQL database
and returns the answer.

**Why this is a differentiator**: MCP is the emerging standard for connecting
AI models to external data and tools. Building an MCP server shows you understand
the AI tooling ecosystem at the protocol level — not just as a user of ChatGPT,
but as someone who can extend it. Very few developers can do this today.

**What you'll learn**:
- MCP protocol: tools, resources, and prompts
- How AI assistants discover and call external tools
- JSON-RPC transport layer (stdio and HTTP+SSE)
- Schema design for tool parameters (so the AI knows how to call your tool)
- Error handling for AI tool calls (the AI needs useful error messages, not stack traces)
- Security: input validation on tool parameters (the AI sends untrusted input)

**Done when**:
- [ ] MCP server running with at least 3 tools exposed
- [ ] Tool 1: Query PostgreSQL (e.g., "top N cities by population in state X")
- [ ] Tool 2: Read/write Redis cache (e.g., "get current exchange rate" using Lab 09's cache)
- [ ] Tool 3: A domain-specific tool of your choice (e.g., validate a CPF, check appointment availability)
- [ ] Server works with Claude Code or another MCP-compatible client
- [ ] Input validation on all tool parameters (reject malicious or malformed inputs)
- [ ] Written explanation: what MCP is, why it exists, and how it differs from function calling

**Local environment**: `docker-compose.yml` with PostgreSQL and Redis (reuse from Labs 08/09). MCP server runs on the host.

**Testing requirement**: Integration tests for each tool: valid input returns expected output, invalid input returns a structured error. Test that the MCP server responds correctly to the JSON-RPC protocol. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: You're building AI infrastructure — use AI freely here. Ask it to test your MCP server by calling it. The protocol understanding is yours; the implementation can benefit from AI review.

---
