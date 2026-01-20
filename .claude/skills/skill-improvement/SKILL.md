---
name: skill-improvement
description: |
  Analyzes LEARNINGS.md for patterns and proposes permanent improvements to skills
  and agents. Closes the feedback loop by turning ad-hoc solutions into standard practices.
allowed-tools:
  - Read
  - Edit
  - Write
  - Glob
  - AskUserQuestion
---

# Skill Improvement

Analyzes learnings and proposes permanent improvements to skills/agents.

## When to Use

- When LEARNINGS.md has 3+ learnings in the same category
- After major projects to consolidate learnings
- When `retrospective` flags a pattern

## Process

### 1. Analyze Learnings

```bash
# Read LEARNINGS.md
# Group by category
# Identify patterns (3+ similar problems)
```

### 2. Find Target File

| Category | File to update |
|----------|----------------|
| `supabase-setup` | `.claude/skills/supabase-setup/SKILL.md` |
| `deployment-workflow` | `.claude/skills/deployment-workflow/SKILL.md` |
| `tech-project-lead` | `.claude/agents/tech-project-lead.md` |
| `frontend-planning` | `.claude/skills/frontend-planning/SKILL.md` |
| etc. | `.claude/skills/[category]/SKILL.md` |

### 3. Propose Change

Present to the user:

```markdown
## Proposed Improvement

**Pattern identified:** [What we've seen repeat]

**Learnings involved:**
- [Learning 1]
- [Learning 2]
- [Learning 3]

**Proposed change to:** `[file]`

**Current:**
[Relevant section as it exists today]

**Proposed:**
[How it should look after the change]

**Rationale:**
[Why this improves the system]
```

### 4. Implement (after approval)

1. Update the skill/agent file
2. Move learnings to "Archived" in LEARNINGS.md
3. Add reference to which file was updated

### 5. Verify

- Read the updated file
- Confirm the change is correct
- Report to the user

## Output

```markdown
## Improvement Complete

**Updated:** `[file]`

**Change:** [Short description]

**Archived learnings:** [count] learnings moved to archive

**LEARNINGS.md status:** [count] active learnings remaining
```

## Rules

1. **Never change without approval** - propose, wait for OK
2. **Minimal change** - add to, don't refactor the entire file
3. **Preserve archive** - move, don't delete learnings
4. **One change at a time** - not multiple files simultaneously

## Pattern Thresholds

| Learning count | Action |
|----------------|--------|
| 1-2 | Keep in LEARNINGS.md, no pattern yet |
| 3+ | Propose permanent improvement |
| 5+ | Prioritize this improvement |
