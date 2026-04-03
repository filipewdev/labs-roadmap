# Rust Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why Rust: memory safety without a garbage collector, the fastest compiled language
> for systems work, increasingly used in embedded (RTOS, IoT firmware) and
> infrastructure (Cloudflare workers, Linux kernel modules, AWS Firecracker).
> It has the steepest learning curve here — the borrow checker will fight you.
> That fight is the learning.

---

### Rust-A — CLI Tool: Real-World File Processor ★

**Companion to**: Lab 00-A (Testing), Lab 17 (Bash scripting)  
**Time box**: 8 hours  
**What**: Build a `logparse` CLI tool that reads Nginx JSON access logs,
filters by status code, aggregates by endpoint, and outputs a report.

**Why Rust for this**:
- Rust's `clap` crate makes CLI ergonomics excellent
- File I/O and JSON parsing without a garbage collector forces you to understand
  ownership before you need it for harder Rust
- The tool is genuinely useful (replaces the `jq` pipeline in Lab 17)

**What you'll learn**:
- Rust ownership and borrowing (the borrow checker's rules)
- Error handling with `?` operator and `anyhow` / `thiserror`
- Iterators and closures (Rust's functional-ish style)
- `serde` for JSON deserialization
- `clap` for argument parsing with auto-generated help text

**Done when**:
- [ ] `logparse --file access.log --status 5xx --top 10` works
- [ ] Output: table of top 10 paths by 5xx count with percentage
- [ ] Streams the file (doesn't load entire file into memory)
- [ ] Rust tests (`cargo test`) cover the parsing and aggregation logic
- [ ] Compiles with `--release` and is under 3MB binary

**Key Rust concept to internalize**: Why does this not compile?
```rust
let v = vec![1, 2, 3];
let first = &v[0];  // borrow
v.push(4);          // ERROR: cannot borrow `v` as mutable because it's also borrowed
println!("{}", first);
```
The compiler prevents a use-after-reallocation bug that would be a silent crash in C.

**Testing**: Unit tests for log line parsing, aggregation logic. Integration test
that processes a generated log file and asserts the top endpoint is correct.

---

### Rust-B — HTTP Server with Axum ★★

**Companion to**: Lab 03 (REST API)  
**Time box**: 10 hours  
**What**: Reimplement Lab 03's book collection REST API in Rust using Axum.
Exact same API contract — compare the two implementations.

**Why this matters**: You'll write the same API twice, in TypeScript and Rust.
The comparison is the education. What's easier? What's harder? What does the
type system catch in Rust that TypeScript misses?

**What you'll learn**:
- Axum routing and extractors (`Path`, `Query`, `Json`, `State`)
- `async`/`await` in Rust (Tokio runtime)
- Rust's type system for API design (exhaustive match on error types)
- `sqlx` for PostgreSQL queries (compile-time SQL verification — this is extraordinary)
- Why Rust's error handling via `Result<T, E>` beats TypeScript's `try/catch`

**Done when**:
- [ ] All CRUD endpoints work with identical JSON contract to Lab 03
- [ ] PostgreSQL via `sqlx` with compile-time query checking
- [ ] Proper error responses (not 500 for everything)
- [ ] Integration tests using `axum::test` (no real HTTP server)
- [ ] `cargo clippy` passes with no warnings

**Key moment**: When you use `sqlx::query_as!()`, the macro verifies your SQL
query against the actual database schema at *compile time*. A typo in a column
name is a compile error, not a runtime error at 3am in production. This is
genuinely better than anything TypeScript ORM can offer.

---

### Rust-C — Build a Memory-Safe CSV Parser ★★

**Companion to**: Lab 26 (Python ML), Lab 08 (PostgreSQL)  
**Time box**: 8 hours  
**What**: Write a CSV parser from scratch (no `csv` crate for the core parser).
Then use it to parse the IBGE municipalities dataset and load it into PostgreSQL.

**Why**: Writing a parser in Rust teaches you why memory layout matters,
how string slicing without copying works (zero-copy parsing with lifetimes),
and how Rust's ownership model enables performance patterns impossible in GC languages.

**Done when**:
- [ ] Custom parser handles: quoted fields, escaped quotes, multi-line fields
- [ ] Zero-copy: parsed fields are `&str` slices of the original input, not `String` copies
- [ ] Benchmarked against the `csv` crate using `criterion`
- [ ] Loads IBGE data into PostgreSQL via `sqlx`
- [ ] Property-based tests using `proptest` (generate random CSV and verify round-trip)

---

### Rust-D — WebAssembly Module ★★

**Companion to**: Lab 01 (TypeScript Types), Lab 04 (WebSocket)  
**Time box**: 8 hours  
**What**: Write a high-performance text processing module in Rust, compile it to
WebAssembly, and call it from your TypeScript code. Use case: fast CPF batch
validation for a form that validates 10,000 CPFs server-side in a bulk import.

**Done when**:
- [ ] Rust crate compiles to `.wasm` via `wasm-pack`
- [ ] TypeScript calls the Wasm module (no JS in the hot path)
- [ ] Benchmarked: Wasm vs pure TypeScript for 100k CPF validations
- [ ] Tests in both Rust (`cargo test`) and TypeScript (testing the Wasm interface)

**The result you should see**: Wasm is 5–15x faster for CPU-bound tasks
like this. The benchmark result becomes part of your README and is a
concrete, credible performance claim.

---

### Rust-E — Embedded: GPIO Control on Raspberry Pi ★★★

**Companion to**: Lab 23 (Raspberry Pi), Lab 24 (Arduino)  
**Time box**: 10 hours  
**What**: Control a GPIO pin on the Raspberry Pi using Rust instead of Python.
Turn an LED on and off from a REST API written in Rust (Axum).

**Why this is ★★★**: Cross-compilation (compile on laptop, run on ARM Pi),
`unsafe` Rust for hardware register access, and understanding why embedded
Rust is displacing C in new firmware projects.

**Done when**:
- [ ] Cross-compiled from x86 laptop to ARM64 Pi using `cross` tool
- [ ] Axum server runs on Pi, responds to `POST /led/{on|off}`
- [ ] GPIO controlled via `rppal` crate (safe Rust GPIO)
- [ ] One `unsafe` block — write to a GPIO register directly — with comment explaining why it's safe

---
