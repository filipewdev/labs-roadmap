# Lab OS-A — Create and Publish a Real npm Library

> **Phase**: Open Source Track (do after Lab 00-A)
> **Time box**: 8 hours
> **Language**: TypeScript
> **Standalone**: Builds directly on Lab 00-A — you're publishing `br-utils`
> **AWS parallel**: Publishing a library is analogous to publishing a Lambda Layer
> or a private package to CodeArtifact — the packaging and versioning concepts are identical

---

## Goal

Take the `br-utils` library from Lab 00-A and publish it to npm as a real,
installable package — with TypeDoc documentation, semantic versioning,
a changelog, and a build pipeline that produces both ESM and CJS output.

After this lab, someone can run `npm install @yourname/br-utils` and use
your CPF validator in their project.

---

## Done When

- [ ] Package published to npm under your scope (`@yourname/br-utils`)
- [ ] TypeDoc documentation generated and deployed to GitHub Pages
- [ ] `CHANGELOG.md` exists following keepachangelog.com format
- [ ] Package exports both ESM (`.mjs`) and CJS (`.js`) via the `exports` field
- [ ] `package.json` has correct `main`, `module`, `types`, and `exports` fields
- [ ] Tree-shaking works: importing only `validateCPF` doesn't bundle the CEP code
- [ ] GitHub Actions publishes a new version automatically when a git tag is pushed
- [ ] README on npm page is useful and professional

---

## Why This Is Harder Than It Looks

Publishing to npm is one command. Building a library that other developers
actually want to use is an entirely different skill set.

When you publish an application, mistakes are invisible to users until
something breaks at runtime. When you publish a library, mistakes are
part of your API — other people's code depends on your design decisions.
Changing a function signature in v1.1.0 can break every project using your library.

The skills this lab teaches are:
- **API design**: what does a good function signature look like from the caller's perspective?
- **Semver discipline**: when is a change a patch, minor, or major version bump?
- **Documentation**: TypeDoc comments that become a browsable docs site
- **Dual module output**: why the same library needs both ESM and CJS builds
- **Backwards compatibility**: how to add features without breaking existing users

---

## Part 1: Package Setup (1.5 hours)

### Scoped Package Name

Publishing as `@yourname/br-utils` (scoped) rather than just `br-utils` means:
- No name collision with the 2 million existing npm packages
- Clear ownership signal
- Free to publish privately if needed later

```bash
# If you don't have an npm account:
npm adduser

# Initialize as a scoped package
pnpm init
# Set name to "@yourname/br-utils" in package.json
```

### The Critical `package.json` Fields

Most developers only set `main`. A proper library sets all of these:

```json
{
  "name": "@yourname/br-utils",
  "version": "0.1.0",
  "description": "Brazilian utility functions: CPF, CNPJ, CEP, BRL currency formatting",
  "keywords": ["brazil", "cpf", "cnpj", "cep", "brl", "validation"],
  "homepage": "https://github.com/yourname/br-utils#readme",
  "repository": {
    "type": "git",
    "url": "https://github.com/yourname/br-utils"
  },
  "license": "MIT",
  "author": "Filipe Brandao <your@email.com>",
  
  "main": "./dist/index.cjs",        ← CJS: what require() uses (Node.js, old bundlers)
  "module": "./dist/index.js",       ← ESM: what import uses (modern bundlers)
  "types": "./dist/index.d.ts",      ← TypeScript types
  
  "exports": {
    ".": {
      "import": "./dist/index.js",   ← ESM import
      "require": "./dist/index.cjs", ← CJS require
      "types": "./dist/index.d.ts"
    },
    "./cpf": {
      "import": "./dist/cpf.js",     ← Tree-shakeable: import only CPF
      "require": "./dist/cpf.cjs",
      "types": "./dist/cpf.d.ts"
    },
    "./cnpj": { ... },
    "./cep":  { ... },
    "./currency": { ... }
  },
  
  "files": ["dist", "README.md", "CHANGELOG.md"],
  ← Only these files go into the npm package (not src, not tests)
  
  "sideEffects": false   ← Tells bundlers this library is safe to tree-shake
}
```

**What each field teaches you:**

`main` vs `module`: Node.js uses `main` for `require()`. Webpack/Rollup/Vite
use `module` for ES module imports. You need both because the ecosystem is still
split between CJS and ESM. This is a real source of pain that understanding
removes.

`exports`: The modern way to define entry points. It supersedes `main` and
`module` in Node 12+. The subpath exports (`"./cpf"`) allow consumers to import
only what they need, enabling tree-shaking.

`files`: Without this, `npm publish` includes everything — your test files,
config files, source maps. The `files` field is an allowlist. Smaller package
= faster installs for everyone.

`sideEffects: false`: Tells bundlers that importing a function from your library
doesn't cause side effects (no global mutations, no console.logs on import).
Required for proper tree-shaking.

---

## Part 2: Build Pipeline with tsup (1 hour)

`tsup` bundles TypeScript into ESM + CJS simultaneously. It's used by most
modern TypeScript libraries (Radix UI, tRPC, Zod all use it or similar tools).

```bash
pnpm add -D tsup
```

```typescript
// tsup.config.ts
import { defineConfig } from 'tsup'

export default defineConfig({
  entry: {
    index: 'src/index.ts',   // main entry
    cpf: 'src/cpf.ts',       // sub-entry for tree-shaking
    cnpj: 'src/cnpj.ts',
    cep: 'src/cep.ts',
    currency: 'src/currency.ts',
    date: 'src/date.ts',
  },
  format: ['esm', 'cjs'],    // output both formats
  dts: true,                  // generate .d.ts files
  splitting: false,           // each entry is its own bundle
  sourcemap: true,
  clean: true,                // clean dist/ before each build
  treeshake: true,
})
```

```json
// package.json scripts
"scripts": {
  "build": "tsup",
  "dev": "tsup --watch",
  "test": "vitest run",
  "typecheck": "tsc --noEmit",
  "docs": "typedoc",
  "prepublishOnly": "pnpm run build && pnpm run test"
}
```

`prepublishOnly` is important: it runs automatically before `npm publish`.
You cannot accidentally publish a broken or untested build.

---

## Part 3: TypeDoc Documentation (1 hour)

TypeDoc converts your JSDoc comments into a browsable documentation website.
This is what other developers read before deciding to use your library.

```typescript
// src/cpf.ts — documentation comments that TypeDoc will render

/**
 * Validates a Brazilian CPF (Cadastro de Pessoas Físicas) number.
 *
 * Accepts both formatted ("123.456.789-09") and unformatted ("12345678909") input.
 * CPFs with all identical digits (e.g., "111.111.111-11") are explicitly invalid
 * per Receita Federal rules, even though they pass the check-digit algorithm.
 *
 * @param cpf - The CPF string to validate (formatted or unformatted)
 * @returns `true` if the CPF is valid, `false` otherwise
 *
 * @example
 * ```typescript
 * validateCPF('123.456.789-09') // true
 * validateCPF('12345678909')    // true (unformatted)
 * validateCPF('111.111.111-11') // false (all identical digits)
 * validateCPF('123.456.789-00') // false (wrong check digits)
 * ```
 */
export function validateCPF(cpf: string): boolean {
  // implementation
}
```

```bash
pnpm add -D typedoc

# typedoc.json
{
  "entryPoints": ["src/index.ts"],
  "out": "docs",
  "includeVersion": true,
  "readme": "README.md"
}
```

**Deploy docs to GitHub Pages:**

```yaml
# .github/workflows/docs.yml
name: Deploy Docs

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - run: pnpm install --frozen-lockfile
      - run: pnpm docs
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs
```

After this, your docs live at `https://yourname.github.io/br-utils`.
That URL goes in your `package.json` `homepage` field and in the npm README.

---

## Part 4: Semantic Versioning and CHANGELOG (1 hour)

### Semver: The Contract You Make With Users

```
MAJOR.MINOR.PATCH  →  1.4.2

PATCH (1.4.2 → 1.4.3): Bug fix. Existing code keeps working.
  Example: validateCPF was returning wrong result for CPFs starting with 0

MINOR (1.4.2 → 1.5.0): New feature. Backwards compatible. Existing code keeps working.
  Example: added formatCNPJ function

MAJOR (1.4.2 → 2.0.0): Breaking change. Existing code MAY break.
  Example: renamed validateCPF to isCPFValid, or changed its return type
```

This is a contract. When you bump a MINOR version, you're telling every user
"update safely, nothing will break." When you bump MAJOR, you're saying "read
the migration guide before updating." Violating this contract breaks trust.

### CHANGELOG.md

```markdown
# Changelog

All notable changes to this project will be documented in this file.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)
Versioning: [Semantic Versioning](https://semver.org/)

## [Unreleased]

## [0.2.0] - 2025-06-15

### Added
- `formatCNPJ(cnpj: string): string` — formats a CNPJ string with punctuation
- Sub-path exports for tree-shaking: `import { validateCPF } from '@yourname/br-utils/cpf'`

### Fixed
- `validateCPF` now correctly handles CPFs with leading zeros

## [0.1.0] - 2025-05-01

### Added
- Initial release
- `validateCPF`, `formatCPF`, `unformatCPF`
- `validateCNPJ`
- `lookupCEP` (requires fetch, works in Node 18+ and browsers)
- `formatBRL`, `parseBRL`
- `formatRelativeDate`, `isBusinessDay`
```

---

## Part 5: Automated Publishing with GitHub Actions (1 hour)

```yaml
# .github/workflows/publish.yml
name: Publish to npm

on:
  push:
    tags:
      - 'v*'   # Triggered when you push a tag like "v0.2.0"

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write   # Required for npm provenance
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          registry-url: 'https://registry.npmjs.org'
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm run build
      - run: pnpm test
      - run: pnpm publish --access public --provenance
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

**Publishing workflow:**
```bash
# 1. Update version in package.json
npm version patch   # or minor or major (updates package.json + creates git tag)

# 2. Update CHANGELOG.md manually

# 3. Push with tags
git push && git push --tags

# GitHub Actions sees the new tag and runs the publish workflow
```

`--provenance` is a newer npm feature that cryptographically links a package
to its source code and build process. It appears as a "Provenance" badge on
the npm page and is increasingly expected for security-conscious users.

---

## Part 6: Test the Published Package (30 min)

This is the step most people skip. Don't.

```bash
# Create a completely separate project
mkdir br-utils-consumer && cd br-utils-consumer
pnpm init
pnpm add @yourname/br-utils

# Test it works as an ESM import
node --input-type=module <<< "
import { validateCPF } from '@yourname/br-utils'
console.log(validateCPF('123.456.789-09'))
"

# Test it works as a CJS require
node -e "
const { validateCPF } = require('@yourname/br-utils')
console.log(validateCPF('123.456.789-09'))
"

# Test tree-shaking: import only CPF functions
# (in a real project with Vite, check the bundle size)
node --input-type=module <<< "
import { validateCPF } from '@yourname/br-utils/cpf'
console.log(validateCPF('123.456.789-09'))
"
```

**Why test the published package separately?**
Your local `src/` directory is different from what gets installed from npm.
It's very common to have a working local build and a broken published package
because of a missing `files` entry or an incorrect `exports` path. Testing
in a separate project catches this.

---

## Key Concepts to Explain in Your README

### The ESM/CJS Split (and why it causes so much pain)

Node.js was built around `require()` — the CommonJS (CJS) module system. ES
Modules (`import`/`export`) were added to the JavaScript standard later, and
Node.js added support for them in v12. The problem: they're not
interchangeable. A file that uses `require()` can't natively import an ESM
module. Browsers only support ESM. Many older tools only support CJS. A
library published in 2025 must support both or it excludes part of its
potential user base. `tsup` solves this by building both output formats from
the same TypeScript source.

### Why APIs are harder to change than implementations

Once `validateCPF(cpf: string): boolean` is part of your public API, you can't
change it to `validateCPF(cpf: string, options?: CPFOptions): ValidationResult`
in a minor version — that would break callers that expect a `boolean`. You can
add new functions freely (minor version), fix bugs freely (patch), but changing
what already exists always costs a major version. This constraint forces you to
think carefully about API design before publishing anything.

---

## AWS Parallel

**npm** → **AWS CodeArtifact**: private npm/Maven/PyPI repository inside your AWS account.
Teams use it to host internal packages that shouldn't be public, with IAM-based
access control and automatic upstream mirroring of public npm.

**Semantic versioning + automated publishing** → **CodePipeline**: the same
concept — a git tag triggers a pipeline that builds, tests, and publishes.
In AWS the pipeline might push to ECR (container images) instead of npm,
but the mental model is identical.

---

## AI Usage Note

After writing your TypeDoc comments, ask AI: *"Read these JSDoc comments for
`validateCPF`. Would a developer who has never used this library understand how
to use it from only the documentation? What's missing?"*

Documentation quality is hard to self-assess because you already know what the
code does. AI is useful as a "naive reader" that simulates someone encountering
your API for the first time.
