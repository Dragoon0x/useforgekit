# Project Templates — Stack-Specific Configurations

## Template Selection

| Detected | Template | Key Features |
|----------|----------|-------------|
| package.json + tsconfig | **TypeScript/Node** | Prettier hook, ESLint, vitest |
| next.config | **Next.js** | App Router patterns, Server Components |
| vite.config | **Vite + React/Vue** | HMR, component patterns |
| pyproject.toml | **Python** | Ruff format hook, pytest, mypy |
| Cargo.toml | **Rust** | rustfmt hook, clippy, cargo test |
| go.mod | **Go** | gofmt hook, go test |
| Gemfile | **Ruby/Rails** | rubocop hook, rspec |

## TypeScript / Node.js Template

### CLAUDE.md Essentials
```
- TypeScript strict mode, no `any`, no `as` without comment
- Named exports only (no default exports)
- Test: `pnpm test` (vitest)
- Lint: `pnpm lint` (eslint + prettier)
```

### Recommended Hooks
- PostToolUse: Prettier auto-format
- PreToolUse: Branch protection

### Recommended Skills
- `api-conventions` (if building APIs)
- `testing-patterns` (vitest/jest patterns)
- `component-patterns` (if React)

### Recommended Agents
- `code-reviewer` (Opus, read-only)
- `test-generator` (Sonnet, write tests/)

## Next.js Template

### CLAUDE.md Additions
```
- Server Components by default, 'use client' only when needed
- `app/` directory for App Router (not pages/)
- Server Actions for mutations, not API routes
- `next/navigation` for routing (not `next/router`)
- Async `cookies()` and `headers()` in Next.js 15
```

### Recommended Skills
- `nextjs-patterns` (App Router, Server Components, caching)
- `tailwind-conventions` (if using Tailwind)

## Python Template

### CLAUDE.md Essentials
```
- Type hints on all function signatures
- Pydantic for data models
- Format: `ruff format .`
- Lint: `ruff check .`
- Test: `pytest`
- Type check: `mypy src/`
```

### Recommended Hooks
- PostToolUse: ruff format + ruff check --fix

## React (Vite) Template

### CLAUDE.md Essentials
```
- Functional components only (no class components)
- Custom hooks in src/hooks/
- Shared types in src/types/
- Component files: ComponentName.tsx + ComponentName.test.tsx
- CSS: Tailwind utility classes, no CSS modules
```

## Rust Template

### CLAUDE.md Essentials
```
- Run `cargo clippy` before committing
- No `unwrap()` in production code — use `?` operator
- Error types in src/error.rs
- Tests inline with `#[cfg(test)]` mod test
```

### Recommended Hooks
- PostToolUse: rustfmt

## Go Template

### CLAUDE.md Essentials
```
- `gofmt` on save (enforced by hook)
- Error handling: always check returned errors
- Table-driven tests
- Interfaces in the consuming package, not the provider
```

### Recommended Hooks
- PostToolUse: gofmt -w
