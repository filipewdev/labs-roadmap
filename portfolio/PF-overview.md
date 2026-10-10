# Portfolio Track — Overview

> A personal portfolio site, built as a series of labs instead of one big
> project. Each lab ends with something live and tested; the site goes public
> after PF-3 and every later lab improves a site that already exists.

---

## Why a Track and Not a Project

Portfolio sites are a classic abandoned side project: the design grows, the
content never gets written, and nothing ships. This track avoids that by:

- **Content before code.** PF-0 decides what the site says, and what it must never say, before anything is built.
- **Infrastructure before features.** PF-1 puts a single page online with production-grade hosting, so every later lab deploys to a real site.
- **A public gate early.** After PF-3 the site is plain but complete: facts, roadmap, project log, all tested. That's the version that goes public.
- **Visual ambition last.** Animation, illustration and interactive pieces come in later labs, one at a time, and never block a release.

---

## The Labs

```
Foundation (public v1 after PF-3)
  PF-0  Content and privacy pass          No code · ~4h
  PF-1  Static site infrastructure on AWS Terraform + YAML · ~8h
  PF-2  Site skeleton in three languages  TypeScript (Astro) · ~8h
  PF-3  Data-driven pages                 TypeScript (Astro) · ~8h

Later (specs to come)
  PF-4  Story pages with page turns       CSS scroll-driven animation
  PF-5  A signature interaction           Your own idea; one per site
  PF-6+ Visual chapters, one per lab      Illustration + one mechanic each
  Add-ons: contact form (Lambda + SES), email list, cookieless analytics
```

---

## Principles

- **A plain view, always.** One page with the facts (role, experience, stack, languages, certifications, links) that loads instantly and is linked from every header. Nobody should have to play with the site to learn who you are.
- **Data, not hardcoded content.** Roadmap and project log come from files and APIs, so updating the site is a commit, not a redesign.
- **Prove it publicly.** Security headers, tests, accessibility and supply-chain checks all leave public evidence: grades, badges, CI runs and decision records.
- **Generic names.** Real people, clients and businesses are described generically. Employer and client work appears only as anonymised case studies.
- **Art never blocks a release.** Every page ships as text first.

---

## How It Fits the Rest of the Curriculum

- **Do PF-0 any time.** It's writing, not code.
- **PF-1 doubles as AWS practice:** S3, CloudFront, Route 53, ACM, IAM and OIDC are core Solutions Architect Associate topics.
- **After the site is public, alternate:** one curriculum lab, then one portfolio lab. Each finished curriculum lab adds an entry to the project log.
- **Prerequisites:** Lab 00-A (testing habits) and Lab 00-C (Git and CI discipline).

---

## Cost

Roughly US$0.50–1 per month plus the domain: a Route 53 hosted zone, with
S3, CloudFront and ACM inside the free tiers at personal-site traffic. Avoid
AWS WAF here; it alone costs more than the rest combined.
