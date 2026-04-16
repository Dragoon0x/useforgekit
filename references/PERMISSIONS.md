# Permissions — Security Strategy

## The Permission Spectrum

```
Locked ←──────────────────────────────→ Open
(approve everything)                    (approve nothing)

  Locked    Guarded    Standard    Open    Full Trust
  ──────    ───────    ────────    ────    ──────────
  Enterprise  Most     Solo dev   Personal  Sandboxed
  Production  projects  trusted   projects  containers
```

## Recommended: Guarded (Most Projects)

```json
{
  "permissions": {
    "allow": [
      "Read", "Grep", "Glob", "Edit", "Write",
      "Bash(npm run *)", "Bash(npx *)", "Bash(node *)",
      "Bash(git status)", "Bash(git diff *)", "Bash(git log *)",
      "Bash(git branch *)", "Bash(git checkout *)",
      "Bash(git add *)", "Bash(git commit *)",
      "Bash(cat *)", "Bash(ls *)", "Bash(find *)",
      "Bash(head *)", "Bash(tail *)", "Bash(wc *)",
      "Bash(grep *)", "Bash(which *)", "Bash(echo *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(git reset --hard *)",
      "Bash(npm publish *)",
      "Bash(curl * | bash)",
      "Bash(sudo *)"
    ]
  }
}
```

## Per-Agent Permissions

| Agent Role | tools Allow | disallowedTools |
|-----------|------------|-----------------|
| Code reviewer | Read, Grep, Glob, Bash(git diff *) | Edit, Write |
| Test generator | Read, Grep, Glob, Write | — |
| Security auditor | Read, Grep, Glob, Bash(npm audit *) | Edit, Write |
| Documentation | Read, Grep, Glob, Edit, Write | Bash |
| Implementation | Read, Grep, Glob, Edit, Write, Bash | — |

Reviewers CANNOT edit. Editors CANNOT review their own work.
This separation prevents agents from marking their own work as correct.
