# Networking & Security Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

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
