# ADR 0001: Use Supabase as Backend

## Status

Accepted

## Context

We need a backend solution for Pacy infrastructure projects that:
- Minimizes setup time
- Provides database, auth, and API out of the box
- Scales without infrastructure management
- Works well with our React/Next.js frontend stack

Options considered:
1. **Supabase** - Postgres + Auth + instant API
2. **Firebase** - Google's BaaS
3. **Custom backend** - Express/Fastify + PostgreSQL
4. **Prisma + PlanetScale** - ORM + serverless MySQL

## Decision

We use **Supabase** as our default backend.

## Rationale

| Factor | Supabase | Firebase | Custom | Prisma+PS |
|--------|----------|----------|--------|-----------|
| Setup time | Minutes | Minutes | Hours | Hours |
| PostgreSQL | Yes | No (NoSQL) | Yes | No (MySQL) |
| Auth built-in | Yes | Yes | No | No |
| Row-level security | Yes | Limited | Manual | Manual |
| Self-hostable | Yes | No | Yes | Partial |
| Open source | Yes | No | Yes | Yes |

Key advantages:
1. **"Supabase IS the backend"** - No separate API layer needed
2. **PostgreSQL** - Standard SQL, familiar, powerful
3. **RLS** - Security at database level, not just API
4. **Instant API** - Auto-generated REST and GraphQL
5. **Local development** - Full local environment with CLI

## Consequences

### Positive
- Fast project starts (database + auth in under an hour)
- Less code to maintain (no custom auth, no API routes for CRUD)
- Security by default (RLS policies)
- Team can focus on frontend/UX

### Negative
- Vendor lock-in (mitigated: open source, self-hostable)
- Learning curve for RLS policies
- Limited compute for complex backend logic (use Edge Functions)

### Neutral
- Must learn Supabase-specific patterns
- Some features require paid tier for production

## References

- [Supabase Documentation](https://supabase.com/docs)
- [Supabase vs Firebase comparison](https://supabase.com/alternatives/supabase-vs-firebase)
- Our skill: `.claude/skills/supabase-setup/SKILL.md`
