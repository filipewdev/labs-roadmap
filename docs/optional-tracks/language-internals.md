# Language Internals Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: every tool you use — TypeScript compiler, ESLint, Prettier,
> SQL parsers, template engines — is built on the same foundation: parsing
> text into a structured tree and transforming it. Building a compiler from
> scratch demystifies all of them. You'll never look at a "syntax error on
> line 42" the same way again.
>
> This is the most intellectually demanding lab in the entire curriculum.
> It's also the one that changes how you think about programming most
> fundamentally.

---

### Lang-A — Build a Tiny Compiler ★★★

**Companion to**: Lab 01 (TypeScript Types), Lab 04 (Go)
**Time box**: 12 hours
**Language**: Go
**What**: Build a compiler for a tiny language that supports: variable
declarations, arithmetic expressions, if/else, while loops, functions,
and print statements. The compiler parses source code into an AST,
type-checks it, and either interprets it directly or compiles to a simple
bytecode that a stack-based VM executes.

**Why Go**: Go's simplicity is an asset here — you want to focus on compiler
concepts, not fight the implementation language. Go's strong typing catches
mistakes early, and its standard library (`unicode`, `strconv`, `fmt`) has
everything you need without external dependencies. No generics needed.

**Why this matters**: Understanding how compilers work makes you better at
everything — writing code (you understand what the compiler does with it),
debugging (error messages make sense), designing APIs (you think in terms
of grammars), and reading other people's code (you can trace evaluation
mentally). It's the most transferable skill in computer science.

**What you'll learn**:
- **Lexer**: turning source text into tokens (identifiers, operators, keywords)
- **Parser**: recursive descent parsing, operator precedence (Pratt parsing)
- **AST**: representing code as a tree of typed nodes
- **Type checker**: verifying types before execution (your language is statically typed)
- **Interpreter** (path A): tree-walking evaluator that executes the AST directly
- **Compiler + VM** (path B): compile AST to bytecode, execute on a stack-based virtual machine
- **Error reporting**: line numbers, column numbers, helpful error messages

**The language (called "Mini")**:
```
// Variable declarations with types
let x: int = 42
let name: string = "hello"
let flag: bool = true

// Arithmetic and comparisons
let result: int = (x + 8) * 2
let check: bool = result > 90

// Control flow
if check {
  print("big number")
} else {
  print("small number")
}

// Loops
let i: int = 0
while i < 10 {
  print(i)
  i = i + 1
}

// Functions
fn add(a: int, b: int): int {
  return a + b
}
print(add(3, 4))
```

**Done when**:
- [ ] Lexer: tokenizes all Mini syntax (identifiers, numbers, strings, operators, keywords)
- [ ] Parser: produces an AST from tokens (recursive descent)
- [ ] Type checker: rejects `let x: int = "hello"` and `if 42 { ... }` with clear errors
- [ ] Path A (Interpreter): tree-walking evaluator executes all Mini features correctly
- [ ] **OR** Path B (Compiler + VM): AST compiles to bytecode, stack-based VM executes it
- [ ] Error messages include line and column numbers
- [ ] At least 5 Mini programs demonstrate all features (variables, math, if/else, loops, functions)
- [ ] REPL: interactive prompt that evaluates Mini expressions line by line
- [ ] Written explanation: how your compiler works, with an AST diagram for a sample program

**Testing requirement**: Unit tests for lexer (token sequences), parser (AST structure),
type checker (valid programs pass, invalid programs produce specific errors), evaluator
(programs produce expected output). At least 20 test programs covering edge cases.
**CI**: `go vet` → `golangci-lint` → `go test -cover` (≥75% coverage).

**AI usage note**: Compiler construction is deep enough that AI explanations are
genuinely useful. Ask: *"How does Pratt parsing handle operator precedence?"* and
*"What's the difference between a tree-walking interpreter and a bytecode VM?"*
But write every line yourself — there's no shortcut to understanding how `a + b * c`
becomes `[PUSH a, PUSH b, PUSH c, MUL, ADD]`.

**Stretch**: Add arrays (`let xs: []int = [1, 2, 3]`) and a `for-in` loop.

---
