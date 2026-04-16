# /plan - Planning System

Comprehensive planning system with master plan and detailed sub-plans. NO CODE is written until user explicitly says "start coding" or "ابدا برمجة".

## Usage
```
/plan create <project>              # Create master plan + sub-plans
/plan show                          # Show master plan overview
/plan show <plan-name>              # Show specific sub-plan in detail
/plan list                          # List all plans with status
/plan edit <plan-name>              # Edit a specific plan
/plan approve <plan-name>           # Mark a plan as approved
/plan approve all                   # Approve all plans
/plan status                        # Show approval status of all plans
/plan export                        # Export all plans as one document
/plan implementation                # Generate implementation roadmap (after all approved)
```

## CRITICAL RULE: NO CODE UNTIL EXPLICITLY TOLD

```
PHASE 1 - DISCOVERY (free discussion):
  /project add <name>     -> Creates project, starts discovery
  Discuss, ask, research  -> Understand the idea deeply
  Diagrams, notes         -> Saved in discovery/ folder

  >>> USER says "ابدا plan" / "start planning" / "خطط" <<<

PHASE 2 - PLANNING (structured plans):
  /plan create <name>     -> Master plan + 10 sub-plans
  Review, edit, approve   -> All plans must be approved

  >>> USER says "ابدا برمجة" / "start coding" / "يلا كود" <<<

PHASE 3 - IMPLEMENTATION (actual code):
  /plan implementation    -> Step-by-step roadmap
  Follow roadmap          -> Build incrementally
```

**Even if ALL plans are approved, DO NOT write any code.**
**Wait for the user's explicit command to start coding.**

## Plan Structure (varies by project complexity)

Plans live at `projects/<project-name>/plans/` (at the workspace root — NOT inside `.claude/`).

### Simple complexity — no formal plans
For Simple projects, `/plan create` is optional. If run, it produces a single
lightweight `QUICK-PLAN.md` (front + back structure only, no 10-file expansion).

### Medium complexity — 5 core plans
```
projects/<project-name>/
  plans/
    MASTER-PLAN.md                    # High-level overview
    01-architecture-plan.md           # System components
    02-database-plan.md               # Schema, indexes
    03-api-plan.md                    # Endpoints, contracts
    05-frontend-plan.md               # UI, components
    06-auth-plan.md                   # Authentication
    IMPLEMENTATION-ROADMAP.md         # Step-by-step order
```

### Complex complexity — all 10 plans
```
projects/<project-name>/
  plans/
    MASTER-PLAN.md                    # High-level overview of everything
    01-architecture-plan.md           # System architecture decisions
    02-database-plan.md               # Database design and schema
    03-api-plan.md                    # API design and endpoints
    04-backend-plan.md                # Backend implementation plan
    05-frontend-plan.md               # Frontend design and implementation plan
    06-auth-plan.md                   # Authentication and authorization plan
    07-infrastructure-plan.md         # DevOps, Docker, CI/CD, deployment
    08-testing-plan.md                # Testing strategy and test plan
    09-security-plan.md               # Security measures plan
    10-monitoring-plan.md             # Observability and monitoring plan
    IMPLEMENTATION-ROADMAP.md         # Step-by-step coding order (generated last)
```

**Which set gets generated?** Read `PROJECT.md` → `**Complexity**:` field and
create only the plans that match that level. Don't force 10 plans on a Simple project.

## Master Plan Format

```markdown
# Master Plan: [Project Name]

**Created**: [date]
**Status**: Draft | Under Review | Approved
**Last Updated**: [date]

---

## 1. Project Overview
[Brief description - what are we building and why]

## 2. Requirements Summary
### Functional Requirements
- FR1: [requirement]
- FR2: [requirement]

### Non-Functional Requirements
| Requirement | Target |
|-------------|--------|
| Scale | [DAU, QPS] |
| Latency | [p99 target] |
| Availability | [SLA] |

## 3. Open Source Assessment
| System | License | Fit % | Decision |
|--------|---------|-------|----------|
| [System A] | MIT | 80% | Consider for [module] |
| [System B] | AGPL | 70% | License risk for SaaS |
| Build from scratch | - | 100% | Full control, higher cost |

**Decision**: [Build / Use / Fork / Hybrid] because [justification]

## 4. Technology Stack
| Layer | Technology | Justification |
|-------|-----------|---------------|
| Frontend | [framework + UI lib] | [why] |
| Backend | [framework] | [why] |
| Database | [DB + ORM] | [why] |
| Cache | [tech] | [why] |
| Queue | [tech] | [why] |
| Deployment | [platform] | [why] |

## 5. Architecture Overview
[High-level architecture description and component diagram]

## 6. Sub-Plans Index

| # | Plan | Status | Description |
|---|------|--------|-------------|
| 01 | [Architecture](./01-architecture-plan.md) | Draft | System components and boundaries |
| 02 | [Database](./02-database-plan.md) | Draft | Schema, indexes, migrations |
| 03 | [API](./03-api-plan.md) | Draft | Endpoints, contracts, versioning |
| 04 | [Backend](./04-backend-plan.md) | Draft | Business logic, services, patterns |
| 05 | [Frontend](./05-frontend-plan.md) | Draft | UI, components, state, routing |
| 06 | [Auth](./06-auth-plan.md) | Draft | Authentication, authorization, roles |
| 07 | [Infrastructure](./07-infrastructure-plan.md) | Draft | Docker, CI/CD, deployment |
| 08 | [Testing](./08-testing-plan.md) | Draft | Test strategy, coverage targets |
| 09 | [Security](./09-security-plan.md) | Draft | Security measures, OWASP |
| 10 | [Monitoring](./10-monitoring-plan.md) | Draft | Metrics, logging, alerting |

## 6. Timeline Estimate
| Phase | Plans | Estimated Duration |
|-------|-------|--------------------|
| Phase 1: Foundation | Architecture, DB, Auth | [X weeks] |
| Phase 2: Core | Backend, API | [X weeks] |
| Phase 3: UI | Frontend | [X weeks] |
| Phase 4: Quality | Testing, Security | [X weeks] |
| Phase 5: Ship | Infrastructure, Monitoring | [X weeks] |

## 7. Risks & Mitigations
| Risk | Impact | Probability | Mitigation |
|------|--------|------------|------------|
| [risk] | High/Med/Low | High/Med/Low | [mitigation] |

## 8. Open Questions
- [ ] [Question 1 that needs answering]
- [ ] [Question 2]
```

## Sub-Plan Format (Example: Database Plan)

```markdown
# Plan 02: Database Design

**Parent**: [MASTER-PLAN.md](./MASTER-PLAN.md)
**Status**: Draft | Under Review | Approved | Needs Revision
**Last Updated**: [date]
**Approved By**: [name/date or "Pending"]

---

## 1. Database Choice
**Selected**: PostgreSQL 16
**Why**: [justification with trade-offs considered]
**Alternatives Rejected**: [MongoDB - because..., MySQL - because...]

## 2. Schema Design

### Table: users
| Column | Type | Constraints | Purpose |
|--------|------|-------------|---------|
| id | UUID | PK, DEFAULT gen_random_uuid() | Unique identifier |
| email | VARCHAR(255) | UNIQUE, NOT NULL | Login credential |
| password_hash | VARCHAR(255) | NOT NULL | Bcrypt hashed password |
| ... | ... | ... | ... |

### Table: [next table]
[same format]

## 3. Relationships
- users 1:N orders (via orders.user_id)
- [all relationships]

## 4. Indexes
| Table | Index | Columns | Purpose |
|-------|-------|---------|---------|
| users | idx_users_email | (email) | Login lookup |
| orders | idx_orders_user_date | (user_id, created_at) | User order history |

## 5. Migration Strategy
- Use [Prisma Migrate / Alembic / Flyway]
- All migrations must be backward-compatible
- Rollback scripts for every migration

## 6. Data Growth Estimates
| Table | Records/day | Size/record | 1 Year | 3 Years |
|-------|------------|-------------|--------|---------|
| users | 1,000 | 500 bytes | 180 MB | 540 MB |
| orders | 10,000 | 1 KB | 3.6 GB | 10.8 GB |

## 7. Scaling Plan
- Phase 1: Single primary + 2 read replicas
- Phase 2: Connection pooling (PgBouncer)
- Phase 3: Partitioning by date for large tables
- Phase 4: Sharding if needed

## 8. Open Items
- [ ] Confirm soft delete vs hard delete strategy
- [ ] Decide on audit trail approach

---
**Review Notes**: [space for user comments during review]
```

## /plan show - Interactive Navigation

When user runs `/plan show`, display:

```
╔══════════════════════════════════════════╗
║       PROJECT: University System         ║
╠══════════════════════════════════════════╣
║                                          ║
║  MASTER PLAN ..................Approved  ║
║                                          ║
║  Sub-Plans:                              ║
║  [01] Architecture ..........Approved   ║
║  [02] Database ..............Approved   ║
║  [03] API ...................Under Review║
║  [04] Backend ...............Draft      ║
║  [05] Frontend ..............Draft      ║
║  [06] Auth ..................Approved   ║
║  [07] Infrastructure ........Draft      ║
║  [08] Testing ...............Draft      ║
║  [09] Security ..............Draft      ║
║  [10] Monitoring ............Draft      ║
║                                          ║
║  Progress: [=====>----------] 3/10      ║
║                                          ║
║  Type `/plan show 03` to view a plan    ║
║  Type `/plan edit 04` to edit a plan    ║
║  Type `/plan approve 01` to approve     ║
║                                          ║
╚══════════════════════════════════════════╝
```

## /plan edit <plan-name>

When editing a plan:
1. Show the current plan content
2. Ask what the user wants to change
3. Make the changes
4. Reset status to "Under Review" if it was "Approved"
5. Update the "Last Updated" date

## /plan approve <plan-name>

Mark a plan as approved:
1. Change status to "Approved"
2. Record approval date
3. Update MASTER-PLAN.md sub-plans table
4. Check if all plans are now approved
5. If all approved, notify: "All plans approved! Say 'ابدا برمجة' or 'start coding' when ready."

## /plan implementation - Implementation Roadmap

**Only generated AFTER all plans are approved.**
Creates `IMPLEMENTATION-ROADMAP.md`:

```markdown
# Implementation Roadmap: [Project Name]

**All Plans Approved**: [date]
**Generated**: [date]

This roadmap breaks the project into ordered implementation steps.
Follow this order to avoid dependency issues and build incrementally.

---

## Phase 1: Project Setup (Day 1)
### Step 1.1: Initialize project
- [ ] Create project directories
- [ ] Initialize backend ([framework])
- [ ] Initialize frontend ([framework])
- [ ] Set up Git repository
- [ ] Create .env.example

### Step 1.2: Infrastructure setup
- [ ] Create docker-compose.yml (DB, Redis, etc.)
- [ ] Configure CI pipeline (.github/workflows/ci.yml)
- [ ] Set up linting and formatting

**Deliverable**: Running empty project with Docker, CI, and dev environment

---

## Phase 2: Database & Domain (Days 2-3)
### Step 2.1: Database schema
- [ ] Create [ORM] schema from Database Plan
- [ ] Run initial migration
- [ ] Create seed data

### Step 2.2: Domain entities
- [ ] Implement User entity
- [ ] Implement [Entity 2]
- [ ] Implement [Entity 3]
- [ ] Unit tests for domain logic

**Deliverable**: Database running with schema, domain entities with tests

---

## Phase 3: Authentication (Days 4-5)
### Step 3.1: Auth backend
- [ ] Implement registration endpoint
- [ ] Implement login endpoint (JWT)
- [ ] Implement refresh token flow
- [ ] Implement auth middleware/guard
- [ ] Tests for auth flow

### Step 3.2: Auth frontend
- [ ] Login page
- [ ] Register page
- [ ] Auth context/store
- [ ] Protected route wrapper
- [ ] Token management (storage, refresh)

**Deliverable**: Working auth flow end-to-end

---

## Phase 4: Core Backend (Days 6-10)
### Step 4.1: [Feature 1] backend
- [ ] Controller + routes
- [ ] Service (use cases)
- [ ] Repository
- [ ] Validation DTOs
- [ ] Tests

### Step 4.2: [Feature 2] backend
[same structure]

[... for each major feature ...]

**Deliverable**: All API endpoints working and tested

---

## Phase 5: Frontend (Days 11-16)
### Step 5.1: Layout and navigation
- [ ] Install and configure [UI library]
- [ ] Build layout (sidebar, navbar)
- [ ] Set up routing
- [ ] Set up API client service

### Step 5.2: [Feature 1] pages
- [ ] List page (with table/cards)
- [ ] Detail page
- [ ] Create/Edit form
- [ ] Delete confirmation

[... for each feature ...]

**Deliverable**: Full working frontend connected to backend

---

## Phase 6: Polish & Quality (Days 17-19)
### Step 6.1: Testing
- [ ] Integration tests for critical paths
- [ ] E2E tests for main user journeys
- [ ] Performance testing for key endpoints

### Step 6.2: Security hardening
- [ ] Rate limiting on all endpoints
- [ ] Input validation audit
- [ ] CORS configuration
- [ ] Security headers (Helmet)

### Step 6.3: Error handling
- [ ] Global error handler
- [ ] Frontend error boundaries
- [ ] Loading and empty states
- [ ] Offline handling

**Deliverable**: Production-quality code with tests and security

---

## Phase 7: Ship (Day 20)
### Step 7.1: Deployment
- [ ] Production Dockerfile
- [ ] Environment configuration
- [ ] Database migration strategy
- [ ] Health check endpoints

### Step 7.2: Monitoring
- [ ] Prometheus metrics
- [ ] Structured logging
- [ ] Basic alerts
- [ ] Health dashboard

### Step 7.3: Documentation
- [ ] Generate/verify all docs (`/docs generate`)
- [ ] API documentation complete
- [ ] README with quick start

**Deliverable**: Deployed, monitored, documented system

---

## Progress Tracker

| Phase | Status | Progress |
|-------|--------|----------|
| 1. Setup | Not Started | [----------] 0% |
| 2. Database & Domain | Not Started | [----------] 0% |
| 3. Authentication | Not Started | [----------] 0% |
| 4. Core Backend | Not Started | [----------] 0% |
| 5. Frontend | Not Started | [----------] 0% |
| 6. Quality | Not Started | [----------] 0% |
| 7. Ship | Not Started | [----------] 0% |

**Overall**: [----------] 0%
```

## Workflow Summary

```
1. /project add my-system
2. /design my-system              -> Creates DESIGN.md
3. /plan create my-system         -> Creates MASTER-PLAN + 10 sub-plans
4. /plan show                     -> Review master plan
5. /plan show 02                  -> Review database plan in detail
6. /plan edit 02                  -> Make changes to database plan
7. /plan approve 02               -> Approve database plan
8. ... review and approve all plans ...
9. /plan status                   -> Confirm all approved
10. User says: "يلا نبدا برمجة"    -> Transition to implementation
11. /plan implementation          -> Generate step-by-step roadmap
12. Follow roadmap step by step
```

## Examples
```
/plan create university-system
/plan show
/plan show 05
/plan edit 03
/plan approve 01
/plan approve all
/plan status
/plan implementation
/plan export
```
