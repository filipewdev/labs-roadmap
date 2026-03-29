# Lab OS-C — Publish a Dart Package to pub.dev

> **Phase**: Open Source Track (do after OS-A and Lab 21)
> **Time box**: 6 hours
> **Language**: Dart/Flutter
> **Why a separate lab from OS-A**: pub.dev has different conventions, scoring,
> and community expectations from npm. The Dart ecosystem values documentation
> quality and platform support declarations heavily — more so than npm.

---

## Goal

Publish a genuinely useful Dart package to pub.dev. The candidate: a
`br_validators` package with Dart/Flutter-native CPF, CNPJ, and CEP validation —
the same logic as `br-utils` from OS-A, but for the Flutter ecosystem.

---

## Done When

- [ ] Package published at `pub.dev/packages/br_validators`
- [ ] pub.dev score: ≥ 130/160 points (achievable with proper setup)
- [ ] Works on all platforms: Android, iOS, Web, Linux, macOS, Windows
- [ ] `dart doc` documentation generated
- [ ] Example app included in `example/` directory
- [ ] `CHANGELOG.md` present and correctly formatted

---

## Why pub.dev Scoring Matters

pub.dev automatically scores every package in three categories:

```
Likes (40 pts max)      — community thumbs-up, grows over time
Pub Points (130 pts max) — automated quality checks (your focus):
  ├── Follows Dart file conventions          (10 pts)
  ├── Provides documentation                 (10 pts)
  ├── Supports multiple platforms            (20 pts)
  ├── Passes static analysis (no warnings)  (30 pts)
  ├── Supports up-to-date dependencies      (10 pts)
  └── Has a changelog                        (10 pts)
Popularity (100 pts)    — download count, grows organically
```

A score of 130/160 is achievable on day one with proper setup.
A score of 100/160 is "lazy setup." The 30-point gap between them is entirely
under your control and takes about 2 extra hours.

---

## Part 1: Package Structure

Dart packages have strong conventions. Follow them exactly:

```
br_validators/
├── lib/
│   ├── br_validators.dart          ← Main library file (exports everything)
│   └── src/
│       ├── cpf.dart
│       ├── cnpj.dart
│       └── cep.dart
├── test/
│   ├── cpf_test.dart
│   ├── cnpj_test.dart
│   └── cep_test.dart
├── example/
│   └── main.dart                   ← Required for pub.dev score
├── pubspec.yaml
├── README.md                       ← First thing users see on pub.dev
├── CHANGELOG.md
├── LICENSE                         ← MIT license (required for pub points)
└── analysis_options.yaml           ← Strict linting config
```

### pubspec.yaml

```yaml
name: br_validators
description: >
  Brazilian document and address validation for Dart and Flutter.
  Validates CPF, CNPJ, and CEP using official algorithms.
  No external dependencies.
version: 0.1.0
homepage: https://github.com/yourname/br_validators

environment:
  sdk: '>=3.0.0 <4.0.0'

# No dependencies — this is a pure Dart package
# Pure Dart = works on ALL platforms automatically

dev_dependencies:
  lints: ^3.0.0
  test: ^1.24.0
```

**No runtime dependencies** is a feature, not a limitation. Every dependency
you add is a dependency your users must manage. A validation package with no
external dependencies is a gift to the ecosystem.

### analysis_options.yaml

```yaml
include: package:lints/recommended.yaml

analyzer:
  errors:
    # Treat these as errors, not warnings
    missing_return: error
    dead_code: warning

linter:
  rules:
    - always_declare_return_types
    - avoid_dynamic_calls
    - avoid_print
    - cancel_subscriptions
    - prefer_const_constructors
    - prefer_final_fields
    - sort_pub_dependencies
```

Running `dart analyze` with zero warnings/errors is worth 30 pub points
and signals that you care about code quality.

---

## Part 2: Dart-Specific Documentation

Dart documentation uses `///` (triple slash) comments, not `/** */`:

```dart
// lib/src/cpf.dart

/// Validates a Brazilian CPF (Cadastro de Pessoas Físicas) number.
///
/// Accepts both formatted and unformatted input:
/// - Formatted: `"123.456.789-09"`
/// - Unformatted: `"12345678909"`
///
/// Returns `false` for CPFs with all identical digits (e.g., `"111.111.111-11"`),
/// even though they pass the check-digit algorithm. This is consistent with
/// Receita Federal rules.
///
/// Example:
/// ```dart
/// BrValidators.validateCPF('123.456.789-09'); // true
/// BrValidators.validateCPF('111.111.111-11'); // false
/// BrValidators.validateCPF('invalid');        // false
/// ```
static bool validateCPF(String cpf) {
  final digits = cpf.replaceAll(RegExp(r'[^\d]'), '');

  if (digits.length != 11) return false;
  if (RegExp(r'^(\d)\1{10}$').hasMatch(digits)) return false;

  // First check digit
  var sum = 0;
  for (var i = 0; i < 9; i++) {
    sum += int.parse(digits[i]) * (10 - i);
  }
  var remainder = sum % 11;
  final firstCheck = remainder < 2 ? 0 : 11 - remainder;
  if (int.parse(digits[9]) != firstCheck) return false;

  // Second check digit
  sum = 0;
  for (var i = 0; i < 10; i++) {
    sum += int.parse(digits[i]) * (11 - i);
  }
  remainder = sum % 11;
  final secondCheck = remainder < 2 ? 0 : 11 - remainder;
  return int.parse(digits[10]) == secondCheck;
}
```

---

## Part 3: The Example App (Required for pub.dev score)

```dart
// example/main.dart
// This file appears directly on the pub.dev package page.
// Write it as if it's a tutorial, not just a demo.

import 'package:br_validators/br_validators.dart';

void main() {
  // CPF Validation
  // The most common Brazilian individual taxpayer document
  print(BrValidators.validateCPF('123.456.789-09')); // true
  print(BrValidators.validateCPF('000.000.000-00')); // false (all zeros)

  // CPF Formatting
  print(BrValidators.formatCPF('12345678909'));   // "123.456.789-09"
  print(BrValidators.unformatCPF('123.456.789-09')); // "12345678909"

  // CNPJ Validation
  // Brazilian company registration number
  print(BrValidators.validateCNPJ('11.222.333/0001-81')); // true

  // CEP Formatting
  // Brazilian postal code — used with the BrasilAPI for address lookup
  print(BrValidators.formatCEP('70040010')); // "70040-010"
}
```

---

## Part 4: Testing in Dart

```dart
// test/cpf_test.dart
import 'package:br_validators/br_validators.dart';
import 'package:test/test.dart';

void main() {
  group('validateCPF', () {
    test('returns true for valid formatted CPF', () {
      expect(BrValidators.validateCPF('123.456.789-09'), isTrue);
    });

    test('returns true for valid unformatted CPF', () {
      expect(BrValidators.validateCPF('12345678909'), isTrue);
    });

    test('returns false for all-identical-digits CPF', () {
      for (var digit = 0; digit <= 9; digit++) {
        final cpf = '$digit' * 11;
        expect(
          BrValidators.validateCPF(cpf),
          isFalse,
          reason: 'CPF $cpf (all $digit) should be invalid',
        );
      }
    });

    test('returns false for wrong check digits', () {
      expect(BrValidators.validateCPF('123.456.789-00'), isFalse);
    });

    test('returns false for CPF with wrong length', () {
      expect(BrValidators.validateCPF('1234567890'), isFalse);   // 10 digits
      expect(BrValidators.validateCPF('123456789012'), isFalse); // 12 digits
    });

    test('returns false for empty string', () {
      expect(BrValidators.validateCPF(''), isFalse);
    });
  });
}
```

Run tests: `dart test`

---

## Part 5: Publishing

```bash
# Verify your package looks correct before publishing
dart pub publish --dry-run
# Read every warning. Fix all of them.

# Check your pub points before publishing (pana = Package ANAlyzer)
dart pub global activate pana
pana .
# You'll see exactly which checks pass/fail

# Publish (requires Google account for authentication)
dart pub publish
```

**The first publish is irreversible**: once a version is published to pub.dev,
it cannot be deleted (only "discontinued"). This is by design — it prevents
the npm left-pad incident (a maintainer deleting a package that millions of
projects depended on). Double-check everything with `--dry-run` first.

---

## Key Difference from npm Publishing

On npm, you can publish something rough and iterate quickly. pub.dev's scoring
system creates public, visible quality signals that persist. A package with
60/160 pub points looks abandoned or low-quality. A package with 130/160 looks
maintained and professional.

The pub.dev community also tends to be smaller and more cohesive than npm.
A useful Brazilian validation package may get noticed by the Flutter Brazil
community. That kind of organic recognition is more valuable than the package
itself for networking purposes.

---

## After Publishing: Cross-Promote

Once both `@yourname/br-utils` (npm) and `br_validators` (pub.dev) are published:

Add to your GitHub profile README:
```markdown
## Published Packages

| Package | Platform | Description |
|---------|----------|-------------|
| [@yourname/br-utils](https://npmjs.com/...) | npm (TypeScript) | Brazilian CPF, CNPJ, CEP utilities |
| [br_validators](https://pub.dev/...)        | pub.dev (Dart)   | Same utilities for Flutter |
```

Two published packages in two ecosystems, covering the same domain, is a
stronger signal than two published packages in the same ecosystem — it shows
breadth and the ability to context-switch between different communities.
