# Pair Programming with Claude Code

## The Dynamic

You are the navigator. Claude is the driver. You decide WHAT to build.
Claude decides HOW to build it. You review every decision.

## Session Patterns

### The Planning Session
```
You: "Let's plan the user authentication system"
Claude: [Explores codebase, proposes architecture]
You: [Approve, modify, or reject decisions]
Claude: [Creates implementation plan]
You: "Approved. Start implementing."
```

### The Implementation Session
```
You: "Implement the login endpoint from our plan"
Claude: [Writes code]
You: [Review in real-time]
Claude: [Writes tests]
You: "Run the tests"
Claude: [Runs tests, fixes failures]
You: "Commit this"
```

### The Debugging Session
```
You: "The login endpoint returns 500 on valid credentials"
Claude: [Traces the error, adds logging]
You: [Provide additional context if needed]
Claude: [Identifies root cause]
You: "That looks right. Fix it."
Claude: [Fixes, writes regression test, verifies]
```

### The Review Session
```
You: "/review"
Claude: [Reviews all changes, reports findings]
You: "Fix the Critical issues. Skip the Suggestions."
Claude: [Fixes critical issues]
You: "/pr"
```

## Communication Tips

| Instead of... | Say... | Why |
|-------------|--------|-----|
| "Make it better" | "The error message should include the invalid field name" | Specific > vague |
| "I don't like this" | "Use a Map instead of an object here because we need ordered keys" | Reason > opinion |
| "Do everything" | "Start with the database schema, then the API, then the frontend" | Ordered > chaotic |
| "Fix all the bugs" | "Fix the null pointer in auth.ts:42, then run tests" | One thing at a time |
| "That's wrong" | "The status code should be 409 (Conflict), not 400 (Bad Request)" | Correct > critical |

## The "Check Your Work" Pattern

After Claude writes code:
```
"Before committing, check your work:
1. Does every function handle errors?
2. Are there any hardcoded values that should be constants?
3. Did you write tests for every exported function?
4. Would a new developer understand this code without comments?"
```

## When to Take Over

| Signal | Action |
|--------|--------|
| Claude is going in circles | "Stop. Let me describe the approach." |
| Claude misunderstands the architecture | "Read [file] first, then try again." |
| Claude makes the same mistake 3 times | "Stop. The issue is [root cause]. Fix only that." |
| Context is getting full | `/compact` or `/clear` |
| Task is too complex for one session | Break into sub-tasks, use subagents |
