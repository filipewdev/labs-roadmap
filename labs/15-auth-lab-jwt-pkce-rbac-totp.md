# Lab 15 — Auth Lab: JWT, PKCE, RBAC + TOTP

**Goal**: Implement a complete authentication system from first principles —
no auth libraries, just the protocols understood from the ground up.

**Time box**: 8 hours

**Language**: TypeScript (Fastify)

**Done when**:
- [ ] Login returns access token (15min) + refresh token (30 days)
- [ ] Refresh token rotation: using a token issues a new one and revokes the old
- [ ] Token reuse detection: reusing a revoked token revokes ALL tokens for that user
- [ ] OAuth 2.0 Authorization Code + PKCE flow for GitHub login
- [ ] RBAC middleware: routes protected by role (`admin`, `user`)
- [ ] TOTP MFA: setup flow (QR code), code verification

**Key concepts**:
- Why access tokens are short-lived and refresh tokens long-lived
- RS256 (asymmetric) vs HS256 (symmetric) — why RS256 is production-correct
- PKCE: prevents authorization code interception in public clients
- Token reuse detection as a signal of theft
- RBAC: permission checks that live in the DB, not in application code

**Testing requirement**: Integration tests for every flow. Specifically: token reuse detection (use a refresh token twice, verify both become invalid), RBAC rejection, TOTP with correct and incorrect codes. Use `testcontainers` for the database.

**AI usage note**: Use AI to verify your understanding of security concepts, not to write security code. Security bugs are invisible until they're catastrophic.

---
