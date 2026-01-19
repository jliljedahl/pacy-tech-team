---
name: architecture-review
description: |
  Framework for reviewing technical architecture decisions. Covers evaluation criteria,
  review process, and verdict formats. Use when reviewing specialist recommendations.
---

# Architecture Review

## Review Criteria

| Criterion | Question | Weight |
|-----------|----------|--------|
| Simplicity | Simplest solution that works? | High |
| Scalability | Handles 10x growth? | Medium |
| Maintainability | Junior dev can understand? | High |
| Cost | Appropriate for scale? | Medium |
| Security | Any vulnerabilities? | High |

## Review Process

### 1. Understand Proposal

- What problem does it solve?
- What was recommended?
- What are the constraints?

### 2. Verify Current Practice

**Always check official documentation.**

Sources to check:
- Official docs (supabase.com, nextjs.org)
- Recent release notes
- Migration guides

### 3. Evaluate

For each criterion, score: ✅ Good / ⚠️ Concern / ❌ Problem

### 4. Deliver Verdict

## Verdict Template

```markdown
## Review: [Topic]

### Proposed
[Summary of recommendation]

### Verification
- Checked: [sources]
- Current best practice: [findings]

### Evaluation

| Criterion | Score | Notes |
|-----------|-------|-------|
| Simplicity | ✅ | |
| Scalability | ✅ | |
| Maintainability | ⚠️ | [concern] |
| Cost | ✅ | |
| Security | ✅ | |

### Verdict
✅ APPROVED / ⚠️ APPROVED WITH CHANGES / ❌ REJECTED

### Rationale
[Why this decision]

### Required Changes
[If any]

### Risks to Monitor
[Future concerns]
```

## Common Review Areas

### Data Model
- Proper normalization
- Appropriate constraints
- Index strategy
- RLS policies

### Tech Choices
- Still maintained?
- Right for our scale?
- Team can learn it?
- Integration with stack?

### Patterns
- Still recommended?
- Correctly applied?
- Unnecessary complexity?
