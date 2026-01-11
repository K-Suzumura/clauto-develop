# Recommended Directory Design

A recommended directory structure for effectively enabling subagent reference scope restrictions.

> **Note**: This structure is a generic template. Adjust it according to your project's technology stack (language, framework). Replace `[placeholder]` with specific directory names.

## Basic Structure

```
project/
├── CLAUDE.md                 # Common constitution (minimal)
├── README.md                 # Project overview
│
├── .claude/                  # Claude configuration
│   └── agents/               # Subagent definitions
│       ├── req-analyzer.md
│       ├── spec-planner.md
│       ├── tech-leader.md
│       ├── frontend-designer.md
│       ├── backend-designer.md
│       ├── code-builder.md
│       ├── code-reviewer.md
│       ├── code-debugger.md
│       ├── code-guide.md
│       └── general-purpose.md
│
├── docs/                     # Documentation
│   ├── requirements/         # Requirements (for Planner)
│   ├── api/                  # API specifications (for Builder)
│   └── architecture/         # Architecture (for Designer)
│
├── specs/                    # Specifications (Planner/Designer output)
│   ├── features/             # Feature specifications
│   └── designs/              # Design documents
│
├── src/                      # Source code
│   ├── [frontend]/           # Frontend related (project-specific structure)
│   ├── [backend]/            # Backend related (project-specific structure)
│   ├── api/                  # API endpoints
│   ├── services/             # Business logic
│   ├── models/               # Data models
│   └── [shared]/             # Common modules
│
├── tests/                    # Test code
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── database/                 # DB related (backend)
│   └── migrations/
│
├── infra/                    # Infrastructure (reference restricted)
│   └── [IaC tools]/          # e.g., terraform/, k8s/, ansible/, etc.
│
└── logs/                     # Logs (for debugger)
```

## Role-based Reference Matrix

| Directory | Planner | Designer | Builder | Reviewer | Guide |
|-----------|---------|----------|---------|----------|-------|
| `.claude/` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `docs/` | ✅ | ✅ | Partial | ✅ | ✅ |
| `specs/` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `src/` | ❌ | ✅ | ✅ | ✅ | ✅ |
| `tests/` | ❌ | ❌ | ✅ | ✅ | ✅ |
| `database/` | ❌ | backend | ✅ | ✅ | ✅ |
| `infra/` | ❌ | tech-leader | ❌ | ❌ | ❌ |
| `logs/` | ❌ | ❌ | ❌ | ✅ | ✅ |
| `.env*` | ❌ | ❌ | ❌ | ❌ | ❌ |

## Role Groups and Responsible Agents

### Planner (Planning & Analysis)
| Agent | Primary Output |
|-------|----------------|
| req-analyzer | `docs/requirements/` |
| spec-planner | `specs/features/` |

### Designer (Design)
| Agent | Primary Reference | Primary Output |
|-------|-------------------|----------------|
| tech-leader | `src/` (structure), `infra/` | `docs/architecture/` |
| frontend-designer | `src/[frontend]/` | `specs/designs/frontend/` |
| backend-designer | `src/api/`, `src/services/` | `specs/designs/backend/` |

### Builder (Implementation)
| Agent | Primary Reference | Primary Output |
|-------|-------------------|----------------|
| code-builder | `specs/`, `docs/api/` | `src/`, `tests/` |

### Reviewer (Review & Maintenance)
| Agent | Primary Reference | Primary Output |
|-------|-------------------|----------------|
| code-reviewer | `src/`, `tests/`, `specs/` | Review comments |
| code-debugger | `src/`, `tests/`, `logs/` | Fix proposals, Bug reports |

### Guide (Navigation)
| Agent | Primary Reference | Primary Output |
|-------|-------------------|----------------|
| code-guide | All (read-only) | Explanations, Guidance |
| general-purpose | All | Task-dependent |

## CLAUDE.md Design Principles

### Bad Example (Bloated)

```markdown
# CLAUDE.md

## Project Overview
This project is... (long explanation)

## Technology Stack
- React 18
- TypeScript 5
- ... (detailed technology list)

## Coding Standards
### Naming Conventions
- Variable names in camelCase
- Class names in PascalCase
... (detailed rules)

## API Design Guidelines
...

## Testing Policy
...

## Deployment Procedures
...
```

### Good Example (Minimal)

```markdown
# CLAUDE.md

## Common Principles
- Do not assume unclear points; always confirm
- Do not make judgments/implementations outside your role
- Only read necessary files

## Role Assignment
- Operate as one of: Planner / Designer / Builder / Reviewer / Guide
- See `.claude/agents/*.md` for details of each role

## Reference Rules
- Reference to confidential information (.env*, credentials*, secrets*) is prohibited
- Strictly follow each role's reference scope

## Prohibited Actions
- Destructive changes without confirmation
- Performing work beyond assigned role
```

## File Placement Best Practices

### 1. Separation of Confidential Information
```
# Files that should never be referenced
.env
.env.local
.env.production
credentials.json
secrets/
```

### 2. Structured Specifications
```
specs/
├── features/           # Feature specifications (spec-planner output)
│   ├── auth.md
│   └── payment.md
└── designs/            # Design documents
    ├── frontend/       # frontend-designer output
    │   └── components.md
    └── backend/        # backend-designer output
        ├── api.md
        └── database.md
```

### 3. Hierarchical Documentation
```
docs/
├── requirements/       # For Planner
│   ├── functional.md
│   └── non-functional.md
├── architecture/       # For Designer
│   └── overview.md
├── api/                # For Builder
│   └── endpoints.md
└── operations/         # For operations team (outside agent reference)
    └── runbook.md
```

## Implementing Reference Restrictions

Add the following at the beginning of each subagent file:

```markdown
## Reference Scope

### Allowed to Reference
- `docs/**`
- `specs/**`
- `.claude/**`

### Prohibited to Reference
- `src/**`
- `infra/**`
- `.env*`
```

This allows agents to recognize their reference scope and avoid unnecessary file access.
