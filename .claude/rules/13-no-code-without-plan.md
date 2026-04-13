---
description: 3-gate workflow - Discovery first, then Planning, then Code. Each phase requires explicit user command to transition.
globs: "*.{ts,js,py,java,go,rs,tsx,jsx,vue,svelte}"
---

# Rule: 3-Gate Workflow

## The Three Gates

### Gate 1: Discovery -> Planning
User must say "ابدا plan" / "start planning" / "خطط" to move from discovery to planning.
During Discovery:
- Discuss the idea freely, ask questions, understand deeply
- Save notes in discovery/DISCUSSION.md
- Generate diagrams in discovery/diagrams/ (Mermaid)
- Research open source in discovery/research/
- Draft requirements in discovery/requirements-draft.md
- NO plan files, NO code files

### Gate 2: All Plans Approved
Before moving to implementation:
- Master Plan must be Approved
- ALL sub-plans must be Approved
- User must have reviewed and approved each one

### Gate 3: Planning -> Implementation
User must say "ابدا برمجة" / "start coding" / "يلا كود" to start writing code.
Even with all plans approved, DO NOT write code until user says so.

## What IS Allowed in Each Phase

| Phase | Allowed | NOT Allowed |
|-------|---------|-------------|
| **Discovery** | Discussion, questions, diagrams (Mermaid), research, draft requirements | Plans, design docs, code, package installs |
| **Planning** | Plan documents, design docs, ADRs, research | Source code, Dockerfiles, CI configs, migrations |
| **Implementation** | Everything: code, tests, Docker, CI, docs | - |

## WHY This Rule Exists
- Discovery ensures the idea is understood before planning
- Planning ensures the approach is agreed before coding
- 3 gates prevent wasted effort and ensure user has full control
- Each phase has a clear purpose and clear deliverables
