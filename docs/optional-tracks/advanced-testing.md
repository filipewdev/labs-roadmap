# Advanced Testing Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: Lab 00-A covers unit and integration testing fundamentals.
> But production systems need more: end-to-end tests that simulate real users,
> regression tests that catch regressions before they reach production, smoke
> tests that validate deployments, contract tests that prevent API breakage
> between services, and load tests that find performance cliffs before users do.
>
> Senior engineers are expected to design testing strategies, not just write
> unit tests. This track fills that gap.

---

### Test-A — End-to-End Testing with Playwright ★

**Companion to**: Lab 00-A (Testing), Lab 03 (REST API), Lab 15 (Auth)
**Time box**: 8 hours
**Language**: TypeScript (Playwright)
**What**: Write a comprehensive E2E test suite for a web application. Cover
the critical user journeys: authentication flow, CRUD operations, error states,
and cross-browser behavior. Build the test infrastructure that makes E2E tests
reliable in CI, not flaky.

**Why Playwright**: It's the current gold standard for E2E testing — faster than
Cypress, supports all browsers, has auto-waiting built in, and handles
authentication state elegantly. Microsoft maintains it actively.

**What you'll learn**:
- Page Object Model: organizing test code so it doesn't become unmaintainable
- Authentication state: login once, reuse across tests (Playwright's `storageState`)
- Auto-waiting and locator strategies: why `getByRole` beats `querySelector`
- Visual regression testing: screenshot comparison to catch unintended UI changes
- Test isolation: each test starts from a clean state (database seeding, API mocking)
- Flaky test prevention: why tests fail intermittently and how to fix it
- CI integration: Playwright in GitHub Actions with trace artifacts on failure
- Parallel execution: run tests across browsers and shards simultaneously

**Done when**:
- [ ] Playwright test suite covering at least 5 critical user journeys
- [ ] Page Object Model implemented for at least 3 pages
- [ ] Authentication: login once, reuse state across test suite (not login per test)
- [ ] Visual regression: screenshot tests for at least 2 key pages with baseline comparison
- [ ] Cross-browser: tests run on Chromium, Firefox, and WebKit
- [ ] Test data isolation: each test seeds its own data and cleans up after
- [ ] CI pipeline: Playwright runs in GitHub Actions with trace + screenshot artifacts on failure
- [ ] Parallel execution configured: tests run across 2+ shards in CI
- [ ] Flaky test detector: re-run failed tests once before marking as failure

**Testing requirement (meta)**: The lab is the tests. Measure: all tests pass on all
3 browsers, visual regression catches an intentional CSS change, CI pipeline completes
in under 5 minutes. **CI**: `playwright test --reporter=html` with artifact upload.

**AI usage note**: E2E tests are where AI review shines. Write the tests yourself,
then ask AI: *"Is this locator strategy resilient to UI refactors?"* and *"What
failure modes does this test miss?"*

---

### Test-B — Contract, Smoke & Load Testing ★★

**Companion to**: Lab 00-A (Testing), Lab 03 (REST API), Lab 11 (RabbitMQ), Test-A (E2E)
**Time box**: 8 hours
**Language**: TypeScript (Pact for contracts) + Go (k6 for load testing)
**What**: Build three testing layers that most teams skip: contract tests that
prevent API breakage between services, smoke tests that validate deployments
in 30 seconds, and load tests that find where your API breaks under pressure.

**Why these three together**: They cover different failure modes. Contract tests
catch "the API changed and the consumer didn't know." Smoke tests catch
"the deployment succeeded but the app is broken." Load tests catch "it works
for 10 users but falls over at 500." Together with E2E (Test-A), they form
a complete testing strategy.

**What you'll learn**:
- **Contract testing (Pact)**: consumer-driven contracts, the Pact broker, provider verification
- **Smoke testing**: the minimum test suite that validates "the system is alive"
- **Load testing (k6)**: virtual users, ramp-up patterns, response time percentiles (p50/p95/p99)
- **Performance profiling**: finding the bottleneck (CPU, memory, database, network)
- **Testing pyramid**: why the ratio of unit/integration/E2E/contract/load tests matters
- **Test environments**: why tests that pass locally but fail in staging aren't useful

**Contract testing section**:
- [ ] Pact consumer test: TypeScript frontend defines expected API responses
- [ ] Pact provider verification: API validates it satisfies the consumer contract
- [ ] Pact Broker (Docker): contracts published and versioned
- [ ] "Can I deploy?" check: verify compatibility before deployment
- [ ] At least 3 API endpoints covered by contracts

**Smoke testing section**:
- [ ] Smoke test suite: 5-10 tests that run in under 30 seconds
- [ ] Covers: health endpoint, auth flow, one read operation, one write operation
- [ ] Runs automatically after every deployment (CI/CD post-deploy step)
- [ ] Alerts on failure (e.g., GitHub Actions notification)

**Load testing section (k6)**:
- [ ] k6 script with realistic user scenario (browse → search → detail → action)
- [ ] Ramp-up test: 1 → 100 → 500 virtual users over 5 minutes
- [ ] Thresholds defined: p95 response time < 500ms, error rate < 1%
- [ ] Bottleneck identified and documented (where does the API start failing?)
- [ ] Load test results graphed (k6 → InfluxDB → Grafana or k6 Cloud)
- [ ] Fix applied: after identifying the bottleneck, improve it and re-test

**Testing requirement (meta)**: Contract tests catch an intentional breaking API change.
Smoke tests detect a broken deployment (kill a dependency, assert smoke fails).
Load test produces a graph showing the breaking point. **CI**: contracts in PR pipeline,
smoke after deploy, load on schedule (not every PR — too slow).

**AI usage note**: k6 scripts are written in JavaScript but run in Go — the mental
model is different from Node.js. Use AI to understand: *"Why can't I use npm packages
in k6?"* The load testing scenarios and threshold decisions should be yours.

---
