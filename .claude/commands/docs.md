# /docs - Documentation System

Automatic documentation generation and maintenance for every project.

## Usage
```
/docs generate                   # Generate full documentation from scratch
/docs update                     # Update docs based on recent changes
/docs status                     # Show what's documented vs what's changed
/docs api                        # Generate/update API documentation only
/docs db                         # Generate/update database documentation only
/docs architecture               # Generate/update architecture docs only
/docs frontend                   # Generate/update frontend component docs only
/docs deploy                     # Generate/update deployment docs only
/docs tools                      # Show recommended documentation tools for current stack
/docs site                       # Set up documentation site (Docusaurus/MkDocs/Mintlify)
```

## Recommended Documentation Tools

Use these tools to auto-generate and host professional documentation.
ALWAYS research the latest docs before setting up any tool.

### API Documentation (auto-generate from code)
| Tool | Best For | Install |
|------|---------|---------|
| **Swagger UI** | Standard OpenAPI rendering | `npm install swagger-ui-express` |
| **Redoc** | Cleaner 3-panel API docs | `npx @redocly/cli preview-docs openapi.yaml` |
| **NestJS Swagger** | NestJS auto-generated | `npm install @nestjs/swagger` |
| **FastAPI** | Built-in (Swagger + ReDoc) | Auto at `/docs` and `/redoc` |
| **Spring springdoc** | Spring Boot auto-generated | `springdoc-openapi-starter-webmvc-ui` |

### Frontend / Component Documentation
| Tool | Best For | Install |
|------|---------|---------|
| **Storybook** | Industry standard (React/Vue/Angular) | `npx storybook@latest init` |
| **Ladle** | Lightweight Storybook alternative (React) | `npm install @ladle/react` |
| **Histoire** | Vue/Svelte component stories | `npm install histoire` |

### Database Documentation
| Tool | Best For | Install |
|------|---------|---------|
| **dbdocs.io** | Code-as-docs (DBML format) | `npm install -g dbdocs` |
| **SchemaSpy** | Auto-generate from live DB | `java -jar schemaspy.jar` |
| **DrawSQL** | Visual ERD from SQL | drawsql.app (web) |
| **Prisma Studio** | Visual DB browser (Prisma) | `npx prisma studio` |

### Architecture Documentation
| Tool | Best For | Install |
|------|---------|---------|
| **Mermaid** | Diagrams in Markdown (built into GitHub) | Already supported in .md files |
| **D2** | Modern architecture diagrams | `curl -fsSL https://d2lang.com/install.sh \| sh` |
| **Structurizr** | C4 model diagrams-as-code | `docker pull structurizr/lite` |

### Documentation Sites
| Tool | Best For | Install |
|------|---------|---------|
| **Docusaurus** | Open source, React-based, free | `npx create-docusaurus@latest docs classic` |
| **MkDocs Material** | Python projects, fast setup | `pip install mkdocs-material` |
| **Mintlify** | AI-native, polished, commercial | `npx mintlify@latest dev` |
| **Starlight** | Astro-based, modern, fast | `npm create astro -- --template starlight` |
| **GitBook** | Non-technical contributors | gitbook.com |

### Auto-Generators
| Tool | What It Generates | Install |
|------|------------------|---------|
| **TypeDoc** | Docs from TypeScript source | `npm install typedoc` |
| **Sphinx** | Docs from Python docstrings | `pip install sphinx` |
| **release-please** | Changelog from conventional commits | GitHub Action |
| **conventional-changelog** | CHANGELOG.md from commits | `npm install conventional-changelog-cli` |

### Recommended Stack by Project Type

**TypeScript Full-Stack:**
```bash
npm install @nestjs/swagger            # API docs (auto from decorators)
npx storybook@latest init              # Component docs
npm install -g dbdocs                  # DB docs (from DBML)
npx create-docusaurus@latest docs      # Documentation site
npm install typedoc                    # Code docs
```

**Python Full-Stack:**
```bash
# FastAPI has built-in Swagger + ReDoc at /docs and /redoc
pip install mkdocs-material            # Documentation site
pip install sphinx sphinx-autodoc-typehints  # Code docs from docstrings
```

**Vue/Nuxt:**
```bash
npm install histoire                   # Component docs (Vue-native)
npm install -g dbdocs                  # DB docs
npx create-docusaurus@latest docs      # Documentation site
```

---

## Behavior

### Documentation is DEFAULT - Not Optional

**Every project automatically gets full documentation.**
When `/implement` is used, documentation is generated alongside the code.
When code changes, documentation is flagged for update.

### /docs generate - Full Documentation Suite

Generates the complete documentation structure:

```
docs/
  README.md                      # Project overview, quick start, tech stack
  ARCHITECTURE.md                # System architecture, components, data flow
  API.md                         # Full API reference (auto-generated from code)
  DATABASE.md                    # Schema documentation, ERD, relationships
  DEPLOYMENT.md                  # How to deploy, environment setup, Docker
  DEVELOPMENT.md                 # Development setup, coding standards, workflow
  TESTING.md                     # Testing strategy, how to run tests, coverage
  SECURITY.md                    # Security practices, auth flow, permissions
  CHANGELOG.md                   # Version history and changes
  TROUBLESHOOTING.md             # Common issues and solutions
  frontend/
    COMPONENTS.md                # UI component catalog with usage examples
    STYLING.md                   # Design system, colors, typography, spacing
    STATE.md                     # State management documentation
    ROUTING.md                   # Route structure and navigation
  api/
    endpoints/                   # Per-endpoint documentation (auto-generated)
      users.md
      auth.md
      [resource].md
```

### Document Formats

#### README.md
```markdown
# [Project Name]

[One-line description]

## Tech Stack
| Layer | Technology |
|-------|-----------|
| Frontend | [framework + UI library] |
| Backend | [framework] |
| Database | [DB + ORM] |
| Cache | [Redis/etc] |
| Queue | [if used] |

## Quick Start

### Prerequisites
- Node.js >= 20
- PostgreSQL >= 16
- Redis >= 7

### Installation
[Step-by-step commands to get running locally]

### Environment Variables
| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| DATABASE_URL | PostgreSQL connection string | Yes | - |
| JWT_SECRET | JWT signing secret | Yes | - |
| REDIS_URL | Redis connection string | No | redis://localhost:6379 |

### Running
[Commands to start dev server]

### Testing
[Commands to run tests]

## Project Structure
[Directory tree with descriptions]

## Architecture Overview
[Brief description + link to ARCHITECTURE.md]

## API Reference
[Brief description + link to API.md]

## Contributing
[Guidelines]
```

#### API.md (Auto-Generated from Code)
```markdown
# API Reference

**Base URL**: `http://localhost:3000/api/v1`
**Auth**: Bearer JWT token in `Authorization` header

## Authentication
### POST /api/v1/auth/login
Login and get JWT token.

**Request:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| email | string | Yes | User email |
| password | string | Yes | User password |

**Response 200:**
| Field | Type | Description |
|-------|------|-------------|
| access_token | string | JWT access token (15min) |
| refresh_token | string | Refresh token (7days) |

**Response 401:**
| Field | Type | Description |
|-------|------|-------------|
| error.code | string | AUTH_FAILED |
| error.message | string | Invalid credentials |

**Example:**
curl -X POST http://localhost:3000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "pass"}'

[... for EVERY endpoint ...]
```

#### DATABASE.md
```markdown
# Database Documentation

## Entity Relationship Diagram
[Text-based ERD or description]

## Tables

### users
| Column | Type | Nullable | Default | Description |
|--------|------|----------|---------|-------------|
| id | UUID | No | gen_random_uuid() | Primary key |
| email | VARCHAR(255) | No | - | Unique email |
| ...

**Indexes:**
- `idx_users_email` UNIQUE on (email) - Login lookup
- `idx_users_created_at` on (created_at) - Sorting

**Relationships:**
- users.id -> orders.user_id (1:N)

[... for EVERY table ...]

## Migrations History
| Version | Name | Date | Description |
|---------|------|------|-------------|
| 001 | init | 2026-04-13 | Create users, roles tables |
| 002 | add_orders | 2026-04-14 | Create orders, order_items |
```

#### ARCHITECTURE.md
```markdown
# System Architecture

## Overview
[System description and purpose]

## Architecture Diagram
[Component diagram in text/mermaid format]

## Components
### [Component Name]
- **Purpose**: [what it does]
- **Technology**: [what it's built with]
- **Communicates with**: [other components]
- **Data stores**: [databases, caches it uses]
- **Key patterns**: [design patterns applied]

## Data Flow
### [Use Case 1]: User Login
1. Client sends POST /api/v1/auth/login
2. API Gateway validates request format
3. Auth Service verifies credentials against DB
4. JWT token generated and returned
5. Client stores token for subsequent requests

## Design Decisions (ADRs)
- [ADR-001: Chose PostgreSQL over MongoDB](./ADR/ADR-001.md)
- [ADR-002: JWT over session-based auth](./ADR/ADR-002.md)

## Scaling Strategy
[Current architecture and growth plan]
```

### /docs update - Smart Documentation Update

When invoked:
1. **Scan for changes**: Compare current codebase against documented state
2. **Identify gaps**:
   - New API endpoints without docs
   - New DB tables/columns without docs
   - New components without docs
   - Changed function signatures
   - New environment variables
   - Updated dependencies
3. **Update only what changed**: Don't regenerate everything
4. **Show summary**:
   ```
   Documentation Update Summary:
   - Updated: API.md (3 new endpoints added)
   - Updated: DATABASE.md (1 new table, 2 new columns)
   - Updated: README.md (new env variable added)
   - No changes: ARCHITECTURE.md, DEPLOYMENT.md
   ```

### /docs status - Documentation Health Check

```
## Documentation Status

| Document | Status | Last Updated | Gaps |
|----------|--------|-------------|------|
| README.md | Current | 2026-04-13 | None |
| API.md | OUTDATED | 2026-04-10 | 3 undocumented endpoints |
| DATABASE.md | OUTDATED | 2026-04-08 | 1 new table undocumented |
| ARCHITECTURE.md | Current | 2026-04-13 | None |
| COMPONENTS.md | MISSING | - | Not generated yet |

Recommendation: Run `/docs update` to fix 2 outdated documents.
```

## Auto-Documentation Rules

### During /implement
- After creating API endpoints -> auto-generate endpoint docs
- After creating DB schema -> auto-generate table docs
- After creating frontend components -> auto-generate component catalog
- After setting up Docker -> auto-generate deployment docs

### Change Detection
The documentation system tracks changes. When significant changes accumulate
without a docs update, it should:
1. Warn the user: "Documentation is outdated. 5 endpoints added since last update."
2. Suggest: "Run `/docs update` to sync documentation."
3. If changes exceed threshold (>10 undocumented items), auto-trigger update.

### What Triggers Auto-Update Suggestion
- 3+ new API endpoints without docs
- 2+ new DB tables without docs
- 5+ new/modified files without docs update
- New environment variables added
- Major dependency version changes

## Examples
```
/docs generate
/docs update
/docs status
/docs api
/docs db
/docs architecture
```
