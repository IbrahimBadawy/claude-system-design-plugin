# System Design Architect - Claude Code Plugin

The most comprehensive system design plugin for Claude Code.
Design, evaluate, plan, and build production-grade distributed systems.

**113 files | 42 commands | 19 rules | 14 skills | 4 templates | 16 design references | 8 books**

## Features

### 1. Smart Scope Detection
Auto-detects project complexity. Simple tasks skip the full workflow:
- **Simple** ("quick script") -> Skip planning, code directly
- **Medium** ("build an API") -> Light design, optional planning
- **Complex** ("design a SaaS") -> Full 3-gate workflow

### 2. 3-Gate Workflow (Discovery -> Planning -> Code)
You control every phase transition. Nothing happens without your command:
- **Phase 1: Discovery** - Free discussion, questions, diagrams, research. NO plans, NO code.
- **Gate 1**: You say "start planning"
- **Phase 2: Planning** - Master plan + 10 sub-plans. Review, edit, approve each.
- **Gate 2**: You say "start coding"
- **Phase 3: Implementation** - Step-by-step roadmap. Real code + auto-docs.

### 3. 4-Step Design Framework (Alex Xu)
Every system design follows: Requirements & Estimation -> High-Level Architecture -> Deep Dive -> Production Readiness. References 16 pre-built design patterns (Rate Limiter, Chat System, Payment System, etc.)

### 4. Domain Knowledge Builder
Working with robotics, PLCs, medical, finance? Build a knowledge base FIRST:
- `/knowledge build <topic>` - Deep web research + organize into structured knowledge
- `/knowledge import <file>` - Import your own PDFs, manuals, datasheets
- Knowledge auto-used during design and implementation

### 5. Open Source Research (Build vs Buy)
Before designing from scratch, search for existing solutions:
- `/opensource <system>` - Find alternatives, compare features, check licenses
- License warnings: MIT (safe), GPL (careful), AGPL (SaaS danger!)
- Decision matrix: Build vs Use vs Fork vs Hybrid

### 6. Full-Stack Implementation
Writes REAL production code. Specify your stack:
- `/implement --frontend react --backend nestjs`
- Researches latest docs BEFORE using any library
- Clean Architecture layers, SOLID principles, GoF patterns
- Supports: React, Vue, Next.js, NestJS, FastAPI, Spring Boot, Go, Django

### 7. Professional Frontend Design
- Asks about your style, layout, and feature preferences
- Picks the best UI library (shadcn, PrimeVue, Ant Design, MUI, etc.)
- Enforces accessibility (WCAG 2.2 AA), RTL support, responsive design
- Works with uipro-cli for 67 styles, 161 color palettes, 57 font pairings

### 8. Module & Service Communication
- `/module create <name>` - Module with manifest, contracts, boundaries
- `/module events map` - Visualize event flow across all modules (Mermaid)
- `/module contract <a> <b>` - Define API/event contracts between modules
- Each module owns its data exclusively

### 9. Structured Discovery Phase
Not just "open discussion" - structured sub-commands:
- `/discover stakeholders` - Map all stakeholders with priorities
- `/discover user-journeys` - Critical user journeys with sequence diagrams
- `/discover constraints` - Technical, legal, budget, timeline limits
- `/discover risks` - Risk register with probability/impact/mitigation
- `/discover mvp` - Define MVP scope (Phase 1 vs later)

### 10. Testing & Chaos Engineering
- `/test strategy` - Design test pyramid (70% unit, 20% integration, 10% e2e)
- `/test contract <a> <b>` - Contract testing between services
- `/test chaos <scenario>` - Chaos experiments (DB failover, cache failure, high load)
- Rule 18: Testing is mandatory, not optional

### 11. Auto Documentation
Documentation generated alongside code, not after:
- API docs (Swagger), DB docs, component docs (Storybook), architecture docs
- Recommends tools: Docusaurus, MkDocs, TypeDoc, Sphinx
- Auto-warns when docs are outdated (3+ undocumented endpoints = warning)

### 12. Export & Integration
- `/export openapi` - Export API as OpenAPI/Swagger YAML
- `/export github-issues` - Create GitHub issues from plan tasks
- `/export terraform` - Export infrastructure as Terraform HCL
- `/export docker` - Generate docker-compose from architecture

### 13. Operations & Reliability
- `/runbook <scenario>` - Operational runbooks (what to do when X fails)
- `/runbook sla <service>` - Define SLA/SLO/SLI with error budgets
- `/env design` - Dev -> Staging -> Prod pipeline
- `/env feature-flags` - Gradual rollout strategy
- `/migration plan` - Zero-downtime migration (expand-contract pattern)

### 14. Security & Compliance
- `/security` - OWASP Top 10 review
- `/privacy` - GDPR/data privacy compliance design
- `/tenancy` - Multi-tenancy architecture (silo/pool/bridge)
- Rule 05: Security by default. Rule 17: Tenant isolation mandatory.

### 15. Code Analysis & Reverse Engineering
- `/analyze codebase` - Scan existing code, extract architecture
- `/analyze debt` - Automated tech debt detection
- `/analyze dependencies` - Outdated, vulnerable, unused packages
- Works with legacy code, not just greenfield projects

### 16. Project Management
- `/project add <name>` - Create tracked project with full folder structure
- `/project deps` - Define core projects and dependent projects
- `/project template <name>` - Convert completed project to reusable template
- `/project docs add <file>` - Add client docs, auto-extract requirements
- Manage multiple client projects simultaneously

### 17. Smart Next-Step Suggestions (Rule 19)
After EVERY command, suggests 2-4 relevant next commands. Never wonder "what do I do next?"

---

## Full Example: Building a University Management System

Here's how you'd use the plugin to build a complete system from scratch:

### Phase 1: Discovery (Free Discussion)

```bash
# Step 1: Create the project
/project add university-system

# Step 2: Add client documents
/project docs add client-requirements.pdf
/project docs add campus-mockups.png

# Step 3: Build domain knowledge
/knowledge build university-lms
/knowledge build student-information-systems

# Step 4: Research open source alternatives
/opensource university-lms
# -> Finds Moodle (GPL), Canvas (AGPL), Open edX (AGPL)
# -> Recommends: Build from scratch (AGPL license risk for SaaS)

# Step 5: Structured discovery
/discover stakeholders
# -> Maps: Students, Instructors, Admin, Parents, IT Department

/discover user-journeys
# -> Maps: Student Registration, Course Enrollment, Grading, Payment
# -> Generates Mermaid sequence diagrams in discovery/diagrams/

/discover constraints
# -> Technical: Must support Arabic (RTL), integrate with existing LDAP
# -> Budget: Max $500/month infrastructure
# -> Scale: 5,000 students, 200 concurrent users

/discover risks
# -> Risk: Integration with legacy student records system
# -> Risk: Peak load during registration period

/discover mvp
# -> Phase 1: Auth + Courses + Enrollment + Grades
# -> Phase 2: Payments + Notifications + Reports
# -> Phase 3: Mobile app + Analytics + AI features

/discover summary
# -> Compiles everything into DISCOVERY-SUMMARY.md
```

**>>> You say: "ابدا plan" (start planning) <<<**

### Phase 2: Planning (Structured Plans)

```bash
# Step 6: Generate all plans
/plan create university-system
# -> Creates MASTER-PLAN.md + 10 sub-plans:
#    01-architecture, 02-database, 03-api, 04-backend,
#    05-frontend, 06-auth, 07-infrastructure, 08-testing,
#    09-security, 10-monitoring

# Step 7: Review and refine
/plan show                    # Overview of all plans
/plan show 02                 # Database plan in detail
/plan edit 05                 # Edit frontend plan (change UI library)
/plan show 06                 # Review auth plan

# Step 8: Design modules
/module create enrollment
/module create grading
/module create payment
/module contract enrollment payment
/module events map
# -> Generates: EnrollmentConfirmed -> PaymentService
#               PaymentCompleted -> EnrollmentService

# Step 9: Approve all plans
/plan approve all
# -> "All plans approved! Say 'ابدا برمجة' when ready."
```

**>>> You say: "ابدا برمجة" (start coding) <<<**

### Phase 3: Implementation (Real Code)

```bash
# Step 10: Get the roadmap
/plan implementation
# -> Generates IMPLEMENTATION-ROADMAP.md with 7 phases

# Step 11: Frontend design
/frontend design
# -> Asks: Dashboard style? -> "Modern & Minimal"
# -> Asks: Layout? -> "Sidebar + Top Navbar"
# -> Recommends: React + shadcn/ui + Tailwind + TanStack Query
# -> Asks: RTL support? -> Yes (Arabic)

# Step 12: Start coding
/implement --frontend react --backend nestjs
# -> Researches latest NestJS, Prisma, React docs
# -> Creates project scaffold with Clean Architecture
# -> Implements: domain -> use cases -> infrastructure -> API -> frontend
# -> Generates tests alongside code
# -> Generates API docs (Swagger) automatically

# Step 13: Testing
/test strategy university-system
# -> 70% unit (domain logic), 20% integration (API), 10% e2e (enrollment flow)

/test chaos database-failover
# -> Designs: kill primary DB, verify replica takes over, zero data loss

# Step 14: Infrastructure
/cicd university-system
# -> GitHub Actions: lint -> test -> build -> deploy staging -> e2e -> deploy prod

/env design
# -> Local (Docker) -> Staging (auto on merge) -> Prod (manual approval)

/export docker
# -> Generates docker-compose.yml with app, PostgreSQL, Redis

# Step 15: Documentation
/docs generate
# -> README.md, API.md, DATABASE.md, ARCHITECTURE.md, DEPLOYMENT.md

# Step 16: Operations
/runbook database-primary-down
/runbook sla enrollment-service
# -> SLO: 99.9% availability, p99 < 500ms

/monitor
# -> Prometheus + Grafana + alerting rules

# Step 17: Save as template
/project template university-system
# -> Saves as reusable template for next university client

# Step 18: Next university client
/project template use university-system cairo-university
# -> Bootstraps new project from template, customize for Cairo University
```

---

## 42 Commands Reference

### Getting Started
| Command | Description |
|---------|-------------|
| `/quickstart` | Interactive guide - top 10 commands, workflow, tips |

### Design & Architecture (10)
| `/design` | `/domain` | `/schema` | `/api` | `/gateway` |
|-----------|----------|----------|--------|-----------|
| `/tenancy` | `/estimate` | `/tradeoff` | `/scale` | `/adr` |

### Evaluation & Quality (8)
| `/evaluate` | `/improve` | `/security` | `/perf` |
|------------|----------|-----------|--------|
| `/debt` | `/privacy` | `/checklist` | `/failure` |

### Implementation (8)
| `/implement` | `/frontend design` | `/frontend libraries` | `/backend libs` |
|-------------|-------------------|---------------------|----------------|
| `/cicd` | `/monitor` | `/cost` | `/postmortem` |

### Planning & Projects (9)
| `/plan create` | `/plan show/edit/approve` | `/plan implementation` |
|---------------|-------------------------|----------------------|
| `/project add` | `/project deps` | `/project template` |
| `/project docs` | `/project docs add` | `/project docs read` |

### Testing & Analysis (7)
| `/test strategy` | `/test contract` | `/test chaos` | `/test data` |
|-----------------|-----------------|--------------|-------------|
| `/analyze codebase` | `/analyze debt` | `/analyze dependencies` |

### Modules (4)
| `/module create` | `/module contract` | `/module events map` | `/module deps graph` |

### Discovery (5)
| `/discover stakeholders` | `/discover user-journeys` | `/discover constraints` |
|-------------------------|-------------------------|----------------------|
| `/discover risks` | `/discover mvp` |

### Operations (5)
| `/runbook` | `/runbook sla` | `/env design` | `/env feature-flags` | `/migration plan` |

### Export (4)
| `/export openapi` | `/export github-issues` | `/export terraform` | `/export docker` |

### Research & Docs (6)
| `/research` | `/opensource` | `/docs generate` | `/docs update` | `/knowledge build` | `/knowledge import` |

## 14 Auto-Invoked Skills

| Skill | Triggers When |
|-------|--------------|
| **design-system** | Designing new systems |
| **evaluate-system** | Reviewing existing systems |
| **implement-system** | Writing code |
| **research-tech** | Comparing technologies |
| **project-manager** | Managing projects |
| **documentation** | Generating docs |
| **frontend-design** | Designing UI |
| **devops** | Setting up CI/CD, Docker |
| **opensource-research** | Searching for existing solutions |
| **knowledge-builder** | Building domain knowledge |
| **web-apps** | Building web apps (SPA, SSR, PWA, framework selection) |
| **desktop-apps** | Building desktop apps (Electron, Tauri, WPF, Qt, packaging) |
| **mobile-apps** | Building mobile apps (React Native, Flutter, Swift, Kotlin) |
| **cross-platform** | Multi-platform targeting or migrating between platforms |

## 19 Always-Active Rules

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
| 13 | 3-Gate Workflow |
| 14 | Domain Modeling |
| 15 | Accessibility |
| 16 | Performance Budgets |
| 17 | Tenant Isolation |
| 18 | Testing Required |
| 19 | Suggest Next Commands |

## Quick Start

```bash
cp -r .claude /your-project/ && cp CLAUDE.md /your-project/
/plugin install context7
claude /your-project
/quickstart
```

## License

MIT License - see [LICENSE](LICENSE) for details.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add commands, rules, skills, and templates.
