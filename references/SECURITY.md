# Security — Protecting Your Code and Secrets

## Secret Management

### Rule: Never Let Claude See Real Secrets

Claude Code runs in your terminal. It can read `.env` files. Configure it
to not access secrets directly.

```json
// settings.json
{
  "permissions": {
    "deny": [
      "Bash(cat .env*)",
      "Bash(echo $*_KEY*)",
      "Bash(echo $*_SECRET*)",
      "Bash(echo $*_TOKEN*)",
      "Bash(echo $*_PASSWORD*)"
    ]
  }
}
```

### Environment Variable Patterns to Deny

| Pattern | What It Protects |
|---------|-----------------|
| `*_KEY` | API keys |
| `*_SECRET` | Client secrets |
| `*_TOKEN` | Auth tokens |
| `*_PASSWORD` | Database passwords |
| `*_PRIVATE_*` | Private keys |
| `*_CREDENTIAL*` | Cloud credentials |

### Safe Alternatives

Instead of: Claude reads `.env` to see the database URL
Do: Tell Claude the variable NAME (`DATABASE_URL`) without the VALUE

```
CLAUDE.md:
- Database: PostgreSQL, connection string in DATABASE_URL env var
- API key env var: STRIPE_SECRET_KEY (do NOT read the value)
```

## Code Security Checklist

When Claude writes code, verify these:

### Input Validation
- [ ] All user input is validated before use
- [ ] SQL queries use parameterized queries (never string concatenation)
- [ ] File paths are sanitized (no path traversal: `../../../etc/passwd`)
- [ ] URLs are validated before fetch/redirect
- [ ] JSON parsing is wrapped in try/catch

### Authentication
- [ ] Passwords are hashed (bcrypt/argon2, never MD5/SHA)
- [ ] JWT secrets are not hardcoded
- [ ] Session tokens are cryptographically random
- [ ] Token expiration is enforced
- [ ] Rate limiting on auth endpoints

### Data Exposure
- [ ] API responses don't include passwords, secrets, or internal IDs
- [ ] Error messages don't reveal stack traces to users
- [ ] Logs don't contain PII or secrets
- [ ] CORS is configured (not `*` in production)

## Supply Chain Security

### Dependency Rules for Claude

```
CLAUDE.md:
- NEVER add a dependency without asking first
- Before suggesting a dependency, check: last updated, weekly downloads, open issues
- Prefer built-in Node.js APIs over npm packages (see dep-audit skill)
- Every new dependency needs justification in the commit message
```

### Checking a Dependency

```
"Before adding [package], check:
1. npm view [package] — last publish date, version count
2. GitHub issues — any security advisories?
3. npm audit — known vulnerabilities?
4. Bundle size — how much does it add?
5. Alternatives — can we use a built-in instead?"
```

## Git Security

```json
// settings.json — deny rules
{
  "permissions": {
    "deny": [
      "Bash(git push --force *)",
      "Bash(git reset --hard *)",
      "Bash(git rebase -i *)"
    ]
  }
}
```

### Pre-Commit Checks

Use a PreToolUse hook to scan for secrets before commits:

```json
{
  "matcher": "Bash(git commit *)",
  "hooks": [{
    "type": "command",
    "command": "git diff --cached | grep -qiE '(api[_-]?key|secret|password|token)\\s*[:=]\\s*[\"'\\'][^\"'\\' ]+[\"'\\']' && { echo '{\"block\": true, \"message\": \"Possible secret in staged changes. Review before committing.\"}' >&2; exit 2; } || true",
    "timeout": 10
  }]
}
```
