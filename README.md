# Pacy Tech Team

Infrastructure team for deploying Pacy projects. Uses Claude Code agents with specialized skills.

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
cd /path/to/your/project
claude
> Jag vill deploya detta projekt med Pacy Tech Team
```

## Agents

| Agent | Role | Model |
|-------|------|-------|
| `tech-project-lead` | Koordinerar allt. Din enda kontakt. | sonnet |
| `backend-architect` | Supabase setup, datamodeller, API design | sonnet |
| `frontend-architect` | React/Next.js, UI, deployment till Render | sonnet |
| `cto` | Granskar beslut, säkerställer best practices | opus |

## Skills

### Infrastructure Skills

| Skill | Agent(s) | Capability |
|-------|----------|------------|
| `supabase-setup` | backend-architect | Skapa Supabase-projekt, köra migrations, sätta upp tabeller |
| `supabase-integration` | backend-architect, frontend-architect | Koppla app till Supabase (auth, db, storage) |
| `render-deployment` | backend-architect, frontend-architect | Deploya till Render via API |

### Development Skills

| Skill | Agent(s) | Capability |
|-------|----------|------------|
| `claude-code-mastery` | cto | Best practices för Claude Code, skills, agents |
| `frontend-planning` | frontend-architect | Planera UI, screens, komponenter |
| `ux-simplicity` | frontend-architect | Designprinciper för enkla gränssnitt |
| `api-design` | backend-architect | REST API patterns med Supabase |

### Process Skills

| Skill | Agent(s) | Capability |
|-------|----------|------------|
| `project-coordination` | tech-project-lead | Koordinera flerstegs-projekt |
| `project-discovery` | backend-architect | Kartlägga data-krav |
| `requirements-analysis` | frontend-architect | Analysera frontend-krav |
| `architecture-review` | cto | Granska tekniska beslut |

## Capabilities

### Supabase (via CLI)

- ✅ Skapa nya projekt
- ✅ Köra SQL migrations
- ✅ Sätta upp RLS policies
- ✅ Hämta API-nycklar

**Konfiguration:** `~/bin/supabase` (CLI installerad)

### Render (via API)

- ✅ Skapa static sites (frontend)
- ✅ Skapa web services (backend)
- ✅ Sätta miljövariabler
- ✅ Trigga deploys
- ⚠️ Free plan kräver dashboard (API kräver starter plan $7/mo)

**Konfiguration:** `RENDER_API_KEY` i `.env`

### Befintliga Tjänster

| Tjänst | Typ | ID |
|--------|-----|-----|
| pacy-frontend | static_site | srv-d52jndt6ubrc739vv1q0 |
| pacy-training-system | web_service | srv-d51sf115pdvs73efj3q0 |

## Default Stack

| Layer | Technology |
|-------|------------|
| Database | Supabase (PostgreSQL + Auth + Storage) |
| Backend | Node.js / Express |
| Frontend | Next.js + Tailwind + shadcn/ui |
| Hosting | Render |

## Environment Variables

Teamet behöver dessa i `.env`:

```bash
# Supabase (för att skapa nya projekt)
# Login via: ~/bin/supabase login

# Render
RENDER_API_KEY=rnd_xxx

# Project-specific (sätts per projekt)
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...
SUPABASE_ANON_KEY=eyJ...
```

## File Structure

```
.claude/
├── agents/
│   ├── tech-project-lead.md
│   ├── backend-architect.md
│   ├── frontend-architect.md
│   └── cto.md
├── skills/
│   ├── api-design/
│   ├── architecture-review/
│   ├── claude-code-mastery/
│   ├── decision-framework/
│   ├── efficient-building/
│   ├── frontend-planning/
│   ├── project-coordination/
│   ├── project-discovery/
│   ├── render-deployment/
│   ├── requirements-analysis/
│   ├── supabase-integration/
│   ├── supabase-setup/
│   ├── tech-radar/
│   └── ux-simplicity/
└── plans/
```

## Account Info

| Service | Account | ID |
|---------|---------|-----|
| Supabase | Joakim's account | org: `olshfmcoszcgfwznrxgs` |
| Render | Pacy Training Program | owner: `tea-d51s8f15pdvs73efe5f0` |
| GitHub | jliljedahl | - |

## Changelog

### 2026-01-19

- Created `claude-code-mastery` skill with comprehensive best practices
- Created `supabase-integration` skill for app connectivity
- Expanded `render-deployment` with full API documentation
- Added Supabase CLI workflow to `supabase-setup`
- Updated CTO with Write/Edit tools and Project Start Protocol
- All skills now follow Anthropic's best practices (frontmatter, trigger keywords)
- Initial commit and push to github.com/jliljedahl/pacy-tech-team
