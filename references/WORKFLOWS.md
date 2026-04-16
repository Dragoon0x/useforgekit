# Workflows — Common Development Patterns

## The Standard Loop

```
1. Understand (read code, read issue)
2. Plan (think through approach)
3. Implement (write code)
4. Test (run tests, fix failures)
5. Review (check quality)
6. Commit (clean commit message)
```

Every workflow follows this loop. Commands automate parts of it.

## Feature Development

```
/fix-issue [number]
→ Claude reads the issue, finds code, implements fix, writes test, commits

OR manually:
1. Create branch: git checkout -b feature/[name]
2. Understand: read relevant code
3. Plan: describe approach before coding
4. Implement: write the code
5. Test: npm test
6. Review: /review (or invoke code-reviewer agent)
7. Commit: git add -A && git commit -m "feat: [description]"
8. PR: /create-pr
```

## Bug Fix

```
1. Reproduce: understand the bug
2. Isolate: find the root cause (don't guess)
3. Fix: smallest change that fixes the root cause
4. Test: write a test that catches this bug
5. Verify: run the new test + full suite
6. Commit: "fix: [what was broken] (#issue)"
```

## Code Review Workflow

```
1. Invoke code-reviewer agent
2. Agent reads diff, checks for issues
3. Agent reports: Critical / Warning / Suggestion
4. Fix Critical issues
5. Address Warnings if time allows
6. Note Suggestions for later
```

## Refactoring Workflow

```
1. Write tests FIRST (if they don't exist)
2. Verify tests pass
3. Refactor in small steps
4. Run tests after EACH step
5. If tests break → revert last step, try smaller change
6. Commit after each successful step
```

## Debugging Workflow

```
1. Reproduce the bug consistently
2. Add logging/breakpoints to trace data flow
3. Form a hypothesis about the cause
4. Test the hypothesis (don't just fix blindly)
5. If wrong → new hypothesis (max 3 attempts before stepping back)
6. Fix the root cause (not the symptom)
7. Write a regression test
8. Remove debugging artifacts
```

## The "Be Careful" Workflow

For production-critical work:
```
1. Create a branch
2. Enable /careful (blocks destructive commands)
3. Enable /freeze (restrict edits to one directory)
4. Make changes
5. Run full test suite
6. Review diff carefully
7. Unfreeze, commit, PR
```
