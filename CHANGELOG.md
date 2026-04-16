# Changelog

All notable changes to this project will be documented in this file.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
