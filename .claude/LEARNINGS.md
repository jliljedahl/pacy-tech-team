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

### [2026-01-22] Supabase Integration för Content-Creating Agents

**Kontext:** Multi-agent content creation fungerade, men all content sparades till lokala filer istället för Supabase
**Problem:**
- Content-creating agents (article-writer, video-narrator, generating-quizzes, generating-exercises) hade instruktioner för file-based storage
- Test: Skapade program "AI-synlig på 30 dagar" med 6 sessioner → 24 content-filer sparades lokalt i `output/pacy-ai-search/content-items/`
- Inget sparades i Supabase-databasen → frontend kunde inte visa content
- Ingen database persistence = systemet fungerade inte som helhet

**Lösning:**
1. **Agents skriver direkt till Supabase via curl + REST API**
   - article-writer.md: POST till `/rest/v1/articles` (markdown med embedded källor)
   - video-narrator.md: POST till `/rest/v1/videos` (script med word count + duration)
   - generating-quizzes.md: Markdown table → konvertera till JSON → POST till `/rest/v1/quizzes`
   - generating-exercises.md: JSONB mapping → POST till `/rest/v1/exercises`

2. **Orchestrator koordinerar session_ids**
   - Pre-fetch session_ids för hela programmet från Supabase
   - Passa explicit session_id till varje agent vid delegation
   - Agents behöver inte query:a själva

3. **Status workflow**
   - Agents sparar som `status: "draft"`
   - Workflow: draft → reviewed → approved
   - Frontend filtrerar på status

4. **Error handling**
   - Retry-logik i orchestrator (max 3 försök)
   - Graceful degradation: Rapportera failure, fortsätt med andra sessioner
   - Validering efter batch: Query database för completion check

5. **Storage format**
   - Articles: Markdown (inkl. akademiska källor med klickbara URLs)
   - Videos: Markdown script
   - Quizzes: JSONB (konverteras från markdown table)
   - Exercises: JSONB (strukturerad data för AI-interaktion)

**Best Practices:**
1. Agents ska använda database-operations skill (redan finns)
2. Orchestrator koordinerar, agents skriver (separation of concerns)
3. Session_id ska passas explicit (inte query:as av varje agent)
4. JSONB för komplexa strukturer (quiz questions, exercise scenarios)
5. Status-field för workflow tracking
6. Markdown-first för content som ska visas i UI och kopieras till Notion

**Källor:**
- [Supabase Edge Functions Docs](https://supabase.com/docs/guides/functions)
- [Supabase Best Practices](https://www.leanware.co/insights/supabase-best-practices)
- CTO review: Direct writes vs Edge Function gateway (Direct wins för simplicity)
- Backend review: Pre-fetch session_ids, retry with graceful degradation

**Kategori:** article-writer.md, video-narrator.md, generating-quizzes.md, generating-exercises.md, content-orchestrator-agent.md
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
