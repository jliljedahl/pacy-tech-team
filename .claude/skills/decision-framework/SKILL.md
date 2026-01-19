---
name: decision-framework
description: |
  Structured approach for evaluating technical decisions. Covers trade-off analysis,
  documentation, and decision records. Use when making or reviewing important choices.
---

# Decision Framework

## When to Use

Use for decisions that:
- Affect architecture
- Are hard to reverse
- Have long-term impact
- Involve trade-offs

## Decision Process

### 1. Frame the Decision

```markdown
**Decision needed:** [What we're deciding]
**Context:** [Why this decision matters]
**Constraints:** [Time, budget, team skills]
```

### 2. Identify Options

List 2-4 realistic options:

| Option | Description |
|--------|-------------|
| A | [approach] |
| B | [approach] |
| C | Do nothing |

### 3. Evaluate Trade-offs

For each option:

| Criterion | Option A | Option B |
|-----------|----------|----------|
| Complexity | Low | High |
| Cost | Free | $50/mo |
| Time to implement | 2 days | 1 week |
| Reversibility | Easy | Hard |
| Team familiarity | High | Low |

### 4. Make Recommendation

```markdown
**Recommended:** Option [X]

**Rationale:**
- [Primary reason]
- [Secondary reason]

**Trade-offs accepted:**
- [What we're giving up]

**Risks:**
- [What could go wrong]
- [Mitigation]
```

### 5. Document Decision

## Decision Record Template

```markdown
# ADR-[number]: [Title]

## Status
Proposed / Accepted / Deprecated / Superseded

## Context
[Why we need to make this decision]

## Decision
[What we decided]

## Options Considered

### Option A: [Name]
**Pros:** 
**Cons:**

### Option B: [Name]
**Pros:**
**Cons:**

## Consequences

### Positive
- [benefit]

### Negative
- [trade-off]

### Risks
- [risk] → [mitigation]

## References
- [relevant docs/links]
```

## Quick Decision Checklist

Before approving:
- [ ] Problem is clearly understood
- [ ] Options were considered
- [ ] Trade-offs are acceptable
- [ ] Current best practice verified
- [ ] Team can execute this
- [ ] Reversible if wrong (or risk is acceptable)

## Default Choices for Pacy

When in doubt, prefer:

| Area | Default |
|------|---------|
| Database | Supabase |
| Backend | Supabase (no separate) |
| Frontend | Next.js App Router |
| Styling | Tailwind + shadcn |
| Hosting | Render |
| Auth | Supabase Auth |

Deviate only with clear rationale.
