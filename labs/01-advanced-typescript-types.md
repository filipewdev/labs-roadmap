# Lab 01 — Advanced TypeScript Types

**Goal**: Stop avoiding generics and utility types. Build a small type-safe form
validation library using only TypeScript's type system.

**Time box**: 6 hours

**Language**: TypeScript (no runtime dependencies — types only)

**Done when**:
- [ ] A `validate<T>(schema: Schema<T>, data: unknown): Result<T, ValidationError[]>` function exists and is fully typed
- [ ] Schema supports: required strings, optional numbers, email pattern, min/max length, nested objects
- [ ] The type system rejects invalid schemas at compile time — no `any` escapes
- [ ] 30+ tests with Vitest, including type-level tests using `expectTypeOf`

**Key concepts**:
- `keyof`, `typeof`, mapped types (`{ [K in keyof T]: ... }`)
- Conditional types (`T extends string ? ... : ...`)
- `infer` keyword (extract the type "inside" a generic)
- Discriminated unions for `Result<T, E>`
- Template literal types

**Testing requirement**: Unit tests for all validators. Use `expectTypeOf` from Vitest to assert that valid inputs produce the right output type. Test that invalid inputs produce typed errors, not generic ones.

**AI usage note**: When stuck on a type error, paste it to AI and ask: *"Explain what this TypeScript error means"* — not *"fix my type"*. Understand the error first, then fix it yourself.

**What to put in README**: "A zero-dependency form validation library built entirely with TypeScript's type system. Schema definitions are type-safe at compile time — invalid schemas fail to compile."

**Stretch**: Add a `transform` step that can coerce strings to numbers or dates as part of validation.

---
