---
name: project-manager
description: Project management skill - triggered when managing projects, tracking progress, switching contexts between different system design projects. Projects live in projects/<name>/ at the workspace root (outside .claude/).
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

# Project Manager Skill

You are managing system design projects within this plugin.

## Project Location (IMPORTANT)

Projects live at the **workspace root**, NOT inside `.claude/`:

```
workspace/
├── .claude/              # Plugin source (shared, committed to git)
│   ├── commands/
│   ├── knowledge/        # GLOBAL knowledge (shared across all projects)
│   ├── rules/
│   └── skills/
├── projects/             # USER PROJECTS (private, git-ignored)
│   ├── <project-1>/
│   ├── <project-2>/
│   └── ...
├── CLAUDE.md
└── README.md
```

**Why this split?**
- `.claude/` = plugin source, safe to share & commit
- `projects/` = your private work, never pushed to plugin repo
- Keeps the plugin source clean and upgrades painless

## Complexity Levels

When creating a project, ASK for complexity. This drives everything downstream:

| Level | Scope | Workflow | Files Created |
|-------|-------|---------|---------------|
| **Simple** | Front + Back only | Skip formal gates, design briefly, code | ~5 files |
| **Medium** | Full-stack + Auth + DB + Tests | Gates optional, 5 core plans | ~15 files |
| **Complex** | Production SaaS, distributed | Full 3-gate workflow, 10 plans, infra | ~40 files |

Store in `PROJECT.md`:
```markdown
**Complexity**: Simple | Medium | Complex
```

All downstream commands (`/design`, `/plan`, `/implement`, `/evaluate`) read this
and scale their output accordingly.

## Project Structure (per complexity)

### Simple
```
projects/<name>/
  PROJECT.md         # Complexity: Simple
  STATUS.md
  knowledge/         # Project-specific knowledge files
  discovery/
    DISCUSSION.md
  src/               # Code directly here (front + back)
```

### Medium
```
projects/<name>/
  PROJECT.md         # Complexity: Medium
  STATUS.md
  knowledge/         # Project-specific knowledge
  discovery/
    DISCUSSION.md
    diagrams/
  plans/             # 5 core plans only
    01-architecture-plan.md
    02-database-plan.md
    03-api-plan.md
    05-frontend-plan.md
    06-auth-plan.md
  design/
    DESIGN.md
  src/
  tests/
  docs/
```

### Complex (default full structure)
```
projects/<name>/
  PROJECT.md         # Complexity: Complex
  STATUS.md
  incoming/
    requirements/
    contracts/
    mockups/
    references/
  discovery/
    DISCUSSION.md
    requirements-draft.md
    diagrams/
    research/
  knowledge/         # Project-specific knowledge
  plans/             # All 10 sub-plans
    MASTER-PLAN.md
    01-architecture-plan.md
    ...
    10-monitoring-plan.md
    IMPLEMENTATION-ROADMAP.md
  design/
    DESIGN.md
    ADR/
  docs/
  src/
  tests/
  infra/
  monitoring/
```

## Creating a New Project

When `/project add <name>` is invoked:

1. **ASK for complexity level** (Simple | Medium | Complex)
2. **Create the full project directory** based on complexity (see above)
3. **Generate PROJECT.md** with the complexity choice recorded
4. **Create STATUS.md** with initial tracking
5. **Enter Discovery Phase**:
   - Ask the user to describe their idea
   - Ask clarifying questions (tailored to complexity — fewer for Simple)
   - Save notes to `discovery/DISCUSSION.md`
   - Generate Mermaid diagrams — render them inline in chat AND save to files
   - For Simple: generate ONE quick architecture sketch
   - For Medium: architecture + data flow
   - For Complex: architecture + data flow + user journey + ER diagram
   - Do NOT create plans or code

## The 3-Gate System (Complex only; relaxed for Medium, skipped for Simple)

| Phase | What Happens | Ends When |
|-------|-------------|-----------|
| **Discovery** | Free discussion, questions, diagrams, research | User says "ابدا plan" |
| **Planning** | Master plan + sub-plans, review, approve | User says "ابدا برمجة" |
| **Implementation** | Code following roadmap, docs alongside | Project complete |

- **Simple**: No gates. Go from idea → design → code quickly.
- **Medium**: Gate 1 (start planning) recommended. Gate 3 (start coding) flexible.
- **Complex**: All 3 gates strictly enforced.

Track the current phase in STATUS.md and PROJECT.md.

## Project Status Dashboard

When `/project status` is invoked:
1. Scan all directories in `projects/` (NOT `.claude/projects/`)
2. Read each project's STATUS.md and PROJECT.md (for complexity)
3. Display dashboard with all projects, complexity, status, and progress

## Context Switching

When `/project switch <name>` is invoked:
1. Load the project's context (PROJECT.md, DESIGN.md)
2. Load the complexity level — affects how all subsequent commands behave
3. Load project-specific knowledge from `projects/<name>/knowledge/`
4. Set as active project

## Knowledge Scopes

Two knowledge scopes exist:

| Scope | Location | Used For |
|-------|---------|----------|
| **Global** | `.claude/knowledge/domains/` | Cross-project knowledge (e.g., HL7 FHIR, OPC-UA, FIX protocol) |
| **Project** | `projects/<name>/knowledge/` | Project-specific knowledge (e.g., this client's legacy API) |

Resolution order when looking up knowledge:
1. Check project-specific knowledge first
2. Fall back to global knowledge
3. Build new knowledge (via `/knowledge build`) if neither has it

## Progress Tracking

STATUS.md format:
```markdown
# Status: [Project Name]
**Last Updated**: [date]
**Complexity**: Simple | Medium | Complex
**Overall Progress**: [percentage]
**Current Phase**: Discovery | Planning | Implementation | Testing | Review | Done

## Completed
- [x] Requirements gathered
- [x] High-level design approved

## In Progress
- [ ] Database schema design (70%)
- [ ] API implementation (30%)

## Upcoming
- [ ] Integration testing
- [ ] Security review
- [ ] Deployment setup

## Blockers
- [Blocker description if any]

## Notes
- [Any relevant notes]
```

## Multi-Project Awareness
- Keep track of all active projects
- Prevent context leakage between projects
- Allow cross-project reference when explicitly requested

## Project Templates

Templates live in `.claude/templates/<template-name>/`:
```
TEMPLATE.md        # Metadata, description, variables, usage
ARCHITECTURE.md    # Generalized architecture (placeholders)
SCHEMA.md          # Generalized data model
API.md             # Generalized API design
STACK.md           # Technology stack with justification
INFRASTRUCTURE.md  # Deployment and infrastructure
CHECKLIST.md       # Customized checklist
```

Converting a project to template:
1. Read all project files (from `projects/<name>/`)
2. Extract reusable patterns
3. Replace specifics with `{{PLACEHOLDERS}}`
4. Save to `.claude/templates/`
5. Record lessons learned from original project

Using a template:
1. Load template, ask customization questions
2. Replace placeholders with user answers
3. Generate full project structure at `projects/<new-name>/`
4. Pre-fill DESIGN.md from template

## Project Dependencies

Dependencies are tracked in `DEPS.md` inside each project:

### Core Projects
- Provide shared APIs, data models, auth, events
- Define contracts that dependents must follow
- Breaking changes require version bumps and dependent notification

### Dependent Projects
- Must follow core's API contracts and auth patterns
- Can have their own local models and APIs
- Must handle core service unavailability (fallbacks)

### Dependency Types
- **extends**: Same codebase, builds on core modules
- **integrates**: Uses core's APIs as external service
- **shares-data**: Accesses core's database/data store
- **inherits-patterns**: Follows core's patterns independently

### When Dependencies Are Set
- `/design` auto-loads core constraints for dependent projects
- `/evaluate` checks dependency alignment
- `/implement` generates integration code (API clients, event handlers)
- `/schema` distinguishes shared vs local tables
- `/security` validates auth alignment with core

## Migration from Old Location

If projects exist at `.claude/projects/`, migrate them:
```bash
mv .claude/projects projects
```
All plugin commands now read from `projects/<name>/` at the workspace root.
