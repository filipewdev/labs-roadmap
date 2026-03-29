# Lab 14 — Docker Compose Production Patterns

**Goal**: Take any previous lab and make its Docker Compose configuration
genuinely production-worthy — resource limits, health checks, proper secrets,
smallest possible images.

**Time box**: 5 hours

**Language**: YAML + Shell (Makefile)

**Done when**:
- [ ] All services have resource limits (`memory`, `cpus`)
- [ ] All services have health checks with reasonable intervals and retries
- [ ] Secrets managed via environment variables (not hardcoded in compose file)
- [ ] `read_only: true` applied where possible
- [ ] A `Makefile` with: `make up`, `make down`, `make logs`, `make shell`
- [ ] Multi-stage Dockerfile producing a final image under 20MB (Go binary)

**Key concepts**:
- Why image size matters (pull time, attack surface, storage cost)
- Multi-stage builds and `FROM scratch` for Go binaries
- Health check vs `depends_on` (why `depends_on` alone isn't enough)
- Resource limits map directly to cgroup limits (from Lab 13)
- `.dockerignore` — what it does and why you always need it

**Testing requirement**: `docker-compose.test.yml` that runs your app's test suite inside Docker. This is how CI runs integration tests correctly.

**AI usage note**: Ask AI to review your Dockerfile: *"What layers could be merged? What's adding unnecessary size?"* This is a legitimate code-review use.

---

## Phase 7 — Security

---
