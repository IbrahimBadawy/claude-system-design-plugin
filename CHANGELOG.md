# Changelog

All notable changes to this project will be documented in this file.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.8.0] - 2026-04-16

### Added — Modular Architecture + Functional Management

Addresses user feedback: "the plugin builds good structure, but apps aren't fully
manageable and aren't modular enough to be cores or modules for other apps."

#### Core Concept 1 — Functional Taxonomy

Every app's capabilities now get classified into 5 categories:

1. **Features** — end-user capabilities (with per-user/role toggles)
2. **Tools** — admin/operator utilities (with audit log)
3. **Tasks** — background/scheduled work (with scheduler UI, retry, pause)
4. **Services** — APIs/events for other apps (with versioning, consumer catalog)
5. **Flows** — multi-step orchestrated processes (with visual designer)

Each category auto-generates an admin UI. The whole app becomes manageable from
one place, not just "backend code that works."

#### Core Concept 2 — Modular Architecture (Core + Modules)

Every system is built as Core + Modules:
- **Core** = identity + auth + event bus + tenant context (nothing domain-specific)
- **Modules** = business capabilities with versioned contracts
- Same codebase can be **standalone**, a **core** for other apps, or a **module**
  inside another core (dual-boot)
- Communication via **ports**, **events**, or **published APIs** only — no
  cross-module imports

Example from user: HR system as core → Attendance plugs in. OR the reverse:
Attendance built standalone, later HR wraps it. Both directions work.

#### New Knowledge Files (2)

- `functional-taxonomy.md` — The 5 categories with admin UI patterns per category
- `modular-architecture.md` — Core + Modules, Ports/Adapters, ACL, lifecycle,
  module catalog, event bus options

#### New Commands (4)

- **`/functional-model`** — Classify all app capabilities into Features/Tools/Tasks/Services/Flows. Auto-generates `FUNCTIONAL-MODEL.md` + admin UIs. Drift audit.
- **`/core-modules`** — Design the core + module split. Generates `MODULAR-ARCH.md`, module manifests, composition root, lifecycle stubs. Modularity audit.
- **`/app-as-module`** — Wrap an existing standalone app to be pluggable into another core. Generates manifest, ports, contracts, ACL, dual-boot support.
- **`/integrate`** — Wire two apps together (core + module). Generates anticorruption layer, event bridge, permission mapping, composition root, contract tests.

#### New Rules (2)

- **Rule 29: Modular by Default** — Every system is Core + Modules with plug-and-play contracts. Banned: cross-module imports, cross-module DB queries, modules mentioned in core.
- **Rule 30: Functional Completeness** — Classify every capability into the 5 categories. Generate admin UI for each. The whole app is toggle-able, configurable, auditable — not just "backend that works."

#### Updated

- **`/discover`** — New sub-commands: `/discover integration` (asks about modularity, existing-system integration, core/module/both direction, event bus choice, ACLs); `/discover functional-inventory` (classify capabilities upfront)
- **`.claude/settings.json`** — Auto-approve file edits in `.claude/**` and `projects/**`; full dev toolchain (pnpm, bun, docker, kubectl, terraform, prisma, drizzle, pytest, playwright, gh, etc.). No more permission prompts for plugin development.

#### Totals

**140+ files · 55 commands · 30 rules · 14 skills · 24 knowledge files**

## [1.7.0] - 2026-04-16

### Added — 12 new authoritative knowledge files, 4 commands, 3 rules

Based on deep web research across 40+ authoritative sources (Laws of UX, Don Norman,
Steve Krug, Refactoring UI, Brad Frost's Atomic Design, W3C Design Tokens, Stephen Few,
Cole Nussbaumer Knaflic, Edward Tufte, Kent C. Dodds Testing Trophy, Feature-Sliced Design,
Bulletproof React, Zalando/Microsoft/Google API Guidelines, OpenTelemetry, OWASP 2025,
NIST Zero Trust, WAI-ARIA APG, WebAIM Million 2025, Nielsen Norman UX writing, GOV.UK,
Material Motion, Apple HIG, and more).

#### New Knowledge Files (global, `.claude/knowledge/`)

**UX & Design**
- `ux-laws.md` — 15 Laws of UX + Norman + Krug + Refactoring UI + 30 auto-enforce patterns
- `design-systems.md` — Atomic Design + W3C tokens + shadcn HSL + Material/Carbon/Fluent comparison + starter token set
- `data-viz.md` — Few, Knaflic, Tufte principles + chart selection matrix + modern dashboard patterns
- `microinteractions.md` — Motion principles + skeletons + optimistic UI + Cmd-K + 30-item polish checklist

**Frontend Architecture**
- `frontend-architecture.md` — Feature-Sliced Design + Bulletproof React + Next.js RSC rules + monorepo decision
- `modern-testing.md` — Testing Trophy + MSW + Playwright + jest-axe + contract testing
- `performance-budget.md` — LCP/INP/CLS targets + Server Components + PPR + fonts + resource hints
- `state-and-data.md` — Server vs client state + TanStack Query v5 + Zustand + forms + URL state (nuqs)

**Backend**
- `api-design-modern.md` — REST baseline + OpenAPI 3.1 + idempotency + RFC 9457 + REST/GraphQL/tRPC/gRPC matrix
- `observability-modern.md` — OpenTelemetry + USE/RED/Golden Signals + SLO/SLI + tracing + structured logs
- `security-modern.md` — OWASP Top 10 + ASVS + Cheat Sheets + security headers + supply chain + Zero Trust
- `resilience-patterns.md` — Circuit breaker + retry with jitter + bulkhead + health checks + saga (Temporal)

**Accessibility, UX Writing, i18n**
- `accessibility-deep.md` — WCAG 2.2 AA+ + WAI-ARIA APG + keyboard contract + APCA + WebAIM big-6 fixes
- `microcopy.md` — NN/g + Mailchimp + Polaris + GOV.UK + ban list + voice/tone framework + inclusive language
- `i18n-rtl.md` — CSS logical properties + ICU MessageFormat + Intl API + icon mirroring + Arabic font stacks

#### New Commands
- **`/design-system`** — generate/audit design tokens (8 categories), atomic design hierarchy, dark mode, base on shadcn/Carbon/Material/Polaris/Fluent
- **`/copy-audit`** — scan UI strings for lorem ipsum, "click here", "Error 500", blame language, idioms. Generate copy by type (error, empty-state, button, confirm, toast). Voice & tone framework
- **`/a11y-audit`** — WCAG 2.2 AA + WAI-ARIA APG compliance + keyboard contract + color contrast + screen reader guidance. Auto-fix safe issues
- **`/i18n`** — setup/add locale/audit hardcoded strings/RTL check/extract keys/font stack recommendation

#### New Rules
- **Rule 26: Design System First** — tokens before UI code. No hex in components, no arbitrary spacing, atomic design enforced, 3-tier token model (primitive/semantic/component)
- **Rule 27: Copy Quality** — no lorem ipsum, no "Click here", no raw error codes. Verb+object buttons. Errors answer what/why/what-now. Inclusive language. Sentence case
- **Rule 28: Data Viz Honesty** — bar charts start at 0, no 3D/chartjunk, chart type matches data, max 7 categorical colors, charts accessible (title+caption+alt text+data-table toggle)

### Research Sources (40+ authoritative references codified)

Laws of UX, Don Norman *The Design of Everyday Things*, Steve Krug *Don't Make Me Think*,
Refactoring UI (Wathan+Schoger), Brad Frost *Atomic Design*, W3C Design Tokens, Style
Dictionary, Material 3, Apple HIG, Fluent 2, IBM Carbon, Shopify Polaris, Ant Design,
Stephen Few, Cole Nussbaumer Knaflic, Edward Tufte, Kent C. Dodds Testing Trophy,
Feature-Sliced Design, Bulletproof React, Next.js App Router, Astro Islands, Turborepo,
Testing Library, Mock Service Worker, Playwright, jest-axe, Pact, web.dev Core Web Vitals,
TanStack Query v5, Zustand, nuqs, Zalando API Guidelines, Microsoft REST Guidelines,
Google API Design Guide, OpenAPI 3.1, Stripe idempotency, RFC 9457, OpenTelemetry, USE
method, RED method, Google SRE golden signals + SLO, OWASP Top 10 2021, OWASP ASVS,
OWASP Cheat Sheets, NIST SP 800-207 Zero Trust, SLSA, Resilience4j, Netflix Chaos
Engineering, Temporal Sagas, WAI-ARIA Authoring Practices, WebAIM Million 2025, WCAG 2.2,
APCA, Microsoft Inclusive Design, W3C COGA, Nielsen Norman UX writing, Mailchimp Style
Guide, Shopify Polaris Content, GOV.UK Content Design, 18F Inclusive Language, MDN CSS
Logical Properties, ICU MessageFormat, Material Motion, Apple HIG Motion.

### Totals

**134+ files · 51 commands · 28 rules · 14 skills · 22 knowledge files**

## [1.6.0] - 2026-04-16

### Added — UX, Pages, RBAC (the "complete app" release)

Addresses the gap where the plugin built solid structure but shipped apps that
felt incomplete on the frontend. From now on, any user-facing app generated by
this plugin ships with full page inventory, full permission system, and full
UX quality kit — no happy-path skeletons.

#### New Commands
- **`/pages`** — Comprehensive page inventory: auth (login/register/forgot/reset/
  verify/2FA/invite), user account (profile/settings/security/billing/API keys),
  admin panel (dashboard/users/roles/permission matrix/audit log), main dashboard,
  per-entity CRUD (list/detail/create/edit/archive), system pages (404/403/500/
  maintenance/rate-limited/offline), legal (terms/privacy/cookie). Includes
  `audit` to check existing code against inventory.
- **`/rbac`** — Full Role-Based Access Control designer. Roles × Permissions ×
  Scopes model (not `is_admin`). Default roles (Super Admin, Tenant Admin,
  Manager, Editor, Member, Viewer, Guest). Permission naming `<resource>:<action>:<scope>`.
  Generates DB schema, seed, backend middleware, frontend `<Can>` component,
  admin UI (roles list, permission matrix, audit log). `audit` verifies every
  endpoint is guarded.
- **`/ux-kit`** — UX quality kit: 5 states per page (empty/loading/error/partial/
  success), feedback patterns (toast/inline/banner/modal/optimistic), destructive
  action protocol (confirm + type-to-confirm + undo), table patterns, form
  patterns, keyboard shortcuts (Cmd-K palette, `?` cheatsheet), a11y (WCAG 2.2 AA),
  visual polish (dark mode, RTL, reduced motion, responsive). Scaffolds components:
  Toast, ConfirmDialog, EmptyState, LoadingSkeleton, ErrorBoundary, Breadcrumbs,
  CommandPalette, KeyboardShortcuts, ThemeToggle, DataTable, BulkActionBar,
  FilterPanel, FileUpload + hooks.

#### New Rules
- **Rule 24: UX Completeness** — Every page MUST have all 5 states, feedback on
  every action, keyboard navigation, WCAG 2.2 AA, dark mode, destructive action
  protocol. "It works" is not done.
- **Rule 25: RBAC by Default** — Any app with user accounts gets the full
  Roles × Permissions × Scopes model + audit log + admin UI. No `is_admin`
  boolean. Every endpoint guarded, every sensitive action audited.

#### Updated
- **`/project`** — New Q9 (App Scope) and Q10 (UX Feel) onboarding questions.
  After onboarding, Claude auto-runs `/pages`, `/rbac`, `/ux-kit`, `/frontend design`
  to produce complete spec before any code.
- **`/frontend design`** — Phase 0 completeness check (verifies PAGES.md, RBAC.md,
  UX-KIT.md exist). Lists the full mandatory page structure (auth, account, admin,
  dashboard, per-entity, system, legal). Adds UX kit companion libraries.
- **`/implement`** — Auto-scaffold list: every user-facing app gets all of the
  above automatically, not just the routes the user explicitly asked for.
- **`frontend-design` skill** — Rewritten with completeness check, full mandatory
  page structure, RBAC-aware UI requirements, and a 4-audit "done" gate
  (pages audit, rbac audit, ux-kit audit, axe-core/Lighthouse).
- **`implement-system` skill** — Reads PAGES.md/RBAC.md/UX-KIT.md artifacts,
  scaffolds the complete set automatically. New Auto-Scaffold section lists
  every component generated.

#### Totals
**119+ files · 47 commands · 25 rules · 14 skills**

## [1.5.0] - 2026-04-16

### Added — User Skill Level + Milestone Validation

- **User Skill onboarding question** asked immediately after Complexity:
  Non-Programmer / Beginner / Intermediate / Professional.
  Controls how many technical questions Claude asks vs. how many decisions
  it makes automatically.

- **Stack questions (Q3-Q8) for Intermediate + Professional only**:
  - Q3 Frontend framework
  - Q4 Backend framework
  - Q5 Database
  - Q6 Deployment OS (Linux / Windows Server / Both / Serverless)
  - Q7 UI visual style (7 options)
  - Q8 UI library preference (e.g. PrimeVue, shadcn/ui, Angular Material, ...)

  Non-Programmer and Beginner skip these — Claude picks sensible defaults
  (Next.js + shadcn/ui + NestJS + PostgreSQL + Linux/Docker + Modern & Minimal)
  and lists them briefly for user review.

- **Rule 22: Skill-Level-Aware** — adapts question depth to user skill. Low-skill
  users get smart defaults; high-skill users get full technical dialogue with
  trade-offs.

- **Rule 23: Milestone Validation** — after EVERY implementation milestone,
  Claude STOPS and runs a 6-step validation loop:
  1. Automated checks (lint, types, tests, build, migrations, security audit)
  2. Acceptance criteria check vs IMPLEMENTATION-ROADMAP.md
  3. Install anything still missing (packages, env vars, migrations, docs)
  4. Ask user for missing info (secrets, credentials, design gaps)
  5. Update PROJECT.md milestone table with validation date + results
  6. Summarize for user (plain English for Beginners, technical for Pros) and wait

  Milestone count adapts to complexity: Simple=2-3, Medium=4-6, Complex=7-10.

### Updated

- `/project` command — now asks the 2 onboarding questions + 6 stack questions
- `/implement` command — full Milestone Validation section added
- `PROJECT.md` template — includes User Skill field + Stack block + Milestone table
- `project-manager` skill — walks through the onboarding flow
- `implement-system` skill — milestone-driven, skill-aware summaries
- `CLAUDE.md`, `README.md`, `index.html` — updated with new rules and flow

### Totals

**116+ files · 44 commands · 23 rules · 14 skills**

## [1.4.0] - 2026-04-16

### Changed — Major workspace restructure
- **Projects moved outside `.claude/`**: User projects now live at `projects/` at the workspace root, not `.claude/projects/`. This keeps plugin source clean and user data private. A one-line migration: `mv .claude/projects projects`.
- **`.gitignore`** updated so `projects/` is never pushed to the plugin repo.

### Added
- **Complexity levels (Simple / Medium / Complex)** prompted on project creation. Every downstream command scales its output to match:
  - Simple — front + back only, no formal plans, code fast
  - Medium — 5 core plans + auth + DB + tests + basic CI
  - Complex — full 3-gate workflow, 10 plans, infra, monitoring, security
- **`/complexity` command** — view / change / suggest / explain complexity.
- **Knowledge scopes (Global vs Project)** made explicit:
  - `--global` → `.claude/knowledge/domains/<topic>.md` (reusable across projects)
  - `--project` → `projects/<active>/knowledge/<topic>/` (client-specific)
  - New operations: `/knowledge promote`, `/knowledge copy --to <project>`, `/knowledge list --global|--project`
- **Rule 20: Chat Visibility** — diagrams and code always rendered inline in chat as Mermaid / fenced code blocks, not just saved to files.
- **Rule 21: Complexity-Aware** — scale depth of work to the project's complexity; ASK if unsure.
- **Diagram standards** — subgraphs, `%% comments`, labeled edges, DB cylinders, queue shapes.

### Improved
- **Design references** — 3 key references upgraded with proper Mermaid diagrams (Rate Limiter, URL Shortener, Chat System) as canonical examples for the new diagram standard.
- **`patterns.md`** — SQL vs NoSQL decision tree converted to a Mermaid flowchart.
- **All command docs** — paths updated from `.claude/projects/` to `projects/`; complexity levels respected everywhere.
- **`/quickstart`** rewritten to reflect the new layout and complexity-first workflow.

### Migration
```bash
mv .claude/projects projects   # one-line migration for existing users
```

## [1.3.0] - 2026-04-13

### Added (8 new commands, 2 new rules)
- **Testing**: `/test strategy`, `/test contract`, `/test chaos`, `/test data`, `/test coverage`
- **Modules**: `/module create`, `/module contract`, `/module events map`, `/module deps graph`, `/module version`
- **Discovery**: `/discover stakeholders`, `/discover user-journeys`, `/discover constraints`, `/discover risks`, `/discover mvp`, `/discover summary`
- **Export**: `/export openapi`, `/export postman`, `/export terraform`, `/export github-issues`, `/export docker`
- **Runbooks**: `/runbook <scenario>`, `/runbook oncall`, `/runbook incident`, `/runbook sla`
- **Environment**: `/env design`, `/env config`, `/env feature-flags`
- **Migration**: `/migration plan`, `/migration rollback`, `/migration seed`
- **Analyze**: `/analyze codebase`, `/analyze debt`, `/analyze coverage`, `/analyze dependencies`, `/analyze architecture`
- **Rule 17**: Tenant Isolation - every query tenant-scoped, no cross-tenant data leakage
- **Rule 18**: Testing Required - test pyramid enforced, coverage targets

### Changed
- Total: 108 files, 42 commands, 18 rules, 10 skills
- Updated all docs: CLAUDE.md, README.md, index.html, CHANGELOG.md

## [1.2.0] - 2026-04-13

### Added
- **Knowledge Builder**: `/knowledge build`, `/knowledge import`, `/knowledge list`, `/knowledge show`, `/knowledge update`, `/knowledge add`, `/knowledge search`, `/knowledge docs`
- **knowledge-builder skill**: Auto-triggers for unfamiliar domains, researches web + docs, imports PDFs/manuals
- Per-topic knowledge folders with docs/ subfolder for user-provided documentation

### Changed
- Project folder structure: added `knowledge/` with per-topic subfolders and docs/

## [1.1.0] - 2026-04-13

### Added
- **3-Gate Workflow**: Discovery -> Planning -> Implementation with explicit user gates
- **Discovery Phase**: Free discussion before planning, with diagrams/ and research/ folders
- **Smart Scope Detection**: Simple/Medium/Complex auto-detection
- **Mermaid diagram generation** during discovery phase
- Project folder structure: discovery/, discovery/diagrams/, discovery/research/

### Changed
- Rule 13 rewritten as "3-Gate Workflow" (was "No Code Without Plan")
- Workflow section in README and index.html completely redesigned
- project.md command updated with new folder structure

## [1.0.0] - 2026-04-13

### Added
- **34 Commands**: quickstart, design, evaluate, improve, implement, plan, frontend, backend-libs, docs, research, opensource, knowledge, schema, api, domain, gateway, tenancy, estimate, tradeoff, scale, cost, adr, security, perf, debt, privacy, cicd, failure, monitor, checklist, postmortem, project, project-deps, project-template
- **16 Rules**: requirements-first, design-before-code, justify-decisions, think-about-failure, security-by-default, clean-code, scalability, observability, data-model, api-standards, research-before-code, auto-documentation, no-code-without-plan (3-gate), domain-modeling, accessibility, performance-budgets
- **10 Skills**: design-system, evaluate-system, implement-system, research-tech, project-manager, documentation, frontend-design, devops, opensource-research, knowledge-builder
- **16 Design References**: Rate Limiter, URL Shortener, Unique ID Generator, Key-Value Store, Chat System, News Feed, Notification System, Search Autocomplete, Web Crawler, Proximity Service, Video Platform, File Storage, Payment System, Leaderboard, Metrics Monitoring, Stock Exchange
- **4 Templates**: SaaS Dashboard, REST API Service, E-Commerce Platform, System Design Document
- **Knowledge Base**: patterns, technology-guide (2026), distributed-systems (DDIA deep read), clean-architecture-guide (SOLID, GoF, Clean Code, Code Complete, Refactoring), recommended-tools (50+), domains/ (custom knowledge)
- **Planning System**: Master plan + 10 sub-plans with 3-gate approval workflow
- **Project Management**: Multi-project tracking, dependencies, templates, core/dependent relationships
- **Auto Documentation**: Docs generated alongside code with Swagger, Storybook, TypeDoc, Docusaurus
- **Open Source Research**: Build vs buy vs fork analysis with license checks
- **Enterprise Profile**: settings.enterprise.json for restricted environments
- **Evaluation Suite**: 5 benchmarks with scoring rubrics + 2 full examples
- **Hooks**: PostToolUse reminder for documentation updates
- **Landing Page**: index.html with full feature showcase
- **LICENSE**: MIT
- **CONTRIBUTING.md**: Guide for adding commands, rules, skills, templates
