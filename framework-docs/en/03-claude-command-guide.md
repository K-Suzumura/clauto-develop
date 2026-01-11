# Claude Code Command Guide

This guide explains basic Claude Code commands and workflow automation through slash commands.

---

## Table of Contents

1. [CLI Basic Commands](#1-cli-basic-commands)
2. [Built-in Slash Commands](#2-built-in-slash-commands)
3. [Keyboard Shortcuts](#3-keyboard-shortcuts)
4. [Custom Slash Commands](#4-custom-slash-commands)
5. [Practical Command Examples](#5-practical-command-examples)
6. [Best Practices](#6-best-practices)

---

## 1. CLI Basic Commands

### 1.1 Installation and Startup

```bash
# Installation (Node.js 18 or higher required)
npm install -g @anthropic-ai/claude-code

# Set API key
export ANTHROPIC_API_KEY="your-api-key"

# Start in interactive mode
claude

# Display help
claude --help
```

### 1.2 Execution Modes

| Command | Description | Example |
|---------|-------------|---------|
| `claude` | Start in interactive mode | `claude` |
| `claude -p "query"` | Execute once and exit (Print mode) | `claude -p "Explain this file"` |
| `claude -c` | Continue previous conversation | `claude -c` |
| `claude -r "session-id"` | Resume specific session | `claude -r "abc123" "Continue execution"` |

### 1.3 Main CLI Options

```bash
# Specify model
claude --model opus

# Enable thinking mode (deeper reasoning)
claude --thinking

# Specify output format
claude -p "query" --output-format json

# Display conversation history
claude --history

# Check configuration
claude config list
```

### 1.4 Configuration File Hierarchy

Claude Code loads configuration in the following hierarchy:

```
~/.claude/settings.json          # User level (global)
.claude/settings.json            # Project level
.claude/settings.local.json      # Local (.gitignore recommended)
```

---

## 2. Built-in Slash Commands

Built-in commands available during interactive mode.

### 2.1 Session Management

| Command | Description |
|---------|-------------|
| `/clear` | Reset conversation history and context |
| `/compact [instructions]` | Summarize and compress conversation (optionally add instructions) |
| `/exit` | Exit Claude Code |

### 2.2 Information & Help

| Command | Description |
|---------|-------------|
| `/help` | Display list of available commands |
| `/cost` | Display current session cost and time |
| `/doctor` | Health check of installation status |

### 2.3 Settings & Integration

| Command | Description |
|---------|-------------|
| `/config` | Open settings panel |
| `/ide` | Manage IDE integration |
| `/mcp` | Access MCP (Model Context Protocol) features |
| `/permissions` | Manage tool permissions |

### 2.4 Usage Examples

```
# Reset long conversation to free memory
/clear

# Summarize conversation and continue
/compact Keep important decisions

# Check current cost
/cost

# Display help
/help
```

---

## 3. Keyboard Shortcuts

Shortcuts available during interactive mode.

### 3.1 Input Related

| Shortcut | Description |
|----------|-------------|
| `Enter` | Send message (single line) |
| `Shift + Enter` | Insert newline (multiline input) |
| `Ctrl + C` | Interrupt current process |
| `Ctrl + D` | End session |

### 3.2 Navigation

| Shortcut | Description |
|----------|-------------|
| `↑` / `↓` | Navigate history forward/backward |
| `Ctrl + L` | Clear screen |
| `Tab` | Complete command/file path |

### 3.3 Editing & Operations

| Shortcut | Description |
|----------|-------------|
| `Ctrl + A` | Move to beginning of line |
| `Ctrl + E` | Move to end of line |
| `Ctrl + U` | Clear line |
| `Ctrl + W` | Delete previous word |

---

## 4. Custom Slash Commands

Create custom commands to automate repetitive tasks.

### 4.1 Command Placement

| Location | Scope | Invocation |
|----------|-------|------------|
| `.claude/commands/` | Project specific | `/project:command-name` |
| `~/.claude/commands/` | All projects | `/user:command-name` |

### 4.2 Basic Structure

Commands are created as Markdown files. The filename becomes the command name.

```
.claude/commands/
├── commit.md          → /project:commit
├── pr.md              → /project:pr
├── test.md            → /project:test
└── review/
    └── security.md    → /project:review:security
```

### 4.3 Frontmatter (Metadata)

```yaml
---
description: Auto-generate commit message
allowed-tools: ["Bash(git:*)"]
model: haiku
argument-hint: <commit type>
---
```

| Option | Description |
|--------|-------------|
| `description` | Description displayed in `/help` |
| `allowed-tools` | Allowed tools (Bash commands, etc.) |
| `model` | Model to use (`haiku` / `sonnet` / `opus`) |
| `argument-hint` | Hint for arguments |

### 4.4 Using Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `$ARGUMENTS` | All arguments | `/commit fix typo` → `fix typo` |
| `$1`, `$2`, ... | Positional arguments | `/branch add-feature` → `$1 = add-feature` |

### 4.5 Executing Bash Commands

Use `!` prefix to execute Bash commands and inject results into the prompt.

```markdown
---
description: Check Git status and commit
---

Please review the following changes:

!git status
!git diff --staged

Generate an appropriate commit message for the above changes.
```

### 4.6 File Reference

Use `@` prefix to reference file contents.

```markdown
Please review the following files:

@src/components/Button.tsx
@src/styles/button.css
```

---

## 5. Practical Command Examples

### 5.1 Auto-generate Commit Message

`.claude/commands/commit.md`:

```markdown
---
description: Generate commit message from changes
allowed-tools: ["Bash(git:*)"]
model: haiku
---

Please analyze the following changes:

!git diff --staged

Generate a commit message in Conventional Commits format:
- feat: New feature
- fix: Bug fix
- docs: Documentation
- refactor: Refactoring
- test: Tests
- chore: Other

Write concisely.
```

### 5.2 Create Pull Request

`.claude/commands/pr.md`:

```markdown
---
description: Create PR
allowed-tools: ["Bash(git:*)", "Bash(gh:*)"]
model: sonnet
---

Please check the following information:

!git log origin/main..HEAD --oneline
!git diff origin/main..HEAD --stat

Create a Pull Request for these changes:
1. Title should concisely describe the changes
2. Body should include overview, purpose, and test method
3. Create using `gh pr create` command
```

### 5.3 Run Tests and Fix

`.claude/commands/test.md`:

```markdown
---
description: Run tests and fix if failed
allowed-tools: ["Bash(npm:*)", "Bash(npx:*)"]
model: sonnet
---

Please run tests:

!npm test

If there are failed tests:
1. Analyze error content
2. Identify cause
3. Implement fix
4. Run tests again to verify
```

### 5.4 Code Review

`.claude/commands/review.md`:

```markdown
---
description: Review changes
model: sonnet
argument-hint: <file path>
---

Please review the following files:

@$ARGUMENTS

Review perspectives:
- Code quality (readability, maintainability)
- Potential bugs
- Security risks
- Performance issues

Report issues and improvement suggestions.
```

### 5.5 Create Branch

`.claude/commands/branch.md`:

```markdown
---
description: Create feature branch
allowed-tools: ["Bash(git:*)"]
model: haiku
argument-hint: <feature description>
---

Please create a branch for the following feature:
$ARGUMENTS

Branch naming conventions:
- Feature addition: feat/description
- Bug fix: fix/description
- Refactoring: refactor/description

Name in English kebab-case and create with `git checkout -b`.
```

### 5.6 Fix Issue

`.claude/commands/fix-issue.md`:

```markdown
---
description: Fix GitHub Issue
allowed-tools: ["Bash(git:*)", "Bash(gh:*)"]
model: sonnet
argument-hint: <Issue number>
---

Please fix Issue #$1.

First, check the Issue content:
!gh issue view $1

Then:
1. Analyze the problem
2. Identify related code
3. Implement fix
4. Create tests
5. Commit

After fix is complete, create a PR to close the Issue.
```

---

## 6. Best Practices

### 6.1 Model Selection Strategy

| Task | Recommended Model | Reason |
|------|-------------------|--------|
| Lint/Format | `haiku` | Fast, low cost |
| Commit message generation | `haiku` | Simple task |
| Code review | `sonnet` | Complex analysis needed |
| Bug fix | `sonnet` | Deep reasoning needed |
| Architecture design | `opus` | Highest quality reasoning |

### 6.2 Minimize Permissions

```yaml
# Good example: Only necessary permissions
allowed-tools: ["Bash(git:*)"]

# Avoid: Excessive permissions
allowed-tools: ["Bash(*)"]
```

### 6.3 Utilize Shell Aliases

```bash
# Add to ~/.bashrc or ~/.zshrc

# Quick commit
alias cc="claude -p '/project:commit'"

# Run tests
alias ct="claude -p '/project:test'"

# Create PR
alias cpr="claude -p '/project:pr'"
```

### 6.4 Organize Commands

```
.claude/commands/
├── git/
│   ├── commit.md
│   ├── branch.md
│   └── pr.md
├── test/
│   ├── unit.md
│   └── e2e.md
└── review/
    ├── security.md
    └── performance.md
```

### 6.5 Context Management Between Tasks

```
# Clear context before switching tasks
/clear

# Summarize when conversation gets long
/compact Keep important decisions and incomplete tasks
```

---

## References

- [Claude Code Official](https://claude.ai/code)
- [Anthropic Official Site](https://www.anthropic.com/)
- [Claude Code Slash Commands Documentation](https://code.claude.com/docs/en/slash-commands)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

---

*This guide is updated periodically. Refer to official documentation for the latest information.*
