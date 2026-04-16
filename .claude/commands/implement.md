# /implement - Start Implementation

Begin actual coding and building the system. This command WRITES REAL CODE.

## Usage
```
/implement                                          # Full implementation (asks for stack)
/implement --frontend react --backend nestjs         # Specify stack directly
/implement --frontend next --backend fastapi          # Another stack example
/implement --frontend angular --backend spring        # Java stack
/implement --frontend vue --backend django            # Python stack
/implement --backend express                          # Backend only
/implement --frontend react-native                    # Mobile frontend only
/implement <component>                                # Implement specific component
/implement --scaffold                                 # Project scaffold only
/implement --api                                      # API layer only
/implement --db                                       # Database layer + migrations only
/implement --tests                                    # Generate tests only
/implement --frontend                                 # Frontend only
/implement --backend                                  # Backend only
/implement --infra                                    # Docker, CI/CD, deployment configs
```

## Stack Configuration

When user specifies `--frontend` and/or `--backend`, use the specified technologies.
If not specified, ASK the user:

```
What technology stack do you want to use?

Frontend:
  1. React (with Vite)
  2. Next.js (React + SSR)
  3. Vue.js (with Vite)
  4. Nuxt (Vue + SSR)
  5. Angular
  6. React Native (mobile)
  7. Flutter (mobile)
  8. None (API only)

Backend:
  1. Node.js + Express
  2. Node.js + NestJS
  3. Python + FastAPI
  4. Python + Django
  5. Go + Gin
  6. Java + Spring Boot
  7. Rust + Axum
  8. C# + .NET

Database:
  1. PostgreSQL (default)
  2. MySQL
  3. MongoDB
  4. SQLite (dev/small projects)

ORM/Query Builder:
  - Node.js: Prisma / TypeORM / Drizzle
  - Python: SQLAlchemy / Django ORM / Tortoise
  - Go: GORM / sqlx
  - Java: JPA/Hibernate
  - Rust: Diesel / SQLx
```

## CRITICAL: Research Before Implementation

**BEFORE writing any code with a framework or library:**

1. **Check if you know the LATEST API** for the chosen technology
2. **If ANY doubt**, use WebSearch/WebFetch to:
   - Find the official documentation site
   - Read the "Getting Started" or "Quick Start" guide
   - Check the latest version and any breaking changes
   - Read the API reference for specific features you'll use
3. **ALWAYS research** for:
   - ORMs (Prisma, Drizzle, SQLAlchemy) - schema syntax changes frequently
   - Auth libraries (NextAuth/Auth.js, Passport, etc.) - APIs change between versions
   - UI frameworks (Tailwind, shadcn, MUI) - class names and components evolve
   - Build tools (Vite, Turbopack, esbuild) - config formats change
   - Cloud SDKs (AWS SDK v3, Firebase v10+) - major version differences
   - Any package you haven't used recently
4. **Fetch the docs page** and extract:
   - Correct import statements
   - Current API signatures
   - Configuration format
   - Required peer dependencies

### Research Workflow
```
BEFORE: About to use Prisma with PostgreSQL
ACTION: 
  1. WebSearch "prisma postgresql setup 2026 docs"
  2. WebFetch the official Prisma docs page
  3. Extract: schema format, client API, migration commands
  4. NOW write code using verified current syntax

BEFORE: About to set up NextAuth
ACTION:
  1. WebSearch "nextauth.js v5 docs 2026" or "auth.js docs"
  2. WebFetch the getting started page
  3. Extract: provider setup, session handling, middleware
  4. NOW implement auth using verified API

BEFORE: About to use a library for the first time
ACTION:
  1. WebSearch "<library-name> official docs"
  2. WebFetch docs page
  3. Read installation, basic usage, and common patterns
  4. NOW implement with confidence
```

## Implementation Flow

### Phase 0: Pre-Flight & Completeness Check
1. **Read project complexity** from `projects/<active>/PROJECT.md` — this determines
   how much scaffolding to generate.
2. **Read user skill** from PROJECT.md — controls explanation verbosity.
3. Verify completeness artifacts exist (if user-facing app):
   - `design/PAGES.md` — run `/pages` if missing
   - `design/RBAC.md` — run `/rbac` if missing (and app has users)
   - `design/UX-KIT.md` — run `/ux-kit` if missing
4. Verify system design exists (check DESIGN.md or current conversation)
5. Confirm technology stack with user (or use Stack section of PROJECT.md)
6. **Research the chosen stack** - fetch latest docs for key technologies
7. Create implementation plan (list of files to create, in order)
8. Generated code lives in `projects/<active>/src/` (NOT in `.claude/`)

### What Gets Auto-Generated (every run)

For any user-facing app, `/implement` automatically scaffolds:

**Auth pages (always):** login, register, forgot password, reset password, email verify,
  2FA (if enabled), invite accept (if multi-user)
**User account:** profile, settings, security, notifications, billing (if paid), API keys,
  danger zone
**Admin panel (if multi-user):** dashboard, users list, user detail, roles, permissions
  matrix, audit log, system settings
**Main dashboard:** greeting + KPIs + charts + recent activity + quick actions
**Per entity (CRUD + extras):** list (with search/sort/filter/pagination/bulk/export),
  detail, create, edit, archive/trash
**System pages:** 404, 403, 500, maintenance, rate-limited, offline (PWA)
**Legal:** Terms of Service, Privacy Policy, Cookie settings
**UX kit components:** Toast, ConfirmDialog, EmptyState, LoadingSkeleton, ErrorBoundary,
  ErrorState, Breadcrumbs, CommandPalette (Cmd-K), KeyboardShortcuts modal, ThemeToggle,
  A11ySkipLink, DataTable, BulkActionBar, FilterPanel, FileUpload
**RBAC infrastructure (if users):** schema + seed + middleware + `<Can>` component +
  ProtectedRoute + audit log writer

All of the above respects the PAGES.md / RBAC.md / UX-KIT.md artifacts.

### Complexity-Driven Output

| Complexity | What gets generated |
|-----------|---------------------|
| **Simple** | Frontend + Backend only. Skip auth scaffold unless asked. No tests by default. No Docker. |
| **Medium** | Full stack + JWT auth + DB migrations + unit/integration tests + Dockerfile + basic CI |
| **Complex** | Full stack + auth + RBAC + tests (all layers) + Docker + CI/CD + infra configs + monitoring + docs |

For **Simple**, skip phases 4-6 unless explicitly requested.
For **Medium**, phases 4-5 are required, phase 6 (infra) is optional.
For **Complex**, all phases are mandatory.

### Phase 1: Project Initialization
Execute actual shell commands to set up the project:

**For Node.js/TypeScript backend (e.g., NestJS):**
```bash
# Research first
# WebSearch "nestjs new project setup 2026"
npx @nestjs/cli new backend --package-manager npm
cd backend
npm install @prisma/client class-validator class-transformer
npm install -D prisma
npx prisma init
```

**For Python backend (e.g., FastAPI):**
```bash
# Research first
# WebSearch "fastapi project structure best practices 2026"
mkdir -p backend/app/{domain,application,infrastructure,shared}
cd backend
python -m venv venv
pip install fastapi uvicorn sqlalchemy alembic pydantic
```

**For React frontend (e.g., Vite):**
```bash
# Research first
# WebSearch "vite react typescript setup 2026"
npm create vite@latest frontend -- --template react-ts
cd frontend
npm install axios react-router-dom @tanstack/react-query
npm install -D tailwindcss @tailwindcss/vite
```

### Phase 2: Backend Implementation (Inside-Out per Clean Architecture)

#### 2.1 Domain Layer
Write actual code files:
- Entities with business rules and validation
- Value objects (Email, Money, DateRange, etc.)
- Repository interfaces (abstract, no implementation)
- Domain events and domain services

#### 2.2 Application Layer
- Use case classes (one per business operation)
- DTOs (input/output data structures)
- Application services that orchestrate use cases
- Input validation schemas

#### 2.3 Infrastructure Layer
- Database schema/models and migrations
- Repository implementations (actual DB queries)
- HTTP controllers with route definitions
- Middleware (auth, validation, error handling, rate limiting, logging)
- External service adapters (payment, email, storage, etc.)
- Configuration and environment management

#### 2.4 Wiring
- Dependency injection setup
- Route registration
- Middleware chain
- Database connection
- App entry point

### Phase 3: Frontend Implementation

#### 3.1 Project Structure
```
src/
  components/        # Reusable UI components
    ui/              # Base UI primitives (Button, Input, Card)
    features/        # Feature-specific components
    layout/          # Layout components (Header, Sidebar, Footer)
  pages/             # Page/route components
  hooks/             # Custom React hooks
  services/          # API client and service layer
  stores/            # State management (Zustand/Redux/Context)
  types/             # TypeScript types/interfaces
  utils/             # Helper functions
  styles/            # Global styles
```

#### 3.2 Implementation Order
1. API client service (axios/fetch wrapper with auth, error handling)
2. Authentication flow (login, register, token management)
3. Layout components (header, sidebar, navigation)
4. Page routing setup
5. Feature components (forms, tables, dashboards)
6. State management for complex features
7. Error boundaries and loading states

### Phase 4: Database & Migrations
- Generate migration files from schema
- Create seed data for development
- Set up connection pooling

### Phase 5: Testing
- Unit tests for domain logic and use cases
- Integration tests for API endpoints
- Component tests for frontend (React Testing Library)
- E2E tests for critical flows (Playwright/Cypress)

### Phase 6: Infrastructure & DevOps
```
docker-compose.yml          # Local development (app, db, redis, etc.)
Dockerfile                  # Production container
.github/workflows/ci.yml    # CI pipeline (lint, test, build)
.env.example                # Environment variables template
```

## Code Quality Enforcement

### While Writing Code
- Follow Clean Code naming conventions
- Keep functions small (< 20 lines)
- Apply SOLID principles
- Use proper error handling (no silent catches, no null returns)
- Add input validation at API boundaries
- Use TypeScript strict mode / Python type hints

### After Each Component
- Run linter: `npm run lint` / `ruff check`
- Run formatter: `npm run format` / `ruff format`
- Run tests: `npm test` / `pytest`
- Fix any issues before moving on

## Milestone Validation (MANDATORY — Rule 23)

Break implementation into **milestones**. After each milestone finishes, STOP and run
the validation loop BEFORE starting the next milestone.

### What a Milestone Is

A milestone is a meaningful, shippable slice of work. Examples:
- `M1` — Project scaffold + dev environment running
- `M2` — Database schema + migrations applied
- `M3` — Auth flow working end-to-end
- `M4` — Core feature X implemented + tested
- `M5` — Frontend wired to backend for feature X
- `M6` — CI pipeline green
- `M7` — Deployed to staging

### The Validation Loop (run after EVERY milestone)

```
┌─────────────────────────────────────────────┐
│  Milestone Mn finished                      │
└───────────────────┬─────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│  1. Run automated checks                    │
│     - lint, typecheck, format               │
│     - unit + integration tests              │
│     - build succeeds                        │
│     - migrations apply cleanly              │
└───────────────────┬─────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│  2. Check acceptance criteria for Mn        │
│     (from IMPLEMENTATION-ROADMAP.md)        │
└───────────────────┬─────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│  3. Install anything still missing           │
│     - missing packages → npm/pip install    │
│     - missing env vars → add to .env.example│
│     - missing migrations → generate + apply │
│     - missing docs → generate (Rule 12)     │
└───────────────────┬─────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│  4. Ask user for any info still needed      │
│     - secrets (API keys, credentials)       │
│     - domain decisions that came up         │
│     - design gaps discovered during coding  │
└───────────────────┬─────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│  5. Update PROJECT.md milestone table       │
│     Mark Mn: Done ✓  +  record validations  │
└───────────────────┬─────────────────────────┘
                    ↓
┌─────────────────────────────────────────────┐
│  6. Summarize milestone for user            │
│     - what was built                        │
│     - checks that passed / issues fixed     │
│     - info you needed from them             │
│     - "ready to start Mn+1?" (wait yes)     │
└─────────────────────────────────────────────┘
```

### Automated Checks Per Milestone

| Check | Node.js | Python | Go | Action if failing |
|-------|---------|--------|----|----|
| Lint | `npm run lint` | `ruff check` | `golangci-lint run` | Fix before proceeding |
| Types | `tsc --noEmit` | `mypy .` | `go vet` | Fix before proceeding |
| Format | `npm run format` | `ruff format` | `gofmt` | Auto-fix |
| Unit tests | `npm test` | `pytest` | `go test ./...` | Fix before proceeding |
| Build | `npm run build` | `python -m build` | `go build` | Fix before proceeding |
| Security audit | `npm audit` | `pip-audit` | `govulncheck` | Flag critical/high |
| Migrations | Prisma/Drizzle/Alembic apply | - | - | Must apply cleanly |

### Missing Info Prompt Template

When Claude needs information from the user at a milestone boundary, ask clearly:

```
## Milestone M3: Auth flow — needs your input before I continue

I've scaffolded the auth module and it compiles. Before I wire it to a real provider,
I need these from you:

1. Which auth provider? (Google / GitHub / email-password / custom)
2. OAuth client ID + secret (if using a provider)
3. Password reset email address (from: noreply@<yourdomain>)
4. Session duration preference (default: 7 days)

Once you provide these, I'll:
- Finish the auth wiring
- Add a seed user for local dev
- Write the integration tests

(Say "use defaults for everything" if you prefer.)
```

### Validation Record in PROJECT.md

After each milestone, append a validation block:

```markdown
## Milestones

| # | Milestone | Status | Validated | Notes |
|---|-----------|--------|-----------|-------|
| M1 | Scaffold + dev env | ✓ Done | 2026-04-16 | lint ✓ build ✓ smoke ✓ |
| M2 | DB schema + migrations | ✓ Done | 2026-04-16 | 12 tables, 8 indexes, migration green |
| M3 | Auth flow | In Progress | - | waiting for OAuth credentials |
```

### Complexity-Driven Milestone Rigor

Same as the rest of the plugin:
- **Simple**: 2-3 milestones, lightweight checks (just build + run).
- **Medium**: 4-6 milestones, lint + tests + build.
- **Complex**: 7-10 milestones, full pipeline (lint + types + unit + integration + build + audit).

### Blocked Milestones

If a milestone cannot finish without user input that hasn't arrived yet:
1. Mark status as `Blocked — waiting for user input`
2. List exactly what's needed
3. Do NOT proceed to the next milestone
4. Offer safe partial work (e.g. "I can finish the UI skeleton while we wait")

## Research Triggers

**ALWAYS fetch docs when:**
- Using a package for the first time in this project
- Setting up authentication (auth libraries change rapidly)
- Configuring ORM/database (schema syntax is specific)
- Setting up deployment (Docker, CI/CD, cloud configs)
- Using any API you're not 100% sure about the current syntax
- User asks for a technology you haven't seen recently
- Error occurs that might be version-related

**HOW to research:**
1. `WebSearch "<technology> official documentation <current year>"`
2. `WebFetch <official-docs-url>` - read the specific page you need
3. Extract the exact syntax, imports, and configuration
4. Apply in your code

## Examples
```
/implement --frontend react --backend nestjs
/implement --frontend next --backend fastapi
/implement --backend express
/implement user-authentication --backend nestjs
/implement --scaffold --frontend react --backend fastapi
/implement --db --backend prisma-postgresql
/implement --tests
/implement --infra
```
