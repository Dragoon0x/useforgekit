# Testing Strategy

## What Claude Does Wrong with Tests

| Bad Pattern | Why It's Bad | Correct Pattern |
|------------|-------------|----------------|
| Tests that mirror implementation | Break on every refactor | Test behavior, not implementation |
| Mocking everything | Tests pass but code is broken | Mock boundaries only (DB, APIs, file system) |
| `expect(result).toBeDefined()` | Passes for ANY non-undefined value | `expect(result).toEqual(specificExpected)` |
| One giant test per function | Hard to diagnose failures | One assertion per test |
| Tests that depend on order | Flaky, fail in isolation | Each test sets up its own state |
| `console.log` in tests | Noise in output | Use assertion messages |
| No edge case tests | Bugs slip through | Test: null, empty, boundary, invalid |

## Test Priority Pyramid

| Priority | Type | Coverage Target | Speed |
|----------|------|----------------|-------|
| P0 | Unit tests (core logic) | 80%+ of core modules | < 1s each |
| P1 | Integration tests (API, DB) | All endpoints | < 5s each |
| P2 | Edge case tests | All exported functions | < 1s each |
| P3 | E2E tests (critical paths) | Sign up, login, core flow | < 30s each |

## Test File Organization

```
src/
├── utils/
│   ├── formatDate.ts
│   └── formatDate.test.ts        ← Co-located (preferred)
└── components/
    ├── Button.tsx
    └── Button.test.tsx

OR

src/utils/formatDate.ts
tests/utils/formatDate.test.ts    ← Mirror structure (also fine)
```

Pick one pattern. Be consistent. Tell Claude which one in CLAUDE.md.

## The AAA Pattern

Every test follows Arrange-Act-Assert:

```typescript
it('formats a valid date correctly', () => {
  // Arrange
  const input = new Date('2026-04-15');

  // Act
  const result = formatDate(input);

  // Assert
  assert.strictEqual(result, 'Apr 15, 2026');
});
```

## Test-Driven Prompt

```
"I want to add [feature]. Write the tests FIRST:
1. Write failing tests for the expected behavior
2. Run them — they should all fail
3. Implement the feature
4. Run tests — they should all pass
5. Refactor if needed
6. Run tests — still passing"
```

## Edge Cases to Always Test

| Input Type | Edge Cases |
|-----------|-----------|
| String | `""`, `" "` (whitespace only), very long (10K+ chars), unicode, emoji, null, undefined |
| Number | `0`, `-1`, `NaN`, `Infinity`, `Number.MAX_SAFE_INTEGER`, float precision |
| Array | `[]`, `[null]`, very large (100K+ items), nested, sparse |
| Object | `{}`, missing required keys, extra keys, circular references |
| Date | Invalid date, leap year (Feb 29), DST transition, timezone edge, epoch (1970-01-01) |
| File | Empty file, binary file, very large file, file not found, permission denied |
| API | 200, 400, 401, 403, 404, 500, timeout, network error, rate limit |

## Coverage Targets

| Component | Target | Why |
|-----------|--------|-----|
| Core business logic | 90%+ | Bugs here = user impact |
| Utilities/helpers | 80%+ | Reused everywhere |
| API endpoints | 100% of routes | Every endpoint tested |
| UI components | 60-70% | Visual testing supplements |
| Config/setup | 30% | Rarely changes |

## Generating Tests with Claude

Best prompt for test generation:

```
"Read src/utils/formatDate.ts. Write vitest tests in src/utils/formatDate.test.ts.

Cover:
1. Every exported function
2. Every parameter combination
3. Null/undefined/empty inputs
4. Invalid inputs (wrong types)
5. Boundary values
6. Error messages are specific and helpful

Use assert.strictEqual for exact matches.
Use assert.throws for error cases with message matching.
Don't use snapshots (they break on format changes).
Don't mock internal functions (test through the public API)."
```
