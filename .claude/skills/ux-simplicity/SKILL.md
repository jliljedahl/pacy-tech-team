---
name: ux-simplicity
description: |
  Design principles for simple, user-focused interfaces. Covers visual hierarchy,
  progressive disclosure, and common patterns. Use when designing screens.
---

# UX Simplicity

## Core Principle

**Every element must earn its place.**

Ask: "What can I remove?"

## Simplicity Test

1. Can a new user complete the main task in 30 seconds?
2. Is there anything I can remove?
3. Would my grandmother understand?

If any answer is "no", simplify.

## Principles

### 1. One Primary Action Per Screen

Make the main action obvious through size, color, position.

```
❌ 12 equal buttons
✅ One large "Create" button + supporting list
```

### 2. Progressive Disclosure

Show only what's needed now.

```
❌ 20 form fields visible
✅ 5 essential fields + "Show advanced"
```

### 3. Minimize Choices

```
❌ 10 status options
✅ 4 clear statuses
```

### 4. Clear Hierarchy

```
1. Page title (largest)
2. Section headers (medium)
3. Content (normal)
4. Supporting info (smaller, muted)
```

### 5. Immediate Feedback

```
❌ Click save, nothing happens
✅ Loading state → success toast
```

## Patterns

### Dashboard
```
┌─────────────────────────────┐
│ Title              [Action] │
├─────────────────────────────┤
│ [Filter/Search]             │
│ ┌─────┐ ┌─────┐ ┌─────┐    │
│ │Item │ │Item │ │Item │    │
│ └─────┘ └─────┘ └─────┘    │
└─────────────────────────────┘
```

### Detail Page
```
┌─────────────────────────────┐
│ ← Back           [Actions]  │
├─────────────────────────────┤
│ Title                       │
│ │Tab 1│Tab 2│Tab 3│        │
│ ┌─────────────────────┐    │
│ │ Content             │    │
│ └─────────────────────┘    │
└─────────────────────────────┘
```

### Form
```
┌─────────────────────────────┐
│ ← Cancel            [Save]  │
├─────────────────────────────┤
│ Label                       │
│ [Input                    ] │
│ Label                       │
│ [Input                    ] │
│ [Show Advanced]             │
└─────────────────────────────┘
```

## States

### Empty
```tsx
<div className="text-center py-12">
  <h3>No programs yet</h3>
  <p>Create your first program</p>
  <Button>Create Program</Button>
</div>
```

### Loading
```tsx
<div className="flex justify-center">
  <Spinner />
</div>
```

### Error
```tsx
<Alert variant="destructive">
  <AlertTitle>Couldn't load data</AlertTitle>
  <AlertDescription>
    <Button onClick={retry}>Retry</Button>
  </AlertDescription>
</Alert>
```

## Checklist

- [ ] One primary action per screen
- [ ] Forms have ≤7 visible fields
- [ ] All async actions show loading
- [ ] All errors are user-friendly
- [ ] Empty states guide to action
