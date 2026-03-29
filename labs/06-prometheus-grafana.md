# Lab 06 — Prometheus + Grafana

**Goal**: Instrument an HTTP server with Prometheus metrics and build a Grafana
dashboard from scratch — no importing JSON templates.

**Time box**: 7 hours

**Language**: Go (instrumentation) + YAML (config)

**Done when**:
- [ ] Go HTTP server exposes `/metrics` with custom metrics
- [ ] At least one Counter, one Gauge, one Histogram in use
- [ ] Prometheus scrapes the server via Docker Compose
- [ ] Grafana dashboard built manually with 5+ panels
- [ ] One alert rule configured (e.g., error rate > 5% for 2 minutes)
- [ ] Node Exporter scraped — host metrics visible in Grafana

**Key concepts**:
- Counter vs Gauge vs Histogram — when to use each (common interview question)
- PromQL: `rate()`, `sum()`, `histogram_quantile()`, label selectors
- Why Prometheus pulls (scrapes) instead of being pushed to
- The scrape interval and its effect on metric resolution
- Alert rule evaluation vs notification delay

**Testing requirement**: Write a `load-test.sh` script that generates traffic against your server, then manually verify the metrics change correctly in Grafana. Document what you observed in the README.

**AI usage note**: When writing PromQL queries, try writing them yourself in the Prometheus UI first. Only use AI to explain what a query returns, not to write it.

**What to put in README**: Include a screenshot of your Grafana dashboard. Describe each metric and why you chose that metric type.

---
