---
name: frontend-planning
description: |
  Framework for creating complete frontend plans before coding. Ensures all decisions
  are made upfront. MUST be used before starting development.
---

# Frontend Planning

## Golden Rule

**No code until the plan is approved.**

## Plan Template

Create `docs/frontend-plan.md`:

```markdown
# Frontend Plan: [Project]

## Overview
[One paragraph: what, who, scope]

## Tech Stack
| Layer | Choice |
|-------|--------|
| Framework | Next.js 14 |
| Styling | Tailwind |
| Components | shadcn/ui |

## Screens

| Screen | Route | Purpose |
|--------|-------|---------|
| Dashboard | / | List programs |
| Program | /programs/[id] | View program |
| Editor | /programs/[id]/sessions/[sid] | Edit content |

## Screen: Dashboard

### Wireframe
```
┌─────────────────────────────┐
│ Programs           [+ New]  │
├─────────────────────────────┤
│ [Search] [Status ▼]         │
│ ┌─────────────────────────┐ │
│ │ Program Name     Draft → │ │
│ │ Client • 5 sessions     │ │
│ └─────────────────────────┘ │
└─────────────────────────────┘
```

### Data
- programs (id, name, status, client_name)

### Actions
- View list
- Filter by status
- Create new

### Components
- ProgramCard
- ProgramList
- StatusFilter

## Screen: [Next]
[Repeat for each screen]

## Components

### Shared
| Component | Location |
|-----------|----------|
| PageLayout | components/layout |
| StatusBadge | components/ui |

### Feature
| Component | Location |
|-----------|----------|
| ProgramCard | components/programs |

## Build Order

### Phase 1: Setup
- [ ] Create Next.js project
- [ ] Add shadcn/ui
- [ ] Setup Supabase client

### Phase 2: Dashboard
- [ ] PageLayout
- [ ] ProgramCard
- [ ] Dashboard page

### Phase 3: [Next Phase]
- [ ] ...

## Open Questions
- [ ] [Questions for stakeholder]

---

**Status:** Draft / Approved
**Approved by:** 
**Date:**
```

## Approval Process

Present to Project Lead:

> "Here's my frontend plan. Before coding:
> 1. Does the screen list cover everything?
> 2. Do wireframes match expectations?
> 3. Is build order acceptable?"

**Do not code until approved.**

## During Development

- Use build order as task list
- Check wireframes when building
- Update plan if scope changes
- Get re-approval for structural changes
