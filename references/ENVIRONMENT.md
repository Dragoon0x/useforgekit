# Environment — Variables, Secrets, and Configuration

## Environment Variable Strategy

### What Claude Should Know (in CLAUDE.md)

```markdown
## Environment Variables
- `DATABASE_URL` — PostgreSQL connection string
- `REDIS_URL` — Redis connection string
- `STRIPE_SECRET_KEY` — Stripe API key
- `NEXT_PUBLIC_API_URL` — Public API endpoint

All secrets are in `.env.local` (gitignored). Never read the values directly.
```

Claude knows the NAMES and PURPOSE. Never the VALUES.

### .env File Hierarchy

| File | Committed | Purpose |
|------|-----------|---------|
| `.env` | Sometimes | Default values (non-secret) |
| `.env.local` | Never | Local overrides with real secrets |
| `.env.development` | Yes | Dev-specific defaults |
| `.env.production` | No | Production values (deploy pipeline) |
| `.env.test` | Yes | Test-specific values (mock keys) |
| `.env.example` | Yes | Template showing required vars |

### .env.example Pattern

Always maintain a `.env.example` showing what's needed without real values:

```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# Auth
JWT_SECRET=your-jwt-secret-here
SESSION_SECRET=your-session-secret-here

# External APIs
STRIPE_SECRET_KEY=sk_test_...
SENDGRID_API_KEY=SG...

# App
NEXT_PUBLIC_API_URL=http://localhost:3000/api
```

### Settings.json Environment

```json
{
  "env": {
    "NODE_ENV": "development",
    "LOG_LEVEL": "debug"
  }
}
```

These are injected into Claude's Bash environment. Use for non-secret
configuration only.
