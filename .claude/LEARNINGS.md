# Team Learnings

> **Inbox för lärdomar.** När en lärdom blivit permanent förbättring i en skill/agent, ta bort den härifrån.

## Hur detta fungerar

1. **retrospective** skill → fångar lärdomar → skriver hit
2. **skill-improvement** skill → analyserar mönster → föreslår uppdateringar
3. Användaren godkänner → skill/agent uppdateras → lärdom tas bort

---

## Active Learnings

<!--
Format:
### [Datum] Kort titel
**Kontext:** Vad hände
**Problem:** Vad gick fel/var svårt
**Lösning:** Hur vi löste det
**Kategori:** [skill-namn eller agent-namn som bör uppdateras]
-->

### [2026-01-22] Hub-and-Spoke för multi-agent orchestrering

**Kontext:** Efter brief-godkännande hoppade frontend direkt till research-director, men den startades utan brief-data
**Problem:**
- Subagenter delar INTE kontext automatiskt - research-director visste inte vad den skulle researcha
- Ingen central koordinering - agenter kunde inte kommunicera
- Frontend pratade direkt med fel agent
**Lösning:**
- Content Orchestrator blev central hub efter brief-fasen
- Frontend skickar auto-signal "Brief har godkänts" vid fas-transition
- Edge function (chat) läser program_knowledge och injicerar i systemprompten
- Orchestrator spawnar research-director med KOMPLETT kontext
- Research-director validerar inputs innan start
**Källor:**
- [Anthropic Multi-Agent Research](https://www.anthropic.com/engineering/multi-agent-research-system)
- [Claude Code Subagents](https://code.claude.com/docs/en/sub-agents)
**Kategori:** content-orchestrator-agent.md, research-director.md, useProject.ts, chat/index.ts
**Status:** Implementerad 2026-01-22

---

## Archived (Integrated)

<!-- Lärdomar som blivit permanenta förbättringar flyttas hit med referens till vilken fil som uppdaterades -->

### [2026-01-21] Brief Interviewer format-läckage fixat

**Kontext:** brief-interviewer frågade om format/tid/typ trots att det inte skulle göras
**Problem:**
- Rubriken "Training Type / Fidelity" misstolkades som format-fråga
- Frågan "Vilken typ av träning är detta?" triggade format-diskussioner
- Saknades explicita STOP-påminnelser för agenten själv
**Lösning:**
- Bytte rubrik till "Source Fidelity"
- Omformulerade frågan till "Behöver innehållet matcha specifika dokument exakt?"
- Lade till CRITICAL BOUNDARY-sektion efter Step 4
- Lade till self-check rader i HANDLING SITUATIONS
- Kategoriserade NEVER-listan i FORMAT/STRUCTURE vs LOGISTICS
**Integrerad i:** `program-creation-agents-v2/.claude/agents/brief-interviewer.md`
