# Command Recipes — 20 Ready-to-Use Commands

Copy these to `.claude/commands/`.

## Development Commands

### 1. /fix-issue
```markdown
Fix GitHub issue #$ARGUMENTS.

1. `gh issue view $ARGUMENTS` — read the issue
2. Read relevant source files
3. Implement the fix (smallest change possible)
4. Write a test that catches this bug
5. `npm test` — verify fix + no regressions
6. `git add -A && git commit -m "fix: [issue title] (#$ARGUMENTS)"`
```

### 2. /implement
```markdown
Implement the feature described: $ARGUMENTS

1. Read CLAUDE.md for project conventions
2. Plan the implementation (list files to modify/create)
3. Write tests first (TDD)
4. Implement the feature
5. Run tests — all must pass
6. Run lint — no errors
7. `git add -A && git commit -m "feat: $ARGUMENTS"`
```

### 3. /refactor
```markdown
Refactor: $ARGUMENTS

IMPORTANT: Run tests BEFORE and AFTER every change. If tests break, revert.

1. Run `npm test` — baseline must pass
2. Make one small structural change
3. Run `npm test` — must still pass
4. Commit: `git commit -m "refactor: [what changed]"`
5. Repeat steps 2-4 until done
```

### 4. /debug
```markdown
Debug: $ARGUMENTS

DO NOT FIX ANYTHING YET.

1. Reproduce the issue
2. Read the relevant code
3. Trace data flow from input to error
4. Form 3 hypotheses about the root cause
5. Test each hypothesis (add logging, check values)
6. Confirm the root cause with evidence
7. THEN propose and implement a fix
8. Write a regression test
```

## Review Commands

### 5. /review
```markdown
Review the current branch changes.

Run the code-reviewer agent:
Agent(subagent_type="code-reviewer", prompt="Review all changes: git diff main..HEAD. Report Critical/Warning/Suggestion with file:line.")
```

### 6. /security
```markdown
Run a security audit on this codebase.

Run the security-auditor agent:
Agent(subagent_type="security-auditor", prompt="Full OWASP Top 10 audit. Check dependencies with npm audit. Report all findings with severity and remediation.")
```

### 7. /a11y
```markdown
Audit the UI code for accessibility violations.

Agent(subagent_type="a11y-auditor", prompt="Check all components in src/components/ for: alt text, aria-labels, contrast, keyboard nav, form labels, heading hierarchy.")
```

## Quality Commands

### 8. /test
```markdown
Generate tests for: $ARGUMENTS

Agent(subagent_type="test-generator", prompt="Generate tests for $ARGUMENTS. Cover: happy path, error cases, edge cases (null, empty, boundary). Use the project's test framework.")
```

### 9. /coverage
```markdown
Check test coverage and find gaps.

1. Run `npm test -- --coverage` (or equivalent)
2. Find files with < 80% coverage
3. List untested exported functions
4. Suggest which tests to add (priority order)
```

### 10. /lint-fix
```markdown
Fix all lint errors in the project.

1. `npx eslint src/ --fix` (auto-fix what can be auto-fixed)
2. Review remaining errors
3. Fix each manually
4. `npx eslint src/` — verify zero errors
5. Commit: `git commit -m "chore: fix lint errors"`
```

## Git Commands

### 11. /pr
```markdown
Create a pull request for the current branch.

1. `git diff main..HEAD --stat` — summarize changes
2. Read all modified files for context
3. Write PR description:
   - **What**: One-line summary
   - **Why**: Problem being solved
   - **How**: Implementation approach
   - **Testing**: How to verify
4. `gh pr create --title "[type]: [description]" --body "[generated]"`
```

### 12. /commit
```markdown
Stage and commit current changes with a proper message.

1. `git diff --stat` — see what changed
2. Read the changes to understand what was done
3. Write a conventional commit message:
   - `feat:` for new features
   - `fix:` for bug fixes
   - `docs:` for documentation
   - `test:` for test changes
   - `refactor:` for code restructuring
   - `chore:` for maintenance
4. `git add -A && git commit -m "[message]"`
```

### 13. /sync
```markdown
Sync current branch with main.

1. `git fetch origin`
2. `git rebase origin/main`
3. If conflicts: show them, ask for resolution guidance
4. `npm test` — verify everything still works
```

## Documentation Commands

### 14. /docs
```markdown
Update all documentation to match current code.

Agent(subagent_type="doc-writer", prompt="Read all .md files and all source code. Update any documentation that doesn't match the current implementation. Ensure all code examples work.")
```

### 15. /changelog
```markdown
Generate changelog entry for the current release.

1. `git log $(git describe --tags --abbrev=0)..HEAD --oneline`
2. Group commits: feat → Added, fix → Fixed, breaking → Changed
3. Write user-facing descriptions (not commit messages)
4. Prepend to CHANGELOG.md
```

## Setup Commands

### 16. /onboard
```markdown
Explain this codebase to a new developer.

Agent(subagent_type="onboarding-guide", prompt="Explore this codebase and explain: what it does, how it's organized, tech stack, key patterns, and where to start.")
```

### 17. /init
```markdown
Initialize Claude Code configuration for this project.

1. Detect the project type from config files
2. Generate CLAUDE.md tailored to the stack
3. Generate .claude/settings.json with appropriate hooks
4. Create .claude/settings.local.json (empty, for personal overrides)
5. Update .gitignore with Claude-specific entries
6. Report what was created
```

## Utility Commands

### 18. /explain
```markdown
Explain: $ARGUMENTS

Read the code at the specified location (file, function, or module).
Explain:
1. What it does (behavior)
2. How it works (implementation)
3. Why it's written this way (design decisions, if apparent)
4. What could go wrong (edge cases, error paths)
```

### 19. /todo
```markdown
Find and list all TODOs, FIXMEs, and HACKs in the codebase.

1. `grep -rn 'TODO\|FIXME\|HACK\|XXX\|WORKAROUND' src/ --include='*.ts' --include='*.tsx' --include='*.py'`
2. Group by file
3. Prioritize: FIXME (high) > HACK (medium) > TODO (low)
4. Suggest which ones to address first
```

### 20. /deps
```markdown
Audit project dependencies.

Agent(subagent_type="dep-auditor", prompt="Full dependency audit: security (npm audit), freshness (npm outdated), necessity (can any be replaced by built-ins?), license compatibility.")
```
