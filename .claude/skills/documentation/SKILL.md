---
name: documentation
description: Auto-documentation skill - triggered when generating, updating, or maintaining project documentation. Generates API docs, database docs, architecture docs, component docs, README, and deployment guides. Also recommends documentation tools (Swagger, Storybook, TypeDoc, Docusaurus, etc.) based on the project stack.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# Documentation Skill

You are generating or updating project documentation. Documentation is DEFAULT, not optional.

## When This Skill Activates
- User runs `/docs generate`, `/docs update`, `/docs status`, `/docs tools`, `/docs site`
- After significant code changes (3+ endpoints, 2+ tables, 5+ files modified)
- When implementing a project (auto-generate alongside code)
- When user asks about documentation

## Documentation Structure
```
docs/
  README.md                  # Quick start, tech stack, env vars
  ARCHITECTURE.md            # System architecture, C4 diagrams, data flow
  API.md                     # Full API reference (endpoints, request/response, curl)
  DATABASE.md                # Schema, ERD, indexes, relationships, migrations
  DEPLOYMENT.md              # Docker, CI/CD, environment setup
  DEVELOPMENT.md             # Dev setup, coding standards, workflow
  TESTING.md                 # Test strategy, how to run, coverage
  SECURITY.md                # Auth flow, encryption, access control
  CHANGELOG.md               # Version history
  TROUBLESHOOTING.md         # Common issues and fixes
  frontend/
    COMPONENTS.md            # Component catalog with usage
    STYLING.md               # Design tokens, colors, fonts, spacing
    ROUTING.md               # Route structure
```

## Documentation Tools by Stack

### Auto-Generate API Docs
| Stack | Tool | How |
|-------|------|-----|
| NestJS | @nestjs/swagger | Decorators auto-generate OpenAPI spec -> Swagger UI at /api |
| FastAPI | Built-in | Auto at /docs (Swagger) and /redoc (ReDoc) |
| Express | swagger-jsdoc + swagger-ui-express | JSDoc comments -> OpenAPI spec |
| Spring Boot | springdoc-openapi | Annotations auto-generate -> Swagger UI |
| Django | drf-spectacular | DRF serializers -> OpenAPI 3.0 |
| Go/Gin | swaggo/swag | Go comments -> Swagger spec |

### Component Documentation
| Stack | Tool | How |
|-------|------|-----|
| React | Storybook | `npx storybook@latest init` - story files per component |
| Vue | Histoire | `npm install histoire` - Vue-native stories |
| Angular | Storybook | Same as React, Angular support built-in |
| React (lightweight) | Ladle | `npm install @ladle/react` - Vite-based, faster |

### Database Documentation
| Tool | How | Best For |
|------|-----|----------|
| dbdocs.io | Write DBML -> hosted docs | Code-as-docs, works with any DB |
| SchemaSpy | `java -jar schemaspy.jar` | Auto-generate from live DB |
| Prisma Studio | `npx prisma studio` | Visual browser for Prisma |
| DrawSQL | drawsql.app | Quick visual ERDs |

### Architecture Diagrams
| Tool | How | Best For |
|------|-----|----------|
| Mermaid | Embed in Markdown (GitHub renders natively) | Quick diagrams in docs |
| D2 | `d2 diagram.d2 output.svg` | Beautiful architecture diagrams |
| Structurizr | DSL -> C4 model diagrams | Professional architecture docs |

### Documentation Sites
| Tool | Best For | Install |
|------|---------|---------|
| Docusaurus | React-based, open source, free | `npx create-docusaurus@latest docs classic` |
| MkDocs Material | Python projects, fast | `pip install mkdocs-material` |
| Mintlify | Commercial, AI-native, polished | `npx mintlify@latest dev` |
| Starlight (Astro) | Modern, fast | `npm create astro -- --template starlight` |

### Code Documentation
| Tool | Language | Install |
|------|----------|---------|
| TypeDoc | TypeScript | `npm install typedoc` |
| JSDoc | JavaScript | `npm install jsdoc` |
| Sphinx | Python | `pip install sphinx sphinx-autodoc-typehints` |
| GoDoc | Go | Built-in (`go doc`) |
| Javadoc | Java | Built-in |

### Changelog Auto-Generation
| Tool | How | Install |
|------|-----|---------|
| release-please | From conventional commits -> CHANGELOG + GitHub release | GitHub Action |
| conventional-changelog | CLI changelog generation | `npm install conventional-changelog-cli` |
| changesets | Monorepo versioning + changelogs | `npm install @changesets/cli` |

## Documentation Quality Rules

### Every Project MUST Have
- [ ] README with Quick Start (new dev running in < 5 minutes)
- [ ] Every public API endpoint documented (method, URL, request, response, errors)
- [ ] Every database table documented (columns, types, indexes, relationships)
- [ ] Every environment variable documented (name, description, required, default)
- [ ] Architecture overview with component diagram

### Auto-Update Triggers
When significant changes happen without docs update:
- 3+ new API endpoints -> WARN user, suggest `/docs update`
- 2+ new DB tables -> WARN user
- 5+ modified files -> Suggest `/docs update`
- 10+ undocumented items -> Auto-run documentation update

### What NOT to Document
- Internal implementation details that change frequently
- Auto-generated code (unless usage is non-obvious)
- Self-documenting clean code (good names don't need comments)

## Process

### When generating docs (`/docs generate`):
1. Scan the codebase for: routes, controllers, models, schemas, env vars, components
2. Extract API endpoints from decorators/annotations/route definitions
3. Extract schema from ORM models or migration files
4. Generate each document using the templates above
5. Recommend and optionally install documentation tools for the stack

### When updating docs (`/docs update`):
1. Compare codebase against existing docs
2. Find gaps (new endpoints, tables, components, env vars)
3. Update only what changed
4. Show summary of updates

### When checking docs (`/docs status`):
1. Scan codebase for documentable items
2. Check which are documented
3. Show health report with gaps
