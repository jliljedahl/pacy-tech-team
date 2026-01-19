---
name: deployment-workflow
description: |
  End-to-end project deployment to Supabase + Render. Use when: deploying a new project,
  setting up production infrastructure, or configuring deployment for existing code.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - AskUserQuestion
---

# Deployment Workflow

Orkestrerar deployment av projekt till Supabase (databas) och Render (hosting).

## Prerequisites

- Supabase CLI: `~/bin/supabase` (authenticated)
- Render API: `RENDER_API_KEY` i Pacy-Tech-team `.env`
- GitHub repo för projektet

## Workflow

### Phase 1: Discovery (GATHER)

1. **Identifiera target repo**
   - Fråga användaren om repo URL eller lokal path
   - Läs `package.json` för projekttyp

2. **Analysera struktur**
   - Monorepo? (`/frontend`, `/backend`)
   - Framework? (Next.js, Vite, Express)
   - Databas-spec? (`docs/*database*.md`)

3. **Bestäm deployment-typ**
   | Struktur | Frontend | Backend |
   |----------|----------|---------|
   | Monorepo | static_site | web_service |
   | Frontend only | static_site | - |
   | Fullstack Next.js | web_service | - |

### Phase 2: Infrastructure Decision

**ALLTID fråga användaren:**

> Vill du:
> A) Skapa nytt Supabase-projekt
> B) Använda befintligt projekt (lista tillgängliga)

**Befintliga projekt:**
- Pacy Demo Project DB (`cetabbnywnebilekvmag`)

### Phase 3: Supabase Setup (ACT)

**Om nytt projekt:**
```bash
~/bin/supabase projects create "Project Name" \
  --org-id olshfmcoszcgfwznrxgs \
  --region eu-central-1 \
  --db-password "SECURE_PASSWORD"
```

**Kör migrations (om databas-spec finns):**
```bash
mkdir -p /tmp/PROJECT && cd /tmp/PROJECT
~/bin/supabase init
~/bin/supabase link --project-ref REF --password "PASSWORD"
# Skapa migration från spec
~/bin/supabase db push
```

**Hämta credentials:**
```bash
~/bin/supabase projects api-keys --project-ref REF
```

### Phase 4: Render Setup (ACT)

**Skapa services via API:**

```bash
# Frontend (static_site)
curl -s "https://api.render.com/v1/services" \
  -X POST -H "Authorization: Bearer $RENDER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "static_site",
    "name": "PROJECT-frontend",
    "ownerId": "tea-d51s8f15pdvs73efe5f0",
    "repo": "https://github.com/USER/REPO",
    "rootDir": "frontend",
    "buildCommand": "npm install && npm run build",
    "staticPublishPath": "./dist"
  }'

# Backend (web_service) - kräver starter plan
curl -s "https://api.render.com/v1/services" \
  -X POST -H "Authorization: Bearer $RENDER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "web_service",
    "name": "PROJECT-api",
    "ownerId": "tea-d51s8f15pdvs73efe5f0",
    "repo": "https://github.com/USER/REPO",
    "rootDir": "backend",
    "serviceDetails": {
      "plan": "starter",
      "region": "frankfurt",
      "runtime": "node",
      "envSpecificDetails": {
        "buildCommand": "npm install && npm run build",
        "startCommand": "npm start"
      }
    }
  }'
```

### Phase 5: Configuration (ACT)

**Uppdatera projektets `.env`:**

```bash
# Skapa/uppdatera .env i målprojektet
cat >> /path/to/project/.env << EOF

# Supabase (added by deployment-workflow)
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...
SUPABASE_ANON_KEY=eyJ...

# Render URLs
VITE_API_URL=https://project-api.onrender.com
EOF
```

**Sätt env vars i Render:**
```bash
curl -s "https://api.render.com/v1/services/SERVICE_ID/env-vars" \
  -X PUT -H "Authorization: Bearer $RENDER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '[
    {"key": "SUPABASE_URL", "value": "https://xxx.supabase.co"},
    {"key": "SUPABASE_SERVICE_KEY", "value": "eyJ..."}
  ]'
```

### Phase 6: Deploy & Verify (VERIFY)

1. **Trigga deploy**
   ```bash
   curl -X POST "https://api.render.com/v1/services/ID/deploys" \
     -H "Authorization: Bearer $RENDER_API_KEY"
   ```

2. **Verifiera**
   - [ ] Render build lyckades
   - [ ] Frontend laddar
   - [ ] Backend health endpoint svarar
   - [ ] Databas-anslutning fungerar

## Output

När deployment är klar, rapportera:

```markdown
## Deployment Complete

| Service | URL |
|---------|-----|
| Frontend | https://project-frontend.onrender.com |
| Backend | https://project-api.onrender.com |
| Supabase | https://xxx.supabase.co |

**Credentials:** Sparade i `/path/to/project/.env`
```
