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

## Core Concepts

### Agent Architecture

```
User → Claude Code → Subagents (Task tool)
                  → Skills (Skill tool)
                  → Hooks (shell scripts)
```

### Subagents vs Skills

| Type | Purpose | Invocation |
|------|---------|------------|
| Subagent | Autonomous multi-step tasks | Task tool, spawns process |
| Skill | Inline guidance/patterns | Skill tool, loads into context |

## Subagent Patterns

### Defining an Agent

File: `.claude/agents/AGENT_NAME.md`

```markdown
---
name: agent-name
description: |
  Single paragraph describing what this agent does.
  Called by [other-agent] when [condition].
model: sonnet | opus | haiku
tools: Read, Write, Edit, Bash, Glob, Grep, WebFetch, WebSearch
skills: skill1, skill2, skill3
---

# Agent Name

Instructions for the agent...
```

### Model Selection

| Model | Use For |
|-------|---------|
| `haiku` | Quick, simple tasks (low cost) |
| `sonnet` | Standard development work |
| `opus` | Complex reasoning, architecture |

### Agent Coordination

```
                    coordinator
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
   specialist-1    specialist-2    specialist-3
```

**Pattern:** One coordinator delegates to specialists. Specialists never call each other directly.

## Skill Patterns

### Defining a Skill

File: `.claude/skills/SKILL_NAME/SKILL.md`

```markdown
---
name: skill-name
description: |
  Brief description of what guidance this skill provides.
  Use when [specific situation].
---

# Skill Name

Content that will be loaded into context when skill is invoked.
```

### User-Invocable Skills

Add to CLAUDE.md or settings.json to make a skill invocable via `/skillname`:

```json
{
  "skills": [
    {
      "name": "/skillname",
      "description": "What this does",
      "skill": "skill-name"
    }
  ]
}
```

## Hooks

### What Hooks Do

Shell scripts that run at specific points in Claude Code's lifecycle.

### Hook Types

| Event | Runs When |
|-------|-----------|
| `PreToolUse` | Before a tool executes |
| `PostToolUse` | After a tool completes |
| `Notification` | When Claude sends a notification |
| `Stop` | When Claude finishes a response |

### Hook Configuration

File: `.claude/settings.json`

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "command": "./scripts/validate-bash.sh"
      }
    ]
  }
}
```

### Hook Best Practices

- Keep hooks fast (they block execution)
- Exit 0 to allow, non-zero to block
- Log to stderr, not stdout
- Test hooks manually first

## Agent SDK

### When to Use

Building custom applications that use Claude as an agent, not just for Claude Code configuration.

### Basic Pattern

```typescript
import Anthropic from '@anthropic-ai/sdk';

const client = new Anthropic();

// Agent loop
async function runAgent(prompt: string) {
  const messages = [{ role: 'user', content: prompt }];

  while (true) {
    const response = await client.messages.create({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 4096,
      tools: [...],
      messages,
    });

    if (response.stop_reason === 'end_turn') {
      return response;
    }

    // Handle tool calls
    if (response.stop_reason === 'tool_use') {
      const toolResults = await executeTools(response);
      messages.push({ role: 'assistant', content: response.content });
      messages.push({ role: 'user', content: toolResults });
    }
  }
}
```

## Verification Checklist

When reviewing Claude Code configurations:

### Agents
- [ ] Clear, single-purpose description
- [ ] Appropriate model for task complexity
- [ ] Minimal required tools
- [ ] Skills match agent responsibilities

### Skills
- [ ] Actionable guidance, not just documentation
- [ ] Specific to a task/domain
- [ ] Examples included
- [ ] No conflicting instructions

### Hooks
- [ ] Fast execution (<100ms ideal)
- [ ] Proper error handling
- [ ] Tested manually
- [ ] Clear purpose documented

## Always Verify

**Use WebSearch** to check current Claude Code documentation:
- Features may have changed
- New capabilities added
- Deprecated patterns

```
Search: "Claude Code [feature] site:docs.anthropic.com"
```
