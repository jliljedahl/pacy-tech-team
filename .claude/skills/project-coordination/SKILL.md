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
- Document what was built
- Transfer knowledge

**Gate:** Customer accepts delivery

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
