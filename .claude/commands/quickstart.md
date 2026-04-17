# /quickstart - Getting Started Guide

Interactive guide for new users. Shows the most important commands and the recommended workflow.

## Usage
```
/quickstart                    # Show the getting started guide
/quickstart commands           # Show top 10 most-used commands
/quickstart workflow           # Show the recommended workflow
/quickstart complexity         # Explain complexity levels
```

## Behavior

When invoked, display this guide:

```markdown
# Welcome to System Design Architect!

## Where does stuff live?

    workspace/
    ├── .claude/            # Plugin (shared, committed)
    │   ├── knowledge/      # GLOBAL knowledge (reusable across projects)
    │   └── ...
    └── projects/           # YOUR projects (private, git-ignored)
        └── <name>/
            ├── knowledge/  # PROJECT-specific knowledge
            ├── discovery/
            ├── plans/
            └── src/

Notice: `projects/` is at the workspace root, NOT inside `.claude/`.

## Pick a complexity level upfront

Every project has a complexity. Bigger projects get more ceremony:

  Simple   — Just frontend + backend. Fast build. Minimal planning.
  Medium   — Full-stack with auth, DB, tests, basic CI/CD.
  Complex  — Production system: 10 plans, microservices, monitoring, security.

Use `/complexity` at any time to view/change it.

## Your First 5 Minutes

### 1. Create a project (pick complexity)
    /project add my-system
    # -> Claude asks: Simple, Medium, or Complex?

### 2. Search for existing solutions first (recommended)
    /opensource my-system-type

### 3. Design the system (scales to complexity)
    /design my-system

### 4. Create plans (count depends on complexity)
    /plan create my-system
    # Simple  -> 0 or 1 plan
    # Medium  -> 5 core plans
    # Complex -> 10 plans + MASTER + ROADMAP

### 5. Review and approve plans (Medium/Complex)
    /plan show          # Overview
    /plan show 03       # View specific plan
    /plan edit 03       # Edit if needed
    /plan approve all   # Approve when satisfied

### 6. Start coding
    Say: "start coding" or "ابدا برمجة"
    /plan implementation   # Get step-by-step roadmap

## Top 12 Commands

| # | Command | What It Does |
|---|---------|-------------|
| 1 | /project add <name> | Create a new project (asks complexity) |
| 2 | /complexity | View or change project complexity |
| 3 | /design <system> | Full system design (scales to complexity) |
| 4 | /plan create <project> | Generate plans (5 or 10 based on complexity) |
| 5 | /implement --frontend react --backend nestjs | Start coding |
| 6 | /evaluate <system> | Evaluate an existing system |
| 7 | /opensource <system> | Find open source alternatives |
| 8 | /research <topic> | Research latest technologies |
| 9 | /knowledge build <topic> --global | Build reusable knowledge |
| 10 | /knowledge build <topic> --project | Build project-specific knowledge |
| 11 | /frontend design | Design UI (asks your preferences) |
| 12 | /docs generate | Generate full documentation |

## All 43+ Commands by Category

**Design**: /design, /domain, /schema, /api, /gateway, /tenancy, /estimate, /tradeoff, /scale, /adr
**Quality**: /evaluate, /improve, /security, /perf, /debt, /privacy, /checklist, /failure
**Build**: /implement, /frontend, /backend libs, /cicd, /monitor, /cost, /postmortem
**Plan**: /plan create/show/edit/approve/implementation
**Projects**: /project add/status/deps/template, /complexity
**Knowledge**: /knowledge build/list/show/promote/copy --global/--project
**Research**: /research, /opensource, /docs

## Key Rules (Always Active)

1. No code without requirements and design (complexity-adjusted)
2. No implementation without approved plans (Complex only)
3. Research docs before using any library
4. Security, accessibility, and performance by default
5. Auto-documentation alongside code
6. **Diagrams as ASCII in chat + Mermaid in files**; code as fenced blocks — Rule 20
7. **Complexity-aware workflow** — Simple projects skip ceremony — Rule 21

## Tips

- Say things naturally: "design a chat system" activates the design skill
- Say "ابدا برمجة" or "start coding" when ready to implement
- Use /complexity suggest if unsure what level fits your project
- Use /plan show anytime to check your progress
- Use /docs status to see if documentation needs updating
- Diagrams will render inline in chat — no need to open files to see them
- Knowledge scopes: `--global` for reusable tech, `--project` for client-specific
```

## Examples

```
/quickstart
/quickstart commands
/quickstart workflow
/quickstart complexity
```
