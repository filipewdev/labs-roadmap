# Lab 19 — n8n Self-Hosted Automation

**Goal**: Self-host n8n and build three workflows you will actually use.

**Time box**: 6 hours

**Language**: YAML (Docker Compose) + JavaScript (code nodes)

**Done when**:
- [ ] n8n running in Docker with persistent storage and basic auth
- [ ] Workflow 1: GitHub PR opened → WhatsApp message via webhook trigger
- [ ] Workflow 2: Form submission → save to PostgreSQL + send confirmation email
- [ ] Workflow 3: Daily digest — pull from BrasilAPI/BACEN (Selic rate), format, send via WhatsApp at 8am
- [ ] Error handling on all workflows: failed executions trigger a notification

**Key concepts**:
- n8n's node model (trigger → transform → action)
- Webhook nodes vs polling triggers
- The n8n expression language (`{{ $json.field }}`)
- Credential management (secrets never in workflow definitions)
- Error workflows (n8n's equivalent of a dead letter queue)

**Testing requirement**: Manual testing with real webhooks. Document test cases in `TESTING.md`: what you sent, what you expected, what happened.

**AI usage note**: Use AI to design workflow logic: *"I want to send a WhatsApp only if a GitHub PR has ≥2 approvals. What nodes would I need?"*

---
