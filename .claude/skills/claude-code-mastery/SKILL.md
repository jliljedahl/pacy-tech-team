---
name: claude-code-mastery
description: |
  Patterns for using Claude Code effectively. Covers subagents, skills, hooks,
  and the Agent SDK. Use when: reviewing Claude Code configurations, creating
  new agents or skills, setting up hooks, or verifying best practices at project start.
allowed-tools:
  - Read
  - Glob
  - Grep
  - WebFetch
  - WebSearch
---

# Claude Code Mastery

## Core Principles

### 1. Less Is More

Every instruction in CLAUDE.md goes into every conversation:
- Extra instructions dilute attention
- Irrelevant context wastes tokens
- Over-specification leads to worse instruction-following

**80% Rule**: If an instruction isn't applicable to 80%+ of sessions, it shouldn't be in CLAUDE.md.

### 2. Progressive Disclosure

Instead of putting everything in CLAUDE.md:
- Essential info in CLAUDE.md
- Specific workflows in slash commands/skills
- Detailed docs in separate files
- Let Claude discover patterns from code

### 3. WHAT/WHY/HOW Framework

Structure CLAUDE.md around:

| Section | Content |
|---------|---------|
| **WHAT** | Tech stack, project structure, key files |
| **WHY** | Purpose, architectural decisions, conventions |
| **HOW** | Commands, verification steps, boundaries |

### 4. Never LLM for Linter

Let linters handle formatting. Don't include:
- Indentation rules
- Quote style
- Line length limits

Instead: `Run npm run lint - uses .eslintrc config`

### 5. The Agent Loop

All effective agents follow:

```
GATHER CONTEXT → TAKE ACTION → VERIFY WORK → REPEAT
```

---

## CLAUDE.md Standards

### Optimal Length

| Complexity | Target | Max |
|------------|--------|-----|
| Simple | 30-50 | 75 |
| Medium | 50-100 | 150 |
| Complex | 100-150 | 200 |

Over 200 lines = too much. Move to skills/commands.

### What to Include

```markdown
## Commands
- npm run dev: Start development
- npm run test: Run tests
- npm run lint: Check style

## Key Directories
- src/: Source code
- tests/: Test files

## Verification
Run before committing:
1. npm run lint
2. npm run test
```

### What NOT to Include

- **Style rules** → Use linters
- **Obvious patterns** → Claude learns from code
- **Rare workflows** → Use slash commands

### Minimal Template

```markdown
# Project Name

> Brief description

## Commands
- [dev command]
- [test command]
- [lint command]

## Key Files
- [entry point]
- [config]

## Verification
1. Run lint
2. Run tests
```

---

## Skill Design

### SKILL.md Format

```markdown
---
name: skill-name
description: |
  What it does. Use when: [specific triggers].
allowed-tools:
  - Tool1
  - Tool2
---

# Skill Title

## When to Use
- Trigger condition 1
- Trigger condition 2

## Prerequisites
- Required setup

## Instructions
Step-by-step guidance

## Examples
Input/output examples

## Verification
How to confirm success
```

### Progressive Disclosure in Skills

| Level | Content | When Loaded |
|-------|---------|-------------|
| 1. Discovery | name + description | Always |
| 2. Core | Instructions, examples | On activation |
| 3. Reference | Detailed docs, scripts | On demand |

Keep SKILL.md to 50-100 lines. Put details in separate files.

### Skill Patterns

| Pattern | Use For |
|---------|---------|
| **How-To** | Teaching specific procedures |
| **Integration** | Connecting to external services |
| **Quality** | Enforcing standards |
| **Template** | Generating boilerplate |

---

## Subagent Patterns

### When to Use

**Good:**
- Code review (specialized focus)
- Research (isolated context)
- Testing (fix failures automatically)
- Debugging (systematic problem-solving)

**Avoid:**
- Simple, quick tasks (overhead not worth it)
- Tasks needing main conversation context
- Interactive workflows needing user feedback

### Subagent File Format

```markdown
---
name: subagent-name
description: Expert [role]. Use PROACTIVELY when [trigger].
tools: Read, Grep, Glob
model: sonnet
---

You are a [role description].

## When Invoked
1. First action
2. Second step
3. Begin without asking questions

## Process
[Detailed workflow]

## Output Format
[Expected structure]
```

### Writing Effective Descriptions

Include proactive trigger words:

```yaml
# Good
description: Expert code reviewer. Use PROACTIVELY after writing or modifying code.
description: Test specialist. MUST BE USED when tests fail or new tests needed.

# Bad
description: Helps with code  # Too vague
description: Testing helper   # No trigger
```

### Model Selection

| Model | Use For |
|-------|---------|
| `haiku` | Fast, simple tasks (research, search) |
| `sonnet` | Standard development work |
| `opus` | Complex reasoning, critical analysis |
| `inherit` | Match main conversation |

---

## Extended Thinking Guide

Use these phrases to control reasoning depth:

| Phrase | Use For | Example |
|--------|---------|---------|
| `"think"` | Simple planning | "Think about how to fix this bug" |
| `"think hard"` | Feature implementation | "Think hard about the API design" |
| `"think harder"` | Architecture decisions | "Think harder about the database schema" |
| `"ultrathink"` | Critical systems | "Ultrathink about the auth security model" |

---

## Workflow Patterns

### Explore → Plan → Code → Commit

Best for: Feature development, complex changes

```
1. EXPLORE: Read relevant files, understand scope
2. PLAN: Create implementation plan ("think hard")
3. CODE: Implement the solution
4. COMMIT: Verify and commit
```

### Test-Driven Development

Best for: Well-defined features, bug fixes

```
1. WRITE TESTS: Create tests for expected behavior
2. VERIFY FAILURE: Confirm tests fail
3. IMPLEMENT: Write code to pass tests
4. REFACTOR: Clean up, keep tests green
```

### Visual Iteration

Best for: UI development

```
1. IMPLEMENT: Create initial version
2. SCREENSHOT: Capture visual result
3. EVALUATE: Compare to expectations
4. ITERATE: Make improvements
```

---

## Verification Strategies

| Strategy | Best For |
|----------|----------|
| **Rules-based** | Linting, tests, type checking |
| **Visual** | UI, screenshots, diffs |
| **LLM-as-Judge** | Code quality, docs clarity (slower) |

Prefer rules-based when possible.

---

## Context Management

### Problem: Context Fills Up
- Use `/clear` between unrelated tasks
- Use subagents for research (isolated context)
- Use `/compact` to summarize

### Problem: Information Overload
- Be specific about what to read
- Use grep to find relevant sections
- Read headers first

### Problem: Losing Track
- Create checklist file for complex tasks
- Use Plan.md for long builds
- Periodically summarize progress

---

## Error Recovery

### When Claude Gets Stuck
1. Interrupt (Escape) and provide guidance
2. Reset with `/clear` if confused
3. Provide examples of what you want
4. Simplify - break into smaller pieces

### When Tests Fail
1. Share the error (don't just say "fix it")
2. Ask Claude to understand the failure first
3. Verify the fix with the same test

---

## Always Verify Current Docs

**Use WebSearch** before advising on Claude Code:
```
Search: "Claude Code [feature] site:docs.anthropic.com"
```

Key resources:
- https://docs.anthropic.com/en/docs/claude-code
- https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering
- https://docs.anthropic.com/en/docs/agents

Features change frequently. Verify before recommending.
