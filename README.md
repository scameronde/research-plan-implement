# OpenCode Agent System

A hierarchical AI agent architecture for code research, planning, and implementation. This system defines 3 primary agents (Researcher, Planner, Implementor) and 6 specialized subagents that work together to assist with software development tasks.

## Quick Start

1. **Install OpenCode** (if not already installed):
   ```bash
   npm install -g @opencode-ai/opencode
   ```

2. **Navigate to project directory**:
   ```bash
   cd /path/to/your/project
   ```

3. **Launch OpenCode with these agents**:
   ```bash
   opencode
   ```

4. **Switch between agents** using `Tab` key or invoke subagents with `@mention`

## Architecture Overview

### Primary Agents (Tab-switchable)

| Agent | Mode | Temp | Purpose |
|-------|------|------|---------|
| **Researcher** | Primary | 0.1 | Maps and documents codebase without making changes |
| **Planner** | Primary | 0.1 | Creates detailed implementation plans grounded in code reality |
| **Implementor** | Primary | 0.2 | Executes plans phase-by-phase with strict verification |

### Subagents (Invoke with @mention)

| Agent | Temp | Purpose |
|-------|------|---------|
| **@codebase-locator** | 0.0 | Quickly finds files and directories by pattern/keyword |
| **@codebase-analyzer** | 0.1 | Explains code by tracing execution paths and data flow |
| **@codebase-pattern-finder** | 0.1 | Discovers code patterns and established conventions |
| **@thoughts-locator** | 0.0 | Searches thoughts/ directory for documentation |
| **@thoughts-analyzer** | 0.1 | Extracts decisions and specs from historical docs |
| **@web-search-researcher** | 0.2 | Researches external libraries and best practices |

## Typical Workflows

### 1. Research a Feature

```
You: How does the authentication system work?

Researcher: I'll analyze the auth system...
[Orchestrates @codebase-locator, @codebase-analyzer, @web-search-researcher]

Output: Research report in thoughts/shared/research/
```

### 2. Plan a Feature

```
You: [Press Tab to switch to Planner]
     Add two-factor authentication using TOTP.

Planner: I'll analyze the existing auth code and create a detailed plan...
[Reads code, validates assumptions, creates phase-based plan]

Output: Implementation plan in thoughts/shared/plans/
```

### 3. Implement a Feature

```
You: [Press Tab to switch to Implementor]
     Execute the plan at thoughts/shared/plans/2024-12-16-2fa.md

Implementor: Loading plan...
[Phase 1 → Code → Test → CHECKPOINT]
[Wait for human approval]
[Phase 2 → Code → Test → CHECKPOINT]
...

Output: Implemented feature with git commits per phase
```

## Configuration

Agent behavior is configured in `.opencode/opencode.json`:

```json
{
  "agent": {
    "researcher": {
      "description": "Orchestrates sub-agents to map the codebase",
      "mode": "primary",
      "temperature": 0.1,
      "tools": { /* ... */ },
      "permission": { /* ... */ }
    }
    // ... more agents
  }
}
```

### Key Settings

- **temperature**: 0.0 (deterministic) to 1.0 (creative)
  - 0.0 for searching (locator agents)
  - 0.1 for analysis (researcher, planner, analyzers)
  - 0.2 for problem-solving (implementor, web-search-researcher)

- **tools**: Enable/disable capabilities per agent
  - `read`: File access
  - `write`: Create files
  - `edit`: Modify files
  - `bash`: Execute commands
  - `context7`: Library documentation lookup
  - `searxng-search`: Web search
  - `sequential-thinking`: Structured reasoning

- **permission**: Control user confirmation prompts
  - `allow`: Automatic execution
  - `ask`: Require confirmation
  - `deny`: Block capability

## File Structure

```
.
├── README.md                    # This file
├── AGENTS.md                    # Development guide for agents
├── .opencode/
│   ├── opencode.json            # Agent configuration
│   ├── README.md                # Detailed documentation
│   ├── QUICK_REFERENCE.md       # Agent quick reference
│   ├── INSTALL.md               # Installation instructions
│   └── agent/                   # Agent definitions
│       ├── researcher.md
│       ├── planner.md
│       ├── implementor.md
│       ├── codebase-locator.md
│       ├── codebase-analyzer.md
│       ├── codebase-pattern-finder.md
│       ├── thoughts-locator.md
│       ├── thoughts-analyzer.md
│       ├── web-search-researcher.md
│       └── python-qa-auditor.md
└── thoughts/shared/             # Agent output artifacts
    ├── research/                # Research reports
    ├── plans/                   # Implementation plans
    └── ...
```

## Key Principles

1. **"AS-IS" Philosophy**: Agents document what exists, not what should exist
2. **Code as Truth**: Live code is more authoritative than documentation
3. **No Hallucinations**: External knowledge comes from context7/searxng tools
4. **Human Checkpoints**: Critical decisions require human approval
5. **File Citations**: All claims backed by `file:line` references
6. **Structured Output**: Markdown artifacts for every major task
7. **Tool Specialization**: Right tool for the right job

## Best Practices

### Choose the Right Agent

| Task | Agent | Why |
|------|-------|-----|
| Understand code | Researcher | Orchestrates analysis, no changes |
| Plan feature | Planner | Grounds plan in real code |
| Build feature | Implementor | Quality gates & verification |
| Find files | @codebase-locator | Fast, deterministic search |
| Understand logic | @codebase-analyzer | Detailed execution tracing |
| Learn patterns | @codebase-pattern-finder | Shows conventions in use |
| Find docs | @thoughts-locator | Searches thoughts/ directory |
| Extract specs | @thoughts-analyzer | Filters key information |
| Research APIs | @web-search-researcher | External validation |

### Provide Clear Context

**Good**:
```
Research how the authentication system works.
```

**Better**:
```
Research how the authentication system works.
Look at src/auth/middleware.ts and src/services/auth-service.ts.
Pay special attention to how JWT tokens are validated.
Include analysis of the existing refresh token strategy.
```

### Trust the Process

- Let agents orchestrate their own subagent calls
- Review alignment checkpoints before approving plans
- Don't skip manual verification steps in implementation

## Tools & MCP Integration

The system uses Model Context Protocol (MCP) tools:

- **sequential-thinking**: Structured reasoning for complex analysis
- **context7**: Official library documentation lookup
- **searxng-search**: Privacy-focused web search

## Customization

### Modify Agent Behavior

Edit `.opencode/opencode.json` to adjust temperature, tools, or permissions.

### Customize Prompts

Edit `.opencode/agent/*.md` files to:
- Refine instructions
- Add project-specific guidelines
- Update output formats
- Adjust constraints

### Add New Agents

Create new `.md` files in `.opencode/agent/` with required frontmatter:

```markdown
---
description: "Agent role description"
mode: subagent
temperature: 0.1
tools:
  read: true
  bash: true
permission:
  bash: ask
---

# Agent Prompt

Your agent instructions here...
```

## Documentation

- **Full Guide**: See `.opencode/README.md` for comprehensive documentation
- **Quick Reference**: See `.opencode/QUICK_REFERENCE.md` for command reference
- **Installation**: See `.opencode/INSTALL.md` for setup instructions
- **Development**: See `AGENTS.md` for agent development guidelines

## Troubleshooting

**Agent not available**
- Ensure `.opencode/` is in project root
- Check `.opencode/opencode.json` for agent registration
- Restart OpenCode after config changes

**Subagent not invoked**
- Use explicit `@agent-name` syntax
- Or describe task clearly for auto-invocation
- Check agent description matches your use case

**Permission denied**
- Check permission settings in `.opencode/opencode.json`
- Respond "allow" to prompts, or set to `"allow"` in config

**Slow performance**
- Use @codebase-locator first to narrow scope
- Be specific about which files to analyze
- Break complex tasks into smaller steps

## Examples

### Example 1: Understand a Feature

```
You: How does the webhook system work?

Researcher: [Invokes @codebase-locator, @codebase-analyzer, @web-search-researcher]

Output: Detailed report with:
- Entry points and file locations
- Request/response flow
- Integration with external services
- Potential improvements (AS-IS style)
```

### Example 2: Plan an Enhancement

```
You: [Tab to Planner]
     Add caching to the API using Redis.
     Existing pattern: src/services/cache.ts

Planner: [Analyzes code, validates assumptions, creates plan]
         Questions for alignment?
         1. Which endpoints should be cached?
         2. Cache TTL strategy?
         3. Cache invalidation triggers?

[After answering]

Output: Detailed implementation plan with phases
```

### Example 3: Execute Implementation

```
You: [Tab to Implementor]
     Execute thoughts/shared/plans/2024-12-cache.md

Implementor: Phase 1: Create Redis client wrapper
             [Code → Test → CHECKPOINT]
             
             Waiting for verification...
             
You: Verified

Implementor: Phase 2: Add caching middleware
             [Code → Test → CHECKPOINT]
             ...
```

## Architecture Diagram

```
┌─────────────────────────────────────────┐
│         PRIMARY AGENTS (Tab)            │
│  Researcher │ Planner │ Implementor     │
└────────────────┬────────────────────────┘
                 │
         Orchestration Layer
                 │
    ┌────────────┼────────────┬──────────────┐
    │            │            │              │
    ▼            ▼            ▼              ▼
┌──────────┐ ┌──────────┐ ┌────────┐ ┌──────────────┐
│@codebase-│ │@codebase-│ │@thoughts│ │@web-search- │
│locator   │ │analyzer  │ │locator  │ │researcher   │
└──────────┘ └──────────┘ └────────┘ └──────────────┘
    │            │            │              │
    └────────────┴────────────┴──────────────┘
                 │
        Codebase / Thoughts / Web
```

## Version

**Agent System Version**: 1.0.0  
**Created**: December 17, 2025  
**OpenCode Compatibility**: 1.0+

## Support & Feedback

- **OpenCode Documentation**: https://opencode.ai/docs
- **OpenCode GitHub**: https://github.com/sst/opencode
- **Report Issues**: https://github.com/sst/opencode/issues

## License

This project is licensed under the MIT License. See LICENSE file for details.

MIT License (c) 2025 Steffen Eichenberg

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
