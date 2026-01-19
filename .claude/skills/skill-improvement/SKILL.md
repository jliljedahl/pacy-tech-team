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

Analyserar lärdomar och föreslår permanenta förbättringar till skills/agents.

## When to Use

- När LEARNINGS.md har 3+ lärdomar i samma kategori
- Efter större projekt för att konsolidera lärdomar
- När `retrospective` flaggar ett mönster

## Process

### 1. Analyze Learnings

```bash
# Läs LEARNINGS.md
# Gruppera efter kategori
# Identifiera mönster (3+ liknande problem)
```

### 2. Find Target File

| Kategori | Fil att uppdatera |
|----------|-------------------|
| `supabase-setup` | `.claude/skills/supabase-setup/SKILL.md` |
| `deployment-workflow` | `.claude/skills/deployment-workflow/SKILL.md` |
| `tech-project-lead` | `.claude/agents/tech-project-lead.md` |
| `frontend-planning` | `.claude/skills/frontend-planning/SKILL.md` |
| etc. | `.claude/skills/[kategori]/SKILL.md` |

### 3. Propose Change

Presentera för användaren:

```markdown
## Proposed Improvement

**Pattern identified:** [Vad vi sett upprepas]

**Learnings involved:**
- [Lärdom 1]
- [Lärdom 2]
- [Lärdom 3]

**Proposed change to:** `[fil]`

**Current:**
[Relevant sektion som finns idag]

**Proposed:**
[Hur det bör se ut efter ändring]

**Rationale:**
[Varför detta förbättrar systemet]
```

### 4. Implement (efter godkännande)

1. Uppdatera skill/agent-filen
2. Flytta lärdomar till "Archived" i LEARNINGS.md
3. Lägg till referens till vilken fil som uppdaterades

### 5. Verify

- Läs uppdaterad fil
- Bekräfta att ändringen är korrekt
- Rapportera till användaren

## Output

```markdown
## Improvement Complete

**Updated:** `[fil]`

**Change:** [Kort beskrivning]

**Archived learnings:** [antal] learnings moved to archive

**LEARNINGS.md status:** [antal] active learnings remaining
```

## Rules

1. **Aldrig ändra utan godkännande** - föreslå, vänta på OK
2. **Minimal ändring** - lägg till, refaktorera inte hela filen
3. **Bevara arkiv** - flytta, radera inte lärdomar
4. **En ändring i taget** - inte flera filer samtidigt

## Pattern Thresholds

| Antal lärdomar | Åtgärd |
|----------------|--------|
| 1-2 | Behåll i LEARNINGS.md, inget mönster än |
| 3+ | Föreslå permanent förbättring |
| 5+ | Prioritera denna förbättring |
