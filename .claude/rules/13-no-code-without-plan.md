---
description: NEVER write implementation code until all plans are approved AND user explicitly says to start coding
globs: "*.{ts,js,py,java,go,rs,tsx,jsx,vue,svelte}"
---

# Rule: No Code Without Approved Plan

## The Two Gates

### Gate 1: All Plans Must Be Approved
Before ANY implementation code can be written:
- Master Plan must be Approved
- ALL sub-plans must be Approved
- User must have reviewed and approved each one

### Gate 2: Explicit User Command
Even with all plans approved, DO NOT write code until user says one of:
- "ابدا برمجة" / "يلا نبدا" / "يلا كود"
- "start coding" / "begin implementation" / "let's code"
- "implement" / "start building"
- `/implement` command

## What IS Allowed Before Approval
- Writing plan documents (MASTER-PLAN.md, sub-plans)
- Writing design documents (DESIGN.md)
- Creating ADRs
- Editing and updating plans
- Research and documentation
- Answering questions about the design

## What is NOT Allowed Before Approval
- Creating source code files (.ts, .js, .py, .java, .go, etc.)
- Running npm init, pip install, or any package installation
- Creating Dockerfiles or CI configs
- Creating database migrations
- ANY file that is part of the actual implementation

## After "Start Coding"
1. Generate IMPLEMENTATION-ROADMAP.md first
2. Follow the roadmap step by step
3. Track progress against the roadmap
4. Update roadmap as implementation progresses

## WHY This Rule Exists
- Prevents wasted effort building the wrong thing
- Ensures the user has full visibility into what will be built
- Allows course correction before any code is written
- Large projects need a clear plan to avoid getting lost
