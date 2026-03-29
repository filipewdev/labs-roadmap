# Lab 02 — Error Handling Patterns

**Goal**: Replace `try/catch` spaghetti with typed, composable error handling
using the `Result` pattern.

**Time box**: 5 hours

**Language**: TypeScript

**Done when**:
- [ ] A `Result<T, E>` type is implemented with `ok()`, `err()`, `map()`, `flatMap()`, `match()` methods
- [ ] A real HTTP client wrapper returns `Result<Data, HttpError>` instead of throwing
- [ ] A multi-step file processing pipeline uses `flatMap` to chain operations without nested try/catch
- [ ] Tests cover: successful path, each error type, chained operations, `match` exhaustiveness

**Key concepts**:
- Why throwing is problematic (invisible control flow, forces callers to know internals)
- `Result<T, E>` / `Either<L, R>` pattern
- Discriminated unions for error types
- Railway-oriented programming (happy path + error path run in parallel)
- When to use `Result` vs when to throw (expected errors vs programmer errors)

**Testing requirement**: Test every method on `Result`. Test the HTTP wrapper with a mock fetch that returns both success and error responses. Verify that `match` handles all cases. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥85% coverage).

**AI usage note**: Ask AI: *"What are the tradeoffs between Result types vs exceptions in TypeScript? When is each more appropriate?"* Use this to form your own opinion, not to get a definitive answer.

**What to put in README**: "Error handling library implementing the Result pattern, enabling composable error propagation without exceptions."

**Stretch**: Add `Result.all(results)` that succeeds only if all results succeed, collecting all errors if any fail.

---
