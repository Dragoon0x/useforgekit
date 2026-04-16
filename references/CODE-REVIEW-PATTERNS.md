# Code Review Patterns

## The Review Checklist (Priority Order)

### P0 — Correctness
- [ ] Does the code do what the PR description says?
- [ ] Are there logic errors (off-by-one, wrong operator, inverted condition)?
- [ ] Are null/undefined values handled?
- [ ] Are error paths tested?
- [ ] Does it handle concurrent access correctly?

### P1 — Security
- [ ] Is user input validated before use?
- [ ] Are SQL queries parameterized (no string concatenation)?
- [ ] Are secrets excluded from code and logs?
- [ ] Is authentication checked on protected endpoints?
- [ ] Is authorization checked (right user, right resource)?

### P2 — Testing
- [ ] Are new functions covered by tests?
- [ ] Do tests cover edge cases (empty, null, boundary)?
- [ ] Do tests verify error messages, not just error occurrence?
- [ ] Are tests independent (don't depend on order)?

### P3 — Quality
- [ ] Are names clear and descriptive?
- [ ] Are functions < 30 lines?
- [ ] Is there unnecessary code complexity?
- [ ] Are there duplicated patterns that should be extracted?
- [ ] Does the code follow project conventions (CLAUDE.md)?

### P4 — Performance
- [ ] N+1 queries in loops?
- [ ] Missing database indexes for new queries?
- [ ] Unnecessary re-renders in React?
- [ ] Large objects in memory when only a subset is needed?
- [ ] Missing pagination on list endpoints?

## Review Output Template

```
## Code Review — [branch-name]

### Summary
[1-2 sentences: what the changes do]

### Critical (must fix)
- `file.ts:42` — [issue description]. Fix: [how to fix]

### Warning (should fix)
- `file.ts:87` — [issue description]. Fix: [how to fix]

### Suggestion (nice to have)
- `file.ts:15` — [suggestion]

### Verdict
- [ ] APPROVED — ready to merge
- [x] CHANGES REQUESTED — [N] critical issues
```
