# Skill and Agent Usage Guide

The clauto-develop framework provides two types of automation mechanisms. This guide explains each role and appropriate usage.

> **Note**: Since v0.4.0, custom commands have been integrated into skills. Workflows invokable as slash commands are defined as "workflow skills" (`user-invocable: true`).

---

## Table of Contents

1. [Overview of the Two Mechanisms](#1-overview-of-the-two-mechanisms)
2. [Layer Structure and Role Division](#2-layer-structure-and-role-division)
3. [Usage by Development Phase](#3-usage-by-development-phase)
4. [Selection Flowchart](#4-selection-flowchart)
5. [Cross-Reference Map](#5-cross-reference-map)
6. [FAQ](#6-faq)

---

## 1. Overview of the Two Mechanisms

### 1.1 Skills

Skills are classified into two types:

#### Reference Skills

| Item | Content |
|------|---------|
| **Role** | Quality standards, checklists, output templates |
| **Invocation** | Claude auto-applies based on context, or pre-loaded via agent `skills` field |
| **Characteristics** | Defines judgment criteria and expected output format |
| **Location** | `~/.claude/skills/` (global) or `.claude/skills/` (project) |

**Examples**: `security-baseline`, `coding-standards`, `spec-reviewer`

#### Workflow Skills (Slash Commands)

| Item | Content |
|------|---------|
| **Role** | Workflow automation |
| **Invocation** | User explicitly invokes with `/skill-name` |
| **Characteristics** | Auto-executes series of tasks, includes interactive confirmations. Defined with `user-invocable: true` |
| **Location** | `~/.claude/skills/` (global) or `.claude/skills/` (project) |

**Examples**: `/spec:init`, `/git:commit`, `/qa:full`

### 1.2 Subagents

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
│                     Skills                                           │
│                                                                     │
│  Workflow Skills (user-invocable: true)                             │
│  - User explicitly invokes with /skill-name                         │
│  - Automates series of work procedures                              │
│  - Can restrict tools with allowed-tools                            │
│  Example: /spec:init → Generate template → Insert TODOs → Suggest   │
│                                                                     │
│  Reference Skills                                                   │
│  - Checklists and judgment criteria                                 │
│  - Output templates and formats                                     │
│  - Can be pre-loaded via agent skills field                         │
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
│  - Pre-load skills via skills field                                 │
│  - Coordination flow with other agents                              │
│                                                                     │
│  Example: code-reviewer → Reference src/tests → Review → code-builder│
└─────────────────────────────────────────────────────────────────────┘
```

### 2.1 Usage Principles

| Aspect | Workflow Skill | Reference Skill | Agent |
|--------|----------------|-----------------|-------|
| **Invoker** | User | Claude (auto) / Agent | Claude (auto) |
| **Granularity** | Entire workflow | Check/judgment | Task execution |
| **State** | Interactive | Reference | Autonomous |
| **Reference Restriction** | allowed-tools | None | Yes |
| **Output** | Next action suggestions | Judgment results/reports | Deliverables |

---

## 3. Usage by Development Phase

### 3.1 Specification Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Spec template generation | `/spec:init` (Workflow Skill) | User-invoked workflow |
| Spec creation | `spec-planner` (Agent) | Creates specs as expert |
| Spec review | `/spec:review` → `spec-reviewer` | Workflow skill for flow, Reference skill for standards |

### 3.2 Design Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Technology selection | `tech-leader` (Agent) | Trade-off analysis needed |
| Architecture review | `architecture-reviewer` (Skill) | Checklist-based judgment |
| Implementation plan creation | `/plan:make` (Workflow Skill) | Task decomposition workflow |

### 3.3 Implementation Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Task implementation | `/impl:run` (Workflow Skill) | Implementation workflow per plan |
| Coding standard check | `coding-standards` (Skill) | Standards-based check |
| Test creation | `test-author` (Skill) | Test pattern application |
| Test execution | `/qa:full` (Workflow Skill) | Execution → result organization workflow |

### 3.4 Review & Release Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Commit | `/git:commit` (Workflow Skill) | Git operation workflow |
| PR creation | `/git:pr` (Workflow Skill) | Git operation workflow |
| PR review | `pr-reviewer` (Skill) + `code-reviewer` (Agent) | Standards application + expert judgment |
| Security check | `security-baseline` (Skill) | Security checklist |
| Review response | `/fixup-from-pr-comments` (Workflow Skill) | Fix workflow |
| Release notes | `release-notes-writer` (Skill) | Template application |

### 3.5 Maintenance Phase

| Task | Mechanism | Reason |
|------|-----------|--------|
| Debugging | `code-debugger` (Agent) → `debug-triage` (Skill) | Agent executes, Skill provides method |
| Refactoring | `/refactor:cleanup` (Workflow Skill) | Cleanup workflow |
| Dependency updates | `dependency-change-reviewer` (Skill) | Risk evaluation criteria |

---

## 4. Selection Flowchart

```
Enter "what you want to do"
        │
        ▼
┌───────────────────────────┐
│ Want to invoke explicitly  │
│ as a slash command?        │
└───────────────────────────┘
        │
   ┌────┴────┐
   ▼         ▼
  Yes        No
   │         │
   ▼         ▼
Workflow  ┌────────────────────────┐
Skill     │ Need judgment criteria? │
          │ Or need task execution? │
          └────────────────────────┘
                    │
              ┌─────┴─────┐
              ▼           ▼
         Judgment     Execution
         Criteria        │
              │           │
              ▼           ▼
         Reference      Agent
           Skill
```

### Quick Decision Table

| What You Want | Selection |
|---------------|-----------|
| "I want to start..." "Execute..." | **Workflow Skill** (slash command) |
| "Check..." "Judge by criteria..." | **Reference Skill** |
| "Think as..." "Analyze from perspective..." | **Agent** |

---

## 5. Cross-Reference Map

### 5.1 Workflow Skill → Reference Skill/Agent

| Workflow Skill | Related Reference Skill | Related Agent |
|----------------|-------------------------|---------------|
| `/spec:init` | - | `spec-planner` |
| `/spec:review` | `spec-reviewer` | - |
| `/plan:make` | - | `spec-planner` |
| `/impl:run` | `coding-standards` | `code-builder` |
| `/qa:full` | `test-author` | - |
| `/git:commit` | - | - |
| `/git:pr` | `pr-reviewer` | - |
| `/git:branch` | - | - |
| `/commit-push-pr` | `pr-reviewer` | - |
| `/fixup-from-pr-comments` | - | `code-builder` |
| `/refactor:cleanup` | `coding-standards` | - |
| `/session:compact-smart` | - | - |
| `/ultrathink` | - | - |

### 5.2 Reference Skill → Workflow Skill/Agent

| Reference Skill | Related Workflow Skill | Related Agent (skills field) |
|-----------------|------------------------|------------------------------|
| `spec-reviewer` | `/spec:review` | `spec-planner` |
| `architecture-reviewer` | - | `tech-leader` |
| `coding-standards` | `/impl:run`, `/refactor:cleanup` | `code-builder`, `code-reviewer` |
| `test-author` | `/qa:full` | `code-builder` |
| `debug-triage` | - | `code-debugger` |
| `pr-reviewer` | `/git:pr`, `/commit-push-pr` | `code-reviewer` |
| `security-baseline` | - | `code-reviewer` |
| `dependency-change-reviewer` | - | `tech-leader` |
| `release-notes-writer` | - | - |

### 5.3 Agent → Workflow Skill/Reference Skill

| Agent | Related Workflow Skill | Pre-loaded Skills (skills field) |
|-------|------------------------|----------------------------------|
| `req-analyzer` | `/spec:init` | - |
| `spec-planner` | `/spec:init`, `/plan:make` | `spec-reviewer` |
| `tech-leader` | - | `architecture-reviewer` |
| `frontend-designer` | - | - |
| `backend-designer` | - | - |
| `code-builder` | `/impl:run`, `/fixup-from-pr-comments` | `coding-standards`, `test-author` |
| `code-reviewer` | `/git:pr` | `pr-reviewer`, `security-baseline`, `coding-standards` |
| `code-debugger` | - | `debug-triage` |
| `code-guide` | - | - |
| `general-purpose` | - | - |

---

## 6. FAQ

### Q1: When both Workflow Skill and Reference Skill have similar functions?

**A**: Prioritize the Workflow Skill (slash command). Workflow skills manage the entire workflow and use reference skill standards internally as needed.

Example: For spec review, execute `/spec:review`. Internally, `spec-reviewer` reference skill standards are applied.

### Q2: When both Reference Skill and Agent have similar functions?

**A**: Judge by task nature.
- Check/judgment is main purpose → **Reference Skill**
- Task execution/deliverable creation is main purpose → **Agent**

Example: Debugging is handled by `code-debugger` (Agent). It references `debug-triage` (Skill) for investigation methods.

### Q3: When adding new automation, which should I create?

**A**: Judge by the following criteria.

| Characteristic | Create |
|----------------|--------|
| Want user to invoke with "/xxx" | Workflow Skill (`user-invocable: true`) |
| Want to define judgment criteria/checklist | Reference Skill |
| Want specific role/reference scope | Agent |

### Q4: Can multiple mechanisms be used for one task?

**A**: Yes, it's common. For example:

```
User: Execute /git:pr
    │
    ├─→ Workflow Skill: /git:pr starts workflow
    │       │
    │       ├─→ Reference Skill: Generate PR body using pr-reviewer standards
    │       │
    │       └─→ Agent: Add review perspectives from code-reviewer view
    │
    └─→ Result: PR is created
```

---

## Appendix: Definition File List

### Skills (22 = 9 reference + 13 workflow)
```
~/.claude/skills/
# Reference Skills (9)
├── spec-reviewer/        # Spec review standards
├── architecture-reviewer/# Architecture review
├── coding-standards/     # Coding standards
├── test-author/          # Test creation patterns
├── debug-triage/         # Debug methods
├── pr-reviewer/          # PR review standards
├── security-baseline/    # Security checks
├── dependency-change-reviewer/  # Dependency update review
├── release-notes-writer/ # Release notes creation
# Workflow Skills (13, user-invocable: true)
├── spec-init/            # /spec:init - Spec template generation
├── spec-review/          # /spec:review - Spec review
├── plan-make/            # /plan:make - Implementation plan creation
├── impl-run/             # /impl:run - Task implementation
├── qa-full/              # /qa:full - Full testing
├── git-branch/           # /git:branch - Branch creation
├── git-commit/           # /git:commit - Commit
├── git-pr/               # /git:pr - PR creation
├── commit-push-pr/       # /commit-push-pr - Integrated workflow
├── fixup-from-pr-comments/   # /fixup-from-pr-comments - Review response
├── refactor-cleanup/     # /refactor:cleanup - Refactoring
├── session-compact-smart/    # /session:compact-smart - Session organization
└── ultrathink/           # /ultrathink - Deep thinking mode
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
