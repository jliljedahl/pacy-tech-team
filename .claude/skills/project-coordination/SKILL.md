---
name: project-coordination
description: |
  Framework for coordinating projects across specialist subagents. Covers phased delivery, 
  delegation patterns, and progress tracking. Use when managing multi-phase projects.
---

# Project Coordination

## Project Phases

```
DISCOVERY → BACKEND → FRONTEND → HANDOFF
```

### Phase 1: Discovery
- Understand requirements
- Review existing docs
- Confirm scope

**Gate:** Customer approves scope

### Phase 2: Backend
- Delegate to backend-architect
- CTO reviews architecture
- Customer approves

**Gate:** Database working, API tested

### Phase 3: Frontend
- Delegate to frontend-architect
- CTO reviews if needed
- Customer approves plan

**Gate:** Application deployed

### Phase 4: Handoff
- Verify everything works
- **Update project README.md** (mandatory - see template below)
- Create internal handoff document
- Run retrospective skill
- Transfer knowledge

**Gate:** Customer accepts delivery AND project is self-documenting

## Delegation Templates

### To Backend Architect

```
Backend Architect, I need you to design the data infrastructure.

Project: [name]
Context: [what we're building]

Requirements:
- [data requirement 1]
- [data requirement 2]

Existing docs: [location]

Please provide: Data model, SQL, API patterns
```

### To Frontend Architect

```
Frontend Architect, backend is ready. Build the UI.

Project: [name]
Users: [who uses this]

Backend:
- Tables: [list]
- API: [endpoints]

Please provide: Plan first, then implementation
```

### To CTO

```
CTO, please review this architecture decision.

Context: [problem we're solving]
Proposed: [what specialist recommended]

Questions:
1. Is this current best practice?
2. Any simpler alternatives?
3. Risks to consider?
```

## Progress Updates

```markdown
## Status: [Project]

Phase: [current] | Progress: [X]%

### Done
- ✅ [completed items]

### In Progress  
- 🔄 [current work]

### Next
- ⏳ [upcoming]

### Blockers
- [if any]
```

## Handoff Document

```markdown
# Handoff: [Project]

## Delivered
- URL: [deployed app]
- Supabase: [project url]

## Architecture
- Database: [tables]
- Frontend: [tech]

## Environment Variables
[list with instructions]

## Key Decisions
| Decision | Rationale |
|----------|-----------|

## Next Steps
[recommendations]
```

## Project README Template

Every project MUST have its README.md updated. This is non-negotiable.

```markdown
# [Project Name]

[One sentence: what this does]

## Quick Start

\`\`\`bash
npm install
npm run dev
\`\`\`

## Architecture

| Component | Technology | URL/Details |
|-----------|------------|-------------|
| Database | Supabase | [project URL] |
| Frontend | Next.js | [deployed URL] |

## Environment Variables

| Variable | Description |
|----------|-------------|
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key |

## Features

- [Feature 1]
- [Feature 2]

## Development

[How to run, test, and deploy]

## Key Decisions

| Decision | Why |
|----------|-----|
| [Choice made] | [Rationale] |
```

**Why this matters:** The customer must be able to maintain and extend the project without us. A good README is the difference between handoff and abandonment.
