# Monorepo — Claude Code in Multi-Package Projects

## CLAUDE.md Hierarchy

In a monorepo, CLAUDE.md files stack:

```
my-monorepo/
├── CLAUDE.md                    # Root — shared conventions, workspace commands
├── packages/
│   ├── core/
│   │   └── CLAUDE.md            # Core-specific — types, patterns
│   ├── web/
│   │   └── CLAUDE.md            # Web-specific — React, routing
│   └── api/
│       └── CLAUDE.md            # API-specific — endpoints, DB
```

When Claude works in `packages/web/`, it loads:
1. Root `CLAUDE.md` (shared rules)
2. `packages/web/CLAUDE.md` (package-specific rules)

Both apply. Package-specific rules take priority on conflicts.

## Root CLAUDE.md for Monorepos

```markdown
# [Monorepo Name]

## Quick Facts
- **Monorepo**: pnpm workspaces
- **Packages**: core, web, api
- **Build all**: `pnpm -r build`
- **Test all**: `pnpm -r test`
- **Install**: `pnpm install` (root only, never in packages)

## Conventions
- Shared types in `packages/core/src/types/`
- Cross-package imports: `@scope/core`, `@scope/web`, `@scope/api`
- NEVER duplicate types across packages
- Changes to core require testing web AND api

## Gotchas
- `pnpm install` only at root. Running in a package breaks the workspace.
- Changing core types may break web and api. Run `pnpm -r typecheck` after.
```

## Package-Level CLAUDE.md

```markdown
# @scope/web

## Quick Facts
- **Framework**: Next.js 15, React 19
- **Build**: `pnpm build` (from this directory)
- **Test**: `pnpm test`
- **Dev**: `pnpm dev` (port 3000)

## Conventions
- Server Components by default
- Shared types from `@scope/core`
- API calls go through `@scope/api` client, not raw fetch
```

## Skills in Monorepos

Skills can be at root level (shared) or package level (specific):

```
my-monorepo/
├── .claude/skills/
│   └── shared-conventions/        # Applies to all packages
│       └── SKILL.md
├── packages/web/.claude/skills/
│   └── react-patterns/            # Only for web package
│       └── SKILL.md
```

## Workspace-Aware Commands

```markdown
# /test-affected command
Test only packages affected by current changes.

1. `git diff main..HEAD --name-only` — list changed files
2. Map files to packages (which package directory?)
3. For each affected package: `cd packages/[name] && pnpm test`
4. If core changed: test ALL packages (`pnpm -r test`)
```
