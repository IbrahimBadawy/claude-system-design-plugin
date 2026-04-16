---
description: After every command completes, suggest 2-4 relevant next commands based on the current workflow stage and what was just done.
globs: "*"
---

# Rule: Suggest Next Commands

After EVERY command execution, show a "What's Next?" section with 2-4 relevant commands.

## Format
```
---
**What's next?**
- `/command1` - [why this is relevant now]
- `/command2` - [why this is relevant now]
- `/command3` - [why this is relevant now]
```

## Suggestion Map

### After Project & Discovery
| After This | Suggest These |
|-----------|---------------|
| `/project add` | `/complexity explain` (if unsure about level), `/project docs add <file>` (if client sent docs), `/discover stakeholders`, `/opensource <system>` |
| `/complexity set` | `/design` or `/project docs add` or `/discover stakeholders` (based on level) |
| `/complexity suggest` | `/complexity set <suggested>`, `/design` |
| `/project docs add` | `/project docs read <file>`, `/discover stakeholders`, `/discover constraints` |
| `/project docs read` | `/discover user-journeys`, `/discover constraints`, `/knowledge build <domain>` |
| `/project docs` | `/project docs add <file>`, `/project docs read <file>` |
| `/discover stakeholders` | `/discover user-journeys`, `/discover constraints`, `/discover risks` |
| `/discover user-journeys` | `/discover constraints`, `/discover mvp`, `/knowledge build <domain>` |
| `/discover constraints` | `/discover risks`, `/discover mvp` |
| `/discover risks` | `/discover mvp`, `/discover summary` |
| `/discover mvp` | `/discover summary`, then say "ابدا plan" |
| `/discover summary` | Say "ابدا plan" to start planning |
| `/opensource` | `/discover summary`, `/tradeoff build vs use` |
| `/knowledge build --global` | `/knowledge build <next-topic>`, `/knowledge list --global` |
| `/knowledge build --project` | `/knowledge build <next-topic> --project`, `/design` |
| `/knowledge promote` | `/knowledge list --global`, `/design` |

### After Design & Architecture
| After This | Suggest These |
|-----------|---------------|
| `/design` | `/evaluate`, `/plan create`, `/domain` |
| `/domain` | `/schema`, `/module create`, `/design` |
| `/schema` | `/api`, `/migration plan`, `/module contract` |
| `/api` | `/schema`, `/security`, `/export openapi` |
| `/gateway` | `/api`, `/security`, `/cicd` |
| `/tenancy` | `/schema`, `/security`, `/privacy` |
| `/estimate` | `/design`, `/cost`, `/scale` |
| `/tradeoff` | `/adr`, `/design`, `/research` |
| `/scale` | `/cost`, `/perf`, `/monitor` |
| `/adr` | `/plan create`, `/design` |

### After Planning
| After This | Suggest These |
|-----------|---------------|
| `/plan create` | `/plan show`, `/plan show 01` |
| `/plan show` | `/plan edit <number>`, `/plan approve <number>` |
| `/plan edit` | `/plan show`, `/plan approve` |
| `/plan approve` | `/plan show` (to check remaining), `/plan approve all` |
| `/plan approve all` | Say "ابدا برمجة" to start coding |
| `/plan implementation` | `/implement`, `/test strategy` |

### After Evaluation & Quality
| After This | Suggest These |
|-----------|---------------|
| `/evaluate` | `/improve`, `/debt`, `/security` |
| `/improve` | `/plan create` (to plan improvements), `/perf`, `/debt` |
| `/security` | `/privacy`, `/runbook incident security-breach` |
| `/perf` | `/scale`, `/monitor`, `/cost` |
| `/debt` | `/improve`, `/analyze codebase` |
| `/privacy` | `/security`, `/tenancy`, `/checklist` |
| `/checklist` | Fix items that failed, then `/implement` or `/evaluate` |
| `/failure` | `/runbook`, `/monitor`, `/test chaos` |

### After Implementation
| After This | Suggest These |
|-----------|---------------|
| `/implement` | `/test strategy`, `/docs generate`, `/cicd` |
| `/frontend design` | `/implement --frontend`, `/frontend libraries` |
| `/frontend libraries` | `/implement --frontend`, `/frontend design` |
| `/backend libs` | `/implement --backend`, `/schema` |
| `/cicd` | `/env design`, `/monitor`, `/runbook oncall` |
| `/monitor` | `/runbook sla`, `/failure`, `/cicd` |
| `/cost` | `/scale`, `/env design` |
| `/postmortem` | `/runbook`, `/monitor`, `/test chaos` |

### After Testing & Analysis
| After This | Suggest These |
|-----------|---------------|
| `/test strategy` | `/test data`, `/implement --tests`, `/cicd` |
| `/test contract` | `/module events map`, `/test strategy` |
| `/test chaos` | `/runbook`, `/failure`, `/monitor` |
| `/test coverage` | `/test strategy`, `/implement --tests` |
| `/analyze codebase` | `/evaluate`, `/debt`, `/improve` |
| `/analyze debt` | `/improve`, `/debt` |
| `/analyze dependencies` | `/security`, `/debt` |

### After Modules
| After This | Suggest These |
|-----------|---------------|
| `/module create` | `/module contract`, `/schema`, `/api` |
| `/module contract` | `/test contract`, `/module events map` |
| `/module events map` | `/module deps graph`, `/design` |
| `/module deps graph` | `/module contract` (fix circular deps), `/evaluate` |

### After Operations & Environment
| After This | Suggest These |
|-----------|---------------|
| `/runbook` | `/monitor`, `/runbook sla`, `/env design` |
| `/runbook sla` | `/monitor`, `/runbook oncall` |
| `/env design` | `/cicd`, `/env feature-flags`, `/env config` |
| `/env feature-flags` | `/implement`, `/test strategy` |
| `/migration plan` | `/migration seed`, `/test strategy`, `/runbook` |

### After Export & Docs
| After This | Suggest These |
|-----------|---------------|
| `/export openapi` | `/export postman`, `/docs generate` |
| `/export github-issues` | `/plan implementation`, `/project status` |
| `/export terraform` | `/cicd`, `/env design` |
| `/docs generate` | `/docs status`, `/project template` |
| `/docs update` | `/docs status`, `/project status` |
| `/research` | `/tradeoff`, `/adr`, `/design` |

## Rules
- Always show suggestions - never skip
- Suggestions must be context-aware (what phase are we in?)
- If in Discovery phase, never suggest /implement
- If plans are not approved, never suggest /implement
- Maximum 4 suggestions to avoid overwhelming
- Include brief reason WHY each command is relevant now
