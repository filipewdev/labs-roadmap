# Lab 00-C — Git Like a Senior Dev + GitHub Actions

> **Time box**: 4 hours  
> **Language**: Shell + YAML  
> **Standalone**: Yes — best done using your Lab 00-A repo as a sandbox

---

## Goal

Establish professional Git habits and set up a real CI pipeline — both of which will be visible to international reviewers the moment they open any of your GitHub repos.

---

## Done When

- [ ] All commits in Lab 00-A repo follow Conventional Commits format
- [ ] The repo has a working GitHub Actions CI (lint + typecheck + test + coverage)
- [ ] You have a `.gitconfig` with useful aliases set up locally
- [ ] You can create, rebase, and squash a feature branch cleanly
- [ ] You understand what `git reflog` is and when it saves you

---

## Why Git Discipline Matters Internationally

When a European or American hiring manager opens your GitHub profile, the first thing they see is your commit history. Before reading a single line of code, they're forming an opinion.

A typical Brazilian agency commit history:
```
fix bug
updates
test
wip
fix
more fixes
final
final2
FINAL (please work)
```

A senior engineer's commit history:
```
feat(auth): implement PKCE flow for OAuth 2.0 authorization
test(auth): add integration tests for token rotation
fix(auth): handle expired refresh token in concurrent requests
docs(auth): document OAuth flow with sequence diagram
refactor(auth): extract token validation into dedicated service
```

The second history communicates what changed, why, and in what order. It's searchable, bisectable, and tells a story. This is what international companies expect.

---

## Part 1: Conventional Commits (1 hour)

### The Format

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

**Types:**
| Type | When to use |
|---|---|
| `feat` | A new feature |
| `fix` | A bug fix |
| `test` | Adding or fixing tests (no production code change) |
| `refactor` | Code change that is neither feat nor fix |
| `docs` | Documentation only |
| `chore` | Build process, dependency updates, tooling |
| `perf` | Performance improvement |
| `ci` | CI/CD changes |

**Examples:**
```
feat(string): add slugify utility function
test(string): add edge cases for empty and special-char inputs
fix(cpf): correct check digit calculation for leading-zero CPFs
refactor(date): extract time-range constants to avoid magic numbers
docs: add usage examples to README
ci: add coverage threshold enforcement to GitHub Actions
```

### The Atomic Commit Rule

One commit = one logical change. Not one file. Not one hour of work. One *concept*.

**Wrong** (too much in one commit):
```bash
git add .
git commit -m "feat: add string utils and their tests and fix the CPF bug and update readme"
```

**Right** (atomic):
```bash
git add src/string.ts
git commit -m "feat(string): add slugify and truncate utilities"

git add src/string.test.ts
git commit -m "test(string): add test suite for slugify and truncate"

git add src/cpf.ts
git commit -m "fix(cpf): correct check digit for 11-digit strings with leading zero"
```

### Exercise: Rewrite Lab 00-A History

Using your Lab 00-A repo, practice interactive rebase to clean up existing commits:

```bash
# Rewrite last 8 commits interactively
git rebase -i HEAD~8

# In the editor, you can:
# pick   = keep commit as-is
# reword = keep changes, edit the message
# squash = fold into previous commit
# fixup  = fold into previous (discard this message)
# drop   = delete commit entirely
```

Goal: your final history for Lab 00-A should read like a clear story of how the library was built.

---

## Part 2: GitHub Actions CI (1.5 hours)

Extend the basic CI from Lab 00-A into a production-quality pipeline.

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  quality:
    name: Quality Checks
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup pnpm
        uses: pnpm/action-setup@v3
        with:
          version: 9

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm           # Cache node_modules between runs

      - name: Install dependencies
        run: pnpm install --frozen-lockfile
        # --frozen-lockfile: fail if lockfile is out of date
        # This prevents "works on my machine" issues

      - name: Type check
        run: pnpm exec tsc --noEmit

      - name: Lint
        run: pnpm exec eslint src --ext .ts
        # Add ESLint if you haven't already: pnpm add -D eslint @typescript-eslint/parser

      - name: Test
        run: pnpm exec vitest run

      - name: Coverage
        run: pnpm exec vitest run --coverage
        
      - name: Check coverage threshold
        run: |
          # Fail if coverage drops below 85%
          # (vitest --coverage already does this if you configure it)
          echo "Coverage check passed"
```

**Add coverage thresholds in vitest config:**

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      thresholds: {
        lines: 85,
        functions: 85,
        branches: 80,
      },
      reporter: ['text', 'lcov'],  // lcov for coverage badge services
    }
  }
})
```

### Understanding the Pipeline

Each step is intentional. Know what would happen if you removed each one:
- Remove `--frozen-lockfile` → CI might install different versions than you tested locally
- Remove typecheck → TypeScript errors only caught at runtime
- Remove coverage threshold → coverage silently degrades over time
- Remove `cache: pnpm` → every run reinstalls node_modules (~2 min wasted per push)

---

## Part 3: Useful Git Configuration (30 min)

```bash
# ~/.gitconfig additions

[alias]
  # Pretty one-line log with graph
  lg = log --oneline --graph --decorate --all

  # Show what changed in last commit
  last = log -1 HEAD --stat

  # Undo last commit but keep changes staged
  undo = reset --soft HEAD~1

  # Clean up merged branches
  cleanup = "!git branch --merged main | grep -v main | xargs git branch -d"

  # Show which branches have been merged
  merged = branch --merged main

[core]
  # Show conflicts more clearly
  autocrlf = input  # on Linux/Mac

[pull]
  rebase = true    # "git pull" rebases instead of merges (cleaner history)

[push]
  autoSetupRemote = true  # No need for -u origin on first push
```

---

## Part 4: The Safety Net — git reflog (30 min)

`git reflog` is the undo button for everything. Even after `git reset --hard`, `git rebase`, or accidentally deleting a branch.

**Exercise: Recover from a "disaster"**

```bash
# Create some commits
echo "change 1" >> test.txt && git add . && git commit -m "feat: change 1"
echo "change 2" >> test.txt && git add . && git commit -m "feat: change 2"
echo "change 3" >> test.txt && git add . && git commit -m "feat: change 3"

# "Accidentally" hard reset to 3 commits ago
git reset --hard HEAD~3

# Panic: your 3 commits are gone!
git log --oneline  # They're not there

# Recovery:
git reflog
# You'll see:
# abc1234 HEAD@{0}: reset: moving to HEAD~3
# def5678 HEAD@{1}: commit: feat: change 3
# ...

# Restore:
git reset --hard def5678  # or whatever the commit hash was
git log --oneline  # All 3 commits are back
```

Reflog retains entries for 90 days by default. It's the safety net under every Git operation.

---

## Branch Workflow for Future Labs

For every lab, use this branch pattern:

```bash
# Start a new lab
git checkout -b lab/04-websocket-chat

# Work in small commits
git commit -m "feat: add WebSocket hub with room support"
git commit -m "test: add unit tests for hub message routing"
git commit -m "fix: handle nil channel on client disconnect"

# When done, clean up history if needed
git rebase -i main

# Merge cleanly
git checkout main
git merge --no-ff lab/04-websocket-chat  # --no-ff preserves branch history
git branch -d lab/04-websocket-chat
```

---

## AI Usage Note

Ask AI to review a specific commit message: *"Is this commit message following Conventional Commits? Is the scope appropriate? Is the description clear?"*

Do not ask AI to write commit messages for you. They describe your thinking process — they should come from you.

---

## What to Put in the README

Add a "Contributing" section to Lab 00-A's README:

```markdown
## Contributing

Commits follow [Conventional Commits](https://www.conventionalcommits.org/).
CI runs on every push: type check → lint → test → coverage threshold.
```

This signals professionalism to anyone who opens the repo.
