# /project - Project Management

Manage projects within this plugin. Track multiple system design projects.

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
```

## Behavior

### /project add <name>

Creates a new project directory at `.claude/projects/<name>/` with:

```
<name>/
  PROJECT.md                    # Project definition
  STATUS.md                     # Current status and progress
  discovery/                    # Phase 1: Discussion & understanding
    DISCUSSION.md               # Notes, questions, answers, decisions
    requirements-draft.md       # Evolving requirements (refined during discussion)
    diagrams/                   # Visual diagrams (Mermaid + HTML)
      architecture.md           # System architecture (Mermaid)
      data-flow.md              # Data flow diagram (Mermaid)
      user-journey.md           # User journey map (Mermaid)
      er-diagram.md             # Entity relationship (Mermaid)
    research/                   # Research done during discovery
      opensource-options.md     # Open source alternatives found
      tech-comparison.md        # Technology comparisons
  knowledge/                    # Domain-specific knowledge (built with /knowledge build)
    <topic>/                    # One folder per topic
      <topic>.md                # Auto-generated structured knowledge
      docs/                     # User-provided docs for THIS topic
        manual.pdf              # PDF manuals, datasheets
        api-reference.md        # API docs
        datasheet.pdf           # Hardware datasheets
        examples/               # Code examples, configs
      urls.md                   # Bookmarked reference URLs
  plans/                        # Phase 2: Structured planning (after "start planning")
    MASTER-PLAN.md
    01-architecture-plan.md
    ... (10 sub-plans)
    IMPLEMENTATION-ROADMAP.md
  design/                       # Detailed design documents
    DESIGN.md                   # Full system design
    ADR/                        # Architecture Decision Records
  docs/                         # Phase 3: Documentation (generated with code)
```

### PROJECT.md Template
```markdown
# Project: [Name]
**Created**: [date]
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
- Generate diagrams as understanding develops
- Research open source when relevant
- Do NOT create plans or write code yet

### /project status
Shows a dashboard of all projects:
```
## Projects Dashboard

| Project | Status | Last Updated | Progress |
|---------|--------|-------------|----------|
| university-system | In Development | 2026-04-13 | [=====>----] 55% |
| payment-gateway | In Design | 2026-04-10 | [==>-------] 20% |
| chat-platform | Active | 2026-04-08 | [=>--------] 10% |
```

### /project switch <name>
Sets the active project context so all commands reference this project.
- /design will design for this project
- /evaluate will evaluate this project
- /implement will implement this project

### Project Workflow (3 Gates)
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

## Examples
```
/project add university-management-system
/project add e-commerce-platform
/project status
/project switch university-management-system
/project update university-management-system
```
