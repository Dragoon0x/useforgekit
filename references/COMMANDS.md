# Commands — Slash Command Workflows

## What Commands Are

Commands are markdown files in `.claude/commands/` that define reusable workflows.
Users invoke them with `/command-name`. They run in the current conversation context.

## Command Anatomy

```markdown
# Fix Issue

Fix the GitHub issue specified by the user.

1. Run `gh issue view $ARGUMENTS` to get details
2. Analyze the issue description and comments
3. Find the relevant code
4. Implement the fix
5. Write tests for the fix
6. Run `npm test` to verify
7. Commit with message: "fix: [issue title] (#[number])"
```

## `$ARGUMENTS`

The special variable `$ARGUMENTS` captures everything the user types after the command name.

```
User: /fix-issue 42
→ $ARGUMENTS = "42"

User: /deploy staging
→ $ARGUMENTS = "staging"
```

## Essential Commands

### /fix-issue

```markdown
Fix GitHub issue $ARGUMENTS.

1. `gh issue view $ARGUMENTS` — read the issue
2. Understand the root cause
3. Find affected files
4. Implement the fix
5. Write a test that would have caught this
6. `npm test` — verify fix and no regressions
7. `git add -A && git commit -m "fix: [title] (#$ARGUMENTS)"`
```

### /create-pr

```markdown
Create a pull request for the current branch.

1. `git diff main..HEAD --stat` — summarize changes
2. Read all modified files
3. Write a PR description:
   - What changed and why
   - How to test
   - Screenshots if UI change
4. `gh pr create --title "[type]: [description]" --body "[generated description]"`
```

### /onboard

```markdown
Explore this codebase and explain the architecture.

1. Read CLAUDE.md, README.md, package.json
2. List top-level directory structure
3. Identify: framework, language, key libraries
4. Find the entry point (main, index, app)
5. Trace a typical request through the code
6. Summarize: architecture, key patterns, where to start
```

### /review

```markdown
Review the current branch changes.

1. `git diff main..HEAD` — read all changes
2. For each file: check for bugs, security issues, missing tests
3. Check: error handling, edge cases, naming, conventions
4. Report: Critical / Warning / Suggestion with file:line references
5. If any Critical findings: do NOT approve
```

### /deploy

```markdown
Deploy to $ARGUMENTS (default: staging).

1. `npm test` — must pass
2. `npm run build` — must succeed
3. `git status` — must be clean
4. Deploy command for $ARGUMENTS environment
5. Verify deployment health
```

## Command vs Skill vs Agent

| Use a Command when... | Use a Skill when... | Use an Agent when... |
|----------------------|--------------------|--------------------|
| User triggers it explicitly | Claude should auto-detect relevance | Task needs isolated context |
| It's a multi-step workflow | It's domain knowledge | It's an independent review |
| It uses `$ARGUMENTS` | It doesn't need user input to activate | It should not modify source |
| It's done in the current context | It adds knowledge to current context | Parallel execution needed |
