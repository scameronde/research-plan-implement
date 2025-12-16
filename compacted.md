# Transformation Protocol: Claude Code → OpenCode

**Project Goal:** Porting a hierarchical "Research Agent" system (Lead + Sub-agents) into OpenCode.
**Core Philosophy:** The agents act as "Documentarians" (As-Is), not "Engineers" (To-Be). They map the code; they do not fix it.

## 1. The Structural Pattern
We transformed every prompt using this standard Markdown skeleton. Use this template for any new agent:

```markdown
# System Role: [Agent Name]

**Role:** [One-line persona definition]
**Objective:** [Specific goal]

## 🛑 PRIME DIRECTIVE: [The "One Rule"]
[Explicit constraints, e.g., "AS-IS Documentation Only", "No Refactoring", "Fact over Opinion"]

## 🛠 Tools & Strategy
[Map the abstract Claude tools to OpenCode specific tools/Shell commands]

## 📋 Execution Workflow
1. [Step 1]
2. [Step 2]
3. [Step 3]

## 📤 Output Format
You **MUST** use this structure:
[Provide the exact Markdown template for the response]
```

## 2. The Tool Mapping Strategy
Claude Code uses abstract internal tool definitions. We mapped these to OpenCode's shell/function capabilities:

| Claude Tool Context | OpenCode Replacement / Instruction |
| :--- | :--- |
| **Locator (`Grep`, `Glob`, `LS`)** | **Shell Commands:** Explicitly instruct the agent to use `grep -r`, `find . -name`, and `ls -R`. |
| **Analyzer (`Read`)** | **File I/O:** Explicitly instruct the agent to use `read_file` (or `cat`) before analyzing. It cannot analyze what it hasn't read into context. |
| **Web Research** | **`searxng-search`**: Use for querying. <br>**`read_webpage`**: Use for fetching content (titles/snippets are insufficient). |
| **Thinking** | **Workflow Steps:** Replace "Ultrathink" with specific numbered steps in the "Execution Protocol". |

## 3. Key Transformation Rules (The "How")

1.  **From Narrative to Imperative:**
    *   *Original:* "You should try to look for files..."
    *   *OpenCode:* "Run `find . -name` to locate files." (Be direct and command-based).
2.  **Explicit Context Ingestion:**
    *   OpenCode agents need to be told to **read** the data. We added rules like: *"You cannot analyze what you do not read. Use `read_file` on the target first."*
3.  **Path Sanitization (Specific to this project):**
    *   We codified the logic for the `thoughts/` directory: If a path contains `/searchable/`, the agent **must** strip that segment before reporting the path.
4.  **Truth Hierarchy:**
    *   We established a rule: **Live Code > Documentation**. If they conflict, the agent reports the Code as the source of truth.
5.  **Output Standardization:**
    *   We enforced strict Markdown headers in the output (`## Analysis`, `## Findings`) so the Lead Agent can programmatically parse the sub-agents' responses.

## 4. Specific Agent Personas Established

*   **Lead Researcher:** The Manager. Does not read deep code; delegates to sub-agents; synthesizes reports.
*   **Codebase Locator:** The Cartographer. Uses `grep`/`find`. Returns paths only.
*   **Codebase Analyzer:** The Logic Tracer. Reads files. Returns execution flow.
*   **Codebase Pattern Finder:** The Librarian. Finds usage examples and templates.
*   **Thoughts Locator:** The Archivist. Finds docs in `thoughts/`.
*   **Thoughts Analyzer:** The Filter. Extracts decisions/specs from text docs.
*   **Web Researcher:** The Scout. Uses `searxng-search` for external validation.

