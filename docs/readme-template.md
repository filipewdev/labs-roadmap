# Lab README Template

> **How to use this**: Copy everything between the `---` dividers below
> into your new lab repo's `README.md`. Delete the HTML comments as you
> fill each section in. Sections marked `<!-- optional -->` can be removed
> if they don't apply to your lab.
>
> Write as you go — not after. The confusion mid-lab is the evidence of learning.
> Once it makes sense, that confusion is hard to reconstruct.

---

# Lab NN — [Lab Name]

![CI](https://github.com/filipewdev/REPONAME/actions/workflows/ci.yml/badge.svg)
[![Coverage](https://img.shields.io/badge/coverage-XX%25-brightgreen)](#tests)

<!-- One sentence: what this is and what it demonstrates.
     Describe behaviour, not technology.
     Good: "A multi-room WebSocket server that detects dead connections via heartbeat."
     Bad:  "A Go server using gorilla/websocket and Redis." -->

> [your one-sentence description here]

---

## What I Built

<!-- 2–4 sentences. What does the running system do?
     What can someone actually do with it?
     No bullet points — write prose. -->

## Architecture

<!-- A simple ASCII or Mermaid diagram showing how the pieces connect.
     Drawing this forces you to understand the system well enough to explain it.
     Even 5 lines is better than nothing.

     Example:
     Browser → ws:// → Go Server → Hub (goroutine) → Room → Client channels
-->

```
[your diagram here]
```

## Running Locally

```bash
# Prerequisites: Docker, [language runtime]
docker compose up -d

# Run
[start command]

# Test
[test command]

# Test with race detector / coverage / etc.
[additional test commands]
```

## Key Concepts Learned

<!-- The most important section. 3–5 concepts, each in plain language.
     Rule: if you can't explain it without jargon, rewrite it.
     Rule: include a "so what?" — why does this matter in practice? -->

### [Concept name]

[Your explanation in 2–5 sentences. Concrete over abstract — use numbers,
comparisons, or analogies. Write as if explaining to someone technical
but unfamiliar with this specific tool.]

### [Concept name]

[Explanation]

### [Concept name]

[Explanation]

## Tests

<!-- Describe your testing strategy, not a list of test names.
     What did you test? What did you deliberately not test and why?
     What pattern did you use (unit, integration, testcontainers, mocks)?  -->

## What I Would Do Differently

<!-- Honest reflection on one or two decisions you'd change.
     This is the section that signals engineering maturity.
     "Nothing, it all went perfectly" is never the right answer. -->

## AWS Equivalent

<!-- One paragraph connecting this lab's tool to its AWS managed equivalent.
     Reference: docs/aws-context.md in labs-roadmap. -->

## Time Spent

| Phase | Hours |
|---|---|
| Setup + reading docs | |
| Implementation | |
| Tests | |
| Debugging | |
| README | |
| **Total** | |

---

<!-- OPTIONAL SECTIONS — delete any that don't apply to this lab -->

## Open API Integration

<!-- optional -->

**API**: [name + URL]
**Used for**: [what it provides]
**Mocked in tests**: [yes/no — and how]

## Stretch Goal

<!-- optional -->

[What you built and what you learned from it beyond the base lab.]

## References

<!-- optional — links to docs, posts, or talks that helped -->

- [Resource name](URL)

---
