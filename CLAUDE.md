# Pacy Infrastructure

Technical consultancy team for Pacy infrastructure projects.

## Team Structure

```
                              YOU
                               │
                               ▼
                    ┌───────────────────┐
                    │ tech-project-lead │ ← Start here
                    └─────────┬─────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌───────────────┐   ┌───────────────┐   ┌───────────────┐
│   backend-    │   │   frontend-   │   │     cto       │
│   architect   │   │   architect   │   │               │
└───────────────┘   └───────────────┘   └───────────────┘
```

## Quick Start

```bash
claude
> /agent tech-project-lead
> [Describe your project]
```

## Agents

| Agent | Role |
|-------|------|
| `tech-project-lead` | Your single contact. Coordinates all work. |
| `backend-architect` | Supabase, data models, API design. |
| `frontend-architect` | React/Next.js, UI/UX, deployment. |
| `cto` | Reviews decisions, ensures best practices. |

## Default Stack

| Layer | Technology |
|-------|------------|
| Database | Supabase (PostgreSQL) |
| Frontend | Next.js + Tailwind + shadcn/ui |
| Hosting | Render |
