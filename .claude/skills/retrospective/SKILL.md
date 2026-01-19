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

Fångar lärdomar efter projekt för kontinuerlig förbättring.

## When to Use

- Efter avslutat projekt
- Efter en svår debugging-session
- När något tog längre tid än förväntat
- När vi hittade en bättre lösning än planerat

## Process

### 1. Gather (Fråga användaren)

```
Tre frågor:

1. Vad gick bra som vi bör fortsätta med?
2. Vad var svårt eller tog längre tid än förväntat?
3. Vad skulle vi göra annorlunda nästa gång?
```

### 2. Categorize

Koppla varje lärdom till relevant skill eller agent:

| Lärdom handlar om | Kategori |
|-------------------|----------|
| Supabase-setup | `supabase-setup` |
| Frontend-struktur | `frontend-planning` |
| Deployment-problem | `deployment-workflow` |
| Projektkoordinering | `project-coordination` |
| Agent-kommunikation | `tech-project-lead` |

### 3. Document

Skriv till `.claude/LEARNINGS.md`:

```markdown
### [YYYY-MM-DD] Kort beskrivande titel

**Kontext:** Vad vi gjorde
**Problem:** Vad som var svårt
**Lösning:** Hur vi löste det / vad vi lärde oss
**Kategori:** skill-namn eller agent-namn
```

### 4. Check for Patterns

Om LEARNINGS.md har 3+ lärdomar i samma kategori:

> "Jag ser ett mönster - flera lärdomar relaterar till [kategori].
> Vill du köra `skill-improvement` för att föreslå permanenta förbättringar?"

## Output

```markdown
## Retrospective: [Projekt/Session]

### Captured Learnings
- [Lärdom 1] → kategori
- [Lärdom 2] → kategori

### Pattern Alert
[Om mönster upptäckts]

### Next Step
[Förslag på vad som bör göras]
```

## Rules

1. **Alltid fråga användaren** - gissa inte lärdomar
2. **Var specifik** - "RLS-policies var svåra" inte "backend var svårt"
3. **Kategorisera korrekt** - viktig för att hitta mönster
4. **Håll kort** - en lärdom = ett problem + en lösning
