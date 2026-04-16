# Safe Refactoring with Claude Code

## The Iron Rule

**Tests first. Always.** If there are no tests, write tests BEFORE refactoring.
If you refactor without tests, you're not refactoring — you're rewriting.

## Safe Refactoring Steps

```
1. Run all tests → must pass (baseline)
2. Make ONE small change
3. Run all tests → must still pass
4. Commit
5. Repeat 2-4 until done
6. Review the full diff at the end
```

## Common Refactoring Patterns

### Extract Function
**When:** A function is > 30 lines, or a block of code has a clear purpose.
```
Before: 50-line function with inline validation
After: 30-line function + extracted validateInput()
Tests: Must still pass without changes
```

### Extract Module
**When:** A file is > 300 lines, or a group of functions belongs together.
```
Before: utils.ts with 40 functions
After: utils/dates.ts, utils/format.ts, utils/validation.ts
Tests: Update import paths, run tests
```

### Replace Conditional with Polymorphism
**When:** Multiple if/else or switch chains on the same value.
```
Before: if (type === 'a') {...} else if (type === 'b') {...}
After: handlers = { a: handleA, b: handleB }; handlers[type]()
Tests: Same behavior, new structure
```

### Inline Temporary Variable
**When:** A variable is used once and the expression is clear.
```
Before: const isValid = input.length > 0; if (isValid) {...}
After: if (input.length > 0) {...}
```

### Rename Symbol
**When:** A name is misleading, abbreviated, or unclear.
```
Claude prompt: "Rename the function 'proc' to 'processPayment' everywhere it's used.
Use the project's rename refactoring tools if available. Run tests after."
```

## Anti-Patterns

| Pattern | Risk | Fix |
|---------|------|-----|
| Refactor + add feature in one commit | Can't revert independently | Separate commits |
| Refactor without tests | Behavior changes undetected | Write tests first |
| Big-bang refactor (everything at once) | Hard to debug if tests break | Small steps |
| Renaming + restructuring simultaneously | Git can't track the rename | Rename first, move second |
| "While I'm here..." changes | Scope creep, unrelated diffs | Separate PR |
