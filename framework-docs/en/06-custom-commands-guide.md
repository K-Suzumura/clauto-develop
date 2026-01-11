# Custom Commands Guide

A list and usage guide for custom commands (slash commands) used in the clauto-develop framework.

---

## Table of Contents

1. [Overview](#1-overview)
2. [Installation](#2-installation)
3. [Specification Phase](#3-specification-phase)
4. [Planning & Implementation Phase](#4-planning--implementation-phase)
5. [Quality Assurance Phase](#5-quality-assurance-phase)
6. [Git Operations](#6-git-operations)
7. [Integrated Workflows](#7-integrated-workflows)
8. [Maintenance & Operations](#8-maintenance--operations)
9. [Command Placement](#9-command-placement)

---

## 1. Overview

### 1.1 What are Custom Commands

Custom commands are slash commands that automate and standardize repetitive tasks. By placing Markdown files in `~/.claude/commands/`, they can be used across all projects.

### 1.2 Design Philosophy

| Principle | Description |
|-----------|-------------|
| **Language Agnostic** | Not dependent on specific programming languages or frameworks |
| **Process Standardization** | Same quality deliverables regardless of who executes |
| **Automation First** | Reduce manual work and prevent human errors |
| **Staged Execution** | Include confirmations at each phase to prevent accidents |

### 1.3 Development Flow and Command Mapping

```
Spec Creation → Plan Creation → Implementation → Testing → Commit → PR → Review Response
      │             │              │            │          │        │          │
/spec:init    /plan:make     /impl:run    /qa:full  /git:commit /git:pr  /fixup-from-pr-comments
/spec:review
```

---

## 2. Installation

### 2.1 Prerequisites

- Claude Code is installed
- clauto-develop repository is cloned

### 2.2 Global Installation

To use custom commands in all projects, run the following commands:

```bash
# Run from the clauto-develop repository root directory
mkdir -p ~/.claude/commands
cp global-commands/*.md ~/.claude/commands/
```

### 2.3 Verify Installation

Start Claude Code and verify that the following commands are recognized:

```
/spec:init
/git:commit
```

You can see the command list with `/help`.

### 2.4 Uninstall

```bash
rm ~/.claude/commands/*.md
```

---

## 3. Specification Phase

### 3.1 /spec:init - Specification Template Generation

| Item | Content |
|------|---------|
| **Purpose** | Generate `docs/spec.md` from template |
| **Automation** | Spec template generation, TODO comment insertion |
| **Versatility** | Usable on all projects as starting point for spec-driven development |

**Usage:**
```
/spec:init
```

**Generated Template:**
- Overview (Background, Purpose, Scope)
- Functional Requirements (Feature list, Detailed specifications)
- Non-functional Requirements (Performance, Security, etc.)
- Technical Architecture
- Data Design
- Constraints & Prerequisites

### 3.2 /spec:review - Specification Review

| Item | Content |
|------|---------|
| **Purpose** | Check specification quality against defined standards |
| **Automation** | Load `docs/spec.md` and output issues as bullet points |
| **Versatility** | Specification ambiguity is a common issue in all development |

**Usage:**
```
/spec:review
```

**Check Items:**
- Completeness (Remaining TODOs, required section fulfillment)
- Clarity (Ambiguous expressions, quantification)
- Consistency (Terminology unification, ID numbering)
- Implementability (Technical feasibility)
- Testability (Acceptance criteria)

---

## 4. Planning & Implementation Phase

### 4.1 /plan:make - Implementation Plan Creation

| Item | Content |
|------|---------|
| **Purpose** | Create implementation task list from specification |
| **Automation** | Task decomposition, implementation order and dependency clarification |
| **Versatility** | Prevents "jumping into implementation" regardless of project size |

**Usage:**
```
/plan:make
```

**Output:**
- Milestones
- Task list (ID, Category, Dependencies, Priority)
- Task details (Overview, Deliverables, Completion criteria)
- Dependency diagram

### 4.2 /impl:run - Task Implementation Execution

| Item | Content |
|------|---------|
| **Purpose** | Start implementation according to plan |
| **Automation** | Implement specified tasks, create/edit files |
| **Versatility** | Implementation phase exists in every project |

**Usage:**
```
/impl:run T-001
/impl:run T-001,T-002,T-003
```

**Execution Flow:**
1. Verify dependent task completion
2. Execute implementation
3. Verification (syntax errors, basic operation)
4. Update task status

---

## 5. Quality Assurance Phase

### 5.1 /qa:full - Full Test Execution

| Item | Content |
|------|---------|
| **Purpose** | Execute test design → execution → result organization in one go |
| **Automation** | Test case organization, test execution, failure reports |
| **Versatility** | Structurally prevents test omissions |

**Usage:**
```
/qa:full
```

**Output:**
- Test summary (Pass/Fail/Skip counts)
- Coverage information
- Failed test details and fix suggestions
- Specification coverage (Test existence per feature)

---

## 6. Git Operations

### 6.1 /git:branch - Branch Creation

| Item | Content |
|------|---------|
| **Purpose** | Create spec-based branches |
| **Automation** | Branch name suggestion, checkout |
| **Versatility** | Used whenever Git operations exist |

**Usage:**
```
/git:branch
/git:branch F-001
```

**Branch Naming Convention:**
- `feature/{issue-id}-{short-description}`
- `fix/{issue-id}-{short-description}`
- `refactor/{description}`

### 6.2 /git:commit - Standard-compliant Commit

| Item | Content |
|------|---------|
| **Purpose** | Create standard-compliant commits |
| **Automation** | Diff summary, commit message generation, commit |
| **Versatility** | Maintains consistent commit quality |

**Usage:**
```
/git:commit
```

**Commit Message Format:**
```
<type>: <subject>

<body>

<footer>
```

### 6.3 /git:pr - Pull Request Creation

| Item | Content |
|------|---------|
| **Purpose** | Standardize PR creation |
| **Automation** | Title/body generation, `gh pr create`, include review perspectives |
| **Versatility** | PR quality = Team productivity |

**Usage:**
```
/git:pr
```

**PR Body Contents:**
- Change summary
- Related Issue
- Changes and rationale
- Test procedures
- Review perspectives
- Checklist

---

## 7. Integrated Workflows

### 7.1 /gh:commit-push-pr - Integrated Version

| Item | Content |
|------|---------|
| **Purpose** | Consolidate daily tasks into one command |
| **Automation** | commit → push → PR |
| **Versatility** | Most frequently used "inner loop" |

**Usage:**
```
/gh:commit-push-pr
```

**Execution Flow:**
1. Commit changes
2. Push to remote
3. Create PR

### 7.2 /fixup-from-pr-comments - Review Response

| Item | Content |
|------|---------|
| **Purpose** | Automate review response |
| **Automation** | Get PR comments, fix, test, additional commit |
| **Versatility** | Review response occurs in every team |

**Usage:**
```
/fixup-from-pr-comments
/fixup-from-pr-comments 123
```

**Execution Flow:**
1. Get and analyze review comments
2. Create fix plan
3. User confirmation
4. Execute fixes
5. Test and commit

---

## 8. Maintenance & Operations

### 8.1 /refactor:cleanup - Code Cleanup

| Item | Content |
|------|---------|
| **Purpose** | Organize without changing behavior |
| **Automation** | Reduce redundant code, improve naming, organize structure |
| **Versatility** | Essential as post-implementation finishing step |

**Usage:**
```
/refactor:cleanup
/refactor:cleanup src/services/
```

**Refactoring Items:**
- Delete unused code
- Improve naming
- Split long functions
- Convert magic numbers to constants

### 8.2 /session:compact-smart - Smart Compact

| Item | Content |
|------|---------|
| **Purpose** | Prevent accidents in long sessions |
| **Automation** | Execute `/compact`, re-record important decisions and TODOs |
| **Versatility** | Essential when using Claude Code for extended periods |

**Usage:**
```
/session:compact-smart
```

**Preserved Information:**
- Project state (branch, uncommitted changes)
- Important decisions
- TODOs/Next actions
- Notes

### 8.3 /ultrathink - Deep Thinking Mode

| Item | Content |
|------|---------|
| **Purpose** | Think deeply and multifacetedly about complex problems |
| **Use Cases** | Architecture design, trade-off analysis, risk assessment |
| **Versatility** | Use when important technical decisions are needed |

**Usage:**
```
/ultrathink
```

**Thinking Process:**
1. Problem clarification
2. Multifaceted analysis (technical, business, operations, security)
3. Option enumeration and comparison
4. Trade-off evaluation
5. Risk analysis
6. Conclusion and recommendations

---

## 9. Command Placement

### 9.1 Global Commands

Commands used across all projects are placed in `~/.claude/commands/`.

```
~/.claude/commands/
├── spec-init.md
├── spec-review.md
├── plan-make.md
├── impl-run.md
├── qa-full.md
├── git-branch.md
├── git-commit.md
├── git-pr.md
├── gh:commit-push-pr.md
├── fixup-from-pr-comments.md
├── refactor-cleanup.md
├── session-compact-smart.md
└── ultrathink.md
```

### 9.2 Project-Specific Commands

Commands used only in specific projects are placed in `.claude/commands/`.

```
project/
└── .claude/
    └── commands/
        └── project-specific-command.md
```

### 9.3 Command File Format

```markdown
# /command-name - Command description

Detailed instructions for the command here.

## Execution Content
1. [Step 1]
2. [Step 2]

## Output Format
...
```

---

## Command Quick Reference

| Command | Phase | Purpose |
|---------|-------|---------|
| `/spec:init` | Specification | Generate spec template |
| `/spec:review` | Specification | Check spec quality |
| `/plan:make` | Planning | Create implementation task list |
| `/impl:run` | Implementation | Execute task implementation |
| `/qa:full` | Quality | Run full tests |
| `/git:branch` | Git | Create branch |
| `/git:commit` | Git | Standard-compliant commit |
| `/git:pr` | Git | Create PR |
| `/gh:commit-push-pr` | Git | Integrated commit→push→PR |
| `/fixup-from-pr-comments` | Git | Review response |
| `/refactor:cleanup` | Maintenance | Code cleanup |
| `/session:compact-smart` | Operations | Session organization |
| `/ultrathink` | Analysis | Deep thinking mode |

---

*This guide is part of the clauto-develop framework.*
