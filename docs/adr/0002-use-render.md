# ADR 0002: Use Render for Hosting

## Status

Accepted

## Context

We need a hosting platform for Pacy infrastructure projects that:
- Deploys from GitHub with minimal configuration
- Supports both static sites and server-side rendering
- Provides predictable pricing
- Has good API for automation

Options considered:
1. **Render** - Modern PaaS with simple pricing
2. **Vercel** - Next.js creators, optimized for frontend
3. **Netlify** - JAMstack focused
4. **Railway** - Developer-friendly PaaS
5. **AWS/GCP** - Full cloud platforms

## Decision

We use **Render** as our default hosting platform.

## Rationale

| Factor | Render | Vercel | Netlify | Railway | AWS |
|--------|--------|--------|---------|---------|-----|
| Static sites | Free | Free | Free | $5/mo | Complex |
| Web services | $7/mo | $20/mo | Limited | $5/mo | Variable |
| Pricing clarity | High | Medium | Medium | High | Low |
| API quality | Excellent | Good | Good | Good | Excellent |
| Complexity | Low | Low | Low | Low | High |
| Monorepo support | Good | Excellent | Good | Good | Manual |

Key advantages:
1. **Simple pricing** - $7/mo for web services, free static sites
2. **Great API** - Full automation possible
3. **Blueprint support** - Infrastructure as code with render.yaml
4. **No surprise bills** - Predictable costs
5. **Good performance** - Frankfurt region for EU

## Consequences

### Positive
- Predictable costs for client projects
- Easy automation via API (service creation, env vars, deploys)
- render.yaml enables repeatable deployments
- Free tier useful for demos and development

### Negative
- Less optimized for Next.js than Vercel
- Starter plan required for always-on services ($7/mo)
- Smaller ecosystem than Vercel/Netlify

### Neutral
- Manual HTTPS cert for custom domains (auto-provisioned)
- Build times comparable to alternatives

## Cost Structure

| Service Type | Plan | Cost |
|--------------|------|------|
| Static Site | Free | $0/mo |
| Web Service | Starter | $7/mo |
| Web Service | Standard | $25/mo |
| Background Worker | Starter | $7/mo |

## References

- [Render Documentation](https://render.com/docs)
- [Render Pricing](https://render.com/pricing)
- Our skill: `.claude/skills/render-deployment/SKILL.md`
