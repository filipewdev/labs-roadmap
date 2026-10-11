# Lab PF-3 — Data-Driven Pages and the Public Gate

**Goal**: Add the roadmap page and the project log, both generated from data,
harden the repository's supply chain, and pass the checklist that takes the
site public.

**Time box**: 8 hours

**Language**: TypeScript (Astro) + YAML

**AWS parallel**: Scheduled rebuilds here use GitHub Actions; on AWS the same idea is EventBridge Scheduler triggering CodeBuild.

**Done when**:
- [ ] Roadmap page built from a schema-validated data file: certifications (earned, in progress, planned), study phases, and teaching totals
- [ ] Each roadmap item has a `visible` flag; hidden items never reach the built HTML
- [ ] Planned items show no target dates
- [ ] Project log built at build time from your public GitHub repos tagged with a chosen topic, showing purpose, stack, key learnings and repo link
- [ ] If the GitHub API fails during a build, the log falls back to the last good snapshot instead of breaking the build
- [ ] A scheduled workflow rebuilds the site daily, so new labs appear without a commit
- [ ] Repository rulesets: required status checks, signed commits, no force pushes, linear history
- [ ] Dependabot for npm, GitHub Actions and Terraform; CodeQL default setup; gitleaks and osv-scanner in CI
- [ ] OpenSSF Scorecard workflow publishing a badge, shown on "How it's built" with the Observatory grade
- [ ] **Public gate**: every page reviewed by a human in all three languages, the PF-0 privacy review re-run, and a link checker passing

**Key concepts**:
- Build-time data vs runtime data: why a static site can still show "live" information
- Schemas as contracts between your content and your pages
- Failing safe: a stale snapshot is better than a broken build
- Supply-chain security: what each of Dependabot, CodeQL, gitleaks, osv-scanner and Scorecard actually checks
- Rulesets vs classic branch protection

**Local environment**: same as PF-2. Use a fine-grained GitHub token with read-only access to public repos when building locally; CI uses the workflow's own token.

**Testing requirement**:
- **Vitest**: roadmap schema tests, including a test that a hidden item never renders; project-log loader tests against recorded API responses, including the failure path that falls back to the snapshot.
- **Playwright**: roadmap and project log render in all three languages; filters on the project log work by keyboard.
- **Link checker** (for example lychee) in CI: no broken internal or external links.
- **CI**: everything from PF-2, plus the link checker and the security scans.

**AI usage note**: Ask AI to explain what a Scorecard check means or why it fails. Design the data schemas yourself: they decide what the site can ever show.

**What to put in README**: "Roadmap and project log generated from data at build time, rebuilt daily, failing safe when an API is down. Supply-chain checks and an OpenSSF Scorecard badge are public."
