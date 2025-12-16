# Installation Guide - OpenCode Agent System

## Prerequisites

1. **OpenCode 1.0+** installed and configured
2. **MCP Servers** configured:
   - `context7` (library documentation)
   - `sequential-thinking` (structured reasoning)
3. **Custom Tool** installed:
   - `searxng-search` (web search)

## Installation Steps

### Step 1: Copy Agent System to Your Project

```bash
# Navigate to your project
cd /path/to/your/project

# Copy the .opencode directory
cp -r /path/to/opencode/rip/agents/.opencode .

# Verify structure
ls -la .opencode/
```

You should see:
```
.opencode/
├── agent/
│   ├── researcher.md
│   ├── planner.md
│   ├── implementor.md
│   ├── codebase-locator.md
│   ├── codebase-analyzer.md
│   ├── codebase-pattern-finder.md
│   ├── thoughts-locator.md
│   ├── thoughts-analyzer.md
│   └── web-search-researcher.md
├── opencode.json
├── README.md
├── QUICK_REFERENCE.md
└── INSTALL.md (this file)
```

### Step 2: Verify MCP Servers

Ensure these MCP servers are configured in your OpenCode config:

**Check**: `~/.config/opencode/config.json` or your project's `opencode.json`

```json
{
  "mcpServers": {
    "context7": {
      // Your context7 configuration
    },
    "sequential-thinking": {
      // Your sequential-thinking configuration
    }
  }
}
```

If not configured, add them according to their installation instructions.

### Step 3: Verify Custom Tools

Ensure `searxng-search` tool is available:

**Option A**: Global installation (recommended)
```bash
# Tool should be in ~/.config/opencode/tool/
ls ~/.config/opencode/tool/searxng-search.*
```

**Option B**: Project-specific
```bash
# Tool in project .opencode/tool/
ls .opencode/tool/searxng-search.*
```

If missing, install the searxng-search tool first.

### Step 4: Launch OpenCode

```bash
cd /path/to/your/project
opencode
```

### Step 5: Verify Agents Are Loaded

In OpenCode, press `Tab` to cycle through agents. You should see:
- **researcher**
- **planner**
- **implementor**

Also verify subagents are available by typing `@` and you should see autocomplete for:
- @codebase-locator
- @codebase-analyzer
- @codebase-pattern-finder
- @thoughts-locator
- @thoughts-analyzer
- @web-search-researcher

### Step 6: Test the System

**Test 1: Researcher Agent**
```
How is this project structured?
```
→ Should invoke @codebase-locator and create a research report

**Test 2: Subagent Invocation**
```
@codebase-locator find all TypeScript files
```
→ Should list TypeScript files by category

**Test 3: Switch Agents**
```
[Press Tab]
Create a plan for adding a simple hello-world endpoint.
```
→ Should switch to planner and analyze the project

## Troubleshooting

### Agents Don't Appear

**Problem**: After launching OpenCode, agents are not available

**Solutions**:
1. Verify `.opencode/` is in the project root (same level as `package.json`, etc.)
2. Check `opencode.json` syntax is valid (no trailing commas, proper JSON)
3. Restart OpenCode completely
4. Check OpenCode version: `opencode --version` (need 1.0+)

### Permission Errors

**Problem**: "Permission denied" when agent tries to use tools

**Solutions**:
1. Check `.opencode/opencode.json` permission settings
2. Respond "allow" when prompted
3. Or change permission to "allow" in config:
   ```json
   "permission": {
     "bash": "allow",
     "edit": "ask"
   }
   ```

### MCP Tools Not Found

**Problem**: "Tool 'context7' not found" or similar

**Solutions**:
1. Verify MCP server is configured in OpenCode config
2. Restart OpenCode to reload MCP connections
3. Check MCP server logs for errors
4. Temporarily disable the tool in `.opencode/opencode.json`:
   ```json
   "tools": {
     "context7": false  // Temporarily disable
   }
   ```

### Subagents Not Invoked

**Problem**: Typing `@agent-name` doesn't work

**Solutions**:
1. Check agent is configured with `"mode": "subagent"` in opencode.json
2. Verify agent name matches exactly (lowercase, hyphens)
3. Try explicit invocation: `@codebase-locator find files`
4. Check OpenCode console for errors

### Slow Performance

**Problem**: Agents take too long to respond

**Solutions**:
1. Be more specific about which files to analyze
2. Use @codebase-locator first to narrow scope
3. Exclude large directories in bash searches:
   ```bash
   grep -r "term" . --exclude-dir={node_modules,dist,build}
   ```
4. Reduce temperature (faster but less creative)

## Configuration Options

### Customize Agent Behavior

Edit `.opencode/opencode.json`:

**Change Temperature**:
```json
"researcher": {
  "temperature": 0.2  // 0.0 = deterministic, 1.0 = creative
}
```

**Adjust Tool Access**:
```json
"planner": {
  "tools": {
    "bash": true,
    "read": true,
    "write": false  // Disable writing
  }
}
```

**Modify Permissions**:
```json
"implementor": {
  "permission": {
    "edit": "allow",  // Auto-allow edits (no prompts)
    "bash": "ask"     // Prompt before bash commands
  }
}
```

### Customize Agent Prompts

Edit the `.md` files in `.opencode/agent/`:

**Example**: Modify researcher to be more verbose
```markdown
---
description: "..."
mode: primary
temperature: 0.1
---

# Research Architect

[Modify the prompt here]
[Add project-specific instructions]
[Update output format]
```

After editing, restart OpenCode for changes to take effect.

## Directory Setup

If using the `thoughts/` directory system (recommended):

```bash
# Create thoughts directory structure
mkdir -p thoughts/shared/{research,plans,tickets,prs,decisions}
mkdir -p thoughts/global
mkdir -p thoughts/$USER  # Personal notes directory
```

This enables:
- Researcher to save reports
- Planner to save plans
- thoughts-locator to find documents
- thoughts-analyzer to extract decisions

## Updates and Maintenance

### Updating Agents

1. Edit the `.md` files in `.opencode/agent/`
2. Restart OpenCode
3. Test changes with simple queries

### Adding New Agents

1. Create new `.md` file in `.opencode/agent/`:
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
   ```

2. Optionally add to `opencode.json` for explicit config

3. Restart OpenCode

### Version Control

**Recommended .gitignore additions**:
```
# Don't ignore the agent system
!.opencode/

# But ignore any local overrides if you create them
.opencode/local.json
.opencode/.env
```

**Commit the agent system**:
```bash
git add .opencode/
git commit -m "Add OpenCode agent system"
```

This allows your team to use the same agents.

## Next Steps

1. ✅ Read `README.md` for full documentation
2. ✅ Check `QUICK_REFERENCE.md` for common commands
3. ✅ Try the example workflows
4. ✅ Customize agents for your project
5. ✅ Share with your team

## Support

- **OpenCode Docs**: https://opencode.ai/docs/agents
- **GitHub**: https://github.com/sst/opencode
- **Discord**: https://opencode.ai/discord

## Success Criteria

You'll know the installation worked when:
- [x] Pressing `Tab` cycles through 3 primary agents
- [x] Typing `@` shows 6 subagent options
- [x] Researcher creates reports in `thoughts/shared/research/`
- [x] Planner creates plans in `thoughts/shared/plans/`
- [x] Implementor executes plans with checkpoints
- [x] All agents follow their specialized roles

**Happy coding with OpenCode!** 🚀
