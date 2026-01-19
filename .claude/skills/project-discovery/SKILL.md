---
name: project-discovery
description: |
  Process for understanding data requirements before designing infrastructure. 
  Covers entity identification, relationships, and access patterns. Use at start of backend work.
---

# Project Discovery

## Process

### 1. Check Existing Docs

```bash
ls docs/
cat docs/*.md
```

Read before asking questions.

### 2. Identify Entities

| Entity | Description | Example |
|--------|-------------|---------|
| clients | Companies | Assa Abloy |
| programs | Training | B2B Marketing |

### 3. Map Relationships

| From | To | Type |
|------|-----|------|
| clients | programs | 1:many |
| programs | chapters | 1:many |

### 4. Define Fields

| Field | Type | Required |
|-------|------|----------|
| id | UUID | Yes |
| name | text | Yes |
| status | text | Yes |

### 5. Access Patterns

| Query | Frequency |
|-------|-----------|
| List programs by client | High |
| Get program with sessions | Medium |

### 6. Security Requirements

| Rule | Implementation |
|------|----------------|
| Tenant isolation | RLS on client_id |

## Output

```markdown
# Data Requirements: [Project]

## Entities
[list]

## Relationships  
[diagram or table]

## Fields
[per entity]

## Access Patterns
[queries needed]

## Security
[who sees what]

## Questions
[unclear items]
```

## Questions to Ask

1. What are the main things users create/view/edit?
2. How are [A] and [B] related?
3. Who can see/modify this data?
4. Expected data volume?
