# Claude Code Skills Guide

This guide explains knowledge encapsulation and workflow automation using Claude Code's Skills feature.

---

## Table of Contents

1. [What are Skills](#1-what-are-skills)
2. [Installation](#2-installation)
3. [How Skills Work](#3-how-skills-work)
4. [Skills Placement](#4-skills-placement)
5. [SKILL.md Structure](#5-skillmd-structure)
6. [Creating Skills](#6-creating-skills)
7. [Practical Skills Examples](#7-practical-skills-examples)
8. [clauto-develop Custom Skills](#8-clauto-develop-custom-skills)
9. [Differences from Slash Commands and Subagents](#9-differences-from-slash-commands-and-subagents)
10. [Best Practices](#10-best-practices)
11. [Troubleshooting](#11-troubleshooting)

---

## 1. What are Skills

### 1.1 Overview

Skills are modules that bundle know-how and procedures for specific tasks. They function as "knowledge packs" that Claude autonomously judges and applies as needed.

### 1.2 Characteristics of Skills

| Feature | Description |
|---------|-------------|
| **Auto-application** | Claude analyzes user requests and automatically selects/applies relevant Skills |
| **Progressive disclosure** | Loads only necessary information incrementally, using context efficiently |
| **Modularization** | Manages instructions, scripts, and resources at folder level |
| **Team sharing** | Version control with Git, shareable across the entire team |

### 1.3 Benefits of Skills

- **Reduced token consumption**: Up to 98% token reduction possible by loading into context only when needed (case study)
- **Quality consistency**: Standardize team-specific rules and procedures
- **Reusability**: Use once-created Skills across multiple projects
- **Automation**: Claude applies appropriate Skills without manual instructions

---

## 2. Installation

### 2.1 Prerequisites

- Claude Code is installed
- clauto-develop repository is cloned

### 2.2 Global Installation

To use custom skills in all projects, run the following commands:

```bash
# Run from the clauto-develop repository root directory
mkdir -p ~/.claude/skills
cp -r global-skills/* ~/.claude/skills/
```

### 2.3 Verify Installation

Verify that the following directory structure is created:

```
~/.claude/skills/
├── spec-reviewer/
├── architecture-reviewer/
├── coding-standards/
├── test-author/
├── debug-triage/
├── pr-reviewer/
├── security-baseline/
├── dependency-change-reviewer/
└── release-notes-writer/
```

### 2.4 Uninstall

```bash
rm -rf ~/.claude/skills/*
```

---

## 3. How Skills Work

### 3.1 Progressive Disclosure

Skills are designed with a 3-layer structure, loading information incrementally as needed.

```
Layer 1: Metadata (name, description)
  ↓ Incorporated into system prompt at startup
  ↓ Claude judges skill usage

Layer 2: SKILL.md body
  ↓ Loaded if determined to be relevant

Layer 3: Supplementary files (scripts, templates, etc.)
  ↓ Loaded only in specific scenarios
```

### 2.2 Skill Discovery and Invocation

1. **At startup**: Claude Code loads `name` and `description` of available Skills
2. **Analysis**: Matches user request with Skill descriptions
3. **Confirmation**: If a matching Skill exists, asks "May I use the ○○ Skill?"
4. **Loading**: After approval, loads `SKILL.md` body into context
5. **Execution**: Executes processing according to instructions

### 2.3 Internal Operation of Skill Tool

```
User Request
    ↓
Claude invokes Skill tool
    ↓
System loads SKILL.md
    ↓
Instructions injected into context
    ↓
Claude executes according to instructions
```

---

## 4. Skills Placement

### 4.1 Placement Directories

| Location | Scope | Purpose |
|----------|-------|---------|
| `.claude/skills/` | Project specific | Project-exclusive Skills |
| `~/.claude/skills/` | All projects | Personal general-purpose Skills |
| Plugin provided | Via plugin | Third-party Skills |

### 4.2 Directory Structure

```
.claude/skills/
├── code-review/
│   ├── SKILL.md           # Main Skill file
│   ├── scripts/
│   │   └── analyze.py     # Helper script
│   └── resources/
│       └── checklist.md   # Checklist
├── documentation/
│   ├── SKILL.md
│   └── templates/
│       └── api-doc.md
└── testing/
    └── SKILL.md
```

---

## 5. SKILL.md Structure

### 5.1 Basic Structure

```markdown
---
name: Skill name
description: Skill description (when to use)
---

# Skill Title

Detailed instructions for Claude here
```

### 4.2 Frontmatter (Metadata)

#### Required Fields

| Field | Description | Limit |
|-------|-------------|-------|
| `name` | Skill name | 64 characters or less |
| `description` | Skill description and usage conditions | 200 characters or less |

#### Optional Fields

| Field | Description | Example |
|-------|-------------|---------|
| `allowed-tools` | Allowed tools | `Read, Grep, Glob` |
| `dependencies` | Required packages | `python>=3.8, pandas>=1.5.0` |

### 4.3 Importance of description

`description` is the most important element for Claude to judge whether to invoke a Skill.

**Good example:**
```yaml
description: Conduct code review for TypeScript projects. Check security, performance, and coding standards. Use during PR review.
```

**Bad example:**
```yaml
description: For code review
```

### 4.4 Frontmatter Notes

- `---` must start from line 1 (no empty lines)
- Use spaces for indentation (no tabs)
- Filename is `SKILL.md` (case-sensitive)

---

## 6. Creating Skills

### 5.1 Minimal Configuration

```
my-skill/
└── SKILL.md
```

```markdown
---
name: Code Quality Check
description: Check TypeScript code quality. Verify lint, type errors, and best practices.
---

# Code Quality Check

Please check code from the following perspectives:

1. **Type safety**: Minimize use of any type
2. **Naming conventions**: Proper use of camelCase, PascalCase
3. **Error handling**: Proper use of try-catch
4. **Comments**: Explanatory comments for complex logic
```

### 5.2 Configuration with Scripts

```
advanced-skill/
├── SKILL.md
├── scripts/
│   └── analyze.sh
└── resources/
    └── rules.json
```

```markdown
---
name: Dependency Analysis
description: Analyze project dependencies and detect security risks. Use when npm audit execution is needed.
allowed-tools: Bash(npm:*)
---

# Dependency Analysis

## Procedure

1. Execute `scripts/analyze.sh` to analyze dependencies
2. Evaluate based on rules in `resources/rules.json`
3. Propose fixes if issues found

## Script Execution

\`\`\`bash
./scripts/analyze.sh
\`\`\`
```

### 5.3 File Reference

You can reference other files within SKILL.md.

```markdown
See the following for detailed checklist:

@resources/checklist.md

When using templates:

@templates/component.tsx
```

---

## 7. Practical Skills Examples

### 6.1 Code Review Skill

`.claude/skills/code-review/SKILL.md`:

```markdown
---
name: Code Review
description: Conduct Pull Request code review. Check security, performance, readability.
allowed-tools: Read, Grep, Glob
---

# Code Review Skill

## Review Perspectives

### Security
- SQL injection vulnerabilities
- XSS vulnerabilities
- Authentication/authorization issues
- Hardcoded sensitive information

### Performance
- N+1 problems
- Unnecessary loops
- Memory leaks
- Inefficient algorithms

### Code Quality
- Clarity of naming
- Single responsibility of functions
- Appropriateness of comments
- Test coverage

## Output Format

| Severity | File:Line | Issue | Fix Suggestion |
|----------|-----------|-------|----------------|
| Critical/Major/Minor | path:line | Description | Proposal |
```

### 6.2 Documentation Generation Skill

`.claude/skills/documentation/SKILL.md`:

```markdown
---
name: API Documentation Generation
description: Auto-generate API documentation from TypeScript/JavaScript code. Parse JSDoc comments.
---

# API Documentation Generation Skill

## Targets

- Functions, classes, interfaces
- Exported type definitions
- REST API endpoints

## Output Format

### Function Documentation

\`\`\`markdown
## Function Name

**Description**: Purpose of function

**Parameters**:
| Name | Type | Required | Description |
|------|------|----------|-------------|

**Return Value**: Type and description

**Example**:
\`\`\`typescript
// Usage example
\`\`\`
\`\`\`

## Language

- Generate documentation in English
- Maintain code examples as-is
```

### 6.3 Test Generation Skill

`.claude/skills/testing/SKILL.md`:

```markdown
---
name: Unit Test Generation
description: Auto-generate unit tests for TypeScript functions. Create test cases in Jest/Vitest format.
allowed-tools: Read, Write
---

# Unit Test Generation Skill

## Test Structure

\`\`\`typescript
describe('Function Name', () => {
  describe('Success cases', () => {
    it('Description of expected behavior', () => {
      // Arrange
      // Act
      // Assert
    });
  });

  describe('Error cases', () => {
    it('Description of error case', () => {
      // Test
    });
  });
});
\`\`\`

## Coverage Goals

- Success cases: Major use cases
- Error cases: Error handling
- Boundary values: Limit value testing
- null/undefined handling

## Naming Conventions

- describe: Name of target
- it: Describe behavior in action form
```

### 6.4 Brand Guidelines Skill

`.claude/skills/brand-guidelines/SKILL.md`:

```markdown
---
name: Brand Guidelines
description: Apply internal brand guidelines to documents and presentations. Use when creating UI text and messages.
---

# Brand Guidelines Skill

## Tone & Voice

- **Friendly**: Non-stuffy expressions
- **Clear**: Avoid jargon, be understandable
- **Professional**: Trustworthy expressions

## Terminology Standardization

| Use | Don't Use |
|-----|-----------|
| user | customer, client |
| log in | sign in |
| settings | preferences, options |

## UI Text

- Buttons: Start with verb (e.g., Save, Submit)
- Errors: State problem and solution clearly
- Confirmation: Positive expression first
```

---

## 8. clauto-develop Custom Skills

12 custom Skills provided by this project. Place in `~/.claude/skills/` for use across all projects.

### 7.1 Skills List

| Category | Skill Name | Purpose |
|----------|------------|---------|
| **Spec** | spec-writer | Convert requirements into spec document template |
| **Spec** | spec-reviewer | Spec quality gate (detect ambiguity, gaps, contradictions) |
| **Plan** | plan-generator | Create task breakdown from spec at standard granularity |
| **Design** | architecture-reviewer | Review responsibility separation and layer design |
| **Implement** | coding-standards | Apply coding standards |
| **Test** | test-author | Generate test perspectives and code from spec |
| **Debug** | debug-triage | Triage problem root cause |
| **Review** | pr-reviewer | Standardize PR review perspectives |
| **PR** | pr-writer | Unify PR body format |
| **Release** | release-notes-writer | Standardize release notes format |
| **Security** | security-baseline | Standardize security review |
| **Dependencies** | dependency-change-reviewer | Review dependency package changes |

### 7.2 Skills Map by Development Phase

```
Requirements ─────────────────────────────────────────────────→ Release

[Analysis]        [Design]         [Implement]      [Review]       [Release]
    │               │                │               │               │
    ▼               ▼                ▼               ▼               ▼
spec-writer    architecture-    coding-       pr-reviewer    release-notes-
    │          reviewer         standards          │          writer
    ▼               │                │               │
spec-reviewer      │           test-author          │
    │               │                │               │
    ▼               │                ▼               │
plan-generator     │          debug-triage          │
                   │                                 │
                   └─────────────────────────────────┘
                            security-baseline
                       dependency-change-reviewer
```

### 7.3 Each Skill Details

#### spec-writer (Specification Writing)

| Item | Content |
|------|---------|
| **Purpose** | Convert requirements into `docs/spec.md` template structure |
| **Use Case** | When requirements are verbal/bullet points, when first creating Spec |
| **Main Functions** | Cover purpose/functional requirements/input/output/errors/prohibitions/directory structure, forbid ambiguous words, require success/failure examples |

#### spec-reviewer (Specification Review)

| Item | Content |
|------|---------|
| **Purpose** | Spec quality gate (detect ambiguity, gaps, contradictions) |
| **Use Case** | Before implementation, after spec changes |
| **Main Functions** | Ambiguous word check, priority confirmation, validation check, example verification, non-functional requirements (performance/security) check |

#### plan-generator (Implementation Plan Generation)

| Item | Content |
|------|---------|
| **Purpose** | Create Spec → Plan task breakdown at "consistent granularity" |
| **Use Case** | Before implementation, work estimation/assignment |
| **Main Functions** | Dependency organization, incremental PR strategy, risk analysis, verification steps, rollback considerations |

#### architecture-reviewer (Architecture Review)

| Item | Content |
|------|---------|
| **Purpose** | Standardize responsibility separation and directory/layer design review |
| **Use Case** | Large feature additions, initial construction, before refactoring |
| **Main Functions** | Boundary checks (domain/app/infra etc.), circular dependency detection, I/O layer separation confirmation, future extension points |

#### coding-standards (Coding Standards)

| Item | Content |
|------|---------|
| **Purpose** | Apply team's "coding standards" on demand (including review) |
| **Use Case** | All implementation and review |
| **Main Functions** | Naming conventions, exception design, logging policy, comment standards, function design, dependency injection, type/null policy |

> **Note**: Since putting everything in CLAUDE.md is heavy, applying through Skills only when needed is effective.

#### test-author (Test Writing)

| Item | Content |
|------|---------|
| **Purpose** | Standardize test perspective and test code generation from Spec |
| **Use Case** | New features, bug fixes, regression test additions |
| **Main Functions** | Success/error/boundary value perspective organization, mock policy, test naming conventions, test data creation rules |

#### debug-triage (Debug Triage)

| Item | Content |
|------|---------|
| **Purpose** | Standardize root cause isolation from logs/stack traces/reproduction steps |
| **Use Case** | Unknown bugs, CI failures, performance degradation |
| **Main Functions** | Reproduction minimization, hypothesis→verification command proposals, observation point identification, temporary workarounds |

#### pr-reviewer (PR Review)

| Item | Content |
|------|---------|
| **Purpose** | Fix PR review perspectives (spec deviation, test gaps, safety) |
| **Use Case** | After PR creation, review requests |
| **Main Functions** | Spec compliance confirmation, impact scope, backward compatibility, logs/metrics, security, documentation updates |

#### pr-writer (PR Writing)

| Item | Content |
|------|---------|
| **Purpose** | Unify PR body format (changes/rationale/tests/impact/procedures) |
| **Use Case** | Before PR creation |
| **Main Functions** | Fixed template, checkboxes, review perspectives, deploy/rollback procedures, screenshot requirements |

#### release-notes-writer (Release Notes Writing)

| Item | Content |
|------|---------|
| **Purpose** | Standardize release notes/change notifications (internal/external) |
| **Use Case** | At release, customer notifications |
| **Main Functions** | User impact, breaking changes, migration procedures, known limitations, monitoring points |

#### security-baseline (Security Baseline)

| Item | Content |
|------|---------|
| **Purpose** | Minimum security review without gaps every time |
| **Use Case** | External I/O, authentication/authorization, file/network, dependency additions |
| **Main Functions** | Input validation, permission checks, secrets management, PII handling, SSRF/SQLi/XSS countermeasures, log safety |

#### dependency-change-reviewer (Dependency Package Change Review)

| Item | Content |
|------|---------|
| **Purpose** | Fix verification perspectives when adding/updating dependencies |
| **Use Case** | New library introduction, version updates |
| **Main Functions** | License confirmation, vulnerability check, size/build impact, alternative candidate review, lockfile policy |

### 7.4 Skills Placement

```
~/.claude/skills/
├── spec-writer/
│   └── SKILL.md
├── spec-reviewer/
│   └── SKILL.md
├── plan-generator/
│   └── SKILL.md
├── architecture-reviewer/
│   └── SKILL.md
├── coding-standards/
│   └── SKILL.md
├── test-author/
│   └── SKILL.md
├── debug-triage/
│   └── SKILL.md
├── pr-reviewer/
│   └── SKILL.md
├── pr-writer/
│   └── SKILL.md
├── release-notes-writer/
│   └── SKILL.md
├── security-baseline/
│   └── SKILL.md
└── dependency-change-reviewer/
    └── SKILL.md
```

### 7.5 Usage Examples

```
# Spec creation
User: "Create a spec for user authentication feature"
→ spec-writer Skill auto-applied

# Spec review
User: "Review this spec"
→ spec-reviewer Skill auto-applied

# Implementation plan
User: "Create implementation plan from this spec"
→ plan-generator Skill auto-applied

# PR creation
User: "Create a PR"
→ pr-writer Skill auto-applied

# Security review
User: "Check the security of this code"
→ security-baseline Skill auto-applied
```

---

## 9. Differences from Slash Commands and Subagents

### 9.1 Comparison Table

| Item | Skills | Slash Commands | Subagents |
|------|--------|----------------|-----------|
| **Invocation** | Auto (Claude judgment) | Manual (`/command`) | Auto/Manual |
| **Context** | Within main conversation | Within main conversation | Independent context |
| **Purpose** | Apply knowledge/rules | Routine workflows | Research/analysis tasks |
| **Arguments** | None | Yes (`$ARGUMENTS`) | Task request text |
| **Location** | `.claude/skills/` | `.claude/commands/` | `.claude/agents/` |

### 9.2 Usage Guidelines

```
┌─────────────────────────────────────────────────────────────┐
│ "Rules and guidelines that should always be applied"        │
│  → CLAUDE.md                                                │
├─────────────────────────────────────────────────────────────┤
│ "Knowledge Claude should auto-apply for specific tasks"     │
│  → Skills                                                   │
├─────────────────────────────────────────────────────────────┤
│ "Explicitly invoked routine workflows"                      │
│  → Slash Commands                                           │
├─────────────────────────────────────────────────────────────┤
│ "Research/analysis in independent context"                  │
│  → Subagents                                                │
└─────────────────────────────────────────────────────────────┘
```

### 8.3 Combining Skills with Subagents

Skills and subagents are designed for different purposes, and combining them enables more effective development.

| Aspect | Skills | Subagents |
|--------|--------|-----------|
| **Invocation** | Auto (Claude judgment) | Auto/Manual |
| **Context** | Within main conversation | Independent context |
| **Purpose** | Apply knowledge/rules | Research/analysis tasks |

**Combination Pattern:**

Subagents determine "**what to do**" while Skills provide "**the criteria for how to do it**".

```
[User] Review this PR as code-reviewer

→ code-reviewer subagent launches
  → pr-reviewer skill auto-applied (review criteria)
  → security-baseline skill auto-applied (security perspective)
  → coding-standards skill auto-applied (coding standards)
```

**Another Example:**

```
[User] Implement authentication feature as code-builder

→ code-builder subagent launches
  → coding-standards skill auto-applied (coding standards)
  → security-baseline skill auto-applied (authentication security)
  → test-author skill auto-applied (test creation criteria)
```

This way, subagents act in specific roles (reviewer, builder, etc.), and the skills necessary for that role are automatically applied, resulting in consistent quality deliverables.

### 8.4 Boris Cherny's View

> "Currently I still use subagents more than Skills"

Skills is still a new feature, and stability improvements in invocation logic are expected. Currently:

- **When precise control needed**: Subagents recommended
- **Want efficiency through auto-application**: Try Skills experimentally

---

## 10. Best Practices

### 10.1 Design Principles

1. **Focus narrowly**
   - 1 Skill = 1 purpose
   - Split large Skills

2. **Clear description**
   - Specifically describe when to use
   - Improve Claude's judgment accuracy

3. **Start simple**
   - First create with Markdown instructions only
   - Add scripts as needed

4. **Include examples**
   - Document input and output examples
   - Clarify expected results

### 9.2 Incremental Development

```
1. Evaluate
   └─ Identify gaps in actual usage

2. Design
   └─ Create basic SKILL.md structure

3. Test
   └─ Verify behavior with various inputs

4. Improve
   └─ Adjust necessary context

5. Split
   └─ Separate into helper files if complex
```

### 9.3 Security

**Warning**: Skills provide new capabilities through instructions and code, but malicious Skills can introduce vulnerabilities.

- Install only from trusted sources
- Review third-party Skill contents before use
- Minimize permissions with `allowed-tools`

### 9.4 Team Sharing

```bash
# Version control with Git
git add .claude/skills/
git commit -m "feat: Add code review Skill"

# Team members get via clone/pull
git pull origin main
```

---

## 11. Troubleshooting

### 11.1 Skill Not Loading

```bash
# Start in debug mode
claude --debug
```

**Checkpoints**:
- Filename is `SKILL.md` (check case)
- Frontmatter format is correct
- `---` starts from line 1
- Indentation uses spaces (not tabs)

### 10.2 Skill Not Activating

**Possible causes**:
- `description` doesn't match request
- More appropriate Skill takes priority

**Solutions**:
- Rewrite `description` more specifically
- Clarify trigger conditions

### 10.3 Not Working as Expected

**Solutions**:
- Clarify SKILL.md instructions
- Add input/output examples
- Break into step-by-step procedures

---

## References

- [Claude Code Official](https://claude.ai/code)
- [Anthropic Official Site](https://www.anthropic.com/)
- [Agent Skills Documentation](https://code.claude.com/docs/en/skills)
- [Agent Skills Design Philosophy](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [Awesome Claude Skills](https://github.com/travisvn/awesome-claude-skills)

---

*This guide is updated periodically. Skills is a new feature, so stability and functionality are expected to improve in future updates.*
