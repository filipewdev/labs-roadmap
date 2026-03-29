# Lab 00-A — Testing Fundamentals

> **Time box**: 8 hours  
> **Language**: TypeScript  
> **AWS parallel**: Lambda function testing patterns, unit testing shared utilities used across services

---

## Goal

Write a small utility library that works with real Brazilian data, then test it
thoroughly with Vitest — covering unit tests, edge cases, HTTP mocking, and
integration with a live API.

---

## Done When

- [ ] Library has 6 functions, all with full test suites
- [ ] CPF and CNPJ validation use the real algorithm (not regex)
- [ ] `formatCEP` is tested with a mocked BrasilAPI call — no real HTTP in tests
- [ ] At least one test uses `vi.spyOn` and one uses fake timers
- [ ] `vitest run --coverage` reports ≥ 90% line coverage
- [ ] CI passes on GitHub Actions

---

## What to Build: `@yourname/br-utils`

A library of Brazilian-specific utilities. This is real-world value — you will
use these in the capstone projects.

```typescript
// src/cpf.ts
validateCPF(cpf: string): boolean
formatCPF(cpf: string): string         // "12345678909" → "123.456.789-09"
unformatCPF(cpf: string): string       // "123.456.789-09" → "12345678909"

// src/cnpj.ts
validateCNPJ(cnpj: string): boolean
formatCNPJ(cnpj: string): string

// src/cep.ts
formatCEP(cep: string): string         // "70040010" → "70040-010"
lookupCEP(cep: string): Promise<CEPResult>  // calls BrasilAPI

// src/currency.ts
formatBRL(amount: number): string      // 1234.5 → "R$ 1.234,50"
parseBRL(formatted: string): number   // "R$ 1.234,50" → 1234.5

// src/date.ts
formatRelativeDate(date: Date, now?: Date): string  // "há 5 minutos"
isBusinessDay(date: Date): boolean     // excludes weekends
```

---

## CEP Lookup: Testing HTTP Calls

The `lookupCEP` function calls BrasilAPI. In tests, you must **never** make real
HTTP calls — they're slow, flaky, and depend on external availability.

```typescript
// src/cep.ts
export interface CEPResult {
  cep: string
  street: string
  neighborhood: string
  city: string
  state: string
}

export async function lookupCEP(cep: string): Promise<CEPResult> {
  const cleaned = cep.replace(/\D/g, '')
  if (cleaned.length !== 8) throw new Error(`Invalid CEP: ${cep}`)

  const response = await fetch(`https://brasilapi.com.br/api/cep/v2/${cleaned}`)
  if (!response.ok) throw new Error(`CEP not found: ${cep}`)

  const data = await response.json()
  return {
    cep: data.cep,
    street: data.street,
    neighborhood: data.neighborhood,
    city: data.city,
    state: data.state,
  }
}
```

```typescript
// src/cep.test.ts
import { describe, it, expect, vi, beforeEach, afterEach } from 'vitest'
import { lookupCEP } from './cep'

describe('lookupCEP', () => {
  beforeEach(() => {
    // Mock global fetch before each test
    vi.stubGlobal('fetch', vi.fn())
  })

  afterEach(() => {
    vi.unstubAllGlobals()
  })

  it('returns formatted address for valid CEP', async () => {
    // Arrange: fake the API response
    vi.mocked(fetch).mockResolvedValueOnce({
      ok: true,
      json: async () => ({
        cep: '70040-010',
        street: 'SPP',
        neighborhood: 'Zona Cívico-Administrativa',
        city: 'Brasília',
        state: 'DF',
      }),
    } as Response)

    // Act
    const result = await lookupCEP('70040010')

    // Assert
    expect(result.city).toBe('Brasília')
    expect(result.state).toBe('DF')
    // Verify we called the right URL
    expect(fetch).toHaveBeenCalledWith('https://brasilapi.com.br/api/cep/v2/70040010')
  })

  it('throws when API returns 404', async () => {
    vi.mocked(fetch).mockResolvedValueOnce({ ok: false } as Response)
    await expect(lookupCEP('00000000')).rejects.toThrow('CEP not found')
  })

  it('throws before calling API if CEP has wrong length', async () => {
    await expect(lookupCEP('1234')).rejects.toThrow('Invalid CEP')
    expect(fetch).not.toHaveBeenCalled()  // Validation happens before HTTP
  })

  it('strips formatting before calling API', async () => {
    vi.mocked(fetch).mockResolvedValueOnce({
      ok: true,
      json: async () => ({ cep: '70040-010', street: '', neighborhood: '', city: '', state: '' }),
    } as Response)

    await lookupCEP('70040-010')  // formatted input

    expect(fetch).toHaveBeenCalledWith('https://brasilapi.com.br/api/cep/v2/70040010')
  })
})
```

**Why mock and not call the real API?**
Real HTTP calls in tests are:
- **Slow**: each call adds 100–500ms to your test suite
- **Flaky**: BrasilAPI could be down during CI
- **Non-deterministic**: the API might change its response format
- **Rate-limited**: CI running 50 times a day would get throttled

You test your code's behaviour around the HTTP call. The API itself is not
your responsibility to test.

---

## CPF Validation: The Real Algorithm

This is one of the most common Brazilian validation functions. Write it from
the specification, not from existing code:

```
CPF has 11 digits: 9 base digits + 2 check digits
Example: 123.456.789-09

Step 1: First check digit
Multiply first 9 digits by weights 10, 9, 8, 7, 6, 5, 4, 3, 2
Sum the products
Remainder = sum % 11
If remainder < 2, first check digit = 0, else first check digit = 11 - remainder

Step 2: Second check digit
Multiply first 10 digits by weights 11, 10, 9, 8, 7, 6, 5, 4, 3, 2
Same remainder logic → second check digit

Edge case: CPFs with all identical digits (000.000.000-00, 111.111.111-11, etc.)
are arithmetically valid but are explicitly invalid by Receita Federal rules.
```

Test this with known-valid and known-invalid CPFs. Use BrasilAPI's `/api/cpf`
if it exists, or known test CPFs from the Receita Federal specification.

---

## What to Write in Your README

Use the lab README template. Here's what the "Key Concepts Learned" section
should look like for this lab — use this as the quality bar for all future labs:

---

### Why HTTP calls must be mocked in tests

When `lookupCEP` is called in production, it reaches BrasilAPI over the internet.
If I called the real API in my tests, the test would take 300ms instead of 1ms,
fail during CI if BrasilAPI is down, and be non-deterministic if the API returns
slightly different data. More importantly, I'm not testing BrasilAPI — I'm testing
*my* code's behaviour when given a successful response, a 404, a network error, etc.
Mocking `fetch` lets me simulate all those cases deterministically.

### The difference between unit tests and integration tests

The tests in this lab are *unit tests*: they test one function in complete isolation.
The `fetch` call is replaced with a mock, the database doesn't exist, nothing external
is touched. A true *integration test* for `lookupCEP` would call the real BrasilAPI
and assert the response format hasn't changed. Both kinds of tests are valuable, but
they serve different purposes. Integration tests go in a separate test file
(`*.integration.test.ts`) and are not run in CI by default.

### Why CPFs with all identical digits fail

`111.111.111-11` passes the check digit algorithm mathematically. The Receita
Federal added an explicit rule excluding these because they're obviously fake —
no legitimate CPF would have all digits the same. This is a reminder that
validation algorithms have two layers: mathematical correctness and business rules.
The business rules must be encoded explicitly; the algorithm alone isn't enough.

---

## AWS Parallel

In AWS, utility functions like these would typically live in a **Lambda Layer** —
a shared dependency package that multiple Lambda functions can reference. The
testing patterns are identical: you mock external HTTP calls, keep functions
pure, and test the business logic in isolation from infrastructure.

When using Lambda for functions that call external APIs (like BrasilAPI), you'd
configure a **VPC endpoint** or use **NAT Gateway** for egress — the networking
knowledge from your SAA studies directly applies.

---

## AI Usage Note

After writing the CPF validation yourself: paste your implementation to AI and ask
*"Is my implementation of the CPF check digit algorithm correct? Test it against
these specific CPFs: [list 5 known-valid and 3 known-invalid]"*.

This is correct AI use: verifying your own work with specific test cases,
not generating the implementation.
