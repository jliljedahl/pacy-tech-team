---
name: retrospective
description: |
  Captures learnings after a project or session. Documents what worked, what didn't,
  and what should be remembered. Writes to LEARNINGS.md for later integration.
allowed-tools:
  - Read
  - Edit
  - AskUserQuestion
---

# Retrospective

Captures learnings after projects for continuous improvement.

## When to Use

- After completing a project
- After a difficult debugging session
- When something took longer than expected
- When we found a better solution than planned

## Process

### 1. Gather (Ask the user)

```
Three questions:

1. What went well that we should continue doing?
2. What was difficult or took longer than expected?
3. What would we do differently next time?
```

### 2. Categorize

Map each learning to the relevant skill or agent:

| Learning relates to | Category |
|---------------------|----------|
| Supabase setup | `supabase-setup` |
| Frontend structure | `frontend-planning` |
| Deployment issues | `deployment-workflow` |
| Project coordination | `project-coordination` |
| Agent communication | `tech-project-lead` |

### 3. Document

Write to `.claude/LEARNINGS.md`:

```markdown
### [YYYY-MM-DD] Short descriptive title

**Context:** What we were doing
**Problem:** What was difficult
**Solution:** How we solved it / what we learned
**Category:** skill-name or agent-name
```

### 4. Check for Patterns

If LEARNINGS.md has 3+ learnings in the same category:

> "I see a pattern - multiple learnings relate to [category].
> Do you want to run `skill-improvement` to suggest permanent improvements?"

## Output

```markdown
## Retrospective: [Project/Session]

### Captured Learnings
- [Learning 1] → category
- [Learning 2] → category

### Pattern Alert
[If pattern detected]

### Next Step
[Suggestion for what should be done]
```

## Rules

1. **Always ask the user** - don't guess learnings
2. **Be specific** - "RLS policies were difficult" not "backend was hard"
3. **Categorize correctly** - important for finding patterns
4. **Keep it short** - one learning = one problem + one solution
