# Prompting Claude Code — What Actually Works

## The Golden Rules

### 1. Be Specific, Not Descriptive

| Bad (descriptive) | Good (specific) |
|-------------------|----------------|
| "Make the login better" | "Add email validation to the login form. Show inline error if email format is invalid." |
| "Fix the bug" | "The /api/users endpoint returns 500 when the email field is null. Add null check." |
| "Write tests" | "Write vitest tests for `src/utils/formatDate.ts`. Cover: valid date, invalid string, null, undefined, edge dates (leap year, Dec 31)." |
| "Refactor this" | "Extract the validation logic in `handleSubmit` (lines 45-82) into a `validateForm()` function in `src/utils/validation.ts`." |
| "Make it faster" | "The `getUsers` query takes 3.2s. Add an index on `users.email` and paginate with cursor-based pagination (limit 50)." |

### 2. Front-Load the Intent

Put what you want FIRST. Context SECOND.

```
BAD:  "So I was looking at the codebase and I noticed that the auth module has
       some issues with token refresh and I think we should probably fix it..."

GOOD: "Fix the token refresh race condition in src/auth/refresh.ts. When two
       requests hit simultaneously, both try to refresh, causing a 401 loop."
```

### 3. Constrain the Scope

Tell Claude what NOT to do. Prevents scope drift.

```
"Add pagination to the users list endpoint. ONLY modify:
- src/api/users.ts (add pagination params)
- src/api/users.test.ts (add pagination tests)
Do NOT modify: the frontend, the database schema, or any other endpoints."
```

### 4. Show the Desired Output

```
"Generate a migration file that adds a `status` column to the `orders` table.
Output should look like:

exports.up = (knex) => knex.schema.alterTable('orders', t => {
  t.enum('status', ['pending', 'shipped', 'delivered']).defaultTo('pending');
});
"
```

### 5. Use Challenge Prompts

Push Claude to verify its own work:

| Prompt | Effect |
|--------|--------|
| "Prove to me this works" | Claude writes verification tests |
| "What could go wrong?" | Claude lists edge cases |
| "Grill me on these changes" | Claude stress-tests the diff |
| "Find 3 bugs in this code" | Claude hunts actively (not just scans) |
| "If you were reviewing this PR, what would you flag?" | Reviewer mindset |
| "What would break if X input was [edge case]?" | Specific edge case thinking |
| "Knowing everything you know now, start over with the elegant solution" | After a messy first attempt |

### 6. Multi-Step Decomposition

Break complex tasks into numbered steps:

```
"Implement user search with these steps:
1. Add a search endpoint to src/api/users.ts (GET /api/users/search?q=...)
2. Implement full-text search using PostgreSQL ts_vector
3. Add debounced search input to src/components/UserSearch.tsx
4. Write tests for the endpoint and the component
5. Run all tests and fix any failures"
```

## Prompt Patterns by Task

### Bug Fix Prompt
```
"Bug: [exact symptom]
Reproduce: [steps or failing test]
Expected: [correct behavior]
Actual: [broken behavior]
Affected file: [path]

Fix this. Don't modify any other files. Write a regression test."
```

### Feature Prompt
```
"Add [feature name] to [location].

Requirements:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

Constraints:
- Only modify [files]
- No new dependencies
- Must pass existing tests

Write tests for the new functionality."
```

### Refactor Prompt
```
"Refactor [what] in [where].

Current problem: [why it's bad]
Desired outcome: [what good looks like]

Rules:
- Run tests BEFORE and AFTER each change
- If tests break, revert and try a smaller step
- Commit after each successful step
- Do NOT change behavior, only structure"
```

### Review Prompt
```
"Review the diff on this branch. For each issue found:
1. File and line number
2. Severity: Critical / Warning / Suggestion
3. What's wrong
4. How to fix it

Don't just say 'looks good.' Find real issues. Check:
- Error handling, edge cases, null safety
- Security (injection, auth, secrets)
- Performance (N+1 queries, missing indexes)
- Test coverage for new code"
```

## Voice-Friendly Triggers

Claude Code responds to natural language. These phrases activate skills and behaviors:

| You Say | What Happens |
|---------|-------------|
| "Be careful" | Activates destructive command warnings |
| "Use plan mode" | Switches to plan-before-code |
| "Use subagents" | Delegates to child Claude instances |
| "Review this" | Triggers code review workflow |
| "What's wrong here?" | Debugging mode |
| "Start fresh" | `/clear` equivalent |
| "Compact this" | Manual compaction |
| "Think harder" | Activates extended thinking |
| "Check your work" | Re-reads and validates recent output |

## Anti-Patterns

| Pattern | Why It Fails | Fix |
|---------|-------------|-----|
| "Do everything" | Too vague, Claude guesses | Numbered steps with constraints |
| Wall of context before the ask | Buried intent | Ask first, context second |
| "Maybe you could..." | Uncertain, Claude hedges | Direct imperative: "Add X to Y" |
| "Fix all the bugs" | Unbounded scope | "Fix the bug in [file] where [symptom]" |
| Copy-pasting stack traces only | No context | Stack trace + what you expected + what happened |
| "Just make it work" | No quality bar | "Make it work AND write tests AND handle errors" |
| Explaining how to code | Wastes tokens | Tell Claude WHAT, not HOW |
