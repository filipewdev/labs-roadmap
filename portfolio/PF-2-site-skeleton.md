# Lab PF-2 — Site Skeleton in Three Languages

**Goal**: Replace the placeholder with a real Astro site in three languages:
shared layout, the plain facts page, a first "How it's built" page, a calm
mode, and test gates in CI, all deployed through the PF-1 pipeline.

**Time box**: 8 hours

**Language**: TypeScript (Astro)

**AWS parallel**: AWS Amplify Hosting would build and deploy this in one service. You're keeping the pieces separate (S3, CloudFront, GitHub Actions) so each one stays visible and under your control.

**Done when**:
- [ ] Astro with static output and TypeScript in strict mode
- [ ] Built-in i18n routing for `en` (default), `pt` and `fr`; every page exists in all three; switching language keeps the current page
- [ ] Shared layout: header with your name, a "Skip to the facts" link, the language switch and a calm-mode switch; footer with links
- [ ] Facts page complete in three languages, rendered from a data file validated by a schema
- [ ] First "How it's built" page: stack, hosting diagram, links to the repo and decision records
- [ ] Calm mode follows `prefers-reduced-motion` by default; a manual toggle overrides it and is remembered per visitor
- [ ] No inline scripts, so the PF-1 header CSP keeps working unchanged. If Astro's output needs inline scripts, record the alternative (Astro's hashed CSP plus the header) in an ADR
- [ ] Observatory still A+ after the switch from placeholder to site
- [ ] Merging to main deploys the site through the PF-1 pipeline

**Key concepts**:
- Islands architecture: HTML by default, JavaScript only where you opt in
- Static i18n routing: one URL per language, no runtime detection on the page
- Content collections with schemas: the build fails when content is wrong
- CSP on a static site: a header policy vs per-page hashes
- Accessibility basics: landmarks, heading order, focus styles, skip links, colour contrast
- Reduced motion as a first-class setting, not an afterthought

**Local environment**: Node (current LTS) and pnpm. `pnpm dev` serves the site locally; `pnpm build && pnpm preview` serves the production build.

**Testing requirement**:
- **Vitest**: schema tests for the facts data; a completeness test that every page and string exists in all three languages.
- **Playwright**: navigation, language switch keeps the page, "Skip to the facts" works by keyboard alone, calm-mode toggle persists across reloads.
- **axe-core** (through Playwright): no WCAG 2.1 AA violations on any page.
- **Lighthouse CI**: accessibility 100, performance 90 or more on every page.
- **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80%) → build → Playwright + axe → Lighthouse CI.

**AI usage note**: Ask AI to explain Astro's i18n options, or why axe flagged something and how to fix it properly. Write the components and layout yourself.

**What to put in README**: "Astro site in English, Portuguese and French with zero JavaScript by default. Accessibility and performance are CI gates, not good intentions."
