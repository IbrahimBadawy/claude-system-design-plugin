# System Design Architect - Claude Code Plugin

The most comprehensive system design plugin for Claude Code.
Design, evaluate, plan, and build production-grade distributed systems.

**85 files | 32 commands | 16 rules | 9 skills | 16 design references | 8 books**

Built on deep knowledge from: System Design Interview (Alex Xu), DDIA (Kleppmann),
Clean Architecture & Clean Code (Martin), Design Patterns (GoF), Refactoring (Fowler),
Code Complete (McConnell).

## Quick Start

```bash
# 1. Copy to your project
cp -r .claude /your-project/
cp CLAUDE.md /your-project/

# 2. Install companion tools
/plugin install context7
npm i -g uipro-cli && uipro init --ai claude
/plugin install security-guidance

# 3. Open in Claude Code and start designing
claude /your-project
/project add my-system
/design my-system
```

## Available Commands

| Command | Description |
|---------|-------------|
| `/design <system>` | Full system design using 4-step framework |
| `/evaluate <system>` | Evaluate an existing system critically |
| `/improve <system>` | Generate improvement recommendations |
| `/implement` | Start coding the current design |
| `/estimate <desc>` | Back-of-envelope capacity calculations |
| `/tradeoff <A> vs <B>` | Structured trade-off analysis |
| `/scale <component>` | Design scaling strategy |
| `/schema <entity>` | Database schema design |
| `/api <resource>` | REST API design |
| `/failure <scenario>` | Failure analysis and recovery |
| `/monitor` | Design observability stack |
| `/security` | Security review |
| `/checklist` | System design review checklist |
| `/adr <title>` | Architecture Decision Record |
| `/postmortem <incident>` | Incident postmortem |
| `/research <topic>` | Research latest technologies |
| `/project add <name>` | Add a new project |
| `/project status` | Show all projects |
| `/project template <name>` | Convert project to reusable template |
| `/project template use <t> <p>` | Create project from template |
| `/project deps` | Show project dependency graph |
| `/project deps set-core <name>` | Mark as core project |
| `/project deps add <p> --depends-on <c>` | Define dependency |
| `/project deps check <name>` | Verify dependency alignment |
| `/cost` | Infrastructure cost estimation |
| `/frontend design` | Professional frontend design (asks style, picks best UI lib) |
| `/frontend libraries <fw>` | Best UI component libraries for a framework |
| `/backend libs <fw>` | Best backend helper libraries |
| `/docs generate` | Generate full project documentation |
| `/docs update` | Update docs for recent changes |
| `/docs status` | Check documentation freshness |
| `/domain <system>` | Domain modeling with DDD |
| `/privacy <system>` | GDPR/data privacy compliance |
| `/tenancy <system>` | Multi-tenancy architecture |
| `/perf <system>` | Performance audit & optimization |
| `/cicd <system>` | CI/CD pipeline design |
| `/gateway <system>` | API gateway & BFF design |
| `/debt <system>` | Technical debt management |
| `/opensource <system>` | Research open source alternatives, build vs buy |
| `/plan create <project>` | Create master plan + 10 sub-plans |
| `/plan show [number]` | View master plan or specific sub-plan |
| `/plan edit <number>` | Edit a specific plan |
| `/plan approve <number/all>` | Approve plan(s) |
| `/plan status` | Approval dashboard |
| `/plan implementation` | Generate implementation roadmap |

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

## 16 Design References

Pre-built architecture references from Alex Xu's books:

| | | | |
|---|---|---|---|
| Rate Limiter | URL Shortener | Unique ID Gen | Key-Value Store |
| Chat System | News Feed | Notifications | Autocomplete |
| Web Crawler | Proximity Service | Video Platform | File Storage |
| Payment System | Leaderboard | Monitoring | Stock Exchange |

## Knowledge Sources

- System Design Interview Vol 1 & 2 (Alex Xu / ByteByteGo)
- Designing Data-Intensive Applications (Martin Kleppmann)
- Clean Architecture (Robert C. Martin)
- Clean Code (Robert C. Martin)
- Design Patterns (Gang of Four)
- Refactoring (Martin Fowler)
- Code Complete (Steve McConnell)

## Plugin Structure (85 files)

```
CLAUDE.md                        # Main configuration
.claude/
  settings.json                  # Permissions
  commands/                      # 32 command files
    design, evaluate, improve, implement, plan,
    frontend, backend-libs, docs, research, opensource,
    schema, api, domain, gateway, tenancy,
    estimate, tradeoff, scale, cost, adr,
    security, perf, debt, privacy, cicd,
    failure, monitor, checklist, postmortem,
    project, project-deps, project-template
  rules/                         # 16 always-active rules
    01-requirements-first    09-data-model
    02-design-before-code    10-api-standards
    03-justify-decisions     11-research-before-code
    04-think-about-failure   12-auto-documentation
    05-security-by-default   13-no-code-without-plan
    06-clean-code            14-domain-modeling
    07-scalability           15-accessibility
    08-observability         16-performance-budgets
  skills/                        # 9 auto-invoked skills
    design-system/           # System design (4-step framework)
    evaluate-system/         # System evaluation & audit
    implement-system/        # Full-stack implementation
    research-tech/           # Technology research
    project-manager/         # Project & plan management
    documentation/           # Auto-docs (Swagger, Storybook, etc.)
    frontend-design/         # UI library selection & design
    devops/                  # Docker, CI/CD, deployment
    opensource-research/     # Build vs buy vs fork
  knowledge/                     # Reference knowledge base
    patterns.md              # 16 architecture patterns
    technology-guide.md      # 2026 tech stack guide
    distributed-systems.md   # DDIA concepts (consensus, CDC, streams)
    clean-architecture-guide.md  # SOLID, GoF, concurrency, Code Complete
    recommended-tools.md     # 50+ companion tools
    design-references/       # 16 full system design references
      01-rate-limiter ... 16-stock-exchange
  projects/                      # Client project tracking
  templates/                     # Reusable project templates
```

## Project Workflow

```
/project add university-system        # 1. Create project
/design university-system             # 2. System design (4-step)
/plan create university-system        # 3. Generate all plans
/plan show                            # 4. Review plans
/plan edit 05                         # 5. Edit frontend plan
/plan approve all                     # 6. Approve when ready
# User says: "ابدا برمجة"              # 7. ONLY NOW coding begins
/plan implementation                  # 8. Get step-by-step roadmap
# Follow roadmap...                   # 9. Build incrementally
/docs update                          # 10. Keep docs in sync
/project template university-system   # 11. Save as reusable template
```

**No code is written until you explicitly say to start coding.**

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
/plugin install vercel              # Vercel deploy
/plugin install stripe              # Payments
```

Full list with 50+ tools: `.claude/knowledge/recommended-tools.md`

