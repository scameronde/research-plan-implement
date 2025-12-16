# OpenCode Agent System

A comprehensive 3-tier agent architecture for code research, planning, and implementation.

## Overview

This agent system provides specialized AI assistants for different aspects of software development:

- **3 Primary Agents**: Researcher, Planner, Implementor (switchable with Tab key)
- **6 Subagents**: Specialized assistants invoked with `@mention`
- **Hierarchical Design**: Lead agents orchestrate specialized subagents
- **Quality-First**: Built-in checkpoints, verification, and "AS-IS" documentation philosophy

## Quick Start

### Installation

1. **Place in your project**:
   ```bash
   # Copy the .opencode directory to your project root
   cp -r .opencode /path/to/your/project/
   ```

2. **Launch OpenCode**:
   ```bash
   cd /path/to/your/project
   opencode
   ```

3. **Switch between agents**:
   - Press `Tab` to cycle through primary agents
   - Type `@agent-name` to invoke subagents

### First Steps

**Research the codebase**:
```
How is authentication handled in this project?
```
→ The **researcher** agent will orchestrate @codebase-locator and @codebase-analyzer

**Plan a feature**:
```
I want to add rate limiting to the API. Create a plan.
```
→ Switch to **planner** agent (Tab), it will analyze code and create a detailed plan

**Implement**:
```
Execute the plan at thoughts/shared/plans/2024-12-rate-limiting.md
```
→ Switch to **implementor** agent (Tab), it will execute phase-by-phase with checkpoints

---

## Primary Agents

### 🔍 Researcher
**Mode**: Primary | **Temp**: 0.1 | **Edit**: Denied

**Purpose**: Map and document the codebase without making changes

**When to use**:
- Understanding how features work
- Creating architectural documentation
- Finding components and their relationships
- Generating research reports

**Key Features**:
- Orchestrates specialized subagents
- Creates Markdown research artifacts
- Uses sequential-thinking for structured analysis
- Follows "AS-IS" documentation principle (no suggestions)

**Tools**: bash, read, write, searxng-search, sequential-thinking, context7

**Example**:
```
Research how the authentication system works and create a report.
```

---

### 📋 Planner
**Mode**: Primary | **Temp**: 0.1 | **Edit**: Denied

**Purpose**: Create detailed technical specifications grounded in code reality

**When to use**:
- Architecting new features
- Breaking down complex changes
- Validating feasibility
- Creating implementation roadmaps

**Key Features**:
- Reads code before planning (no hallucinations)
- Highlights discrepancies between requests and reality
- Interactive alignment before finalizing plans
- Phase-based implementation breakdowns

**Tools**: bash, read, write, context7, searxng-search, sequential-thinking

**Example**:
```
Create a plan for adding OAuth2 support to the existing auth system.
```

---

### 🛠️ Implementor
**Mode**: Primary | **Temp**: 0.2 | **Edit**: Ask

**Purpose**: Execute implementation plans with strict quality gates

**When to use**:
- Building features from plans
- Systematic code changes
- Quality-focused development
- Test-driven implementation

**Key Features**:
- Requires plan before coding (NO PLAN, NO CODE)
- Human checkpoints after each phase
- Automated verification (build, test, lint)
- Resume capability for interrupted work

**Tools**: bash, read, write, edit, sequential-thinking

**Example**:
```
Implement the plan at thoughts/shared/plans/2024-12-16-oauth2.md
```

---

## Subagents

Invoke these specialists using `@agent-name` in your messages.

### 📁 @codebase-locator
**Temp**: 0.0 | **Tools**: bash, read

Finds files and directories by pattern or keyword.

**Use for**:
- "Where is the authentication middleware?"
- "Find all API route files"
- "Locate test files for the auth module"

**Output**: File paths organized by category (implementation, tests, config, types)

---

### 🔬 @codebase-analyzer
**Temp**: 0.1 | **Tools**: bash, read

Explains how code works by tracing execution paths.

**Use for**:
- "How does the login flow work?"
- "Explain the data transformation in OrderProcessor"
- "Trace the API request lifecycle"

**Output**: Detailed execution flow with file:line references

---

### 📚 @codebase-pattern-finder
**Temp**: 0.1 | **Tools**: bash, read

Finds code examples and established conventions.

**Use for**:
- "How do we typically handle pagination?"
- "Show me examples of error handling"
- "What's the pattern for React components here?"

**Output**: Multiple pattern examples with usage distribution

---

### 📂 @thoughts-locator
**Temp**: 0.0 | **Tools**: bash, read

Searches the `thoughts/` directory for documentation.

**Use for**:
- "Find the ticket for feature X"
- "Locate design docs about authentication"
- "Search for past decisions on database choice"

**Output**: Categorized list of relevant documents with descriptions

---

### 📖 @thoughts-analyzer
**Temp**: 0.1 | **Tools**: bash, read

Extracts decisions and specs from historical documents.

**Use for**:
- "What were the constraints for the auth system?"
- "Extract the technical specs from ticket ENG-123"
- "Summarize the key decisions from this design doc"

**Output**: Structured extraction of decisions, constraints, and specifications

---

### 🌐 @web-search-researcher
**Temp**: 0.2 | **Tools**: searxng-search, context7

Researches external libraries and best practices.

**Use for**:
- "How do I use Stripe payment intents?"
- "What's the latest Next.js routing approach?"
- "Find best practices for PostgreSQL connection pooling"

**Output**: Research report with official docs, code examples, and confidence scoring

---

## Agent Hierarchy

```
┌─────────────────────────────────────────┐
│         PRIMARY AGENTS (Tab)            │
│  Researcher │ Planner │ Implementor     │
└────────────────┬────────────────────────┘
                 │
                 ├─── @codebase-locator
                 ├─── @codebase-analyzer
                 ├─── @codebase-pattern-finder
                 ├─── @thoughts-locator
                 ├─── @thoughts-analyzer
                 └─── @web-search-researcher
```

**Primary agents** can invoke **subagents** automatically based on the task, or you can invoke subagents manually with `@mention`.

---

## Workflows

### Research Workflow

1. **Ask a research question**:
   ```
   How does the payment processing system work?
   ```

2. **Researcher orchestrates**:
   - @codebase-locator → Finds payment-related files
   - @codebase-analyzer → Explains the logic flow
   - @web-search-researcher → Validates Stripe API usage

3. **Output**: Markdown research report in `thoughts/shared/research/`

---

### Planning Workflow

1. **Switch to Planner** (Tab key)

2. **Describe the feature**:
   ```
   Add two-factor authentication using TOTP. Users should be able to
   enable it in settings and use an authenticator app.
   ```

3. **Planner analyzes**:
   - Reads existing auth code
   - Uses @codebase-pattern-finder for auth patterns
   - Uses @web-search-researcher for TOTP libraries
   - Highlights discrepancies

4. **Alignment checkpoint**: Planner presents findings

5. **After approval**: Creates detailed plan in `thoughts/shared/plans/`

---

### Implementation Workflow

1. **Switch to Implementor** (Tab key)

2. **Provide plan path**:
   ```
   Implement the plan at thoughts/shared/plans/2024-12-16-2fa.md
   ```

3. **Implementor executes**:
   - Phase 1 → Code → Test → **CHECKPOINT** ⏸️
   - Wait for human: "Verified"
   - Phase 2 → Code → Test → **CHECKPOINT** ⏸️
   - Continue...

4. **Output**: Implemented feature with git commits per phase

---

## Configuration

The agent system is configured in `.opencode/opencode.json`:

```json
{
  "agent": {
    "researcher": { /* config */ },
    "planner": { /* config */ },
    "implementor": { /* config */ },
    "codebase-locator": { /* config */ },
    // ... more agents
  },
  "permission": {
    "bash": "ask",
    "edit": "ask"
  },
  "tools": {
    "searxng-search": true,
    "sequential-thinking": true,
    "context7": true
  }
}
```

### Temperature Settings

- **0.0** (codebase-locator, thoughts-locator): Deterministic searching
- **0.1** (researcher, planner, analyzers): Focused analysis
- **0.2** (implementor, web-search-researcher): Slight creativity for problem-solving

### Tool Access

**Primary agents**:
- Full read/write access (researcher, planner: no edit)
- Bash with "ask" permission
- Access to MCP tools (context7, sequential-thinking)

**Subagents**:
- Read-only (except web-search-researcher: no file access)
- Limited bash (search operations)
- Specialized tool access based on role

---

## Best Practices

### 1. Use the Right Agent for the Job

| Task | Agent | Why |
|------|-------|-----|
| Understand code | Researcher | Orchestrates analysis without changes |
| Plan feature | Planner | Grounds plan in code reality |
| Build feature | Implementor | Quality gates & checkpoints |
| Find files | @codebase-locator | Fast, focused search |
| Understand logic | @codebase-analyzer | Detailed tracing |
| Learn patterns | @codebase-pattern-finder | Shows conventions |
| Find docs | @thoughts-locator | Searches thoughts/ |
| Extract decisions | @thoughts-analyzer | Filters signal from noise |
| Research APIs | @web-search-researcher | External validation |

### 2. Trust the Process

- **Researcher**: Let it orchestrate subagents—it knows when to delegate
- **Planner**: Review the alignment checkpoint before approving
- **Implementor**: Don't skip manual verification steps

### 3. Provide Context

**Good**:
```
I want to add caching to the API. We're using Redis for sessions already.
Look at @src/cache/redis.ts for the pattern.
```

**Better**:
```
Add request caching to the API endpoints in @src/api/routes.ts.
Use the same Redis client pattern from @src/cache/redis.ts.
Cache GET requests for 5 minutes, invalidate on POST/PUT/DELETE.
Follow the existing middleware pattern in @src/middleware/auth.ts.
```

### 4. Follow the "AS-IS" Philosophy

Agents document **what exists**, not what should exist:
- They won't suggest refactoring unless asked
- They won't fix bugs unless explicitly requested
- They focus on current reality, not ideal state

To get suggestions, explicitly ask:
```
After documenting the auth system, suggest improvements.
```

---

## File Structure

```
.opencode/
├── agent/                          # Agent definitions
│   ├── researcher.md               # Primary: Research orchestrator
│   ├── planner.md                  # Primary: Implementation architect
│   ├── implementor.md              # Primary: Code executor
│   ├── codebase-locator.md         # Subagent: File finder
│   ├── codebase-analyzer.md        # Subagent: Logic analyzer
│   ├── codebase-pattern-finder.md  # Subagent: Pattern finder
│   ├── thoughts-locator.md         # Subagent: Doc finder
│   ├── thoughts-analyzer.md        # Subagent: Doc analyzer
│   └── web-search-researcher.md    # Subagent: External research
├── opencode.json                   # Agent configuration
└── README.md                       # This file
```

---

## MCP Tools

This agent system uses Model Context Protocol (MCP) tools:

### sequential-thinking
Structured reasoning for complex tasks. Used by:
- Researcher (planning, synthesis)
- Planner (decomposition, analysis)
- Implementor (phase tracking)

### context7
Official library documentation lookup. Used by:
- Researcher (external dependencies)
- Planner (API verification)
- Web-search-researcher (primary tool)

### searxng-search
Privacy-focused web search. Used by:
- Researcher (general research)
- Planner (best practices)
- Web-search-researcher (community solutions)

---

## Customization

### Adjust Agent Behavior

Edit `.opencode/opencode.json` to modify:
- **Temperature**: 0.0-1.0 (higher = more creative)
- **Tools**: Enable/disable specific capabilities
- **Permissions**: Change ask/allow/deny settings

### Modify Agent Prompts

Edit the Markdown files in `.opencode/agent/` to:
- Refine instructions
- Add project-specific guidelines
- Update output formats
- Adjust directives

### Add New Agents

Create new `.md` files in `.opencode/agent/`:

```markdown
---
description: "Your agent description"
mode: subagent
temperature: 0.1
tools:
  bash: true
  read: true
---

# Your Agent Prompt
...
```

---

## Troubleshooting

### Agent not available
- Ensure `.opencode/` is in project root
- Check `opencode.json` for correct agent registration
- Restart OpenCode after config changes

### Subagent not invoked
- Use `@agent-name` syntax explicitly
- Or describe the task clearly for auto-invocation
- Check agent description matches your use case

### Permission denied
- Check `permission` settings in `opencode.json`
- Respond "allow" to permission prompts
- Or change to `"allow"` in config for that tool

### Slow performance
- Reduce context by being specific about files
- Use @codebase-locator first to narrow scope
- Break complex tasks into smaller steps

---

## Examples

### Example 1: Understanding a Feature

```
User: How does the webhook processing system work?

Researcher: I'll analyze the webhook system. Let me start by locating relevant files.

[Invokes @codebase-locator]
[Invokes @codebase-analyzer]
[Invokes @web-search-researcher for Stripe webhook docs]

Researcher: I've created a research report at:
thoughts/shared/research/2024-12-16-webhook-processing.md

Summary:
- Entry point: src/api/webhooks.ts:15
- Signature verification using Stripe SDK
- Event routing via EventEmitter pattern
- Queue processing for async operations
...
```

### Example 2: Planning a Feature

```
User: [Tab to Planner]
      Add email notifications when orders are placed.

Planner: I'll analyze the current system to create a plan.

[Reads src/models/order.ts, src/services/order-service.ts]
[Uses @codebase-pattern-finder for notification patterns]
[Uses @web-search-researcher for SendGrid API]

Planner: Analysis complete. I found:
- Current order creation flow in order-service.ts:45
- Existing notification system using SendGrid
- Email templates in views/emails/

Discrepancy: The ticket doesn't specify which email template to use.

Questions:
1. Use existing "order-confirmation.hbs" template?
2. Send immediately or queue for async processing?
3. Include order items in email or just summary?

[After user responds]

Planner: Creating implementation plan...
Written to: thoughts/shared/plans/2024-12-16-order-notifications.md
```

### Example 3: Implementation

```
User: [Tab to Implementor]
      Execute thoughts/shared/plans/2024-12-16-order-notifications.md

Implementor: Loading plan... Baseline tests passing ✓

Phase 1: Email Template Creation
[Modifies views/emails/order-confirmation.hbs]
[Runs tests]

🏁 Phase 1 Ready for Review
Automated Tests: ✅ All passing
Manual Verification:
1. Check email template renders correctly
2. Verify all order data is included

⏸️ Waiting for confirmation...

User: Verified

Implementor: Phase 2: Email Service Integration
[Continues...]
```

---

## Philosophy

This agent system is built on key principles:

1. **"AS-IS" Documentation**: Document reality, not wishes
2. **Code as Truth**: Live code > documentation > speculation
3. **No Hallucinations**: Use context7/searxng for external knowledge
4. **Human Checkpoints**: Critical decisions need human approval
5. **Structured Output**: Markdown artifacts for every major task
6. **Tool Specialization**: Right tool for the right job
7. **Cite Everything**: All claims backed by file:line references

---

## Version

**Agent System Version**: 1.0.0  
**Created**: December 16, 2024  
**OpenCode Compatibility**: 1.0+

---

## Support

For issues or questions:
- OpenCode Docs: https://opencode.ai/docs/agents
- GitHub Issues: https://github.com/sst/opencode/issues
- Discord: https://opencode.ai/discord

---

## License

These agents are provided as-is for use with OpenCode. Customize freely for your projects.
