# System Design Architect - Claude Code Plugin

The most comprehensive system design plugin for Claude Code.
Design, evaluate, plan, and build production-grade distributed systems — from quick MVPs to multi-tenant SaaS.

**147+ files | 57 commands | 31 rules | 14 skills | 27 knowledge files**

---

## What's New in v1.9.2 — Rule 20 correction: ASCII in chat, Mermaid in files

Mermaid diagrams were showing as **raw source code** in Claude Code CLI and
most VS Code panels, not as rendered graphics. Rule 20 now splits the two
surfaces:

- **Chat body** → **ASCII / Unicode box-drawing** (renders everywhere, zero setup)
- **Saved `.md` files** → **Mermaid** (renders in VS Code Preview, claude.ai, GitHub)

ASCII templates now provided in Rule 20 for: architecture, sequence,
tree/hierarchy, state machine, ER-lite, KPI tables. Viewer matrix included
so you know which surface renders what.

11 files updated (CLAUDE.md, 5 commands, 3 skills, Rule 20 itself) so the
whole plugin speaks the same language.

## What's New in v1.9.1 — Multi-Dimensional Permissions (correction)

v1.9.0 hardcoded "5-Dimensional" everywhere based on the SIS example. This
was wrong — different domains need different dimension counts. v1.9.1 fixes
this to be **domain-specific N** where N is determined during discovery.

### 3 universal dimensions + N-3 domain-specific

| Dimension | Universal? | Notes |
|-----------|-----------|-------|
| Organizational | ✓ always | Hierarchical tree (shape varies) |
| Functional | ✓ always | App hierarchy |
| Action | ✓ always | Extensible enum |
| Academic year | Domain (SIS) | Temporal range |
| Semester | Domain (SIS) | Enum set |
| Shift | Domain (hospital/factory) | Enum set |
| Region | Domain (multinational) | Hierarchical |
| Tenant | Domain (multi-tenant SaaS) | Reference |
| Plant / Line | Domain (factory) | Hierarchical |
| Project / Phase | Domain (consulting) | Reference / Temporal |
| Fiscal period | Domain (finance/ERP) | Temporal |
| Legal entity | Domain (regulated) | Reference |
| Resource owner | Domain (CRM) | Reference |

### N by domain

| Domain | N |
|--------|---|
| Simple SaaS | 3 |
| CRM / Hospital / E-commerce | 4 |
| SIS / Retail / Factory / Multi-tenant SaaS / Projects | 5 |
| ERP | 6 |
| Regulated enterprise | 7+ |

Q0 in `/discover permissions` sets N before any other questions. The
plugin generates schema, evaluator, and admin UI for **whatever N the
domain declares**.

## v1.9.0 — Shared Components (§7) + Multi-Dim Permissions (§8)

The canonical architecture spec now includes two new major sections (§7 & §8)
authored by the plugin user. Everything in the plugin conforms.

### §7 — Shared Components & Shared Features

Any capability appearing in 2+ places MUST be factored into shared code at
the appropriate ownership layer:

| Layer | Owned by | Home for |
|-------|---------|---------|
| **Core-level** | Core itself | Cross-cutting: DataTable, Form, Toast, auth, i18n |
| **Domain-level** | Foundation module | Module-family-specific: GradeTable for academic modules |
| **Module-level** | Single module | Module-internal helpers |

- **New command:** `/shared-components` — catalog, create, promote, audit, fix
- **New Rule 31: Shared First** — no copy-paste, no forks, no module-specific
  logic in shared code
- Promotion as deliberate versioned refactor (never an ad-hoc copy)

### §8 — Multi-Dimensional Permissions (domain-specific N)

**Profiles are the primary admin abstraction** — versioned, cloneable.
**Assignment modes**: profile-based / group-based / user-specific (denials win).
**4 canonical reports required**: who-can / what-can-user-do / profiles-with / recent-changes.

**Discovery-phase requirement:** `/discover permissions` asks Q0 (dimension
setup) + Q1-10. `/implement` refuses to scaffold permission-sensitive
modules without answers.

### New Knowledge Files
- `shared-components.md` — 3-layer ownership, promotion, catalog, contracts
- `permissions-model.md` — **N-dimensional** (flexible) DB schema, evaluator, admin UI, reports

---

## v1.8.1 — Formalized Architecture Spec

The modular architecture is now governed by a formal specification
(`architecture-spec.md`). Every modular feature conforms to it.

### Five Integration Roles (was 3)

| Role | Description |
|------|-------------|
| **Core** | Hosts other systems/modules |
| **Module-of** | Plugs into another Core |
| **Standalone** | Runs independently |
| **Bridge** *(new)* | Mediates 2+ independent Cores (translation, sync, event broker) |
| **Dependent** *(new)* | Module-of that requires specific sibling modules on the same Core |

### New Lifecycle Guarantees

- **Symmetric lifecycle** — install/uninstall MUST be inverse operations. CI enforces zero residue over 10 cycles.
- **Upgrade with rollback** — formal `upgrade()` hook with rollback support
- **Graceful degradation** — missing optional siblings don't break the system; declared per-dependency in the manifest
- **Manifest-first development** — manifest authored before code; enforced in CI

### Architecture Spec as Source of Truth

`architecture-spec.md` is now canonical. `modular-architecture.md` is the
practical guide. Rule 29 enforces all 6 design principles from the spec.

---

## v1.8.0 — Modular + Fully Manageable

Two big additions that make apps feel finished instead of "just backend code":

### 1. Functional Taxonomy (every capability classified + manageable)

Every app capability gets classified into one of 5 categories:

| Category | Example | What you get |
|----------|---------|--------------|
| **Features** | "Submit expense", "Apply for leave" | User-facing, permission-gated, toggle-able |
| **Tools** | "Impersonate user", "Bulk import" | Admin-only, audited, type-to-confirm |
| **Tasks** | "Monthly payroll", "Nightly sync" | Scheduled, retry-able, pausable |
| **Services** | "attendance-API", "user-directory" | Versioned, consumer catalog, rate-limited |
| **Flows** | "Onboarding", "Leave approval" | Visual workflow, SLA tracking, instances |

Each category gets an auto-generated admin UI. The whole app is manageable from one
dashboard — toggles, audit logs, scheduler, API catalog, workflow designer.

### 2. Modular Architecture (Core + Modules, plug-and-play)

Every system is built as **Core + Modules**:
- **Core** = identity + auth + event bus + tenant context
- **Modules** = business capabilities with versioned contracts

Same codebase can run:
- **Standalone** (with local adapters)
- As a **core** for other apps (provides APIs/events)
- As a **module** inside a different core (plugs into their identity/auth)

Example: build HR as core → attendance/payroll/portal plug in. OR build attendance
standalone → later it becomes a module inside someone's HR. Both directions work.

### 4 New Commands
- **`/functional-model`** — classify all capabilities, generate admin UIs
- **`/core-modules`** — design core + module architecture
- **`/app-as-module`** — wrap existing app to be pluggable
- **`/integrate`** — wire two apps (core + module) with ACL, event bridge, permission map

### 2 New Rules
- **Rule 29: Modular by Default** — every system is core + modules, no cross-module imports
- **Rule 30: Functional Completeness** — classify everything, generate admin UIs, whole app is manageable

### Auto-approve permissions
Updated `settings.json` with permissive defaults for `.claude/**` and `projects/**`, plus full dev toolchain. No more permission prompts for plugin development.

---

## What Landed in v1.7.0 — Deep Knowledge Drop

Grounded in research across 40+ authoritative sources (Laws of UX, Don Norman, Refactoring UI,
Atomic Design, Stephen Few, Cole Nussbaumer, Tufte, Testing Trophy, FSD, OpenAPI 3.1,
OWASP 2025, OpenTelemetry, WAI-ARIA APG, WebAIM, Nielsen Norman, and more).

### 12 New Authoritative Knowledge Files
- **UX:** `ux-laws.md`, `design-systems.md`, `data-viz.md`, `microinteractions.md`
- **Frontend:** `frontend-architecture.md`, `modern-testing.md`, `performance-budget.md`, `state-and-data.md`
- **Backend:** `api-design-modern.md`, `observability-modern.md`, `security-modern.md`, `resilience-patterns.md`
- **Polish:** `accessibility-deep.md`, `microcopy.md`, `i18n-rtl.md`

### 4 New Commands
- **`/design-system`** — design tokens + atomic design + dark mode + base on shadcn/Carbon/Material
- **`/copy-audit`** — ban lorem ipsum, "click here", "Error 500"; verb+object buttons; voice/tone framework
- **`/a11y-audit`** — WCAG 2.2 AA + WAI-ARIA APG + keyboard + APCA contrast + auto-fix
- **`/i18n`** — setup + RTL + CSS logical properties + ICU plurals + Arabic font stacks

### 3 New Rules
- **Rule 26: Design System First** — tokens before UI code; no hex in components
- **Rule 27: Copy Quality** — no lorem ipsum; verb+object buttons; inclusive language
- **Rule 28: Data Viz Honesty** — bar charts start at 0; no chartjunk; charts accessible

---

## What Landed in v1.6.0 (previous release)

The "complete app" release. No more happy-path skeletons.

- **`/pages`** — generates a FULL page inventory: auth, user account, admin panel, main dashboard, per-entity CRUD, system pages (404/403/500), legal. Nothing forgotten.
- **`/rbac`** — generates a FULL Role-Based Access Control system: roles × permissions × scopes, default roles (Super Admin → Guest), audit log, admin UI with permission matrix. No more `is_admin` boolean.
- **`/ux-kit`** — scaffolds complete UX kit: empty/loading/error/success states, toasts, confirmation dialogs, command palette (Cmd-K), keyboard shortcuts, dark mode, RTL, accessibility. Every page feels finished.
- **Rule 24: UX Completeness** — every page has 5 states, feedback on every action, WCAG 2.2 AA. "It works" is not done.
- **Rule 25: RBAC by Default** — any app with users gets the full permission model from day 1.

`/implement` now auto-generates ALL of this by default. Open the app and every page you expect is there — and every button, form, and permission check actually works.

## What Landed in v1.5.0 (previous release)

- **User Skill Level onboarding** — every new project asks your programming skill
  (Non-Programmer / Beginner / Intermediate / Professional). Low skill = Claude picks
  everything. High skill = full technical dialogue with trade-offs.
- **Stack questions for Intermediate+** — Claude ASKS frontend, backend, database,
  deployment OS (Windows/Linux), UI visual style, and UI library preference
  (e.g. PrimeVue, shadcn/ui, Angular Material) upfront.
- **Milestone Validation (Rule 23)** — after every implementation milestone, Claude
  stops, runs all checks (lint/tests/build), installs anything missing, asks for any
  missing info (secrets, credentials), and waits for your OK before the next milestone.
- **Rule 22: Skill-Level-Aware** — Non-Programmers get plain English; Professionals
  get ADRs and trade-off tables.

## What Landed in v1.4.0

- **Projects moved outside `.claude/`** — your work now lives at `projects/` at the workspace root (private, git-ignored). The plugin stays portable.
- **Complexity levels** — every project picks **Simple / Medium / Complex** upfront. Scope adapts automatically.
- **Knowledge scopes** — `--global` (cross-project) vs `--project` (client-specific). Build once, reuse everywhere.
- **Inline diagrams (Rule 20)** — ASCII/Unicode diagrams drawn in chat (render everywhere), Mermaid saved to files for VS Code Preview / claude.ai / GitHub.
- **Complexity-aware workflow (Rule 21)** — small projects skip ceremony, big projects get the full treatment.

Migration for existing users:
```bash
mv .claude/projects projects
```

---

## Workspace Layout

```
workspace/
├── .claude/            # Plugin source (shared, committed to git)
│   ├── commands/       # 44 slash commands
│   ├── knowledge/      # GLOBAL knowledge (cross-project: HL7, OPC-UA, FIX...)
│   │   ├── domains/    # Custom global knowledge (/knowledge build --global)
│   │   └── design-references/
│   ├── rules/          # 21 always-active rules
│   └── skills/         # 14 auto-invoked skills
├── projects/           # YOUR projects (private, git-ignored)
│   └── <name>/
│       ├── PROJECT.md         # Includes Complexity: Simple | Medium | Complex
│       ├── knowledge/         # PROJECT-specific knowledge
│       ├── discovery/
│       ├── plans/
│       ├── design/
│       ├── src/
│       └── docs/
├── CLAUDE.md
└── README.md
```

---

## Onboarding in 2 Questions (+ 6 stack questions for experienced users)

When you run `/project add <name>`, Claude asks:

**Q1 — Complexity:** Simple / Medium / Complex
**Q2 — Your Skill Level:** Non-Programmer / Beginner / Intermediate / Professional

Then, **only if you picked Intermediate or Professional**:

**Q3** Frontend framework · **Q4** Backend framework · **Q5** Database
**Q6** Deployment OS (Linux / Windows / Both / Serverless)
**Q7** Visual style · **Q8** UI library (e.g. PrimeVue, shadcn/ui, Angular Material)

Non-Programmers and Beginners get sensible defaults — Claude picks everything and
explains each choice briefly. Professionals get full trade-off tables and ADRs.

## Core Features

### 1. Complexity-Driven Workflow

Every project has a complexity level that drives how much ceremony to use:

| Level | Use When | Output |
|-------|---------|--------|
| **Simple** | Quick MVP, small tool, prototype | Front + Back, 1 diagram, code fast |
| **Medium** | Full-stack app, moderate scale | 5 core plans, auth, DB, tests, basic CI |
| **Complex** | Production SaaS, distributed system | All 10 plans, infra, monitoring, security |

`/complexity` — view, change, or let Claude suggest the right level.

### 1b. Skill-Level-Aware Interaction (NEW)

How verbose and technical Claude is depends on your skill:

| Skill | Behavior |
|-------|---------|
| **Non-Programmer** | Plain English, no jargon, picks everything for you |
| **Beginner** | Explains each tech choice in 1-2 sentences |
| **Intermediate** | Asks you for stack choices, respects your opinions |
| **Professional** | Full trade-off dialogue, ADRs, deep technical questions |

### 1c. Milestone Validation (NEW)

During `/implement`, work is broken into milestones. After EACH milestone, Claude:

1. Runs all checks (lint, types, tests, build, migrations, security audit)
2. Verifies acceptance criteria vs IMPLEMENTATION-ROADMAP.md
3. Installs anything still missing (packages, env vars, migrations, docs)
4. Asks you for any missing info (secrets, credentials, design decisions)
5. Updates PROJECT.md milestone table
6. Summarizes what was built and waits for "proceed" before next milestone

Count adapts to complexity: Simple = 2-3 · Medium = 4-6 · Complex = 7-10.

### 2. Two Knowledge Scopes (NEW)

- **Global** (`.claude/knowledge/domains/`) — reusable across projects (HL7 FHIR, OPC-UA, FIX, Kubernetes patterns)
- **Project** (`projects/<name>/knowledge/`) — specific to one client or system

```bash
/knowledge build opc-ua --global             # Build once, reuse everywhere
/knowledge build client-legacy-api --project # Per-project knowledge
/knowledge promote <topic>                   # Move project → global
/knowledge copy <topic> --to <project>       # Copy global → project
```

### 3. 3-Gate Workflow (Complex mode)

- **Phase 1: Discovery** — free discussion, diagrams, research. NO plans, NO code.
- **Gate 1**: you say "start planning"
- **Phase 2: Planning** — Master plan + 10 sub-plans. Review, edit, approve each.
- **Gate 2**: you say "start coding"
- **Phase 3: Implementation** — step-by-step roadmap. Real code + auto-docs.

Simple mode skips all gates. Medium mode uses gates loosely.

### 4. 4-Step Design Framework (Alex Xu)

Every Complex system design follows:
Requirements & Estimation → High-Level Architecture → Deep Dive → Production Readiness.
References 16 pre-built design patterns (Rate Limiter, Chat System, Payment System, etc.)

### 5. Inline Diagrams & Code (Rule 20)

Every diagram is drawn so the user **actually sees a picture**, not source code.

- **In the chat body** — **ASCII / Unicode box-drawing**. Renders as graphics
  in every viewer (Claude Code CLI, VS Code panel, claude.ai, Claude Desktop,
  any terminal). Zero setup.
- **In saved files** — **Mermaid** inside ```` ```mermaid ```` fences.
  Renders when the `.md` is opened in VS Code Preview (Ctrl+Shift+V),
  claude.ai, or GitHub.
- **Tables** for structured data; **fenced code blocks** with language tags for code.

Chat-side example (renders as a picture in any terminal):

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Browser    │───▶│ Load Balancer│───▶│ API Gateway  │
└──────────────┘     └──────────────┘     └──────┬───────┘
                                                  │
                            ┌─────────────────────┼─────────────────────┐
                            ▼                     ▼                     ▼
                    ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
                    │Core Services │      │  PostgreSQL  │      │    Redis     │
                    └──────────────┘      └──────────────┘      └──────────────┘
```

File-side (same diagram, saved as Mermaid, renders in VS Code Preview):

````markdown
```mermaid
graph TB
    Browser --> LB[Load Balancer] --> API[API Gateway]
    API --> Core[Core Services]
    API --> DB[(PostgreSQL)]
    API --> Cache[(Redis)]
```
````

ASCII templates are provided in Rule 20 for: architecture, sequence,
tree/hierarchy, state machine, ER-lite, KPI tables + Unicode bar charts.

### 6. Domain Knowledge Builder

Working with robotics, PLCs, medical, finance? Build a knowledge base FIRST:
- `/knowledge build <topic> --global` — for standard tech/protocols
- `/knowledge build <topic> --project` — for client-specific systems
- `/knowledge import <file>` — import your own PDFs, manuals, datasheets

Knowledge auto-used during design and implementation (project-specific first, then global).

### 7. Open Source Research (Build vs Buy)

Before designing from scratch:
- `/opensource <system>` — find alternatives, compare features, check licenses
- License warnings: MIT (safe), GPL (careful), AGPL (SaaS danger!)
- Decision matrix: Build vs Use vs Fork vs Hybrid

### 8. Full-Stack Implementation

Writes REAL production code, scaled to complexity:
- `/implement --frontend react --backend nestjs`
- Researches latest docs BEFORE using any library
- Clean Architecture layers, SOLID principles, GoF patterns
- Supports: React, Vue, Next.js, NestJS, FastAPI, Spring Boot, Go, Django

### 9. Professional Frontend Design

- Asks about your style, layout, and feature preferences
- Picks the best UI library (shadcn, PrimeVue, Ant Design, MUI, etc.)
- Enforces accessibility (WCAG 2.2 AA), RTL support, responsive design
- Works with uipro-cli for 67 styles, 161 color palettes, 57 font pairings

### 10. Module & Service Communication

- `/module create <name>` — module with manifest, contracts, boundaries
- `/module events map` — visualize event flow across all modules (Mermaid)
- `/module contract <a> <b>` — define API/event contracts between modules
- Each module owns its data exclusively

### 11. Structured Discovery Phase (Complex)

Not just "open discussion" — structured sub-commands:
- `/discover stakeholders` — map stakeholders with priorities
- `/discover user-journeys` — sequence diagrams for critical journeys
- `/discover constraints` — technical, legal, budget, timeline limits
- `/discover risks` — risk register with probability/impact/mitigation
- `/discover mvp` — MVP scope (Phase 1 vs later)

### 12. Testing & Chaos Engineering

- `/test strategy` — test pyramid (70% unit, 20% integration, 10% e2e)
- `/test contract <a> <b>` — contract testing between services
- `/test chaos <scenario>` — chaos experiments
- Rule 18: Testing is mandatory (scaled to complexity)

### 13. Auto Documentation

Documentation generated alongside code:
- API docs (Swagger), DB docs, component docs (Storybook), architecture docs
- Recommends tools: Docusaurus, MkDocs, TypeDoc, Sphinx
- Auto-warns when docs are outdated

### 14. Export & Integration

- `/export openapi` — export API as OpenAPI/Swagger YAML
- `/export github-issues` — create GitHub issues from plans
- `/export terraform` — export infrastructure as Terraform HCL
- `/export docker` — generate docker-compose from architecture

### 15. Operations & Reliability

- `/runbook <scenario>` — operational runbooks
- `/runbook sla <service>` — SLA/SLO/SLI with error budgets
- `/env design` — Dev → Staging → Prod pipeline
- `/env feature-flags` — gradual rollout
- `/migration plan` — zero-downtime migration

### 16. Security & Compliance

- `/security` — OWASP Top 10 review
- `/privacy` — GDPR / data privacy compliance
- `/tenancy` — multi-tenancy architecture (silo/pool/bridge)
- Rule 05 + Rule 17 enforced

### 17. Code Analysis & Reverse Engineering

- `/analyze codebase` — extract architecture from code
- `/analyze debt` — automated tech debt detection
- `/analyze dependencies` — outdated, vulnerable, unused packages

### 18. Project Management

- `/project add <name>` — asks complexity, creates structure
- `/complexity` — manage project complexity
- `/project deps` — core/dependent project relationships
- `/project template <name>` — save as reusable template
- `/project docs add <file>` — ingest client docs

### 19. Smart Next-Step Suggestions (Rule 19)

After EVERY command, suggests 2-4 relevant next commands.

---

## Full Example: Building a University Management System (Complex)

### Phase 1: Discovery (Free Discussion)

```bash
# 1. Create project — pick Complex when asked
/project add university-system

# 2. Add client documents
/project docs add client-requirements.pdf

# 3. Build domain knowledge — reusable global knowledge
/knowledge build university-lms --global
/knowledge build student-information-systems --global

# Client-specific: the legacy SIS they currently use
/knowledge build client-legacy-sis --project

# 4. Research open source alternatives
/opensource university-lms
# → Moodle (GPL), Canvas (AGPL), Open edX (AGPL)
# → Recommends: build from scratch (AGPL risk for SaaS)

# 5. Structured discovery
/discover stakeholders
/discover user-journeys
/discover constraints
/discover risks
/discover mvp
/discover summary
```

**→ Say: "ابدا plan" (start planning)**

### Phase 2: Planning (10 Plans)

```bash
/plan create university-system      # Master + 10 sub-plans
/plan show                           # Overview
/plan show 02                        # Database plan in detail
/plan edit 05                        # Edit frontend plan
/module create enrollment
/module events map                   # Event flow diagram (inline)
/plan approve all
```

**→ Say: "ابدا برمجة" (start coding)**

### Phase 3: Implementation

```bash
/plan implementation                 # Step-by-step roadmap
/frontend design                     # Asks style → picks shadcn/ui
/implement --frontend react --backend nestjs
/test strategy university-system
/cicd university-system
/env design
/export docker                       # docker-compose.yml
/docs generate
/runbook sla enrollment-service
/monitor
/project template university-system  # Save for next client
```

---

## Quick example: a Simple project

```bash
/project add personal-todo-app
# Claude asks: Simple | Medium | Complex? → pick Simple

# One quick discussion and sketch
"I want a basic todo app with auth, PostgreSQL backend"

# Claude produces an inline architecture diagram + component list.
# No formal plans. Straight to code:

/implement --frontend react --backend express
```

---

## 44 Commands Reference

### Getting Started
| Command | Description |
|---------|-------------|
| `/quickstart` | Interactive guide — top 12 commands & workflow |
| `/complexity` | View / change / suggest project complexity |

### Design & Architecture (10)
`/design` · `/domain` · `/schema` · `/api` · `/gateway` · `/tenancy` · `/estimate` · `/tradeoff` · `/scale` · `/adr`

### Evaluation & Quality (8)
`/evaluate` · `/improve` · `/security` · `/perf` · `/debt` · `/privacy` · `/checklist` · `/failure`

### Implementation (8)
`/implement` · `/frontend design` · `/frontend libraries` · `/backend libs` · `/cicd` · `/monitor` · `/cost` · `/postmortem`

### Planning & Projects (9)
`/plan create` · `/plan show/edit/approve` · `/plan implementation` · `/project add` · `/project deps` · `/project template` · `/project docs` · `/complexity`

### Testing & Analysis (7)
`/test strategy` · `/test contract` · `/test chaos` · `/test data` · `/analyze codebase` · `/analyze debt` · `/analyze dependencies`

### Modules (4)
`/module create` · `/module contract` · `/module events map` · `/module deps graph`

### Discovery (5)
`/discover stakeholders` · `/discover user-journeys` · `/discover constraints` · `/discover risks` · `/discover mvp`

### Operations (5)
`/runbook` · `/runbook sla` · `/env design` · `/env feature-flags` · `/migration plan`

### Export (4)
`/export openapi` · `/export github-issues` · `/export terraform` · `/export docker`

### Research & Docs (7)
`/research` · `/opensource` · `/docs generate` · `/docs update` · `/knowledge build` · `/knowledge import` · `/knowledge promote/copy`

---

## 14 Auto-Invoked Skills

| Skill | Triggers When |
|-------|--------------|
| **design-system** | Designing new systems (scales to complexity) |
| **evaluate-system** | Reviewing existing systems |
| **implement-system** | Writing code |
| **research-tech** | Comparing technologies |
| **project-manager** | Managing projects (uses `projects/` at root) |
| **documentation** | Generating docs |
| **frontend-design** | Designing UI |
| **devops** | Setting up CI/CD, Docker |
| **opensource-research** | Searching for existing solutions |
| **knowledge-builder** | Building domain knowledge (global or project scope) |
| **web-apps** | Web apps (SPA, SSR, PWA, framework selection) |
| **desktop-apps** | Desktop apps (Electron, Tauri, WPF, Qt) |
| **mobile-apps** | Mobile apps (React Native, Flutter, Swift, Kotlin) |
| **cross-platform** | Multi-platform targeting |

---

## 25 Always-Active Rules

| # | Rule |
|---|------|
| 01 | Requirements First |
| 02 | Design Before Code |
| 03 | Justify Decisions |
| 04 | Think About Failure |
| 05 | Security by Default |
| 06 | Clean Code |
| 07 | Scalability |
| 08 | Observability |
| 09 | Data Model is King |
| 10 | API Standards |
| 11 | Research Before Code |
| 12 | Auto Documentation |
| 13 | 3-Gate Workflow (Complex only) |
| 14 | Domain Modeling |
| 15 | Accessibility |
| 16 | Performance Budgets |
| 17 | Tenant Isolation |
| 18 | Testing Required |
| 19 | Suggest Next Commands |
| 20 | Chat Visibility — ASCII diagrams in chat, Mermaid in files, tables for data |
| 21 | Complexity-Aware — scale effort to project size |
| 22 | Skill-Level-Aware — adapt question depth to user skill |
| 23 | Milestone Validation — validate + install + ask + wait after every milestone |
| 24 | UX Completeness — every page has 5 states + feedback + keyboard + a11y |
| 25 | RBAC by Default — any app with users gets roles × permissions × audit |
| 26 | Design System First — tokens before UI code, no hex, atomic design |
| 27 | Copy Quality — no lorem ipsum, verb+object buttons, inclusive language |
| 28 | Data Viz Honesty — no chartjunk, no truncated axes, accessible charts |
| 29 | Modular by Default — every system is Core + Modules, plug-and-play |
| 30 | Functional Completeness — classify capabilities, auto admin UIs, manageable |
| 31 | **Shared First** — factor-before-copy, 3-layer ownership, no forks (NEW) |

---

## Quick Start

```bash
# 1. Clone
git clone https://github.com/IbrahimBadawy/claude-system-design-plugin
cd claude-system-design-plugin

# 2. Install companion tools (recommended)
/plugin install context7
npm i -g uipro-cli && uipro init --ai claude
/plugin install security-guidance
/plugin install code-review
/plugin install playwright

# 3. Open in Claude Code
claude .

# 4. Start designing (it'll ask your complexity)
/project add my-system
/quickstart
```

---

## License

MIT License — see [LICENSE](LICENSE) for details.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add commands, rules, skills, and templates.
