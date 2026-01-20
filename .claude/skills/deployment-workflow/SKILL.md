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

Orchestrates deployment of projects to Supabase (database) and Render (hosting).

## Prerequisites

- Supabase CLI: `~/bin/supabase` (authenticated)
- Render API: `RENDER_API_KEY` in Pacy-Tech-team `.env`
- GitHub repo for the project

## Workflow

### Phase 1: Discovery (GATHER)

1. **Identify target repo**
   - Ask user for repo URL or local path
   - Read `package.json` for project type

2. **Analyze structure**
   - Monorepo? (`/frontend`, `/backend`)
   - Framework? (Next.js, Vite, Express)
   - Database spec? (`docs/*database*.md`)

3. **Determine deployment type**
   | Structure | Frontend | Backend |
   |----------|----------|---------|
   | Monorepo | static_site | web_service |
   | Frontend only | static_site | - |
   | Fullstack Next.js | web_service | - |

### Phase 2: Infrastructure Decision

**ALWAYS ask the user:**

> Do you want to:
> A) Create a new Supabase project
> B) Use an existing project (list available ones)

**Existing projects:**
- Pacy Demo Project DB (`cetabbnywnebilekvmag`)

### Phase 3: Supabase Setup (ACT)

**If new project:**
```bash
~/bin/supabase projects create "Project Name" \
  --org-id $SUPABASE_ORG_ID \
  --region eu-central-1 \
  --db-password "SECURE_PASSWORD"
```

**Run migrations (if database spec exists):**
```bash
mkdir -p /tmp/PROJECT && cd /tmp/PROJECT
~/bin/supabase init
~/bin/supabase link --project-ref REF --password "PASSWORD"
# Create migration from spec
~/bin/supabase db push
```

**Get credentials:**
```bash
~/bin/supabase projects api-keys --project-ref REF
```

### Phase 4: Render Setup (ACT)

**Create services via API:**

```bash
# Frontend (static_site)
curl -s "https://api.render.com/v1/services" \
  -X POST -H "Authorization: Bearer $RENDER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "static_site",
    "name": "PROJECT-frontend",
    "ownerId": "'$RENDER_OWNER_ID'",
    "repo": "https://github.com/USER/REPO",
    "rootDir": "frontend",
    "buildCommand": "npm install && npm run build",
    "staticPublishPath": "./dist"
  }'

# Backend (web_service) - requires starter plan
curl -s "https://api.render.com/v1/services" \
  -X POST -H "Authorization: Bearer $RENDER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "web_service",
    "name": "PROJECT-api",
    "ownerId": "'$RENDER_OWNER_ID'",
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

**Update project's `.env`:**

```bash
# Create/update .env in target project
cat >> /path/to/project/.env << EOF

# Supabase (added by deployment-workflow)
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...
SUPABASE_ANON_KEY=eyJ...

# Render URLs
VITE_API_URL=https://project-api.onrender.com
EOF
```

**Set env vars in Render:**
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

1. **Trigger deploy**
   ```bash
   curl -X POST "https://api.render.com/v1/services/ID/deploys" \
     -H "Authorization: Bearer $RENDER_API_KEY"
   ```

2. **Verify**
   - [ ] Render build succeeded
   - [ ] Frontend loads
   - [ ] Backend health endpoint responds
   - [ ] Database connection works

## Output

When deployment is complete, report:

```markdown
## Deployment Complete

| Service | URL |
|---------|-----|
| Frontend | https://project-frontend.onrender.com |
| Backend | https://project-api.onrender.com |
| Supabase | https://xxx.supabase.co |

**Credentials:** Saved in `/path/to/project/.env`
```
