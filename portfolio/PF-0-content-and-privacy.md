# Lab PF-0 — Content and Privacy Pass

**Goal**: Decide what the portfolio says, and what it must never say, before
writing any code. Leave with the first texts written, the domain registered
and the first decision record in place.

**Time box**: 4 hours

**Language**: Writing (Markdown)

**Done when**:
- [ ] Content inventory: every fact, story and project you might show, in one private list
- [ ] Each item classified as **public**, **public with care** (and how to frame it) or **private**, with a one-line reason
- [ ] Consent from anyone the site mentions or describes recognisably (family, former colleagues, clients)
- [ ] Title-screen text written: name, role, one sentence (≤ 40 words)
- [ ] Facts page text written in the default language: role, years, scope, stack, languages, education, certifications, links
- [ ] Site map sketched: every page with its path, and which page is the plain view
- [ ] Domain registered with WHOIS privacy; renewal price checked
- [ ] `docs/adr/0001-stack-and-hosting.md` written: context, options considered, decision, consequences

**Key concepts**:
- Content-first design: structure follows what you have to say
- Personal threat model: what a stranger, a recruiter, a colleague and an attacker could each do with each fact
- Confidentiality: employer processes, client names, incidents and internal metrics are never yours to publish
- Generic naming: "a donation platform", not the product's name, protects people and makes your work reusable
- Architecture Decision Records: context → decision → consequences, written when the decision is made

**Before you start**: List the people who appear in your story. Anyone who could recognise themselves gets asked before launch, not after.

**Classification guide**:

| Usually public | Public with care | Usually private |
|---|---|---|
| Career path, titles, years | Growth areas, framed as what you do now | Plans to change jobs, salary, money |
| Certifications, stack, languages | Team results, as totals only | Client names, systems, incidents |
| Your own projects and labs | Former businesses, without names or money | Family details, routine, exact location |
| Values and how you work | Your age (prefer "years of experience") | Side work, personal struggles in raw form |

**Testing requirement**: A privacy review instead of tests. Ask one trusted person to read the classification and the facts page and point out anything they'd be uncomfortable seeing public. Run `gitleaks detect` on every repo the site will link to. Search your own name and note what's already public.

**AI usage note**: AI is useful for turning your notes into a first draft and for questioning your classification ("What could someone do with this fact?"). Rewrite every public sentence in your own voice. Don't paste anything you've classified as private into a tool you don't control.

**What to put in README** (of the portfolio repo, once it exists): "Content and privacy decisions are recorded before code. See ADR-0001 for the stack and hosting decision."
