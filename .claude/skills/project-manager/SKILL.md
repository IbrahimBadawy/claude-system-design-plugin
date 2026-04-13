---
name: project-manager
description: Project management skill - triggered when managing projects, tracking progress, switching contexts between different system design projects.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

# Project Manager Skill

You are managing system design projects within this plugin.

## Project Structure

Each project lives in `.claude/projects/<project-name>/`:
```
<project-name>/
  PROJECT.md    # Project definition, requirements, team, timeline
  DESIGN.md     # System design document
  STATUS.md     # Current status and progress tracking
  ADR/          # Architecture Decision Records
    ADR-001.md
    ADR-002.md
```

## Creating a New Project

When `/project add <name>` is invoked:

1. Create the project directory structure
2. Generate PROJECT.md with template:
   - Project name, date, status
   - Overview section (ask user to describe)
   - Requirements section (functional + non-functional)
   - Constraints section
   - Team section
   - Timeline with milestones
3. Create empty DESIGN.md with link to /design command
4. Create STATUS.md with initial tracking
5. Create ADR/ directory

## Project Status Dashboard

When `/project status` is invoked:
1. Scan all directories in `.claude/projects/`
2. Read each project's STATUS.md
3. Display dashboard with all projects, their status, and progress

## Context Switching

When `/project switch <name>` is invoked:
1. Load the project's context (PROJECT.md, DESIGN.md)
2. Set as active project
3. All subsequent commands reference this project's context

## Progress Tracking

STATUS.md format:
```markdown
# Status: [Project Name]
**Last Updated**: [date]
**Overall Progress**: [percentage]
**Current Phase**: Design | Implementation | Testing | Review | Done

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
1. Read all project files
2. Extract reusable patterns
3. Replace specifics with `{{PLACEHOLDERS}}`
4. Save to `.claude/templates/`
5. Record lessons learned from original project

Using a template:
1. Load template, ask customization questions
2. Replace placeholders with user answers
3. Generate full project structure
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
