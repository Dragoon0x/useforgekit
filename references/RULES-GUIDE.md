# Rules — Background Knowledge Files

## What Rules Are

Rules are markdown files in `.claude/rules/` that provide background knowledge.
They're like skills but simpler — no frontmatter, no slash command, no on-demand loading.
Claude reads them based on filename relevance.

## When to Use Rules vs Skills vs CLAUDE.md

| Content | Size | Trigger | Put In |
|---------|------|---------|--------|
| Always needed, short | < 10 lines | Every session | CLAUDE.md |
| Always needed, long | 10-300 lines | Every session | Rule |
| Sometimes needed | Any | On demand | Skill |
| User-triggered workflow | Any | `/name` | Command |

## Rule File Structure

```
.claude/rules/
├── style.md              # Code style conventions
├── architecture.md       # Architecture decisions
├── testing.md            # Testing requirements
├── security.md           # Security requirements
├── naming.md             # Naming conventions
├── error-handling.md     # Error handling patterns
├── git.md                # Git commit and branching conventions
└── review-checklist.md   # Code review criteria
```

## Rule File Format

No frontmatter needed. Just markdown:

```markdown
# Code Style Rules

- Use `const` by default. Use `let` only when reassignment is needed. Never `var`.
- Prefer early returns over nested conditionals.
- Maximum function length: 30 lines. Extract if longer.
- Maximum file length: 300 lines. Split if longer.
- One export per file for components. Barrel files only in `src/` root.
```

## Essential Rules by Project Type

### TypeScript Project
```
.claude/rules/
├── typescript.md         # strict mode, no any, interface > type
├── naming.md             # camelCase vars, PascalCase types, UPPER_SNAKE constants
├── imports.md            # named exports, no barrel re-exports in deep modules
└── error-handling.md     # custom error classes, always catch with typed errors
```

### React Project (add to above)
```
.claude/rules/
├── components.md         # functional only, hooks rules, prop types
├── state.md              # when useState vs useReducer vs context vs library
└── styling.md            # Tailwind conventions, responsive patterns
```

### API Project (add to TypeScript)
```
.claude/rules/
├── api-design.md         # URL patterns, status codes, pagination
├── validation.md         # Zod schemas, input sanitization
└── database.md           # query patterns, migration rules, index strategy
```

## Sizing

Keep each rule file under 300 lines. If it grows larger, split into multiple files.
Claude loads rules contextually — smaller files mean more targeted loading.
