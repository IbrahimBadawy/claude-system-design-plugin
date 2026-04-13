# /quickstart - Getting Started Guide

Interactive guide for new users. Shows the most important commands and the recommended workflow.

## Usage
```
/quickstart                    # Show the getting started guide
/quickstart commands           # Show top 10 most-used commands
/quickstart workflow           # Show the recommended workflow
```

## Behavior

When invoked, display this guide:

```markdown
# Welcome to System Design Architect!

## Your First 5 Minutes

### 1. Create a project
/project add my-system

### 2. Search for existing solutions first
/opensource my-system-type

### 3. Design the system (4-step framework)
/design my-system

### 4. Create plans (master + 10 sub-plans)
/plan create my-system

### 5. Review and approve plans
/plan show          # Overview
/plan show 03       # View specific plan
/plan edit 03       # Edit if needed
/plan approve all   # Approve when satisfied

### 6. Start coding (only after approval)
Say: "start coding" or "ابدا برمجة"
/plan implementation   # Get step-by-step roadmap

## Top 10 Commands (Start Here)

| # | Command | What It Does |
|---|---------|-------------|
| 1 | /design <system> | Full system design from scratch |
| 2 | /plan create <project> | Generate master plan + 10 sub-plans |
| 3 | /implement --frontend react --backend nestjs | Start coding |
| 4 | /evaluate <system> | Evaluate an existing system |
| 5 | /opensource <system> | Find open source alternatives |
| 6 | /research <topic> | Research latest technologies |
| 7 | /frontend design | Design UI (asks your preferences) |
| 8 | /schema <entity> | Design database schema |
| 9 | /docs generate | Generate full documentation |
| 10 | /checklist | Full design review checklist |

## All 32 Commands by Category

**Design**: /design, /domain, /schema, /api, /gateway, /tenancy, /estimate, /tradeoff, /scale, /adr
**Quality**: /evaluate, /improve, /security, /perf, /debt, /privacy, /checklist, /failure
**Build**: /implement, /frontend, /backend libs, /cicd, /monitor, /cost, /postmortem
**Plan**: /plan create/show/edit/approve/implementation
**Projects**: /project add/status/deps/template
**Research**: /research, /opensource, /docs

## Key Rules (Always Active)
- No code without requirements and design
- No implementation without approved plans
- Research docs before using any library
- Security, accessibility, and performance by default
- Auto-documentation alongside code

## Tips
- Say things naturally: "design a chat system" activates the design skill
- Say "ابدا برمجة" or "start coding" when ready to implement
- Use /plan show anytime to check your progress
- Use /docs status to see if documentation needs updating
```
