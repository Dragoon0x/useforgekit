# Debugging with Claude Code

## The Iron Law

**No fixes without investigation.** Claude's instinct is to guess-and-patch.
That works 40% of the time and creates new bugs 30% of the time.
Force systematic debugging.

## The 3-Strike Rule

After 3 failed fix attempts on the same bug, Claude MUST:
1. Stop trying to fix
2. Write a root cause analysis
3. Present findings to you
4. Ask for direction before attempting another fix

## Debugging Prompt Template

```
"Bug: [exact symptom]
Reproduce: [steps or failing test]

DO NOT FIX ANYTHING YET. First:
1. Read the relevant code
2. Trace the data flow from input to the error
3. Form 3 hypotheses about the root cause
4. Test each hypothesis (add logging, check values)
5. Identify the confirmed root cause
6. THEN propose a fix
7. Implement the fix
8. Write a regression test
9. Verify fix + no regressions"
```

## Debugging Methodology

### Step 1: Reproduce

Before anything else, reproduce the bug consistently.

```
"Run the failing test / trigger the error. Show me the exact error output."
```

If it can't be reproduced → intermittent issue → add logging first.

### Step 2: Trace

Follow the data from input to error:

```
"Trace the request from [entry point] through [middleware] to [handler] to [database].
At each step, show me what the data looks like."
```

### Step 3: Hypothesize

```
"Based on the trace, form 3 hypotheses:
1. [Most likely cause]
2. [Second possibility]
3. [Less obvious but possible]

For each: how would you test it?"
```

### Step 4: Test Hypotheses

```
"Test hypothesis 1 by adding a console.log at [location] showing [value].
Run the failing test and show me the output."
```

### Step 5: Confirm Root Cause

```
"Based on the evidence, the root cause is [X] because [evidence].
Write the minimal fix."
```

### Step 6: Regression Test

```
"Write a test that would have caught this bug. The test should fail
without the fix and pass with the fix."
```

## Common Debugging Scenarios

### "It Works Locally But Fails in CI"
```
"Diff the environments:
1. Node version (local vs CI)
2. Environment variables (.env vs CI secrets)
3. Dependencies (node_modules vs fresh install)
4. OS differences (macOS vs Linux)
5. File system (case sensitivity)
6. Timezone"
```

### "It Used to Work, Now It Doesn't"
```
"Use git bisect to find the breaking commit:
git bisect start
git bisect bad HEAD
git bisect good [last-known-good-commit]
[test at each step]
git bisect reset"
```

### "The Error Message Is Useless"
```
"Add structured error context:
try { ... }
catch (error) {
  throw new Error(`Failed to process user ${userId}: ${error.message}`, { cause: error });
}"
```

### "It's Slow But Not Wrong"
```
"Profile the operation:
1. Add timing: console.time('operation'); ... console.timeEnd('operation');
2. For DB queries: EXPLAIN ANALYZE [query]
3. For API calls: measure each external request independently
4. Report: which step takes the longest?"
```

## Scope Freezing During Debug

When debugging, prevent Claude from "fixing" unrelated code:

```
"While debugging this issue, do NOT modify any files except:
- src/auth/refresh.ts
- tests/auth/refresh.test.ts

If you see other issues, note them but don't fix them."
```

Or use `/freeze src/auth/` to enforce via hook.
