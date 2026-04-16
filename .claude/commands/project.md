# /project - Project Management

Manage projects within this plugin. Track multiple system design projects.

> **Location**: Projects live in `projects/<name>/` at the **workspace root** (outside `.claude/`).
> This keeps your private project data separate from the plugin source and is automatically git-ignored.

## Usage
```
/project add <name>           # Create a new project
/project status               # Show all projects and their status
/project status <name>        # Show specific project status
/project update <name>        # Update project status
/project list                 # List all projects
/project switch <name>        # Set active project context
/project archive <name>       # Archive a completed project
/project template <name>      # Convert completed project to reusable template (see project-template.md)
/project template list        # List available templates
/project template use <t> <p> # Create new project from template
/project deps                 # Show dependency graph (see project-deps.md)
/project deps set-core <name> # Mark project as core/shared
/project deps add <p> --depends-on <core>  # Add dependency
/project deps check <name>    # Verify dependency alignment
/project docs                 # List all documents in current project
/project docs add <file>      # Add a document to the right folder
/project docs read <file>     # Read and summarize a project document
```

## Complexity Level (NEW)

When creating a project, ASK the user to pick a complexity level. This sets the depth
of design, planning, and scaffolding:

| Level | Use When | What You Get |
|-------|---------|-------------|
| **Simple** | Quick MVP, small tool, prototype | Front + Back only. Skip heavy planning. Code fast. |
| **Medium** | Feature-rich app, moderate scale | Front + Back + Auth + DB + Basic CI + Tests |
| **Complex** | Production SaaS, distributed system, multi-tenant | Full 3-gate workflow, all 10 plans, infra, monitoring, security |

**How to pick (ASK the user):**

```
Before I create the project, pick a complexity level:

  1. Simple   - Just frontend + backend. Fast build. Minimal planning.
  2. Medium   - Full-stack app with auth, DB, tests, basic CI/CD.
  3. Complex  - Production system: all 10 plans, microservices, monitoring, security, multi-tenancy.

Which one?
```

Save the choice in `PROJECT.md` under `Complexity`. All subsequent commands
(`/design`, `/plan create`, `/implement`) adapt their output based on this level.

## Project Documents

### /project docs
Lists ALL documents across all project folders:
```
## Documents: university-system

### Incoming (from client)
  requirements/
    - client-requirements.pdf (2.3 MB, added 2026-04-13)
    - updated-scope.docx (500 KB, added 2026-04-14)
  mockups/
    - dashboard-wireframe.png (1.1 MB)
  contracts/
    - service-agreement.pdf (340 KB)

### Knowledge (domain research)
  kuka-robot/docs/
    - krl-manual.pdf (5.2 MB)
  plc-siemens/docs/
    - s7-datasheet.pdf (2.1 MB)

### Discovery (research outputs)
  research/
    - opensource-options.md
    - tech-comparison.md
  diagrams/
    - architecture.md (Mermaid)
    - data-flow.md (Mermaid)

### Generated Docs (auto-generated with code)
  docs/
    - API.md
    - DATABASE.md
    - README.md
```

### /project docs add <file>
Adds a document to the appropriate folder. ASK where it belongs:
1. "Is this from the client?" -> `incoming/requirements/` or `incoming/mockups/` etc.
2. "Is this domain documentation?" -> `knowledge/<topic>/docs/`
3. "Is this a reference?" -> `incoming/references/`

After adding, READ the file and extract key information:
- If PDF: Extract text, summarize main points, save summary in DISCUSSION.md
- If requirements doc: Extract requirements, update requirements-draft.md
- If mockup: Note the screens/flows shown, update DISCUSSION.md
- If contract: Note scope, timeline, constraints, update constraints in PROJECT.md

### /project docs read <file>
Read a document and provide:
1. Summary of contents
2. Key points extracted
3. How it affects the design (requirements, constraints, etc.)
4. Suggest updating relevant project files with new information

## Behavior

### /project add <name>

Creates a new project directory at `projects/<name>/` (at workspace root, OUTSIDE `.claude/`).

**Step 1** — ASK for complexity level (Simple/Medium/Complex). See "Complexity Level" above.

**Step 2** — Create the directory structure. Scope varies by complexity:

**Simple** (lightweight):
```
projects/<name>/
  PROJECT.md                    # Project definition (includes Complexity: Simple)
  STATUS.md                     # Current status
  knowledge/                    # Project-specific knowledge
  discovery/                    # Notes + quick diagrams
    DISCUSSION.md
  src/                          # Code goes here (front + back)
```

**Medium** (balanced):
```
projects/<name>/
  PROJECT.md                    # Complexity: Medium
  STATUS.md
  knowledge/                    # Project-specific knowledge
  discovery/
    DISCUSSION.md
    diagrams/
      architecture.md
  plans/                        # Only essential plans (architecture, DB, API, frontend, auth)
  design/
    DESIGN.md
  src/
  tests/
  docs/
```

**Complex** (full workflow — default for production systems):
```
projects/<name>/
  PROJECT.md                    # Complexity: Complex
  STATUS.md
  incoming/                     # Client-provided documents
    requirements/               # Requirements docs from client
    contracts/                  # Agreements, SOWs, proposals
    mockups/                    # UI mockups, wireframes, designs
    references/                 # Any reference material, standards
  discovery/                    # Phase 1: Discussion & understanding
    DISCUSSION.md
    requirements-draft.md
    diagrams/                   # Visual diagrams (Mermaid)
      architecture.md
      data-flow.md
      user-journey.md
      er-diagram.md
    research/
      opensource-options.md
      tech-comparison.md
  knowledge/                    # Project-specific knowledge
    <topic>/
      <topic>.md                # Auto-generated structured knowledge
      docs/                     # User-provided docs for THIS topic
        manual.pdf
        api-reference.md
        examples/
      urls.md
  plans/                        # Phase 2: All 10 sub-plans
    MASTER-PLAN.md
    01-architecture-plan.md
    ... (10 sub-plans)
    IMPLEMENTATION-ROADMAP.md
  design/
    DESIGN.md
    ADR/
  docs/                         # Phase 3: Documentation
  src/
  tests/
  infra/                        # Docker, CI/CD, Terraform
  monitoring/                   # Observability configs
```

### PROJECT.md Template
```markdown
# Project: [Name]
**Created**: [date]
**Complexity**: Simple | Medium | Complex
**Phase**: Discovery | Planning | Implementation | Review | Completed
**Client**: [if applicable]

## Overview
[Brief description]

## Requirements (evolve during discovery)
### Functional Requirements
- FR1: ...

### Non-Functional Requirements
- Scale: ...
- Latency: ...
- Availability: ...

## Constraints
- [Constraint 1]

## Team
- [Role]: [name]

## Timeline
| Milestone | Target Date | Status |
|-----------|------------|--------|
| Discovery Complete | [date] | In Progress |
| Plans Approved | [date] | Pending |
| MVP | [date] | Pending |
| Launch | [date] | Pending |
```

### After creating the project, START the Discovery Phase:
- Ask the user to describe their idea
- Ask clarifying questions about: users, problems, goals, scale, constraints
- Save notes in `discovery/DISCUSSION.md`
- Generate diagrams as understanding develops (save in `discovery/diagrams/`)
- Render those diagrams IN THE CHAT as Mermaid code blocks too (not just files) — see Rule 20
- Research open source when relevant
- Do NOT create plans or write code yet

### /project status
Shows a dashboard of all projects:
```
## Projects Dashboard

| Project | Complexity | Status | Last Updated | Progress |
|---------|-----------|--------|-------------|----------|
| university-system | Complex | In Development | 2026-04-13 | [=====>----] 55% |
| payment-gateway | Medium | In Design | 2026-04-10 | [==>-------] 20% |
| chat-platform | Simple | Active | 2026-04-08 | [=>--------] 10% |
```

### /project switch <name>
Sets the active project context so all commands reference this project.
- /design will design for this project
- /evaluate will evaluate this project
- /implement will implement this project

### Project Workflow (3 Gates — full version for Complex)
```
1. /project add university-system       # Create project -> starts Discovery
2. Discuss the idea freely              # Ask questions, understand, research
3. Diagrams saved to discovery/         # Mermaid architecture, data flow, ERD
4. Say "ابدا plan" or "start planning"   # >>> Gate 1: Discovery -> Planning
5. /plan create university-system       # Master plan + 10 sub-plans
6. /plan show, /plan edit, /plan approve # Review and approve all plans
7. Say "ابدا برمجة" or "start coding"    # >>> Gate 3: Planning -> Implementation
8. /plan implementation                 # Step-by-step roadmap
9. Follow roadmap, build incrementally  # Code + docs generated together
10. /project template university-system  # Save as reusable template
```

**Shortcuts for Simple/Medium:**
- **Simple** → Skip Gate 1 & 2. After `/project add`, design briefly, then code. No formal plans required.
- **Medium** → Gates are optional. Usually: discuss → quick design doc → build. Only create the core 5 plans (architecture, DB, API, frontend, auth).

## Migration from old location

If you have projects in the old `.claude/projects/<name>/` location:
```bash
# Move them to the new root-level projects/ folder
mv .claude/projects projects
```
The plugin will use `projects/` at the workspace root going forward.

## Examples
```
/project add university-management-system
/project add e-commerce-platform
/project status
/project switch university-management-system
/project update university-management-system
```
