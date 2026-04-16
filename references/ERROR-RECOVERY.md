# Error Recovery — When Claude Breaks Things

## Prevention: The Safety Stack

Layer protections so no single failure causes damage:

```
Layer 1: Git branch (never edit on main)         ← Hook enforces
Layer 2: Permission deny list (no rm -rf)         ← Settings enforce
Layer 3: Scope freeze (only edit specified files)  ← /freeze command
Layer 4: Test verification (tests must pass)       ← Stop hook
Layer 5: Manual review (you approve the diff)      ← Before commit
```

## Recovery Playbook

### "Claude deleted/broke a file"

```bash
# Option 1: Git restore (if not committed)
git checkout -- path/to/broken/file

# Option 2: Git restore from commit (if committed)
git checkout HEAD~1 -- path/to/broken/file

# Option 3: Full branch reset (nuclear)
git stash  # save any good work
git reset --hard origin/main
git stash pop  # re-apply good work
```

### "Claude installed wrong dependencies"

```bash
# Restore package.json and lock file
git checkout -- package.json pnpm-lock.yaml
pnpm install
```

### "Claude made 50 changes and now nothing works"

```bash
# See what changed
git diff --stat

# Revert everything
git checkout -- .

# Or revert specific files
git checkout -- src/broken-file.ts
```

### "Claude committed broken code to the branch"

```bash
# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last N commits
git reset --soft HEAD~N

# Then selectively commit good changes
git add good-file.ts
git commit -m "fix: only the good parts"

# Discard bad changes
git checkout -- bad-file.ts
```

### "Claude messed up the database"

```bash
# If using migrations
npx prisma migrate reset  # drops and recreates
# or
npx knex migrate:rollback

# If direct SQL (worst case)
# Restore from backup
pg_restore -d mydb latest-backup.sql
```

## Post-Incident Checklist

After recovering:

1. **Add to CLAUDE.md:** "NEVER [thing that went wrong]"
2. **Add a hook:** If it should be prevented automatically
3. **Add to deny list:** If it was a dangerous command
4. **Add a test:** If it was a code bug Claude should have caught
5. **Review permissions:** Were they too broad?

## The "Start Over" Decision

When to `/clear` and restart vs trying to fix:

| Situation | Action |
|-----------|--------|
| Claude is confused about the codebase | `/clear` and restart |
| One file is wrong | `git checkout -- file` and continue |
| Multiple files are wrong | `git checkout -- .` and `/clear` |
| Claude keeps making the same mistake | Fix CLAUDE.md, then `/clear` |
| Context is corrupted (nonsensical output) | `/clear` immediately |
| Good work mixed with bad | `git stash` good parts, reset, reapply |
