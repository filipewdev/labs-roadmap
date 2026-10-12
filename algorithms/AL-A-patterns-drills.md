# Lab AL-A — Algorithms and Data Structures: Patterns Drills

**Goal**: Solve problems from every major pattern in one language, each with
tests and a written complexity analysis, and finish able to solve a
medium-difficulty problem in about 30 minutes while explaining your approach.

**Time box**: A season of 8–10 weeks, 3–4 sessions a week of 30–45 minutes (about 25 hours in total). One board card for the whole season.

**Language**: One language for the whole season. TypeScript (your strongest) or Python (concise, and common in timed practice). Don't switch mid-season.

**Done when**:
- [ ] A `dsa-drills` repo with one folder per pattern and one file per problem, each with its tests
- [ ] At least 75 problems solved, covering every pattern below (the first half of the NeetCode roadmap is a good plan)
- [ ] Every solution has a header comment: the pattern, time and space complexity, and one line on why
- [ ] A `PATTERNS.md` cheat sheet in your own words: for each pattern, the signal that tells you to use it, a template, and two example problems
- [ ] A `LOG.md` with each session's date, problem, minutes taken, and whether you needed a hint
- [ ] At least 10 problems re-solved from scratch a week or more after the first attempt, faster than the first time
- [ ] Three practice sessions done out loud: someone watches (a colleague, a friend, or a recording of yourself), you explain your approach before writing code, then state the complexity
- [ ] The last two weeks: medium problems solved in 30 minutes or less, most of the time

**Patterns to cover**:
- Arrays and hashing
- Two pointers
- Sliding window
- Stack
- Binary search
- Linked lists
- Trees: traversals, BFS and DFS
- Heaps and priority queues
- Backtracking
- Graphs: BFS, DFS, topological sort
- Dynamic programming: 1D, then 2D
- Greedy
- Intervals
- Bit manipulation and math (a few problems only)

**Key concepts**:
- Big-O for time and space: what dominates, amortised cost, and why O(n log n) is usually the floor for sorting
- The cost of each operation in arrays, hash maps, sets, stacks, queues, heaps, trees and graphs
- Recursion vs iteration, and when the call stack becomes a problem
- Memoisation vs tabulation in dynamic programming
- Recognising a pattern from the problem statement: what in the wording points to it
- Trading space for time, and when it's worth it

**Watch out for**:
- Reading the solution too early. The struggle is where the learning happens; use a fixed attempt time first.
- Collecting solved counts. 75 problems you can re-solve beat 300 you've only seen once.
- Skipping the out-loud practice. Solving silently and explaining while you solve are different skills.

**Local environment**: Just the language toolchain. No containers needed.

**Testing requirement**: Every problem has a test file covering the examples from the statement plus your own edge cases (empty input, one element, duplicates, large input). CI runs on every push:
- TypeScript: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥ 90%; the solutions are small, so coverage should be near total)
- Python: `ruff` → `pytest --cov` (≥ 90%)

**AI usage note**: Don't let AI solve the problem, give you the pattern, or autocomplete your code during an attempt. Turn editor AI suggestions off for this repo. After you've solved it, AI is useful for questions like *"Is there a solution with better space complexity?"*, *"What edge case would break this?"* or *"Explain why this is O(n log n) and not O(n²)"*. Check complexity claims against a reference; AI gets them wrong sometimes.

**What to put in README**: "Data structures and algorithms practice, organised by pattern. Every solution has tests and a complexity analysis; `PATTERNS.md` holds the patterns in my own words, and `LOG.md` the practice record."
