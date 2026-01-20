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
| `tech-project-lead` | Koordinerar allt. Din enda kontakt. | opus |
| `backend-architect` | Supabase setup, datamodeller, API design | sonnet |
| `frontend-architect` | React/Next.js, UI, deployment till Render | sonnet |
| `cto` | Granskar beslut, säkerställer best practices | opus |
| `code-review` | Snabb kodgranskning, kvalitetskontroll | sonnet |

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
| `testing` | frontend-architect, code-review | Vitest, React Testing Library, testmönster |

### Process Skills

| Skill | Agent(s) | Capability |
|-------|----------|------------|
| `project-coordination` | tech-project-lead | Koordinera flerstegs-projekt |
| `project-discovery` | backend-architect | Kartlägga data-krav |
| `requirements-analysis` | frontend-architect | Analysera frontend-krav |
| `architecture-review` | cto | Granska tekniska beslut |
| `deployment-workflow` | tech-project-lead | End-to-end deployment (Supabase + Render) |

### Continuous Learning Skills

| Skill | Agent(s) | Capability |
|-------|----------|------------|
| `retrospective` | alla | Fånga lärdomar efter projekt, identifiera mönster |
| `skill-improvement` | alla | Analysera LEARNINGS.md, föreslå permanenta förbättringar |
| `decision-framework` | alla | Strukturerad beslutsprocess, dokumentation |
| `efficient-building` | alla | Minimera tokens, återanvänd kod, undvik omskrivningar |

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
│   ├── cto.md
│   └── code-review.md
├── skills/
│   ├── api-design/
│   ├── architecture-review/
│   ├── claude-code-mastery/
│   ├── decision-framework/
│   ├── deployment-workflow/
│   ├── efficient-building/
│   ├── frontend-planning/
│   ├── project-coordination/
│   ├── project-discovery/
│   ├── render-deployment/
│   ├── requirements-analysis/
│   ├── retrospective/
│   ├── skill-improvement/
│   ├── supabase-integration/
│   ├── supabase-setup/
│   ├── tech-radar/
│   ├── testing/
│   └── ux-simplicity/
├── plans/
└── LEARNINGS.md          # Captures patterns from projects
                           # Used by retrospective & skill-improvement
                           # Enables continuous team improvement
```

## Account Info

| Service | Account | Env Variable |
|---------|---------|--------------|
| Supabase | Joakim's account | `$SUPABASE_ORG_ID` |
| Render | Pacy Training Program | `$RENDER_OWNER_ID` |
| GitHub | jliljedahl | - |

See `.env.example` for all required environment variables.

## Changelog

### 2026-01-20 (v3) - CTO Review & Improvements

- **New Agent: `code-review`**
  - Fast code review (sonnet model)
  - Security, quality, React-specific checks
  - Lighter than CTO for quick feedback

- **New Skill: `testing`**
  - Vitest setup and patterns
  - React Testing Library examples
  - Supabase test data patterns
  - E2E basics with Playwright

- **Documentation Standard**
  - tech-project-lead now REQUIRES README.md update before handoff
  - README template added to project-coordination skill
  - Projects must be self-documenting

- **Architecture Decision Records**
  - Created `docs/adr/0001-use-supabase.md`
  - Created `docs/adr/0002-use-render.md`
  - Template in decision-framework skill

- **Environment Variables**
  - Extracted hardcoded IDs to `.env`
  - Added `SUPABASE_ORG_ID` and `RENDER_OWNER_ID`
  - Created `.env.example` template

- **Language Consistency**
  - Translated Swedish skills to English
  - All skills now in English (matching agents)

- **Tool Improvements**
  - Added WebSearch to tech-project-lead
  - Added testing skill to frontend-architect
  - Generalized Next.js version reference

### 2026-01-19 (v2)

- **Continuous Learning System**
  - Created `deployment-workflow` skill for end-to-end Supabase + Render deployment
  - Created `retrospective` skill for capturing project learnings
  - Created `skill-improvement` skill for analyzing patterns and proposing permanent improvements
  - Added LEARNINGS.md system for iterative team improvement

### 2026-01-19 (v1)

- Created `claude-code-mastery` skill with comprehensive best practices
- Created `supabase-integration` skill for app connectivity
- Expanded `render-deployment` with full API documentation
- Added Supabase CLI workflow to `supabase-setup`
- Updated CTO with Write/Edit tools and Project Start Protocol
- All skills now follow Anthropic's best practices (frontmatter, trigger keywords)
- Initial commit and push to github.com/jliljedahl/pacy-tech-team
