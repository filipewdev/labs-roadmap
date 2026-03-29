# Lab 05 — Nginx as a Reverse Proxy

**Goal**: Put your Lab 04 Go server behind Nginx with SSL, rate limiting, and
proper WebSocket proxying.

**Time box**: 5 hours

**Language**: Nginx config + Shell

**Done when**:
- [ ] Nginx serves Lab 04's chat server at `https://localhost`
- [ ] Self-signed cert configured (Let's Encrypt for real domain, optional)
- [ ] WebSocket connections work through Nginx (requires specific headers)
- [ ] Rate limiting: 10 req/s per IP, 429 on breach
- [ ] Security headers set (HSTS, X-Frame-Options, X-Content-Type-Options)
- [ ] Nginx access logs in JSON format

**Key concepts**:
- `proxy_pass` and upstream blocks
- The two headers that make WebSockets work through a proxy (`Upgrade`, `Connection`)
- `limit_req_zone` and burst sizing
- `proxy_read_timeout` — why the default kills WebSocket connections
- SSL/TLS configuration (modern cipher suites, TLS 1.2+ only)

**Local environment**: Nginx and the Lab 04 Go server in `docker-compose.yml`. `docker compose up -d` gives you the full proxy setup for testing.

**Testing requirement**: Write a `test.sh` script that verifies: rate limiting fires at expected threshold, WebSocket upgrade succeeds through the proxy, HTTP redirects to HTTPS. Automated assertions, not manual checking. **CI**: run `test.sh` in GitHub Actions after `docker compose up -d`.

**AI usage note**: Nginx config documentation is dense. Ask AI: *"Explain what `limit_req zone=api burst=20 nodelay` does — specifically what 'burst' and 'nodelay' mean."* Understanding directives is fine AI use.

**What to put in README**: "Production Nginx configuration for WebSocket proxying, with rate limiting, SSL termination, and security headers. All settings explained with rationale."

---

## Phase 3 — Observability

---
