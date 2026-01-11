# Claude Code Subagent Development Guide

A practical guide for efficient development using subagents with Claude Code.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [CLAUDE.md Configuration](#2-claudemd-configuration)
3. [Subagent List](#3-subagent-list)
4. [Development Flow Overview](#4-development-flow-overview)
5. [Phase Details](#5-phase-details)
6. [How to Use Subagents](#6-how-to-use-subagents)
7. [Practical Use Cases](#7-practical-use-cases)
8. [Best Practices](#8-best-practices)
9. [Tips for Development](#9-tips-for-development)
10. [FAQ](#10-faq)
11. [Appendix: Quick Reference](#appendix-quick-reference)

---

## 1. Introduction

### 1.1 What are Subagents

Subagents are specialized agents that Claude Code uses to efficiently handle complex tasks. They are launched as independent processes from the main Claude session and perform processing specialized for specific purposes.

### 1.2 Benefits of Using Subagents

| Benefit | Description |
|---------|-------------|
| **Parallel Processing** | Can execute multiple tasks simultaneously |
| **Context Isolation** | Isolate context per task for efficient processing |
| **Specialization** | Each agent optimized for specific tasks |
| **Autonomy** | Autonomously complete complex multi-step tasks |

### 1.3 Basic Principles

- **Strict Role Division**: Each agent works only within their role scope
- **Confirm Unclear Points**: Always confirm rather than assuming ambiguous points
- **Follow Reference Scope**: Prohibit access to confidential information, reference only necessary files

---

## 2. CLAUDE.md Configuration

### 2.1 What is CLAUDE.md

`CLAUDE.md` is a configuration file placed at the project root that provides Claude with project-specific context and instructions.

### 2.2 Example CLAUDE.md for Projects

```markdown
# Project Overview

This project is [Project Name].

## Language Settings

- **Development Language**: English
- **Comments**: Write in English
- **Commit Messages**: Write in English
- **Documentation**: Create in English

## Technology Stack

- Frontend: [Framework / Language]
- Backend: [Framework / Language]
- Database: [Database]
- Testing: [Test Framework]

## Project Structure

```
src/
├── [module1]/    # Project-specific structure
├── [module2]/    # Project-specific structure
├── services/     # Business logic
└── utils/        # Utility functions
```

> **Note**: Adjust directory structure according to your project's technology stack.

## Development Standards

### Coding Style

Follow naming conventions of your project's language/framework.

- Function/Variable names: [camelCase/snake_case]
- Class/Type names: PascalCase
- Constants: UPPER_SNAKE_CASE
- File names: [kebab-case/snake_case]

### Comment Standards

- Write function documentation comments (JSDoc, docstring, etc.)
- Add inline comments for complex logic

### Git Standards

- Write commit messages descriptively
- Prefixes: feat:, fix:, docs:, refactor:, test:, chore:

Examples:
- `feat: Add user authentication`
- `fix: Fix login error`

## Common Commands

Document commands according to your project's build tools/package manager.

```bash
# Start development server
[command]

# Run tests
[command]

# Build
[command]
```

## Notes

- Manage environment variables in `.env.local` (do not commit)
- Do not include API keys or sensitive information in code
- Consult before adding new dependencies
```

### 2.3 CLAUDE.md Best Practices

1. **Keep it Concise**: Include only necessary information
2. **Structure Clearly**: Clearly separate sections
3. **Keep Updated**: Update as project changes
4. **Share with Team**: Include in version control

---

## 3. Subagent List

### 3.1 List by Role Group

| Role Group | Agent | Description |
|------------|-------|-------------|
| **Planner** | req-analyzer | Requirements analysis, definition |
| **Planner** | spec-planner | Specification planning, technical spec creation |
| **Designer** | tech-leader | Technology selection, architecture policy decisions |
| **Designer** | frontend-designer | UI/UX design, component design |
| **Designer** | backend-designer | API design, database design |
| **Builder** | code-builder | Code implementation, test creation |
| **Reviewer** | code-reviewer | Code review, quality assurance |
| **Reviewer** | code-debugger | Bug investigation, fixes |
| **Guide** | code-guide | Codebase navigation, explanations |
| **Guide** | general-purpose | General task handling |

### 3.2 Agent Details

#### req-analyzer (Requirements Analyst)

| Item | Content |
|------|---------|
| **Role** | Analyst who converts vague requirements into concrete specs |
| **Use** | Requirement clarification, definition, stakeholder needs identification |
| **Characteristics** | Questioning ability, requirement structuring, ambiguity elimination |
| **Output** | Requirements list, use cases, acceptance criteria |

#### spec-planner (Specification Planner)

| Item | Content |
|------|---------|
| **Role** | Planner who converts requirements into implementable technical specs |
| **Use** | Technical spec creation, API design, data model design |
| **Characteristics** | Detail-oriented, consistency assurance, implementation feasibility verification |
| **Output** | Technical specifications, API specs, data schemas |

#### tech-leader (Tech Lead)

| Item | Content |
|------|---------|
| **Role** | Technology selection, architecture design, team technical guideline creation |
| **Use** | Tech stack selection, design pattern decisions, technical problem solving |
| **Characteristics** | Holistic perspective, trade-off analysis, best practice application |
| **Output** | Technology selection rationale, architecture diagrams, design guidelines |

#### frontend-designer (Frontend Designer)

| Item | Content |
|------|---------|
| **Role** | UI/UX design, component design, frontend architecture |
| **Use** | Component structure design, state management design, styling policy creation |
| **Characteristics** | User experience focus, reusability consideration, performance optimization |
| **Output** | Component design, screen flow diagrams, state management design |

#### backend-designer (Backend Designer)

| Item | Content |
|------|---------|
| **Role** | API design, database design, backend architecture |
| **Use** | RESTful API design, data modeling, authentication/authorization design |
| **Characteristics** | Scalability focus, security consideration, efficient data access |
| **Output** | API design documents, ER diagrams, sequence diagrams |

#### code-builder (Code Builder)

| Item | Content |
|------|---------|
| **Role** | Code implementation based on design specifications |
| **Use** | Feature implementation, test code creation, refactoring |
| **Characteristics** | High-quality code generation, coding standard compliance, efficient implementation |
| **Output** | Implementation code, test code, documentation comments |

#### code-reviewer (Code Reviewer)

| Item | Content |
|------|---------|
| **Role** | Review code quality, security, performance |
| **Use** | Code quality checking, bug detection, security issue discovery |
| **Perspectives** | Quality, bugs, security, project standard compliance |
| **Output** | Review comments, improvement suggestions, fix location identification |

#### code-debugger (Code Debugger)

| Item | Content |
|------|---------|
| **Role** | Bug cause identification, root cause analysis, fix proposal |
| **Use** | Error investigation, debugging, performance issue diagnosis |
| **Characteristics** | Logical cause tracking, reproduction step identification, effective fix suggestions |
| **Output** | Cause analysis reports, fix code, recurrence prevention measures |

#### code-guide (Code Guide)

| Item | Content |
|------|---------|
| **Role** | Codebase navigation, implementation location identification, code understanding support |
| **Use** | File search, code search, architecture explanation |
| **Characteristics** | Fast search, clear explanations, context provision |
| **Output** | File lists, code explanations, structure diagrams |

#### general-purpose (General Purpose Agent)

| Item | Content |
|------|---------|
| **Role** | All-purpose agent capable of handling any task |
| **Use** | Complex investigations, code search, multi-step tasks, uncategorized tasks |
| **Characteristics** | High autonomy, broad tool access, flexible problem-solving ability |
| **Tools Used** | All tools |

---

## 4. Development Flow Overview

### 4.1 Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Development Flow Overview                     │
└─────────────────────────────────────────────────────────────────────┘

  [User Request]
       │
       ▼
  ┌─────────────┐
  │req-analyzer │ ─────────► Requirements Definition, Use Cases
  │ (Analysis)   │
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │spec-planner │ ─────────► Technical Specification, Data Model
  │ (Planning)   │
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │tech-leader  │ ─────────► Technology Selection Doc, Architecture Policy
  │ (Selection)  │
  └──────┬──────┘
         │
    ┌────┴────┐
    ▼         ▼
┌────────┐ ┌────────┐
│frontend│ │backend │ ────► Component Design, API Design, DB Design
│designer│ │designer│
└───┬────┘ └───┬────┘
    │          │
    └────┬─────┘
         │
         ▼
  ┌─────────────┐
  │code-builder │ ─────────► Implementation Code, Test Code
  │ (Build)      │
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │code-reviewer│ ─────────► Review Results, Improvement Suggestions
  │ (Review)     │
  └──────┬──────┘
         │
         ▼ (When issues found)
  ┌─────────────┐
  │code-debugger│ ─────────► Bug Analysis Report, Fix Proposal
  │ (Debug)      │
  └─────────────┘
```

### 4.2 Development Phase to Agent Mapping

```
Request → Analysis → Spec → Design → Implementation → Review → Maintenance
  │         │         │       │           │            │          │
  │         │         │       │           │            │          └─ code-debugger
  │         │         │       │           │            └─ code-reviewer
  │         │         │       │           └─ code-builder
  │         │         │       └─ tech-leader / frontend-designer / backend-designer
  │         │         └─ spec-planner
  │         └─ req-analyzer
  └─ general-purpose (all phases) / code-guide (all phases)
```

### 4.3 Deliverable Handoff Flow

```
┌──────────────┐
│ req-analyzer │
│ Requirements │
│ Use Cases    │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ spec-planner │
│ Tech Spec    │
│ Data Model   │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ tech-leader  │
│ Tech Select  │
│ ARC Policy   │
└──────┬───────┘
       │
   ┌───┴───┐
   ▼       ▼
┌──────┐ ┌──────┐
│front │ │back  │
│design│ │design│
└──┬───┘ └──┬───┘
   │        │
   └───┬────┘
       ▼
┌──────────────┐
│ code-builder │
│ Impl Code    │
│ Test Code    │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│code-reviewer │
│Review Results│
└──────────────┘
```

---

## 5. Phase Details

### 5.1 Phase 1: Requirements Analysis (req-analyzer)

Converts user's vague requirements into concrete specifications.

#### How to Invoke

```
As req-analyzer, please analyze the following requirement:
"I want a feature for users to manage tasks"
```

#### Main Tasks

- Analysis and clarification of user requirements
- Organization of functional/non-functional requirements
- Use case creation
- Acceptance criteria formulation

#### Output Deliverables

| Deliverable | Description | Handoff To |
|-------------|-------------|------------|
| Requirements Definition | Functional, non-functional requirements, acceptance criteria | spec-planner |
| Use Cases | Usage scenarios | spec-planner, frontend-designer |
| Unresolved Items List | Items requiring confirmation | User |

#### Question Perspectives (5W1H)

- **Who**: Who will use this feature
- **What**: What specifically should it do
- **When**: When/under what conditions is it used
- **Where**: On which screen/endpoint
- **Why**: Why is this feature needed
- **How**: How should it work

---

### 5.2 Phase 2: Specification Planning (spec-planner)

Converts requirements into implementable technical specifications.

#### How to Invoke

```
As spec-planner, please create technical specifications from the following requirements:
[Requirements definition from req-analyzer]
```

#### Main Tasks

- Functional specification document creation
- Conceptual data model design
- Interface contract definition
- Constraints and rules definition

#### Output Deliverables

| Deliverable | Description | Handoff To |
|-------------|-------------|------------|
| Technical Specification | Functional specs, data model, constraints | backend-designer, frontend-designer |

#### Notes

- Define "what to build" (not "how to build" - that's Designer's role)
- Don't go into implementation details

---

### 5.3 Phase 3: Technology Selection (tech-leader)

Determines optimal technology stack and architecture for the project.

#### How to Invoke

```
As tech-leader, please make technology selections based on the following specification:
[Technical specification from spec-planner]
```

#### Main Tasks

- Technology stack selection (language, framework, DB, etc.)
- Architecture policy decisions
- Design principle formulation
- Trade-off analysis

#### Output Deliverables

| Deliverable | Description | Handoff To |
|-------------|-------------|------------|
| Technology Selection Document | Adopted technologies and rationale | All designers, code-builder |
| Architecture Policy | Configuration patterns, design principles | backend-designer, frontend-designer |

#### Decision Perspectives

1. Alignment with business requirements
2. Team proficiency
3. Ecosystem maturity
4. Future potential
5. Cost

---

### 5.4 Phase 4: Design (frontend-designer / backend-designer)

Performs concrete implementation design.

#### frontend-designer

```
As frontend-designer, please perform UI design based on the following specification:
[Technical spec + tech-leader's policy]
```

**Main Tasks**:
- Component design
- State management design
- Screen design
- Styling policy

**Output Deliverables**:
- Component design document
- Screen design document
- State management design document

#### backend-designer

```
As backend-designer, please perform API/DB design based on the following specification:
[Technical spec + tech-leader's policy]
```

**Main Tasks**:
- Detailed API endpoint design
- Database physical design
- Authentication/authorization flow design
- Integration design

**Output Deliverables**:
- API design document
- DB design document
- Sequence diagrams

---

### 5.5 Phase 5: Implementation (code-builder)

Implements code based on design specifications.

#### How to Invoke

```
As code-builder, please implement based on the following design:
[Design documents from frontend-designer / backend-designer]
```

#### Implementation Flow

1. **Specification Review**: Clarify implementation content
2. **Existing Code Review**: Understand related existing implementations
3. **Implementation**: Write code according to specifications
4. **Test Creation**: Create unit tests
5. **Verification**: Confirm correct operation

#### Output Deliverables

| Deliverable | Description | Handoff To |
|-------------|-------------|------------|
| Implementation Code | Feature implementation | code-reviewer |
| Test Code | Unit tests, integration tests | code-reviewer |
| Implementation Report | Changed files, notes | User |

---

### 5.6 Phase 6: Review (code-reviewer)

Evaluates code quality, security, and performance.

#### How to Invoke

```
As code-reviewer, please review the following implementation:
[Implementation code from code-builder]
```

#### Review Perspectives

| Perspective | Check Items |
|-------------|-------------|
| Code Quality | Naming, responsibilities, duplication, error handling |
| Security | SQL injection, XSS, authentication/authorization |
| Performance | N+1 problems, unnecessary loops, memory leaks |
| Testing | Coverage, edge cases |
| Design | SOLID principles, abstraction, dependencies |

#### Severity Definitions

| Level | Meaning | Action |
|-------|---------|--------|
| Critical | Security issues, potential data loss | Must fix immediately |
| Major | Bugs, performance issues | Fix before merge |
| Minor | Code quality, style issues | Recommended fix |
| Suggestion | Improvement proposals | For consideration |

---

### 5.7 Phase 7: Debugging (code-debugger)

Identifies and fixes bugs.

#### How to Invoke

```
As code-debugger, please investigate the following bug:
[Error content, reproduction steps]
```

#### Debugging Flow

1. **Understand Problem**: Error message, deviation from expected behavior
2. **Gather Information**: Error logs, stack trace analysis
3. **Hypothesize**: List possible causes
4. **Verify**: Add logs, create minimal reproduction case
5. **Fix and Confirm**: Address root cause, check side effects

#### Output Deliverables

| Deliverable | Description | Handoff To |
|-------------|-------------|------------|
| Bug Analysis Report | Root cause, fix proposal | code-builder |
| Fix Code | Fix implementation | code-reviewer |
| Recurrence Prevention | Measures to spread horizontally | tech-leader |

---

## 6. How to Use Subagents

### 6.1 Basic Invocation

Subagents are automatically selected based on user requests, but can also be explicitly specified.

**Implicit Invocation (Recommended):**
```
"Tell me about this project's structure"
→ code-guide agent auto-selected

"Create an implementation plan for the new feature"
→ spec-planner agent auto-selected

"Analyze this requirement"
→ req-analyzer agent auto-selected
```

**Explicit Invocation:**
```
"Use code-guide agent to find authentication-related code"
"Design implementation steps with spec-planner agent"
"Consider architecture with tech-leader agent"
```

### 6.2 Parallel Execution

Multiple independent tasks can be executed simultaneously.

**Example: Run multiple investigations in parallel**
```
"Investigate the following in parallel:
1. Authentication feature implementation location
2. Database connection settings
3. API endpoint list"
```

### 6.3 Background Execution

Long-running tasks can be executed in the background.

**Example:**
```
"Run tests in the background while proceeding with code review"
```

### 6.4 Agent Resume

Previous agent sessions can be resumed to continue work with maintained context.

**Example:**
```
"Continue with the previous investigation"
"Start implementation based on the Plan agent's earlier results"
```

---

## 7. Practical Use Cases

### 7.1 New Feature Implementation

**User Request**: "I want to add a feature for users to create, edit, and delete tasks"

#### Step 1: Requirements Analysis

```
As req-analyzer, please analyze the following requirement:
"I want to add a feature for users to create, edit, and delete tasks"
```

**Example Output**:
- Functional Requirements: CRUD operations for tasks
- Non-functional Requirements: Response within 1 second
- Use Cases: UC-1 Create Task, UC-2 Edit Task...

#### Step 2: Specification Planning

```
As spec-planner, please create technical specifications from the following requirements:
[Output from req-analyzer]
```

**Example Output**:
- Data Model: Task { id, title, description, status, ... }
- API Specification Overview: POST /tasks, GET /tasks/:id, ...

#### Step 3: Technology Selection

```
As tech-leader, please make technology selections
```

#### Step 4: Design

```
As backend-designer, please perform API/DB design
As frontend-designer, please perform UI design
```

#### Step 5: Implementation

```
As code-builder, please implement based on the design
```

#### Step 6: Review

```
As code-reviewer, please review the implementation
```

---

### 7.2 Bug Fix

**Report**: "Task edits are not being saved"

#### Step 1: Debug

```
As code-debugger, please investigate the following bug:
"Task edits are not being saved"
Reproduction steps: 1. Open task 2. Edit content 3. Click save button
```

**Example Output**:
- Root Cause: API request payload doesn't include ID
- Fix Proposal: Include ID in editTask function request

#### Step 2: Implement Fix

```
As code-builder, please implement the following fix:
[Fix proposal from code-debugger]
```

#### Step 3: Review

```
As code-reviewer, please review the fix
```

---

### 7.3 Refactoring

```
Step 1: Current State Analysis
"Analyze the users module structure and identify improvement points"
→ code-guide agent analyzes

Step 2: Design
"Create a refactoring plan"
→ tech-leader agent creates design policy

Step 3: Execute
"Execute refactoring according to the plan"
→ code-builder agent executes

Step 4: Review
"Review the refactoring results"
→ code-reviewer agent reviews
```

---

### 7.4 Codebase Understanding

```
As code-guide, please explain the authentication feature implementation location and mechanism
```

**Example Output**:
- Authentication is implemented in `src/services/auth-service.ts`
- Uses JWT tokens, expires in 1 hour
- Middleware is in `src/middleware/auth.ts`

---

## 8. Best Practices

### 8.1 Writing Effective Prompts

**Good Examples:**
```
✅ "Investigate the API call patterns under src/services and
    identify where they can be consolidated"

✅ "I want to implement authentication feature.
    - Use JWT tokens
    - Support refresh tokens
    - Session management with Redis"
```

**Bad Examples:**
```
❌ "Look at the code" (unclear purpose)

❌ "Make it nice" (too vague)
```

### 8.2 Agent Selection Guidelines

| Situation | Recommended Agent |
|-----------|-------------------|
| Want to clarify requirements | req-analyzer |
| Want to create technical specs | spec-planner |
| Want to decide architecture | tech-leader |
| UI component design | frontend-designer |
| API/DB design | backend-designer |
| Want to implement code | code-builder |
| Want to review code | code-reviewer |
| Want to investigate/fix bugs | code-debugger |
| Want to find code locations | code-guide |
| Need complex investigation | general-purpose |

### 8.3 Performance Optimization

1. **Choose Appropriate Thoroughness**
   - Simple search: quick
   - Normal investigation: medium
   - Comprehensive analysis: very thorough

2. **Utilize Parallel Execution**
   - Execute independent tasks in parallel
   - Execute dependent tasks sequentially

3. **Clarify Context**
   - Provide necessary information upfront
   - Reduce ambiguity

### 8.4 Common Problems and Solutions

| Problem | Solution |
|---------|----------|
| Agent behaves unexpectedly | Make prompt more specific |
| Processing is slow | Lower thoroughness / narrow scope |
| Results are insufficient | Request additional investigation / increase thoroughness |

---

## 9. Tips for Development

### 9.1 Communication

Claude Code fully understands instructions in any language. Communicate naturally.

```
✅ "Implement user registration feature"
✅ "Investigate the cause of this error"
✅ "Write tests"
```

### 9.2 Code Comments

Code comments in any language are correctly understood and generated.

**Example (TypeScript):**
```typescript
/**
 * Create a user
 * @param name - User name
 * @returns Created user
 */
function createUser(name: string): User {
  // Perform validation
  validateName(name);

  // Save user
  return saveUser({ name });
}
```

### 9.3 File Naming Notes

English file names are recommended.

```
✅ Recommended: user-service.ts
```

### 9.4 Git Operations

```bash
# Commit message examples
git commit -m "feat: Add login feature"
git commit -m "fix: Fix authentication error"

# Branch naming
git checkout -b feature/login     # Recommended
```

### 9.5 Error Message Localization

User-facing error messages can be implemented in any language.

**Example (TypeScript/JavaScript):**
```typescript
const errorMessages = {
  required: 'This field is required',
  invalidEmail: 'Invalid email format',
  tooShort: 'Must be at least {min} characters',
};
```

---

## 10. FAQ

### Q1: Don't know which agent to use

**A**: Use the following criteria:

| What I Want to Do | Agent to Use |
|-------------------|--------------|
| Organize requirements | req-analyzer |
| Create specifications | spec-planner |
| Select technology | tech-leader |
| Design UI/components | frontend-designer |
| Design API/DB | backend-designer |
| Write code | code-builder |
| Review code | code-reviewer |
| Fix bugs | code-debugger |
| Find code locations | code-guide |
| None of the above | general-purpose |

### Q2: Can I skip agents?

**A**: For small changes, some phases can be skipped. However, the following are recommended to not skip:
- **New feature development**: req-analyzer → spec-planner → design → implementation
- **Bug fix**: code-debugger → code-builder → code-reviewer

### Q3: Can I use multiple agents simultaneously?

**A**: Parallel execution is possible for independent tasks. For example, frontend-designer and backend-designer can work simultaneously. However, execute sequentially when there are dependencies.

---

## Appendix: Quick Reference

### Subagent Quick Reference Table

| What I Want to Do | Agent | Example |
|-------------------|-------|---------|
| Analyze requirements | req-analyzer | "Analyze this feature requirement" |
| Create specifications | spec-planner | "Create API specifications" |
| Select technology | tech-leader | "Propose optimal architecture" |
| Design UI | frontend-designer | "Design component structure" |
| Design API | backend-designer | "Design API endpoints" |
| Implement code | code-builder | "Implement this feature" |
| Review code | code-reviewer | "Review these changes" |
| Fix bugs | code-debugger | "Identify cause of this error" |
| Find/understand code | code-guide | "Find authentication-related files" |
| Complex investigation | general-purpose | "Investigate dependency impacts" |

### Common Prompt Templates

```
# Investigation
"Investigate {target} and tell me about {perspective}"

# Implementation
"Implement {feature}. Requirements are {requirements}"

# Review
"Review {target} and check {perspective}"

# Planning
"Create implementation plan to achieve {goal}"
```

---

## References

- Agent definitions: `.claude/agents/*.md`
- [Serena MCP Integration Guide](./08-serena-integration-guide.md) - Semantic code operations using LSP
- [Command/Skill/Agent Usage Guide](./07-command-skill-agent-guide.md)

---

*This guide is updated periodically. Refer to Claude Code's official documentation for the latest information.*
