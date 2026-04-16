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

## Onboarding Questions (ALWAYS ask in this order)

When creating a project, ALWAYS ask these questions in order **before** any
further design or code. Save answers in `PROJECT.md`.

### Question 1: Complexity Level

| Level | Use When | What You Get |
|-------|---------|-------------|
| **Simple** | Quick MVP, small tool, prototype | Front + Back only. Skip heavy planning. Code fast. |
| **Medium** | Feature-rich app, moderate scale | Front + Back + Auth + DB + Basic CI + Tests |
| **Complex** | Production SaaS, distributed system, multi-tenant | Full 3-gate workflow, all 10 plans, infra, monitoring, security |

Ask:
```
Question 1/2: What's the complexity level of this project?

  1. Simple   - Just frontend + backend. Fast build. Minimal planning.
  2. Medium   - Full-stack app with auth, DB, tests, basic CI/CD.
  3. Complex  - Production system: all 10 plans, microservices, monitoring, security, multi-tenancy.

Which one?
```

### Question 2: User Skill Level (NEW)

This controls HOW MANY questions are asked and HOW MANY decisions Claude makes automatically.

| Level | Behavior |
|-------|---------|
| **Non-Programmer** | Claude picks almost everything. Asks only high-level product questions (what should the app do, who are users). Never asks about frameworks, libraries, DBs — picks sensible defaults. |
| **Beginner** | Claude picks most technical choices. Explains them as it goes. Asks only about the product and one or two clear preferences. |
| **Intermediate** | **Claude MUST ASK the user** for: frontend framework, backend framework, database, deployment OS (Windows/Linux), UI library preference, visual style. User has full control. |
| **Professional** | Full technical dialogue. Asks about specifics like ORM choice, caching strategy, message queue, auth flow, infra stack. Shows trade-offs and expects opinions. |

Ask:
```
Question 2/2: What's your programming skill level?

  1. Non-Programmer  - I'll pick almost everything for you. You describe what you want.
  2. Beginner         - I'll pick most tech choices and explain them.
  3. Intermediate     - I'll ask you: frontend, backend, DB, OS (Win/Linux), UI style, library preferences.
  4. Professional     - Full technical dialogue; I'll present trade-offs and expect opinions.

Which one?
```

Save BOTH answers in `PROJECT.md`:

```markdown
**Complexity**: Simple | Medium | Complex
**User Skill**: Non-Programmer | Beginner | Intermediate | Professional
```

All downstream commands read BOTH fields. See Rule 21 (complexity) and Rule 22 (skill-level).

### Stack Questions (for Intermediate + Professional only)

After the 2 onboarding questions, if User Skill is **Intermediate** or **Professional**,
ASK these additional questions BEFORE any design or code. Claude picks for Non-Programmer
and Beginner users automatically.

```
Since you picked Intermediate/Professional, I need a few more choices so I can prep
everything correctly:

Q3: Frontend framework?
  1. React (with Vite)         - Most popular, large ecosystem
  2. Next.js                    - React + SSR + file routing
  3. Vue.js (with Vite)        - Clean syntax, simpler learning curve
  4. Nuxt                       - Vue + SSR
  5. Angular                    - Enterprise, batteries included
  6. Svelte / SvelteKit         - Lean, compile-time
  7. None (API only / headless)

Q4: Backend framework?
  1. Node.js + NestJS          - Opinionated, TypeScript-first, DI
  2. Node.js + Express/Fastify - Minimal, flexible
  3. Python + FastAPI          - Type hints, auto docs, async
  4. Python + Django           - Batteries included, ORM, admin
  5. Java + Spring Boot        - Enterprise, mature
  6. Go + Gin/Fiber            - Performance, simple
  7. C# + .NET                 - Enterprise, Windows-friendly
  8. Rust + Axum               - Performance + safety

Q5: Database?
  1. PostgreSQL                - Default for most cases
  2. MySQL / MariaDB           - Wide hosting support
  3. MongoDB                   - Schema-flexible documents
  4. SQLite                    - Dev / small / embedded
  5. Supabase (Postgres + BaaS)
  6. Firebase                  - Mobile-first, realtime

Q6: Deployment target OS?
  1. Linux                     - Standard cloud (AWS, GCP, Azure, Docker)
  2. Windows Server            - IIS, Windows-specific tooling
  3. Both / containerized      - Docker images, runs anywhere
  4. Serverless / PaaS only    - Vercel / Netlify / Render / Railway

Q7: UI style?
  1. Modern & Minimal          - Clean, lots of whitespace (SaaS-style)
  2. Corporate & Professional  - Structured, formal
  3. Playful & Colorful        - Rounded, gradients
  4. Dark & Sleek              - Dark-first, glassmorphism
  5. Data-Dense                - Bloomberg-like, information-heavy
  6. Material Design           - Google style
  7. Apple-like                - Clean, premium

Q8: UI library preference?
  Based on your frontend:
  - React    →  shadcn/ui  |  Ant Design  |  MUI  |  Chakra  |  Mantine  |  NextUI
  - Vue      →  PrimeVue   |  Vuetify     |  Element Plus   |  Naive UI  |  Quasar
  - Angular  →  Angular Material  |  PrimeNG  |  NG-ZORRO
  - Svelte   →  Skeleton   |  Carbon       |  Flowbite
  - No preference → I'll pick the best fit

Please pick Q3-Q8 now (or say "you decide" for any of them).
```

For **Non-Programmer** and **Beginner**, SKIP the stack questions and pick sensible
defaults, then list them briefly so the user knows what they're getting:

```
Based on your skill level, I've picked defaults:
- Frontend: Next.js + shadcn/ui (modern, fast, easy)
- Backend: Node.js + NestJS
- Database: PostgreSQL
- Deploy: Linux (Docker) — works anywhere
- Style: Modern & Minimal

Want to change anything? (otherwise I'll continue)
```

Save all answers in `PROJECT.md`:

```markdown
## Stack
- Frontend: <answer>
- Backend: <answer>
- Database: <answer>
- Deploy OS: Linux | Windows | Both
- UI Library: <answer>
- Visual Style: <answer>
```

### Question 9: App Scope (ALL skill levels — determines page inventory)

After the stack, always ask:

```
Question 9: What does your app include? (check all that apply)

  [ ] User accounts (login/register/profile)
  [ ] Admin panel (manage users, roles, audit logs)
  [ ] Main dashboard with KPIs & charts
  [ ] Core entities to manage: _______  (e.g. Products, Orders, Customers, Projects)
  [ ] Billing / subscriptions
  [ ] API for third parties
  [ ] Notifications (email, push, in-app)
  [ ] Search (global Cmd-K)
  [ ] File uploads / attachments
  [ ] Comments / discussions
  [ ] Audit log (who did what)
  [ ] Multi-tenancy (organizations/workspaces)
  [ ] Internationalization (multiple languages)
  [ ] Dark mode
  [ ] RTL (Arabic/Hebrew)
  [ ] Public marketing pages
```

### Question 10: UX / Dashboard Feel

```
Question 10: UX preferences (pick one for each)

Data density: Compact  |  Normal  |  Spacious
Default theme: Light  |  Dark  |  System preference
Dashboard style: Cards & charts  |  Table-heavy  |  Visual / illustrative
Empty-state feel: Friendly illustrations  |  Minimal text  |  Step-by-step onboarding
```

This drives `/pages`, `/rbac`, `/ux-kit`, `/frontend design`.

### What Happens Next

After onboarding (Q1-Q10 answered), Claude automatically runs:
1. `/pages` — builds PAGES.md with every page the app needs
2. `/rbac` — if user accounts picked, builds RBAC.md + schema
3. `/ux-kit` — builds UX-KIT.md with states, components, a11y rules
4. `/frontend design` — asks about visual style, picks UI library

These produce a complete spec before any code is written.

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
**User Skill**: Non-Programmer | Beginner | Intermediate | Professional
**Phase**: Discovery | Planning | Implementation | Review | Completed
**Client**: [if applicable]

## Overview
[Brief description]

## Stack (filled from onboarding questions)
- Frontend: [framework]
- Backend: [framework]
- Database: [DB]
- Deploy OS: Linux | Windows | Both
- UI Library: [library]
- Visual Style: [style]

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

## Milestones
| # | Milestone | Target Date | Status | Validation |
|---|-----------|------------|--------|-----------|
| M1 | Discovery Complete | [date] | In Progress | - |
| M2 | Plans Approved | [date] | Pending | - |
| M3 | MVP | [date] | Pending | - |
| M4 | Launch | [date] | Pending | - |
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
