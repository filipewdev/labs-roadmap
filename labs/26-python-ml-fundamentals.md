# Lab 26 — Python ML Fundamentals

**Goal**: Learn enough Python, NumPy, Pandas, and PyTorch to feel at home in
the ML ecosystem — without relying on magic you don't understand.

**Time box**: 8 hours

**Language**: Python

**Done when**:
- [ ] Python virtual environments, pip, and pyproject.toml configured correctly
- [ ] NumPy: array operations without loops (vectorization), broadcasting, slicing
- [ ] Pandas: load IBGE CSV, filter, group, aggregate, handle missing values
- [ ] PyTorch: define a simple neural network, forward pass, compute loss, one backprop step
- [ ] Both Jupyter notebook and script form of the same code — know when to use each

**Key concepts**:
- Python vs TypeScript mental model (dynamic typing, duck typing, GIL)
- Why NumPy is fast (C under the hood, contiguous memory layout)
- Tensors: the fundamental data structure of every ML framework
- `dtype` and why `float32` vs `float64` matters for GPU memory
- The training loop: forward pass → loss → backward pass → optimizer step

**Testing requirement**: pytest tests for your NumPy utility functions. Learn `pytest.approx` for floating-point comparison.

**AI usage note**: Python syntax questions are fine for AI. The concepts — why vectorization, what a tensor is, how backprop works — require your own understanding to be useful.

---

## Open Source Track

The OS track labs are standalone — they don't depend on completing other phases
first (except as noted). They can be interleaved with any phase.

---

### OS-A — Publish npm Library

**Goal**: Take the `br-utils` library from Lab 00-A and publish it to npm as a
real, installable package — with TypeDoc documentation, dual ESM/CJS output,
a changelog, and automated publishing via GitHub Actions.

**Time box**: 8 hours

**When to do**: Right after Lab 00-A — you already have the code.

**Done when**:
- [ ] Package published at `npmjs.com/package/@yourname/br-utils`
- [ ] TypeDoc documentation deployed to GitHub Pages
- [ ] `CHANGELOG.md` following keepachangelog.com format
- [ ] Package exports both ESM and CJS via the `exports` field
- [ ] Tree-shaking works: importing only `validateCPF` doesn't bundle CEP code
- [ ] GitHub Actions publishes automatically when a git tag is pushed
- [ ] Verified working in a separate consumer project (CJS + ESM both tested)

**Key concepts**:
- The ESM/CJS split and why a library needs both
- `package.json` exports field — the modern way to define entry points
- `sideEffects: false` — what it tells bundlers and why it enables tree-shaking
- Semantic versioning as a contract with your users
- `--provenance` flag — cryptographic link between package and source

**Full spec**: [open-source/OS-A-publish-npm-library.md](../open-source/OS-A-publish-npm-library.md)

---

### OS-B — First OSS Contribution

**Goal**: Make a real, merged contribution to an open source project you don't own.
Not a typo fix — a code, test, or documentation change that solves a real problem.

**Time box**: 6–10 hours (spread over 2 weekends — finding the right issue takes time)

**When to do**: Anytime after Lab 00-C (you need comfortable Git workflow).

**Done when**:
- [ ] A PR is open on a real project (not your own)
- [ ] The PR addresses a specific issue, filed by someone else
- [ ] Your PR description explains the problem, the solution, and the testing
- [ ] You've responded to at least one round of code review feedback

**Key concepts**:
- Reading unfamiliar code (start from tests, not source)
- Discussion-first, code-second (comment on the issue before writing code)
- Matching the project's existing style (you are a guest in their codebase)
- Handling code review feedback professionally in English

**Full spec**: [open-source/OS-B-first-contribution.md](../open-source/OS-B-first-contribution.md)

---

### OS-C — Publish pub.dev Package

**Goal**: Publish the same `br-utils` functionality as a Dart package to pub.dev,
targeting a score of ≥130/160 pub points from day one.

**Time box**: 6 hours

**When to do**: After Lab 21 (you're working heavily in Dart anyway).

**Done when**:
- [ ] Package published at `pub.dev/packages/br_validators`
- [ ] pub.dev score ≥ 130/160 points
- [ ] Works on all platforms: Android, iOS, Web, Linux, macOS, Windows
- [ ] Example app included in `example/` directory
- [ ] `dart analyze` reports zero warnings or errors

**Key concepts**:
- pub.dev scoring system — what each category measures
- `pubspec.yaml` fields that affect discoverability
- `///` doc comments and why the example app appears on the pub.dev page
- `pana` tool — run it locally before publishing to see your score

**Full spec**: [open-source/OS-C-pubdev-package.md](../open-source/OS-C-pubdev-package.md)

---

## Capstone Reference

The capstones are not lab cards. They have full specification documents.
Do not start them until you've completed most of the labs in their prerequisite list —
each technology in the stack will already be familiar from a standalone lab.

**Capstone I — Therapy Scheduling Platform** → [capstones/C1-therapy-platform.md](../capstones/C1-therapy-platform.md)
Build after: Labs 03, 08, 09, 11, 14, 15, 19

**Capstone II — HomeHub** → [capstones/C2-homehub.md](../capstones/C2-homehub.md)
Build after: Labs 05, 06, 07, 08, 18, 21, 22, 23, 24, 25
