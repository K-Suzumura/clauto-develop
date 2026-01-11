# Command, Skill, and Agent Usage Guide

The clauto-develop framework provides three types of automation mechanisms. This guide explains each role and appropriate usage.

---

## Table of Contents

1. [Overview of the Three Mechanisms](#1-overview-of-the-three-mechanisms)
2. [Layer Structure and Role Division](#2-layer-structure-and-role-division)
3. [Usage by Development Phase](#3-usage-by-development-phase)
4. [Selection Flowchart](#4-selection-flowchart)
5. [Cross-Reference Map](#5-cross-reference-map)
6. [FAQ](#6-faq)

---

## 1. Overview of the Three Mechanisms

### 1.1 Custom Commands

| Item | Content |
|------|---------|
| **Role** | Workflow automation |
| **Invocation** | User explicitly invokes with `/command-name` |
| **Characteristics** | Auto-executes series of tasks, includes interactive confirmations |
| **Location** | `~/.claude/commands/` (global) or `.claude/commands/` (project) |

**Examples**: `/spec:init`, `/git:commit`, `/qa:full`

### 1.2 Custom Skills

| Item | Content |
|------|---------|
| **Role** | Quality standards, checklists, output templates |
| **Invocation** | Claude auto-applies based on context, or user explicitly requests |
| **Characteristics** | Defines judgment criteria and expected output format |
| **Location** | `~/.claude/skills/` (global) or `.claude/skills/` (project) |

**Examples**: `security-baseline`, `coding-standards`, `spec-reviewer`

### 1.3 Subagents

| Item | Content |
|------|---------|
| **Role** | Expert persona (with specific perspective, reference scope, responsibilities) |
| **Invocation** | Claude auto-selects for complex tasks, or user specifies |
| **Characteristics** | Reference scope restrictions, coordination with other agents, deliverable handoffs |
| **Location** | `.claude/agents/` (project-specific) |

**Examples**: `spec-planner`, `code-reviewer`, `tech-leader`

---

## 2. Layer Structure and Role Division

```
┌─────────────────────────────────────────────────────────────────────┐
│                     Custom Commands                                  │
│                                                                     │
│  Workflow layer that defines "what to do"                           │
│  - User explicitly invokes                                          │
│  - Automates series of work procedures                              │
│  - Can use Skills/Agents internally                                 │
│                                                                     │
│  Example: /spec:init → Generate template → Insert TODOs → Suggest   │
└───────────────────────────────┬─────────────────────────────────────┘
                                │ Reference/Use
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     Custom Skills                                    │
│                                                                     │
│  Standards layer that defines "how to judge"                        │
│  - Checklists and judgment criteria                                 │
│  - Output templates and formats                                     │
│  - Domain-specific expert knowledge                                 │
│                                                                     │
│  Example: security-baseline → 10 security checks → Severity rating  │
└───────────────────────────────┬─────────────────────────────────────┘
                                │ Reference/Use
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     Subagents                                        │
│                                                                     │
│  Persona layer that defines "who to act as"                         │
│  - Specific roles and responsibilities                              │
│  - Reference scope restrictions (security)                          │
│  - Coordination flow with other agents                              │
│                                                                     │
│  Example: code-reviewer → Reference src/tests → Review → code-builder│
└─────────────────────────────────────────────────────────────────────┘
```

### 2.1 Usage Principles

| Aspect | Command | Skill | Agent |
|--------|---------|-------|-------|
| **Invoker** | User | Claude (auto) | Claude (auto) |
| **Granularity** | Entire workflow | Check/judgment | Task execution |
| **State** | Interactive | Reference | Autonomous |
| **Reference Restriction** | None | None | Yes |
| **Output** | Next action suggestions | Judgment results/reports | Deliverables |

---

## 3. Usage by Development Phase

### 3.1 Specification Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Spec template generation | `/spec:init` (Command) | User-invoked workflow |
| Spec creation | `spec-planner` (Agent) | Creates specs as expert |
| Spec review | `/spec:review` → `spec-reviewer` | Command for workflow, Skill for standards |

### 3.2 Design Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Technology selection | `tech-leader` (Agent) | Trade-off analysis needed |
| Architecture review | `architecture-reviewer` (Skill) | Checklist-based judgment |
| Implementation plan creation | `/plan:make` (Command) | Task decomposition workflow |

### 3.3 Implementation Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Task implementation | `/impl:run` (Command) | Implementation workflow per plan |
| Coding standard check | `coding-standards` (Skill) | Standards-based check |
| Test creation | `test-author` (Skill) | Test pattern application |
| Test execution | `/qa:full` (Command) | Execution → result organization workflow |

### 3.4 Review & Release Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Commit | `/git:commit` (Command) | Git operation workflow |
| PR creation | `/git:pr` (Command) | Git operation workflow |
| PR review | `pr-reviewer` (Skill) + `code-reviewer` (Agent) | Standards application + expert judgment |
| Security check | `security-baseline` (Skill) | Security checklist |
| Review response | `/fixup-from-pr-comments` (Command) | Fix workflow |
| Release notes | `release-notes-writer` (Skill) | Template application |

### 3.5 Maintenance Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Debugging | `code-debugger` (Agent) → `debug-triage` (Skill) | Agent executes, Skill provides method |
| Refactoring | `/refactor:cleanup` (Command) | Cleanup workflow |
| Dependency updates | `dependency-change-reviewer` (Skill) | Risk evaluation criteria |

---

## 4. Selection Flowchart

```
Enter "what you want to do"
        │
        ▼
┌───────────────────────┐
│ Want to invoke        │
│ explicitly?           │
└───────────────────────┘
        │
   ┌────┴────┐
   ▼         ▼
  Yes        No
   │         │
   ▼         ▼
Command   ┌────────────────────────┐
          │ Need judgment criteria? │
          │ Or need task execution? │
          └────────────────────────┘
                    │
              ┌─────┴─────┐
              ▼           ▼
         Judgment     Execution
         Criteria        │
              │           │
              ▼           ▼
            Skill       Agent
```

### Quick Decision Table

| What You Want | Selection |
|---------------|-----------|
| "I want to start..." "Execute..." | **Command** |
| "Check..." "Judge by criteria..." | **Skill** |
| "Think as..." "Analyze from perspective..." | **Agent** |

---

## 5. Cross-Reference Map

### 5.1 Command → Skill/Agent

| Command | Related Skill | Related Agent |
|---------|---------------|---------------|
| `/spec:init` | - | `spec-planner` |
| `/spec:review` | `spec-reviewer` | - |
| `/plan:make` | - | `spec-planner` |
| `/impl:run` | `coding-standards` | `code-builder` |
| `/qa:full` | `test-author` | - |
| `/git:commit` | - | - |
| `/git:pr` | `pr-reviewer` | - |
| `/git:branch` | - | - |
| `/gh:commit-push-pr` | `pr-reviewer` | - |
| `/fixup-from-pr-comments` | - | `code-builder` |
| `/refactor:cleanup` | `coding-standards` | - |
| `/session:compact-smart` | - | - |
| `/ultrathink` | - | - |

### 5.2 Skill → Command/Agent

| Skill | Related Command | Related Agent |
|-------|-----------------|---------------|
| `spec-reviewer` | `/spec:review` | `spec-planner` |
| `architecture-reviewer` | - | `tech-leader` |
| `coding-standards` | `/impl:run`, `/refactor:cleanup` | `code-builder` |
| `test-author` | `/qa:full` | - |
| `debug-triage` | - | `code-debugger` |
| `pr-reviewer` | `/git:pr`, `/gh:commit-push-pr` | `code-reviewer` |
| `security-baseline` | - | `code-reviewer` |
| `dependency-change-reviewer` | - | `tech-leader` |
| `release-notes-writer` | - | - |

### 5.3 Agent → Command/Skill

| Agent | Related Command | Related Skill |
|-------|-----------------|---------------|
| `req-analyzer` | `/spec:init` | - |
| `spec-planner` | `/spec:init`, `/plan:make` | `spec-reviewer` |
| `tech-leader` | - | `architecture-reviewer`, `dependency-change-reviewer` |
| `frontend-designer` | - | `coding-standards` |
| `backend-designer` | - | `coding-standards` |
| `code-builder` | `/impl:run`, `/fixup-from-pr-comments` | `coding-standards`, `test-author` |
| `code-reviewer` | `/git:pr` | `pr-reviewer`, `security-baseline` |
| `code-debugger` | - | `debug-triage` |
| `code-guide` | - | - |
| `general-purpose` | - | - |

---

## 6. FAQ

### Q1: When both Command and Skill have similar functions?

**A**: Prioritize Command. Commands manage the entire workflow and use Skill standards internally as needed.

Example: For spec review, execute `/spec:review`. Internally, `spec-reviewer` Skill standards are applied.

### Q2: When both Skill and Agent have similar functions?

**A**: Judge by task nature.
- Check/judgment is main purpose → **Skill**
- Task execution/deliverable creation is main purpose → **Agent**

Example: Debugging is handled by `code-debugger` (Agent). It references `debug-triage` (Skill) for investigation methods.

### Q3: When adding new automation, which should I create?

**A**: Judge by the following criteria.

| Characteristic | Create |
|----------------|--------|
| Want user to invoke with "/xxx" | Command |
| Want to define judgment criteria/checklist | Skill |
| Want specific role/reference scope | Agent |

### Q4: Can multiple mechanisms be used for one task?

**A**: Yes, it's common. For example:

```
User: Execute /git:pr
    │
    ├─→ Command: /git:pr starts workflow
    │       │
    │       ├─→ Skill: Generate PR body using pr-reviewer standards
    │       │
    │       └─→ Agent: Add review perspectives from code-reviewer view
    │
    └─→ Result: PR is created
```

---

## Appendix: Definition File List

### Commands (13)
```
~/.claude/commands/
├── spec-init.md          # Spec template generation
├── spec-review.md        # Spec review
├── plan-make.md          # Implementation plan creation
├── impl-run.md           # Task implementation
├── qa-full.md            # Full testing
├── git-branch.md         # Branch creation
├── git-commit.md         # Commit
├── git-pr.md             # PR creation
├── gh:commit-push-pr.md  # Integrated workflow
├── fixup-from-pr-comments.md  # Review response
├── refactor-cleanup.md   # Refactoring
├── session-compact-smart.md   # Session organization
└── ultrathink.md         # Deep thinking mode
```

### Skills (9)
```
~/.claude/skills/
├── spec-reviewer/        # Spec review standards
├── architecture-reviewer/# Architecture review
├── coding-standards/     # Coding standards
├── test-author/          # Test creation patterns
├── debug-triage/         # Debug methods
├── pr-reviewer/          # PR review standards
├── security-baseline/    # Security checks
├── dependency-change-reviewer/  # Dependency update review
└── release-notes-writer/ # Release notes creation
```

### Agents (10)
```
.claude/agents/
├── req-analyzer.md       # Requirements analyst
├── spec-planner.md       # Specification planner
├── tech-leader.md        # Tech lead
├── frontend-designer.md  # Frontend designer
├── backend-designer.md   # Backend designer
├── code-builder.md       # Code builder
├── code-reviewer.md      # Code reviewer
├── code-debugger.md      # Code debugger
├── code-guide.md         # Code guide
└── general-purpose.md    # General purpose agent
```

---

*This guide is part of the clauto-develop framework.*
