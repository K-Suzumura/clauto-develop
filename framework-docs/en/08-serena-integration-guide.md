# Serena MCP Integration Guide

Serena is an MCP server that provides semantic code understanding and editing capabilities using LSP (Language Server Protocol). Combined with this framework's subagents, it enables more precise code operations.

---

## Table of Contents

1. [What is Serena](#1-what-is-serena)
2. [Installation](#2-installation)
3. [Configuration Options](#3-configuration-options)
4. [Available Tools](#4-available-tools)
5. [Integration with Subagents](#5-integration-with-subagents)
6. [Usage Examples](#6-usage-examples)
7. [Troubleshooting](#7-troubleshooting)
8. [Best Practices](#8-best-practices)

---

## 1. What is Serena

### 1.1 Overview

Serena is a toolkit for AI coding agents with the following features:

| Feature | Description |
|---------|-------------|
| **Semantic Understanding** | Operations that understand code structure, not just text search |
| **LSP Integration** | Support for 30+ programming languages |
| **IDE-level Features** | Go to definition, find references, refactoring, etc. |
| **MCP Compatible** | Seamless integration with Claude Code |

### 1.2 Why Serena is Needed

Differences from regular file search (Glob, Grep):

```
┌─────────────────────────────────────────────────────────────┐
│                    Regular File Search                       │
├─────────────────────────────────────────────────────────────┤
│ Grep "function getUser"                                      │
│   → Text match search                                        │
│   → All different functions with same name found             │
│   → Comments containing the text also found                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    Serena Semantic Search                    │
├─────────────────────────────────────────────────────────────┤
│ find_symbol "getUser"                                        │
│   → Searches only functions recognized as symbols            │
│   → Also gets type information, parameter information        │
│   → Can understand call hierarchy, reference relationships   │
└─────────────────────────────────────────────────────────────┘
```

### 1.3 Supported Languages

| Category | Languages |
|----------|-----------|
| Scripts | Python, TypeScript, JavaScript, Ruby |
| Systems | Rust, Go, C, C++ |
| JVM | Java, Kotlin, Scala, Groovy |
| Others | C#, PHP, Swift, Lua, etc. |

---

## 2. Installation

### 2.1 Prerequisites

- **uv package manager**: Python package management tool
- **Claude Code**: Acts as MCP client

```bash
# Install uv (if not installed)
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 2.2 Adding to Project

**Method 1: Add via Claude Code command (Recommended)**

```bash
cd your-project

# Add locally to project
claude mcp add serena -- uvx --from git+https://github.com/oraios/serena serena start-mcp-server --context claude-code --project-from-cwd
```

**Method 2: Create .mcp.json directly**

Create `.mcp.json` in project root:

```json
{
  "mcpServers": {
    "serena": {
      "type": "stdio",
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/oraios/serena",
        "serena",
        "start-mcp-server",
        "--context",
        "claude-code",
        "--project-from-cwd"
      ],
      "env": {
        "ENABLE_TOOL_SEARCH": "true"
      }
    }
  }
}
```

**Method 3: Global Installation**

To use Serena in all projects:

```bash
claude mcp add --scope user serena -- uvx --from git+https://github.com/oraios/serena serena start-mcp-server --context=claude-code --project-from-cwd
```

### 2.3 Verify Operation

Start Claude Code and verify Serena is recognized:

```bash
claude

# In Claude Code
"Use serena to display the symbol list for this project"
```

---

## 3. Configuration Options

### 3.1 Context Settings

Specify operating mode with `--context` option:

| Context | Description |
|---------|-------------|
| `claude-code` | Optimized for Claude Code. Disables unnecessary tools |
| `ide-assistant` | For IDE extensions. Full features |
| `minimal` | Minimal configuration |

### 3.2 Project Settings

| Option | Description |
|--------|-------------|
| `--project <path>` | Explicitly specify project directory |
| `--project-from-cwd` | Auto-detect current directory as project |

### 3.3 Environment Variables

| Variable | Description | Recommended |
|----------|-------------|-------------|
| `ENABLE_TOOL_SEARCH` | On-demand tool loading (token saving) | `true` |

### 3.4 Project-Specific Settings

Create `.serena/project.yml` for detailed settings:

```yaml
# .serena/project.yml
name: my-project
language_servers:
  - typescript-language-server
  - pyright
exclude_patterns:
  - "**/node_modules/**"
  - "**/dist/**"
  - "**/.git/**"
```

---

## 4. Available Tools

### 4.1 Symbol Search

| Tool | Description | Example Use |
|------|-------------|-------------|
| `get_symbols` | Get list of modules/classes/functions | "Show symbol list for this file" |
| `find_symbol` | Search for specific symbol | "Find UserService class" |
| `semantic_search` | Search for semantically related code | "Find code related to authentication" |

### 4.2 Navigation

| Tool | Description | Example Use |
|------|-------------|-------------|
| `go_to_definition` | Jump to symbol definition | "Show this function's definition" |
| `find_references` | Search symbol usage locations | "Where is this function used?" |
| `get_call_hierarchy` | Get call hierarchy | "Show callers of this function" |

### 4.3 Information Retrieval

| Tool | Description | Example Use |
|------|-------------|-------------|
| `get_hover_info` | Display type info, documentation | "What's this variable's type?" |
| `get_diagnostics` | Get static analysis errors/warnings | "Check problems in this file" |

### 4.4 Editing (code-builder only)

| Tool | Description | Example Use |
|------|-------------|-------------|
| `apply_edit` | Semantic editing (rename, etc.) | "Change this function name" |
| `insert_after_symbol` | Insert code after symbol | "Add method to this class" |

---

## 5. Integration with Subagents

### 5.1 Serena Usage by Agent

| Agent | Serena Use | Main Purpose |
|-------|------------|--------------|
| **code-guide** | ✅ Needed (read-only) | Improved navigation accuracy with symbol search, definition jump |
| **code-builder** | ✅ Needed (full features) | Semantic editing, impact scope identification |
| **code-reviewer** | ✅ Needed (read-only) | Reference tracking, diagnostics retrieval |
| **code-debugger** | ✅ Needed (read-only) | Call stack tracking, cause tracing |
| **general-purpose** | ⚠️ Optional | Use as needed |
| **Others** | ❌ Not needed | Basic tools sufficient |

### 5.2 Integration Flow

```
┌──────────────────────────────────────────────────────────────┐
│                    Serena Usage in Development Flow           │
└──────────────────────────────────────────────────────────────┘

[Requirements Analysis] req-analyzer
    │ ※ Serena not needed (spec-level work)
    ▼
[Specification Planning] spec-planner
    │ ※ Serena not needed (spec-level work)
    ▼
[Design] backend-designer / frontend-designer
    │ ※ Serena not needed (design-level work)
    ▼
[Implementation] code-builder ← Full Serena usage
    │   - get_symbols: Understand existing structure
    │   - find_references: Check impact scope
    │   - apply_edit: Semantic refactoring
    │   - get_diagnostics: Check errors
    ▼
[Review] code-reviewer ← Serena read-only usage
    │   - get_call_hierarchy: Check call relationships
    │   - find_references: Check change impact scope
    │   - get_diagnostics: Check static analysis results
    ▼
[Debug] code-debugger ← Serena read-only usage
        - go_to_definition: Trace cause
        - get_call_hierarchy: Call stack analysis
        - get_hover_info: Check type info
```

---

## 6. Usage Examples

### 6.1 Code Understanding (code-guide)

```
"Tell me where authentication is implemented"

→ code-guide uses Serena:
  1. semantic_search finds code related to "authentication"
  2. get_symbols lists related classes/functions
  3. go_to_definition checks each symbol's definition
```

### 6.2 Refactoring (code-builder)

```
"Change the getUser method in UserService class to getUserById"

→ code-builder uses Serena:
  1. find_symbol identifies target method
  2. find_references checks all usage locations
  3. apply_edit renames at symbol level
  4. get_diagnostics checks for errors after change
```

### 6.3 Bug Investigation (code-debugger)

```
"Investigate the cause of this NullPointerException"

→ code-debugger uses Serena:
  1. go_to_definition traces from error location to cause
  2. get_call_hierarchy checks callers
  3. get_hover_info checks variable type info
  4. find_references investigates other related usages
```

### 6.4 Code Review (code-reviewer)

```
"Review the changes in this PR"

→ code-reviewer uses Serena:
  1. get_diagnostics checks static analysis issues
  2. find_references checks impact scope of changes
  3. get_call_hierarchy checks call relationship changes
```

---

## 7. Troubleshooting

### 7.1 Serena Won't Start

**Symptom**: Serena tools not recognized in Claude Code

**Causes and Solutions**:

| Cause | Solution |
|-------|----------|
| uv not installed | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| .mcp.json syntax error | Check JSON syntax |
| Network issues | Check GitHub access |

```bash
# Test starting Serena manually
uvx --from git+https://github.com/oraios/serena serena start-mcp-server --help
```

### 7.2 Language Server Not Working

**Symptom**: Symbol search not working for specific language

**Solutions**:

1. Check if language server for target language is installed
2. Explicitly specify language server in `.serena/project.yml`

```yaml
# .serena/project.yml
language_servers:
  - typescript-language-server  # TypeScript/JavaScript
  - pyright                     # Python
  - rust-analyzer               # Rust
  - gopls                       # Go
```

### 7.3 Performance is Slow

**Symptom**: Serena responses are slow

**Solutions**:

1. Set `ENABLE_TOOL_SEARCH=true` (token saving mode)
2. Exclude unnecessary directories

```yaml
# .serena/project.yml
exclude_patterns:
  - "**/node_modules/**"
  - "**/dist/**"
  - "**/build/**"
  - "**/.git/**"
```

---

## 8. Best Practices

### 8.1 Decision Criteria

| Situation | Recommended Tool |
|-----------|------------------|
| Find file by name | Glob (basic tool) |
| Find code by keyword | Grep (basic tool) |
| Find function/class definition | Serena `find_symbol` |
| Find function usage locations | Serena `find_references` |
| Check variable type | Serena `get_hover_info` |
| Rename refactoring | Serena `apply_edit` |

### 8.2 Effective Usage Patterns

**Large Codebase Investigation**:

```
1. semantic_search to identify rough scope
2. get_symbols to understand structure
3. go_to_definition to check details
4. find_references to cover related locations
```

**Safe Refactoring**:

```
1. find_references to pre-check impact scope
2. get_diagnostics to check current errors
3. apply_edit for semantic editing
4. get_diagnostics to check for new errors
```

### 8.3 When Not to Use Serena

| Situation | Reason |
|-----------|--------|
| Simple text search | Grep is faster |
| Config file editing | Basic tools sufficient |
| New file creation | Use Write tool |
| Small projects | Overhead is large |

---

## Reference Links

- [Serena Official Repository](https://github.com/oraios/serena)
- [Serena Documentation](https://oraios.github.io/serena/)
- [Subagent Guide](./05-claude-subagent-guide.md)
- [Command/Skill/Agent Usage Guide](./07-command-skill-agent-guide.md)

---

*This guide is part of the clauto-develop framework.*
