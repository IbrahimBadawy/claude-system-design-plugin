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

1. **PROJECT.md** - Project definition
```markdown
# Project: [Name]
**Created**: [date]
**Status**: Active | In Design | In Development | In Review | Completed | Archived
**Client**: [if applicable]

## Overview
[Brief description of the project]

## Requirements
### Functional Requirements
- FR1: ...

### Non-Functional Requirements
- Scale: ...
- Latency: ...
- Availability: ...

## Constraints
- [Constraint 1]
- [Constraint 2]

## Team
- [Role 1]: [name]

## Timeline
| Milestone | Target Date | Status |
|-----------|------------|--------|
| Design Complete | [date] | Pending |
| MVP | [date] | Pending |
| Launch | [date] | Pending |

## Notes
[Any additional context]
```

2. **DESIGN.md** - System design document (use /design to fill this)
3. **STATUS.md** - Current progress tracking
4. **ADR/** - Directory for Architecture Decision Records

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

### Project Workflow
1. `/project add university-core` - Create core project
2. `/design university-core` - Design the core system
3. `/project deps set-core university-core` - Mark as core
4. `/project add student-portal` - Create dependent project
5. `/project deps add student-portal --depends-on university-core` - Link them
6. `/design student-portal` - Design (auto-loads core constraints)
7. `/implement student-portal` - Start coding
8. `/project deps check student-portal` - Verify alignment
9. `/project template university-core` - Convert to reusable template when done
10. `/project template use university-core new-college` - Reuse for new client

## Examples
```
/project add university-management-system
/project add e-commerce-platform
/project status
/project switch university-management-system
/project update university-management-system
```
