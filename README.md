# System Design Architect - Claude Plugin

A comprehensive Claude Code plugin for professional system design work.
Built on knowledge from 8 foundational software engineering books and modern best practices.

## Quick Start

Open this directory in Claude Code. The plugin loads automatically.

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

## Knowledge Sources

- System Design Interview Vol 1 & 2 (Alex Xu / ByteByteGo)
- Designing Data-Intensive Applications (Martin Kleppmann)
- Clean Architecture (Robert C. Martin)
- Clean Code (Robert C. Martin)
- Design Patterns (Gang of Four)
- Refactoring (Martin Fowler)
- Code Complete (Steve McConnell)

## Plugin Structure

```
CLAUDE.md              # Main configuration
.claude/
  commands/            # 18 individual command files
  rules/               # 10 always-active rules
  skills/              # 5 auto-invoked skills
  knowledge/           # Reference knowledge base
  projects/            # Client project tracking
  settings.json        # Permissions
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

