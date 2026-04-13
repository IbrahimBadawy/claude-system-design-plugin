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

### Phase 0: Pre-Flight
1. Verify system design exists (check DESIGN.md or current conversation)
2. Confirm technology stack with user
3. **Research the chosen stack** - fetch latest docs for key technologies
4. Create implementation plan (list of files to create, in order)

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
