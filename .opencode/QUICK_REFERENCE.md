# OpenCode Agent System - Quick Reference

## Primary Agents (Switch with Tab)

| Agent | Purpose | Can Edit? | Key Feature |
|-------|---------|-----------|-------------|
| **researcher** | Map & document codebase | ❌ | Orchestrates subagents, creates research reports |
| **planner** | Create implementation plans | ❌ | Grounds plans in code reality |
| **implementor** | Execute plans with quality gates | ✅ (ask) | Phase-by-phase with human checkpoints |

## Subagents (Invoke with @mention)

| Agent | When to Use | Output |
|-------|-------------|--------|
| **@codebase-locator** | Find files/directories | File paths by category |
| **@codebase-analyzer** | Understand how code works | Execution flow analysis |
| **@codebase-pattern-finder** | Learn project conventions | Code examples & patterns |
| **@thoughts-locator** | Find docs in thoughts/ | Document list |
| **@thoughts-analyzer** | Extract decisions from docs | Key findings & specs |
| **@web-search-researcher** | Research external libraries | Research report with sources |

## Common Workflows

### Research Codebase
```
How does [feature] work?
```
→ Researcher orchestrates subagents → Creates report in `thoughts/shared/research/`

### Plan Feature
```
[Tab to planner]
Add [feature description]. Consider [constraints].
```
→ Planner analyzes code → Presents findings → Creates plan in `thoughts/shared/plans/`

### Implement
```
[Tab to implementor]
Execute thoughts/shared/plans/YYYY-MM-DD-[topic].md
```
→ Implementor runs phases → Pauses for verification → Commits per phase

### Find Files
```
@codebase-locator find all authentication files
```
→ Returns categorized file list

### Understand Logic
```
@codebase-analyzer explain the order processing flow
```
→ Returns execution trace with file:line references

### Learn Patterns
```
@codebase-pattern-finder show how we handle API errors
```
→ Returns 2-3 examples with actual code

### Research Library
```
@web-search-researcher how to use Stripe webhooks in Node.js
```
→ Returns official docs + code examples + confidence score

## Keybinds

| Key | Action |
|-----|--------|
| **Tab** | Switch between primary agents |
| `@agent-name` | Invoke subagent |

## File Outputs

| Agent | Creates Files In |
|-------|------------------|
| Researcher | `thoughts/shared/research/YYYY-MM-DD-[topic].md` |
| Planner | `thoughts/shared/plans/YYYY-MM-DD-[topic].md` |
| Implementor | Commits code + updates plan status |

## Temperature Settings

| Setting | Agents | Behavior |
|---------|--------|----------|
| **0.0** | locators | Deterministic search |
| **0.1** | researcher, planner, analyzers | Focused analysis |
| **0.2** | implementor, web-researcher | Slight creativity |

## Tool Access

| Agent | bash | read | write | edit | context7 | searxng |
|-------|------|------|-------|------|----------|---------|
| researcher | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ |
| planner | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ |
| implementor | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| codebase-* | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| thoughts-* | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| web-search | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |

## Quick Tips

### Give Context
❌ "Add caching"
✅ "Add Redis caching to API routes following the pattern in @src/middleware/cache.ts"

### Use @ Mentions
❌ "Find the auth files"
✅ "@codebase-locator find all authentication-related files"

### Trust the Process
- Let **researcher** orchestrate (don't micromanage subagents)
- Review **planner** alignment before approval
- Don't skip **implementor** checkpoints

### Follow AS-IS Philosophy
Agents document **what exists**, not what should exist.

To get suggestions:
```
After documenting X, suggest improvements.
```

## Example Conversations

**Research**:
```
User: How does authentication work?
→ Researcher orchestrates @codebase-locator, @codebase-analyzer
→ Creates thoughts/shared/research/2024-12-16-auth-system.md
```

**Planning**:
```
User: [Tab to planner] Add 2FA using TOTP
→ Planner reads existing auth code
→ Uses @codebase-pattern-finder for auth patterns
→ Uses @web-search-researcher for TOTP libraries
→ Presents findings for approval
→ Creates thoughts/shared/plans/2024-12-16-2fa.md
```

**Implementation**:
```
User: [Tab to implementor] Execute the 2FA plan
→ Phase 1 → Code → Test → CHECKPOINT ⏸️
User: Verified
→ Phase 2 → Code → Test → CHECKPOINT ⏸️
User: Verified
→ Complete ✅
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Agent not found | Restart OpenCode after adding .opencode/ |
| Permission denied | Respond "allow" or change permission in opencode.json |
| Slow performance | Be specific about files, use @codebase-locator to narrow scope |
| Subagent not invoked | Use explicit @mention syntax |

## Configuration

Edit `.opencode/opencode.json`:

```json
{
  "agent": {
    "researcher": {
      "temperature": 0.1,  // Adjust 0.0-1.0
      "tools": { /* ... */ },
      "permission": { "bash": "ask" }
    }
  }
}
```

## MCP Tools

- **sequential-thinking**: Structured reasoning (researcher, planner, implementor)
- **context7**: Official library docs (researcher, planner, web-search-researcher)
- **searxng-search**: Web search (researcher, planner, web-search-researcher)

---

**Full documentation**: See `.opencode/README.md`
