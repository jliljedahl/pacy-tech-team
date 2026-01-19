---
name: api-design
description: |
  Patterns for using Supabase's auto-generated REST API. Covers queries, filters, 
  relationships, and error handling. Use when documenting API usage for frontend.
---

# API Design

## Supabase Client Setup

```typescript
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)
```

## Query Patterns

### Select All

```typescript
const { data, error } = await supabase
  .from('programs')
  .select('*')
```

### Select Specific Columns

```typescript
const { data, error } = await supabase
  .from('programs')
  .select('id, name, status')
```

### With Relationships

```typescript
const { data, error } = await supabase
  .from('programs')
  .select(`
    id, name,
    clients (name),
    chapters (id, title)
  `)
```

### Filter

```typescript
const { data, error } = await supabase
  .from('programs')
  .select('*')
  .eq('status', 'published')
  .eq('client_id', clientId)
```

### Sort

```typescript
const { data, error } = await supabase
  .from('programs')
  .select('*')
  .order('created_at', { ascending: false })
```

### Pagination

```typescript
const { data, error } = await supabase
  .from('programs')
  .select('*')
  .range(0, 9) // First 10
```

### Single Record

```typescript
const { data, error } = await supabase
  .from('programs')
  .select('*')
  .eq('id', programId)
  .single()
```

## Mutations

### Insert

```typescript
const { data, error } = await supabase
  .from('programs')
  .insert({ name: 'New Program', client_id: clientId })
  .select()
  .single()
```

### Update

```typescript
const { data, error } = await supabase
  .from('programs')
  .update({ status: 'published' })
  .eq('id', programId)
  .select()
  .single()
```

### Delete

```typescript
const { error } = await supabase
  .from('programs')
  .delete()
  .eq('id', programId)
```

## Error Handling

```typescript
const { data, error } = await supabase
  .from('programs')
  .select('*')

if (error) {
  console.error('Error:', error.message)
  return
}

// Use data
```

## Common Errors

| Code | Meaning |
|------|---------|
| PGRST301 | Row not found |
| 42501 | Permission denied (RLS) |
| 23505 | Unique violation |
| 23503 | Foreign key violation |
