# AGENTS.md: Agent Development Guide

## Overview
This is a specialized OpenCode agent system repository containing 3 primary agents and 6 subagents defined in `.opencode/`. All agent prompts are Markdown files in `.opencode/agent/`. Configuration is in `.opencode/opencode.json`.

## Build, Lint, Test Commands

**Validate YAML/JSON config**:
```bash
node -e "console.log(JSON.parse(require('fs').readFileSync('.opencode/opencode.json')))" 
```

**Run single test** (if test suite exists):
```bash
npm test -- --testNamePattern="<test-name>"
```

**Build check** (validate Markdown syntax):
```bash
grep -r "^---" .opencode/agent/*.md  # Verify frontmatter
```

## Code Style Guidelines

### Agent Files (.opencode/agent/*.md)

**Structure**:
- YAML frontmatter with `description`, `mode` (primary/subagent), `temperature` (0.0-1.0), `tools`, `permission`
- Markdown content: agent prompt and directives

**Frontmatter Requirements**:
```yaml
---
description: "Brief role description"
mode: primary          # or subagent
temperature: 0.1       # 0.0 (deterministic) to 1.0 (creative)
tools:
  read: true          # lowercase, boolean values
  write: true
  bash: true
permission:
  edit: ask            # or deny/allow
  bash: ask
---
```

### Naming Conventions

- **Primary agents**: researcher, planner, implementor (lowercase, hyphens for multi-word)
- **Subagents**: codebase-locator, codebase-analyzer, thoughts-analyzer (descriptive, hyphenated)
- **Directories**: `.opencode/`, `thoughts/shared/` for artifact storage

### Prompt Writing Style

**Non-negotiable directives**:
- Use imperative voice: "You are the Researcher" (not "This is a researcher")
- Include "Prime Directive" sections for critical constraints
- Reference file:line in examples: `src/auth.ts:45`
- Avoid vague instructions; be explicit about outputs
- Document what the agent CANNOT do (forbidden tools, forbidden phrases)

**Error Handling**:
- Agents must cite specific file paths when referencing code
- Use `read` tool for file access, not `bash cat`
- If plan is missing/vague, agent should STOP and ask for clarification
- No hallucination of library APIs; use `context7` tool instead

### Imports & External Tools

**Tool Access Pattern**:
- `read`: For exploring codebase (primary)
- `bash`: For validation/testing only (needs permission)
- `context7`: For official library documentation
- `searxng-search`: For external research
- `sequential-thinking`: For complex reasoning
- `todowrite/todoread`: For task tracking
- Do NOT use bash for `grep`, `cat`, `find` — use dedicated tools instead

### Temperature Settings

| Value | Use Case | Example Agents |
|-------|----------|---|
| 0.0 | Deterministic search | codebase-locator, thoughts-locator |
| 0.1 | Focused analysis | researcher, planner, analyzers |
| 0.2 | Problem-solving creativity | implementor, web-search-researcher |

## Repository Structure

```
.opencode/
├── agent/                  # Agent definitions (Markdown files)
│   ├── researcher.md       # Primary: Orchestrates research
│   ├── planner.md          # Primary: Creates implementation plans
│   ├── implementor.md      # Primary: Executes plans
│   ├── codebase-locator.md # Subagent: Finds files/patterns
│   ├── codebase-analyzer.md# Subagent: Traces execution flow
│   ├── codebase-pattern-finder.md
│   ├── thoughts-locator.md
│   ├── thoughts-analyzer.md
│   └── web-search-researcher.md
├── opencode.json           # Configuration (temperature, tools, permissions)
└── README.md               # Full documentation
```

## Key Constraints

1. **NO external rules files**: No `.cursorrules` or `.github/copilot-instructions.md` in this repo
2. **JSON-only config**: `.opencode/opencode.json` is the single source of truth
3. **Markdown-first**: Agent logic lives in `.md` files, not code
4. **Three-tier hierarchy**: Only primary agents can invoke subagents; subagents are stateless

## License

This project is licensed under the MIT License. See LICENSE file for details.

MIT License (c) 2025 Steffen Eichenberg

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
