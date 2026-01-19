---
name: requirements-analysis
description: |
  Process for understanding frontend requirements from project docs and data models.
  Identifies users, workflows, and required screens. Use before designing UI.
---

# Requirements Analysis

## Process

### 1. Gather Information

- Read project documentation
- Understand data model from backend
- Identify API endpoints available

### 2. Identify Users

| User Type | Goal |
|-----------|------|
| Content Creator | Create training programs |
| Reviewer | Approve content |

### 3. Map Workflows

For each user, what tasks do they need to complete?

```
Content Creator:
1. View all programs
2. Select program to edit
3. Navigate to session
4. Edit content
5. Save changes
```

### 4. Derive Screens

| Screen | Purpose | Priority |
|--------|---------|----------|
| Dashboard | List programs | P0 |
| Program View | Show chapters/sessions | P0 |
| Session Editor | Edit content | P0 |

### 5. Define Screen Requirements

For each screen:

```markdown
## Screen: Dashboard

**Data:** programs with client name, status, session count
**Actions:** view, filter, search, create
**Components:** ProgramList, FilterBar, SearchInput
```

## Output

```markdown
# Requirements: [Project]

## Users
[who and what they need]

## Workflows
[step by step tasks]

## Screens
[list with purpose]

## Screen Details
[data, actions, components for each]

## Questions
[unclear items]
```

## Red Flags

- No clear primary user
- Too many screens for MVP
- Missing data from backend
- Assumed features not confirmed
