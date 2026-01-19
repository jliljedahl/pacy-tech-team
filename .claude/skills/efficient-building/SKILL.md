---
name: efficient-building
description: |
  Strategies for building efficiently and minimizing token usage. Covers planning,
  code reuse, and avoiding rewrites. Use throughout development.
---

# Efficient Building

## Core Principle

**Think twice, code once.**

Planning saves tokens. Rewrites double the cost.

## Strategies

### 1. Plan Completely First

Before coding:
- [ ] Requirements documented
- [ ] Screens finalized
- [ ] Components planned
- [ ] Build order clear
- [ ] Plan approved

### 2. Build Foundation First

Order matters:

```
1. Project setup (once)
2. Supabase client (once)
3. Types (once)
4. Shared components (once)
5. Layouts (once)
6. Pages (compose above)
```

### 3. Use Templates

**Page template:**
```tsx
export default function Page() {
  const { data, isLoading, error } = useQuery(...)
  
  if (isLoading) return <Loading />
  if (error) return <Error error={error} />
  
  return <PageLayout title="...">...</PageLayout>
}
```

**Component template:**
```tsx
interface Props {
  // props
}

export function Component({ prop }: Props) {
  return (...)
}
```

### 4. Batch Related Work

```
❌ Create Button → Create Page → Fix Button → Update Page
✅ Create ALL components → Create ALL pages
```

### 5. Write Complete Files

```
❌ // TODO: Add error handling
✅ Full implementation with all states
```

### 6. Use Libraries

```
❌ Custom button: 200 tokens
✅ shadcn button: 20 tokens
```

Libraries:
- UI: shadcn/ui
- Icons: lucide-react
- Forms: react-hook-form + zod
- Data: @tanstack/react-query

## File Structure

```
src/
├── app/              # Routes
├── components/
│   ├── ui/           # shadcn
│   └── [feature]/    # Feature components
├── lib/
│   ├── supabase.ts
│   └── utils.ts
└── types/
```

## Anti-Patterns

| Anti-Pattern | Cost |
|--------------|------|
| No plan | Rewrites |
| Custom everything | Wasted effort |
| Incomplete files | Revisits |
| Context switching | Setup overhead |
| Unasked features | Total waste |

## Checklist

Before coding:
- [ ] I know exactly what I'm building
- [ ] I have all information needed
- [ ] I'm using existing components

After each file:
- [ ] File is complete
- [ ] Follows patterns
- [ ] No duplicated code
