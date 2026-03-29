# Lab OS-B — Your First Real Open Source Contribution

> **Phase**: Open Source Track (do after OS-A)
> **Time box**: 6–10 hours (spread over 2 weekends — finding the right issue takes time)
> **Language**: Whatever the project uses
> **Standalone**: Yes — but OS-A teaches you the professionalism this lab requires

---

## Goal

Make a real, merged contribution to an open source project that you don't own.
Not a typo fix. Not adding yourself to a "contributors" list. A genuine code,
documentation, or test contribution that solves a real problem for real users.

---

## Done When

- [ ] A PR is open on a real project (not a practice repo, not your own project)
- [ ] The PR addresses a specific issue filed by someone else, or a problem you found
- [ ] Your PR description follows the project's contributing guidelines
- [ ] You've responded to at least one round of code review feedback
- [ ] **Bonus**: PR is merged — but this is outside your control and should not be the goal

---

## Why "Not a Typo Fix" Is the Rule

Typo fix PRs are a common "open source contribution" hack for resumes. Hiring
managers know this. A PR that fixes a typo in a README took 5 minutes and
required no understanding of the codebase.

A contribution that:
- Adds a missing test case for an edge case you found
- Fixes a bug you encountered while using the library
- Adds a feature requested in an issue with 50 upvotes
- Improves documentation for a confusing API with a real example

...required you to read and understand the codebase, write code that passes
their CI, and communicate your approach to strangers in English. That is a
different kind of signal.

---

## Part 1: Finding the Right Project (2–3 hours, asynchronous)

This is the hardest part. Don't rush it. A bad choice means hours of wasted effort.

### Criteria for a Good First Project

**Use it yourself**: The best contributions come from real frustration with a tool.
If you've hit a bug or a missing feature in a library you actually use, that's
a perfect contribution. You already understand the context.

**Active but not overwhelming**: Look for projects with:
- Last commit: within the past month
- Maintainers who respond to issues within a week or two
- Not thousands of open issues (you'll get lost)

**Has a CONTRIBUTING.md**: This signals the maintainers care about contributor
experience. Projects without one are often harder to contribute to.

**Has a CI you can run locally**: If you can run `npm test` or `cargo test`
and see tests pass, you can verify your contribution before submitting.

### Where to Look

**GitHub search operators** — the most targeted approach:

```
# TypeScript projects with "good first issue" label
language:TypeScript label:"good first issue" is:open

# Go projects needing help with documentation
language:Go label:"help wanted" label:documentation is:open

# Projects you actually use — check their issues tab
```

**Ecosystems to explore based on your stack:**

```
TypeScript/Node.js:
  - BrasilAPI — the API you're using in labs. Brazilian, friendly to contributors
  - tsx (TypeScript executor) — small, very active
  - Zod — validation library, careful maintainers, well-documented contributing process
  - Fastify plugins — many are small, self-contained, need tests

Flutter/Dart:
  - Any pub.dev package you use regularly
  - The Flutter framework itself has "good first issue" labels
  - BrazilianDevs community packages

Go:
  - After Lab 04, you know enough Go to contribute to small CLI tools

Documentation:
  - MDN Web Docs (documentation for web APIs — extremely valuable, always needs work)
  - The Go standard library documentation (hard, but prestigious)
```

**A specific suggestion**: After completing your labs, check if BrasilAPI
has open issues. You've used it, you understand what it does, and it's a
Brazilian project — contributing back is a meaningful act, not just portfolio padding.

### What "Good First Issues" Actually Look Like

Good: "The `validateCEP` function doesn't handle CEPs with leading zeros" + failing test case
Good: "Add TypeScript types to this JavaScript library" + list of untyped functions
Good: "The README doesn't explain the options parameter for X function" + specific user confusion

Not good: "Improve performance" (too vague)
Not good: "Add feature X" (major scope, may require maintainer discussion first)
Not good: "Fix typo in README" (too trivial)

---

## Part 2: Before Writing a Single Line of Code (1 hour)

This step is where most first-time contributors fail. They write code first
and ask questions later — or never. Professional open source culture is
discussion-first, code-second.

### Read the CONTRIBUTING.md Completely

Look for:
- Development setup instructions (how to run tests locally)
- Coding style guidelines (some projects use specific formatting)
- Commit message requirements (some require Conventional Commits)
- PR size preferences (some ask for small, focused PRs)
- Whether they want an issue filed before a PR

### Comment on the Issue First

Before writing code, comment on the issue:

```
"I'd like to work on this. My understanding of the problem is [your summary].
I'm thinking of fixing it by [brief approach description]. Does that sound right?
Any other edge cases I should consider?"
```

This accomplishes three things:
1. Claims the issue so two people don't work on the same thing simultaneously
2. Gets early feedback — maybe your approach is wrong and you find out before writing code
3. Shows the maintainer you understand the problem before proposing a solution

If no issue exists for the bug you found, file one first and wait a day or two
for maintainer feedback before writing the fix.

### Set Up the Dev Environment Properly

```bash
# Fork the repo on GitHub, then:
git clone https://github.com/YOUR_USERNAME/PROJECT_NAME
cd PROJECT_NAME

# Add upstream remote (important — you'll need to sync later)
git remote add upstream https://github.com/ORIGINAL_OWNER/PROJECT_NAME

# Install dependencies exactly as the project specifies
# (don't substitute pnpm for npm unless they say it's supported)
npm install   # or whatever their docs say

# Verify tests pass BEFORE you make any changes
npm test      # all tests must pass before you start
```

**Why verify tests pass first**: If you make changes and tests fail, you need
to know whether *you* broke them or they were already broken. Starting from
green is non-negotiable.

---

## Part 3: Writing the Contribution (2–4 hours)

### Branch Naming

```bash
git checkout -b fix/cep-leading-zeros
# or
git checkout -b feat/add-cnpj-formatter
# or
git checkout -b docs/clarify-cpf-edge-cases
```

Use the same type prefixes as Conventional Commits. It signals professional
Git habits before the maintainer even reads your code.

### The Contribution Itself

Write code as if the maintainer is going to read every line critically —
because they are. Specifically:

**Match the existing style**: If the project uses `function` declarations,
don't use arrow functions. If it uses `const` for everything, use `const`.
If tests use `describe`/`it`, use those. Your PR should look like it was
written by someone who has been on the project for months, not a visitor.

**Add tests for everything you change**: A PR with no tests will almost always
get asked for tests before merging. Save the round-trip by including them
from the start.

**One concern per commit**: Don't fix a bug AND refactor a function AND update
the README in one commit. Three commits for three changes. If you notice
something else that should be fixed, file a separate issue instead of fixing
it in your PR — scope creep makes code review harder.

**Run the linter before pushing**:
```bash
npm run lint        # fix any style issues
npm run typecheck   # no TypeScript errors
npm test            # all tests pass
```

---

## Part 4: The PR Description (30 min)

The PR description is as important as the code. Many first-time contributors
write one sentence. Professional contributions look like this:

```markdown
## Summary

Fixes #123 — `validateCEP` incorrectly rejects valid CEPs that start with 0 (e.g., `01310-100`).

## Problem

The current implementation uses `parseInt()` to strip formatting, which drops
leading zeros from strings like `01310-100` → `1310100` (7 digits), causing
the length check to fail.

## Solution

Replace `parseInt()` with a regex that removes only non-digit characters,
preserving leading zeros: `cep.replace(/\D/g, '')`.

## Testing

Added two test cases to `src/cep.test.ts`:
- `'01310-100'` (São Paulo city center CEP with leading zero) → valid
- `'01001-000'` (Praça da Sé) → valid

Both previously returned `false` and now return `true`.

## Checklist

- [x] Tests added
- [x] `npm test` passes
- [x] No changes to public API (backwards compatible)
```

**What each section communicates to the maintainer:**

- **Summary**: what this PR does in one sentence + the issue number (GitHub links them automatically)
- **Problem**: proves you understand the bug, not just the fix
- **Solution**: explains *why* you made this specific change (not obvious!)
- **Testing**: saves the maintainer from having to find your tests
- **Checklist**: shows you read their contributing guidelines

---

## Part 5: Handling Code Review

Code review on an open source contribution is different from internal team review.
The maintainer doesn't know you. They're gatekeeping their project's quality.
Feedback can feel blunt.

**The right mindset**: they're reviewing the code, not you. A request for
changes is not a rejection — it means they think your contribution is worth
their time to improve.

**Common review responses and how to handle them:**

`"Can you add a test for the case where X?"` → Add it. Don't argue unless X
is truly irrelevant. They know their codebase better than you.

`"This should use X pattern instead of Y"` → Ask why if you don't understand,
then implement it their way. You're a guest in their codebase.

`"I'd prefer if this was named Z"` → Change it. Naming is subjective; defer
to the maintainer.

`"This approach has a performance problem with large inputs"` → This is
valuable feedback. Understand the problem, fix it, and document what you
learned in your lab README.

**Responding to feedback in GitHub:**
- Reply to specific comments, not just "done" on the PR
- Mark conversations as resolved after addressing them
- Re-request review after pushing changes

---

## Part 6: After the PR Is Merged (or Not)

### If Merged

Congratulations. Your code is now running in production for every user of
that library.

The most important next step: update your GitHub profile's pinned repositories
to include a link to the contribution (the merged PR, not a fork). The specific
line to add to your README or LinkedIn:

> "Contributor to [Project Name] — [one sentence describing what you fixed/added]"
> [link to merged PR]

This is concrete evidence. Not "I contribute to open source" — "I fixed this
specific thing in this specific project, here's the PR."

### If Not Merged

This happens often. Maintainers may close a PR because:
- They have different priorities
- The approach isn't right for the project
- They want to solve it differently
- The project is abandoned

None of these mean your contribution was bad. What matters for your learning
is the process: you read a codebase you didn't write, understood it well
enough to change it, communicated professionally in English in a public forum,
and handled code review. That skill is real regardless of the merge outcome.

If a PR is closed without explanation, it's fine to politely ask why. The
answer is usually educational.

---

## Key Concepts to Explain in Your README

### Reading code you didn't write

Contributing to open source requires a skill most developers never practice
in their day jobs: reading unfamiliar code without any context and understanding
it well enough to modify it safely. I spent [X hours] reading the codebase
before writing a single line. The key strategy: start at the test files, not
the source — tests show you what the code is supposed to do and often explain
edge cases better than the implementation.

### The difference between a fork and a contribution

A fork is a copy of a project under your account. You can do anything to it.
A contribution is a proposed change to someone else's project — it requires
their approval and must meet their standards. The professional discipline of
writing code that *other people* will be responsible for maintaining is
different from writing code only you will maintain.

### Why open source etiquette matters

Open source maintainers are typically unpaid and time-constrained. A PR that's
hard to review (large scope, no tests, no description, doesn't follow project
conventions) costs the maintainer significant time. A well-prepared PR that's
easy to review gets merged faster and builds a relationship with the maintainer
that can lead to further contributions or even maintainer status over time.

---

## A Note on Language

Most open source communication happens in English. Your issue comments,
PR description, and review responses will all be in English. This is a feature,
not a bug — every open source interaction is practice for international work
communication. Write clearly and professionally, but don't overthink it.
Maintainers are used to contributors from all over the world and appreciate
clarity over perfection.

---

## AWS Parallel

Open source contribution workflow maps directly to professional team workflows
at international companies:

- **Fork → branch → PR → code review → merge** is exactly how engineering
  teams at AWS, Google, Stripe etc. work internally
- **CONTRIBUTING.md** → internal team's engineering wiki / RFC process
- **Code review on GitHub** → the same on internal GitHub Enterprise or GitLab
- **CI must pass before merge** → standard at every company above startup stage

The difference is that in open source you're learning this workflow without
a colleague to ask for help — which makes you more self-sufficient when you
encounter it professionally.
