# System Design Architect - Claude Code Plugin

The most comprehensive system design plugin for Claude Code.
Design, evaluate, plan, and build production-grade distributed systems.

**109 files | 42 commands | 19 rules | 10 skills | 4 templates | 16 design references | 8 books**

Built on deep knowledge from: System Design Interview (Alex Xu), DDIA (Kleppmann),
Clean Architecture & Clean Code (Martin), Design Patterns (GoF), Refactoring (Fowler),
Code Complete (McConnell).

## Quick Start

```bash
# 1. Copy to your project
cp -r .claude /your-project/
cp CLAUDE.md /your-project/

# 2. Install companion tools (only context7 is required, rest are optional)
/plugin install context7            # Required: fresh library docs

# Optional but recommended:
npm i -g uipro-cli && uipro init --ai claude  # UI/UX design
/plugin install security-guidance              # Security scanning

# 3. Open in Claude Code
claude /your-project
/quickstart                       # See getting started guide
```

## Smart Scope Detection

The plugin auto-detects project complexity and adjusts its behavior:

| Mode | When | What Happens |
|------|------|-------------|
| **Simple** | "quick script", "small fix", "just build" | Skip planning, light design, code directly |
| **Medium** | "add a feature", "build an API" | Light design, optional planning, then code |
| **Complex** | "design a system", "build a SaaS" | Full 4-step framework + plans required |

You're never forced into the full workflow for simple tasks.

## 33 Commands

### Getting Started
| Command | Description |
|---------|-------------|
| `/quickstart` | Interactive guide - top 10 commands, workflow, tips |

### Design & Architecture
| Command | Description |
|---------|-------------|
| `/design <system>` | Full system design (4-step framework) |
| `/domain <system>` | Domain modeling with DDD (bounded contexts, aggregates) |
| `/schema <entity>` | Database schema design |
| `/api <resource>` | REST API design |
| `/gateway <system>` | API gateway & BFF pattern |
| `/tenancy <system>` | Multi-tenancy architecture |
| `/estimate <desc>` | Back-of-envelope calculations |
| `/tradeoff <A> vs <B>` | Structured trade-off analysis |
| `/scale <component>` | Scaling strategy |
| `/adr <title>` | Architecture Decision Record |

### Evaluation & Quality
| Command | Description |
|---------|-------------|
| `/evaluate <system>` | Evaluate existing system critically |
| `/improve <system>` | Generate improvement roadmap |
| `/security` | Security review (OWASP) |
| `/perf <system>` | Performance audit & optimization |
| `/debt <system>` | Technical debt management |
| `/privacy <system>` | GDPR & data privacy compliance |
| `/checklist` | Full design review checklist |
| `/failure <scenario>` | Failure analysis & recovery |

### Implementation
| Command | Description |
|---------|-------------|
| `/implement --frontend react --backend nestjs` | Full-stack implementation |
| `/frontend design` | Professional UI design (asks style, picks best library) |
| `/frontend libraries <fw>` | Best UI component libraries for a framework |
| `/backend libs <fw>` | Best backend helper libraries |
| `/cicd <system>` | CI/CD pipeline design |
| `/monitor` | Observability stack design |
| `/cost` | Infrastructure cost estimation |
| `/postmortem <incident>` | Incident postmortem |

### Planning & Projects
| Command | Description |
|---------|-------------|
| `/plan create <project>` | Master plan + 10 sub-plans |
| `/plan show / edit / approve` | Review and approve plans |
| `/plan implementation` | Step-by-step coding roadmap |
| `/project add <name>` | Create new project |
| `/project deps` | Project dependency graph |
| `/project template <name>` | Convert to reusable template |

### Testing & Quality
| Command | Description |
|---------|-------------|
| `/test strategy <project>` | Design test pyramid (unit/integration/e2e) |
| `/test contract <a> <b>` | Contract testing between modules |
| `/test chaos <scenario>` | Chaos engineering experiments |
| `/test data <entity>` | Generate test fixtures & factories |
| `/analyze codebase` | Reverse-engineer architecture from code |
| `/analyze debt` | Automated tech debt detection |
| `/analyze dependencies` | Dependency health check |

### Modules & Services
| Command | Description |
|---------|-------------|
| `/module create <name>` | Create module with manifest & contracts |
| `/module contract <a> <b>` | Define API/event contract between modules |
| `/module events map` | Visualize event flow across modules |
| `/module deps graph` | Module dependency graph |

### Discovery (Structured)
| Command | Description |
|---------|-------------|
| `/discover stakeholders` | Map all stakeholders |
| `/discover user-journeys` | Map critical user journeys |
| `/discover constraints` | Technical, legal, budget constraints |
| `/discover risks` | Build risk register |
| `/discover mvp` | MVP scope (Phase 1 vs later) |

### Operations & Environment
| Command | Description |
|---------|-------------|
| `/runbook <scenario>` | Create operational runbook |
| `/runbook sla <service>` | Define SLA/SLO/SLI |
| `/env design` | Dev -> Staging -> Prod pipeline |
| `/env feature-flags <feature>` | Feature flag rollout strategy |
| `/migration plan <change>` | Zero-downtime migration plan |

### Export & Integration
| Command | Description |
|---------|-------------|
| `/export openapi` | Export API as OpenAPI/Swagger |
| `/export github-issues` | Create GitHub issues from plans |
| `/export terraform` | Export as Terraform IaC |
| `/export docker` | Generate docker-compose |

### Research & Documentation
| Command | Description |
|---------|-------------|
| `/research <topic>` | Research latest technologies |
| `/opensource <system>` | Open source alternatives (build vs buy vs fork) |
| `/docs generate` | Full documentation suite |
| `/docs update` | Update docs for recent changes |
| `/knowledge build <topic>` | Build domain knowledge (research web + organize) |
| `/knowledge import <file>` | Import PDF, docs, or URL into knowledge |

## 9 Auto-Invoked Skills

Skills activate automatically based on context - no commands needed.

| Skill | Triggers When |
|-------|--------------|
| **design-system** | Designing new systems (4-step framework) |
| **evaluate-system** | Reviewing/auditing existing systems |
| **implement-system** | Writing code (researches docs first) |
| **research-tech** | Comparing technologies |
| **project-manager** | Managing projects, plans, templates |
| **documentation** | Generating/updating docs (Swagger, Storybook, Docusaurus) |
| **frontend-design** | Designing UI (asks style, picks best library, a11y) |
| **devops** | Setting up Docker, CI/CD, deployment |
| **opensource-research** | Searching for existing solutions (build vs buy) |
| **knowledge-builder** | Building domain knowledge (robotics, PLC, medical, etc.) |

## 19 Always-Active Rules

| # | Rule | What It Enforces |
|---|------|-----------------|
| 01 | Requirements First | Never code without understanding the problem |
| 02 | Design Before Code | API + data model + architecture first |
| 03 | Justify Decisions | Every choice needs "why" + trade-offs |
| 04 | Think About Failure | Timeouts, retries, circuit breakers |
| 05 | Security by Default | Auth, encryption, validation in every layer |
| 06 | Clean Code | Naming, functions, error handling, SOLID |
| 07 | Scalability | Design for 10x, build for 1x |
| 08 | Observability | Metrics, logging, tracing from day 1 |
| 09 | Data Model is King | Access patterns first, schema second |
| 10 | API Standards | Versioning, pagination, idempotency |
| 11 | Research Before Code | Fetch latest docs before using any library |
| 12 | Auto Documentation | Docs generated alongside code |
| 13 | No Code Without Plan | All plans approved + explicit user command |
| 14 | Domain Modeling | DDD: bounded contexts, aggregates, events |
| 15 | Accessibility | WCAG 2.2 AA, keyboard nav, RTL support |
| 16 | Performance Budgets | API p99, Core Web Vitals, bundle limits |
| 17 | Tenant Isolation | Every query tenant-scoped, no data leakage |
| 18 | Testing Required | Test pyramid enforced, coverage targets |
| 19 | Suggest Next Commands | After every command, suggest relevant next steps |

## 4 Ready-Made Templates

| Template | Description | Stack |
|----------|-------------|-------|
| **SaaS Dashboard** | Multi-tenant admin panel with RBAC, charts, dark mode | React + shadcn + NestJS + PostgreSQL |
| **REST API Service** | Clean Architecture backend with auth, validation, Swagger | NestJS / FastAPI / Go |
| **E-Commerce** | Full store: catalog, cart, orders, payments, search | Next.js + NestJS + Stripe + Kafka |
| **Design Document** | Complete system design template (Alex Xu method) | Framework-agnostic |

## 16 Design References

Pre-built architecture references from Alex Xu's books:

| | | | |
|---|---|---|---|
| Rate Limiter | URL Shortener | Unique ID Gen | Key-Value Store |
| Chat System | News Feed | Notifications | Autocomplete |
| Web Crawler | Proximity Service | Video Platform | File Storage |
| Payment System | Leaderboard | Monitoring | Stock Exchange |

## Domain Knowledge Builder

Build custom knowledge bases for ANY domain before designing or coding.

```bash
/knowledge build kuka-robot           # Deep web research + organize
/knowledge build siemens-plc          # PLC programming knowledge
/knowledge import krl-manual.pdf      # Import your own docs/manuals
/knowledge list                       # See all knowledge bases
/knowledge show opc-ua                # View specific knowledge
```

**Example: Industrial Automation ERP**
```
/knowledge build kuka-robot           # KUKA robot APIs & KRL language
/knowledge build siemens-plc          # Siemens S7 PLC programming
/knowledge build opc-ua               # OPC-UA industrial protocol
/knowledge build scada-systems        # SCADA architecture
/knowledge import krl-manual.pdf      # Import your own documentation

# Now Claude understands the entire domain
/design automation-erp                # Designs using that knowledge
```

Each topic gets its own organized folder:
```
knowledge/
  kuka-robot/
    kuka-robot.md             # Auto-generated: concepts, APIs, protocols, examples
    docs/                     # YOUR files: PDFs, manuals, datasheets
    urls.md                   # Reference URLs
```

Works for any domain: Robotics, Medical (HL7/FHIR), Finance (FIX), IoT (MQTT), Manufacturing (SCADA), and more.

## Knowledge Sources (8 Books - Read Cover to Cover)

| Book | Key Knowledge Extracted |
|------|----------------------|
| System Design Interview Vol 1 & 2 | 4-step framework, 16 system designs, estimation |
| Designing Data-Intensive Applications | Replication, partitioning, consensus, CDC, streams |
| Clean Architecture | Dependency Rule, SOLID, component principles, Humble Objects |
| Clean Code | Naming, functions, concurrency, objects vs data structures |
| Design Patterns (GoF) | 23 patterns, 8 redesign causes, pattern combinations |
| Refactoring | 24 code smells, expand-contract, branch by abstraction |
| Code Complete | Defensive programming, table-driven methods, performance tuning |

## Plugin Structure (109 files)

```
CLAUDE.md                           # Main configuration
.claude/
  settings.json                     # Permissions + hooks
  commands/                         # 42 command files
    quickstart, design, evaluate, improve, implement,
    plan, frontend, backend-libs, docs, research,
    opensource, schema, api, domain, gateway, tenancy,
    estimate, tradeoff, scale, cost, adr, security,
    perf, debt, privacy, cicd, failure, monitor,
    checklist, postmortem, project, project-deps,
    project-template
  rules/                            # 19 always-active rules
    01-requirements-first ... 16-performance-budgets
  skills/                           # 9 auto-invoked skills
    design-system/    evaluate-system/   implement-system/
    research-tech/    project-manager/   documentation/
    frontend-design/  devops/            opensource-research/
  knowledge/                        # Reference knowledge base
    patterns.md                     # 16 architecture patterns
    technology-guide.md             # 2026 tech stack guide
    distributed-systems.md          # DDIA (consensus, CDC, streams)
    clean-architecture-guide.md     # SOLID, GoF, refactoring, Code Complete
    recommended-tools.md            # 50+ companion tools
    design-references/              # 16 system design references
  skills/design-system/templates/   # 4 ready-made templates
    saas-dashboard.md    rest-api-service.md
    e-commerce.md        system-design-document.md
  projects/                         # Client project tracking
  templates/                        # Your reusable project templates
```

## 3-Gate Workflow

Every complex project passes through 3 phases with explicit user control:

```
PHASE 1: DISCOVERY (free discussion)
  /project add university-system        # Create project
  Discuss the idea freely               # Ask questions, understand
  Diagrams saved to discovery/          # Mermaid architecture, data flow, ERD
  Research saved to discovery/          # Open source options, tech comparison

  >>> Say "ابدا plan" or "start planning" <<<

PHASE 2: PLANNING (structured plans)
  /plan create university-system        # Master plan + 10 sub-plans
  /plan show, /plan edit                # Review and refine
  /plan approve all                     # Approve when satisfied

  >>> Say "ابدا برمجة" or "start coding" <<<

PHASE 3: IMPLEMENTATION (actual code)
  /plan implementation                  # Step-by-step roadmap
  Follow roadmap incrementally          # Code + docs generated together
  /project template university-system   # Save as reusable template
```

| Gate | You Say | What Happens Next |
|------|---------|------------------|
| **Gate 1** | "start planning" | Discovery ends, formal planning begins |
| **Gate 2** | Approve all plans | Plans are locked |
| **Gate 3** | "start coding" | Implementation begins |

### Project Folder Structure
```
projects/<project-name>/
  PROJECT.md                    # Project definition
  STATUS.md                     # Progress tracking
  discovery/                    # Phase 1: Free discussion
    DISCUSSION.md               # Notes, questions, decisions
    requirements-draft.md       # Evolving requirements
    diagrams/                   # Mermaid diagrams (rendered on GitHub)
      architecture.md
      data-flow.md
      er-diagram.md
    research/                   # Research during discovery
      opensource-options.md
      tech-comparison.md
  knowledge/                    # Domain knowledge (built with /knowledge build)
    kuka-robot/                 # Example: KUKA robot knowledge
      kuka-robot.md             # Auto-generated structured knowledge
      docs/                     # YOUR documentation files
        krl-manual.pdf          # PDF manuals you provide
        api-reference.md        # API docs
        examples/               # Code examples, configs
      urls.md                   # Reference URLs
    plc-siemens/                # Example: Siemens PLC knowledge
      plc-siemens.md
      docs/
        s7-datasheet.pdf
  plans/                        # Phase 2: Structured planning
    MASTER-PLAN.md
    01-architecture-plan.md ... 10-monitoring-plan.md
    IMPLEMENTATION-ROADMAP.md
  design/                       # Design documents
    DESIGN.md
    ADR/
  docs/                         # Phase 3: Auto-generated documentation
```

## Recommended Companion Tools

```bash
# Essential (install first)
/plugin install context7            # Fresh library docs
npm i -g uipro-cli && uipro init --ai claude  # UI/UX design
/plugin install security-guidance   # Security pre-hook
/plugin install code-review         # Automated review
/plugin install playwright          # E2E testing

# Stack-specific
/plugin install prisma              # Prisma ORM
/plugin install supabase            # Supabase
/plugin install vercel              # Vercel deploy
/plugin install stripe              # Payments
/plugin install terraform           # Infrastructure as Code
```

Full list with 50+ tools: `.claude/knowledge/recommended-tools.md`
