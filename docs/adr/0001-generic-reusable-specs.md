# ADR-0001: Specs are generic and reusable

- **Status:** Accepted
- **Date:** 2026-10-12
- **Note:** Recorded after the practice was already in place, to make it explicit.

## Context

This repository is public. It holds the specs for a curriculum of weekend
labs, plus capstones and tracks that grow from real needs: a scheduling
platform for a professional practice, a home automation hub, a personal
portfolio, problems met at work.

Three forces pull against each other:

- **Specs are better when they come from real problems.** Invented problems
  produce shallow labs.
- **Real problems involve real people.** Family members, clients, employers
  and colleagues have a right to privacy, and work for clients is often
  covered by confidentiality agreements.
- **Anyone can read the repository,** including colleagues, managers and
  strangers who might want to follow the curriculum themselves.

The repository's other principles (every lab standalone and weekend-sized,
tests in every lab, AI as a reviewer rather than a generator) all assume
specs that someone else could pick up and follow.

## Options considered

### Option A: Public, with real names and details

- Pro: The most concrete and honest about where each lab comes from.
- Con: Exposes people who never agreed to appear, and can break
  confidentiality obligations to clients and employers.
- Con: Specs tied to one person's situation are hard for anyone else to reuse.

### Option B: Make the whole repository private

- Pro: No privacy risk.
- Con: Loses the public record of progress and the chance for others to reuse
  the curriculum.
- Con: Removes the discipline of writing for a reader.

### Option C: Public and generic, with private details kept elsewhere

- Pro: Keeps the repository public and reusable.
- Pro: Protects people, clients and employers by design, not case by case.
- Con: Some context is lost in the public specs, and keeping two places in
  sync takes effort.

## Decision

We will keep this repository public and write every spec generically. Real
people, clients and businesses are described by their role or kind ("a
therapist's practice", "a donation platform", "a logistics company"), never by
name. Personal plans, private context and anything confidential live outside
this repository, in a private place.

The test for any content: **could a stranger with the same problem follow this
spec, and would everyone it describes be comfortable reading it?** If either
answer is no, it doesn't belong here.

## Consequences

- **Every new spec or change is reviewed for names** of people, clients,
  employers and products before merging, including in examples, filenames and
  commit messages.
- **Specs describe problems and requirements**, not the situation they came
  from. Where a real constraint matters, it's stated as a requirement ("must
  work offline in rural areas"), not as a story about a client.
- **Personal goals and plans stay out**, as do incidents, internal metrics and
  any client's systems.
- **Implementation repos follow the same rule**, since they're public too.
- **Some richness is lost.** That's accepted: the private notes keep the full
  context for the person doing the labs.
- **Reuse becomes possible:** the written content is licensed CC BY-NC-SA 4.0
  and the code MIT, so others can follow and adapt the curriculum.
