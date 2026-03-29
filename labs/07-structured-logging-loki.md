# Lab 07 — Structured Logging + Loki

**Goal**: Add structured JSON logging to Lab 06's server, ship logs to Loki, and
correlate a log with a metric spike using the same request ID.

**Time box**: 5 hours

**Language**: Go + YAML

**Done when**:
- [ ] All logs are structured JSON using `log/slog` (Go's built-in structured logger)
- [ ] Every request log includes: request ID, method, path, status, duration, user agent
- [ ] Errors include: error message, stack context, request ID
- [ ] Promtail ships logs to Loki
- [ ] Grafana Loki datasource configured — you can search logs by request ID
- [ ] A log panel added to the Lab 06 dashboard showing error logs

**Key concepts**:
- Structured logging vs printf-style (`log.Printf` vs `slog.Info`)
- Why request IDs enable correlation between metrics and logs
- Log levels and when to use each (DEBUG/INFO/WARN/ERROR)
- Loki's label model (why high-cardinality labels are expensive)
- LogQL: `{job="myapp"} | json | status >= 500`

**Testing requirement**: Write a test that captures log output and asserts a specific field is present (e.g., error logs include `request_id`). This pattern appears repeatedly in production code.

**AI usage note**: Configuration files are fine to debug with AI. The concepts — why labels matter, what cardinality means — should be your own understanding.

---

## Phase 4 — Databases

---
