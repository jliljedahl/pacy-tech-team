---
name: render-deployment
description: |
  Guide for deploying applications to Render. Covers static sites, web services,
  monorepo blueprints, and environment variables. Use when: deploying to Render,
  setting up render.yaml, configuring server hosting, or deploying Next.js/React apps.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
---

# Render Deployment

## Service Types

| Type | Use For | Cost |
|------|---------|------|
| Static Site | Next.js export, React | Free |
| Web Service | Next.js with SSR | $7+/mo |

## Static Site (Recommended for MVP)

### Next.js Config

```javascript
// next.config.js
module.exports = {
  output: 'export',
  images: { unoptimized: true }
}
```

### Render Settings

| Setting | Value |
|---------|-------|
| Build Command | `npm run build` |
| Publish Directory | `out` |
| Node Version | 18+ |

## Web Service (SSR)

### Render Settings

| Setting | Value |
|---------|-------|
| Build Command | `npm run build` |
| Start Command | `npm start` |
| Node Version | 18+ |

## Environment Variables

In Render dashboard → Environment:

```
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...
```

## Deployment Steps

1. Push code to GitHub
2. Create new service in Render
3. Connect GitHub repo
4. Configure build settings
5. Add environment variables
6. Deploy

## Custom Domain

1. Add domain in Render settings
2. Update DNS:
   - CNAME: `your-app.onrender.com`
3. Wait for SSL certificate

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Build fails | Check Node version, dependencies |
| 404 on routes | Configure redirects for SPA |
| Env vars not working | Prefix with `NEXT_PUBLIC_` |
| Slow cold starts | Web Service has spindown on free tier |

## Render.yaml (Optional)

```yaml
services:
  - type: web
    name: my-app
    env: node
    buildCommand: npm run build
    startCommand: npm start
    envVars:
      - key: NEXT_PUBLIC_SUPABASE_URL
        sync: false
```

---

## Monorepo Deployment

### Project Structure

```
project/
├── backend/
│   ├── package.json
│   └── src/
├── frontend/
│   ├── package.json
│   └── src/
├── render.yaml
└── package.json (root, optional)
```

### render.yaml Blueprint

```yaml
services:
  # Backend API
  - type: web
    name: my-app-api
    env: node
    rootDir: backend
    buildCommand: npm install && npm run build
    startCommand: npm start
    healthCheckPath: /health
    envVars:
      - key: NODE_ENV
        value: production
      - key: SUPABASE_URL
        sync: false
      - key: SUPABASE_SERVICE_KEY
        sync: false
      - key: SUPABASE_ANON_KEY
        sync: false

  # Frontend (Static Site)
  - type: web
    name: my-app-frontend
    env: static
    rootDir: frontend
    buildCommand: npm install && npm run build
    staticPublishPath: ./dist
    envVars:
      - key: VITE_SUPABASE_URL
        sync: false
      - key: VITE_SUPABASE_ANON_KEY
        sync: false
      - key: VITE_API_URL
        sync: false
    routes:
      - type: rewrite
        source: /*
        destination: /index.html
```

### Health Check Endpoint

Add to backend:

```typescript
// backend/src/routes/health.ts
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});
```

### Persistent Disk (File Uploads)

```yaml
services:
  - type: web
    name: my-app-api
    env: node
    rootDir: backend
    buildCommand: npm install && npm run build
    startCommand: npm start
    disk:
      name: uploads
      mountPath: /var/data/uploads
      sizeGB: 1
```

Access in code:

```typescript
const UPLOAD_DIR = process.env.UPLOAD_PATH || '/var/data/uploads';
```

### Environment Variables Best Practices

```yaml
envVars:
  # Public values - set directly
  - key: NODE_ENV
    value: production

  # Secrets - set in Render dashboard (sync: false)
  - key: SUPABASE_SERVICE_KEY
    sync: false

  # Cross-service references
  - key: API_URL
    fromService:
      name: my-app-api
      type: web
      property: host
```

### Deployment Steps (Monorepo)

1. Create `render.yaml` in repo root
2. Push to GitHub
3. In Render: New → Blueprint
4. Connect repo, Render detects `render.yaml`
5. Configure secret env vars in dashboard
6. Deploy

### Service Communication

Frontend → Backend:

```typescript
// Frontend: .env
VITE_API_URL=https://my-app-api.onrender.com

// Frontend: api.ts
const API_URL = import.meta.env.VITE_API_URL;

export async function fetchData() {
  const response = await fetch(`${API_URL}/api/data`);
  return response.json();
}
```

### Troubleshooting Monorepo

| Issue | Fix |
|-------|-----|
| Wrong directory built | Check `rootDir` in render.yaml |
| Static site 404s | Add rewrite rule for SPA |
| Backend can't start | Check `startCommand`, port binding |
| Disk not persisting | Verify disk mount path matches code |
| Services can't communicate | Use `fromService` or full URLs |
