---
name: tech-radar
description: |
  Process for verifying technology recommendations against current best practices.
  Uses web search to check official documentation. Use when validating tech choices.
---

# Tech Radar

## Purpose

Verify that recommendations are current. Technology changes fast.

## Verification Process

### 1. Identify Claim

What technology or pattern is being recommended?

Example: "Use Supabase Edge Functions for API logic"

### 2. Find Official Source

| Technology | Documentation |
|------------|---------------|
| Supabase | supabase.com/docs |
| Next.js | nextjs.org/docs |
| React | react.dev |
| Tailwind | tailwindcss.com/docs |
| Render | render.com/docs |
| Vercel | vercel.com/docs |

### 3. Search Current Docs

Use WebSearch or WebFetch to verify:

```
Search: "[technology] [feature] documentation 2024"
```

Check:
- Is this still recommended?
- Any deprecation notices?
- Any breaking changes?
- Better alternatives now?

### 4. Check Version

- What version does our stack use?
- What version do docs reference?
- Any migration needed?

### 5. Report Findings

```markdown
## Tech Radar: [Topic]

### Claim
[What was recommended]

### Verification
- Source: [official doc URL]
- Doc date: [when updated]
- Current recommendation: [what docs say]

### Status
✅ CURRENT / ⚠️ OUTDATED / ❌ DEPRECATED

### Notes
[Any relevant details]

### Alternative (if outdated)
[Current recommended approach]
```

## Red Flags

| Flag | Action |
|------|--------|
| Doc says "legacy" | Find current approach |
| No recent updates | Check if maintained |
| Breaking changes in changelog | Verify compatibility |
| Community moving away | Consider alternatives |

## Quick Checks

### Supabase
- Edge Functions vs REST API
- RLS patterns
- Auth methods

### Next.js
- App Router vs Pages Router
- Server Components
- Data fetching patterns

### React
- Hooks patterns
- State management
- Component patterns

## Stay Current

For each project, verify:
- [ ] Framework version is supported
- [ ] Patterns match current docs
- [ ] No deprecated APIs used
