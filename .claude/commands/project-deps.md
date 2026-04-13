# /project deps - Project Dependencies & Relationships

Define and manage relationships between projects. Establish core projects that other projects depend on.

## Usage
```
/project deps                                           # Show dependency graph for all projects
/project deps <project-name>                            # Show deps for specific project
/project deps set-core <project-name>                   # Mark a project as a core/shared project
/project deps add <project> --depends-on <core>         # Add dependency relationship
/project deps remove <project> --depends-on <core>      # Remove dependency
/project deps check <project>                           # Check if project follows core constraints
```

## Concepts

### Core Project
A **core project** is a foundational system that other projects build upon or integrate with.
Examples:
- `auth-service` - Central authentication used by all other services
- `university-core` - Core university data model and APIs
- `shared-infrastructure` - Common infrastructure (DB, cache, queue)

### Dependent Project
A project that relies on or extends a core project. It must:
- Follow the core project's API contracts
- Use compatible technology stack
- Respect the core's data model and boundaries
- Align with the core's security and auth patterns

### Dependency Types
- **extends**: Builds directly on top of core (same codebase/monorepo)
- **integrates**: Uses core's APIs as an external service
- **shares-data**: Accesses core's database or shared data store
- **inherits-patterns**: Follows core's architectural patterns but is independent

## Behavior

### /project deps set-core <project-name>

Mark a project as core. Creates/updates `DEPS.md` in the project:

```markdown
# Dependencies: [Project Name]
**Type**: Core
**Date**: [date]

## This is a Core Project

### Provides
- [API endpoints that dependent projects consume]
- [Shared data models]
- [Authentication/Authorization services]
- [Shared infrastructure components]

### Contracts (must not break)
- API: [versioned endpoints, backward compatible]
- Data Model: [shared tables/collections, migration rules]
- Auth: [token format, scopes, roles]
- Events: [published events, schema]

### Dependent Projects
| Project | Dependency Type | What It Uses |
|---------|----------------|-------------|
| [project-a] | integrates | Auth API, User model |
| [project-b] | extends | Course module, Student model |

### Change Rules
- Breaking changes to core APIs require version bump (v1 -> v2)
- Database migrations must be backward compatible
- New events can be added, existing event schemas can only be extended
- All dependents must be notified before core changes
```

### /project deps add <project> --depends-on <core>

Add a dependency. Ask for:
1. **Dependency type**: extends | integrates | shares-data | inherits-patterns
2. **What it uses**: Which APIs, models, services from the core
3. **Integration points**: How they communicate (REST, gRPC, events, shared DB)

Updates both projects' DEPS.md files:

**In the dependent project's DEPS.md:**
```markdown
# Dependencies: [Dependent Project]
**Type**: Dependent

## Depends On

### [Core Project Name]
- **Type**: integrates
- **Uses**:
  - Auth API (POST /api/v1/auth/verify-token)
  - User model (GET /api/v1/users/:id)
  - Role-based access control (scopes: admin, instructor, student)
- **Integration**: REST API calls with Bearer JWT
- **Fallback**: If core is down, use cached user data (TTL: 5 min)

### Constraints from Core
- Must use JWT tokens issued by core auth service
- Must validate tokens on every request
- Must use core's user ID format (UUID v4)
- Must handle core API versioning (currently v1)
- Must subscribe to core events for data consistency

### Shared Data Model (from core)
| Entity | Source | How Accessed | Sync Strategy |
|--------|--------|-------------|---------------|
| Users | Core DB | API call | Cache locally, TTL 5min |
| Roles | Core DB | API call | Cache locally, TTL 1hr |
| Courses | Core DB | Shared read replica | Real-time |
```

### /project deps check <project>

Verify that a dependent project follows core constraints:

```markdown
## Dependency Check: [Project Name]

### Core: [Core Project Name]
| Check | Status | Details |
|-------|--------|---------|
| Uses core auth | PASS | JWT validation on all endpoints |
| Compatible user model | PASS | Uses UUID v4 for user IDs |
| API version compatible | PASS | Using core API v1 |
| Fallback strategy | WARN | No fallback if core auth is down |
| Event subscription | FAIL | Not subscribed to user.updated event |
| Technology alignment | PASS | Same PostgreSQL version |

### Recommendations
1. [FAIL] Subscribe to `user.updated` event to keep local cache in sync
2. [WARN] Add circuit breaker + cached fallback for core auth service
```

### /project deps (show all)

Display the full dependency graph:

```
## Project Dependency Graph

                    ┌──────────────────┐
                    │  university-core  │  [CORE]
                    │  (Auth, Users,    │
                    │   Courses, Roles) │
                    └────────┬─────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
    ┌─────────▼───┐  ┌──────▼─────┐  ┌────▼──────────┐
    │ student-    │  │ instructor-│  │ admin-        │
    │ portal      │  │ portal     │  │ dashboard     │
    │ [integrates]│  │ [integrates│  │ [extends]     │
    └─────────────┘  └────────────┘  └───────────────┘

### Summary
| Core Project | Dependents | Health |
|-------------|-----------|--------|
| university-core | 3 projects | 2 PASS, 1 WARN |

### Dependency Matrix
|                  | university-core | shared-infra |
|-----------------|----------------|-------------|
| student-portal  | integrates     | inherits    |
| instructor-portal| integrates    | inherits    |
| admin-dashboard | extends        | inherits    |
```

## Workflow Example

```
# 1. Design the core system
/project add university-core
/design university-core-system
/project deps set-core university-core

# 2. Add dependent projects
/project add student-portal
/project deps add student-portal --depends-on university-core

/project add instructor-portal  
/project deps add instructor-portal --depends-on university-core

# 3. Design dependents (they inherit core constraints)
/design student-portal
# -> Automatically uses core's auth, user model, and API contracts

# 4. Check alignment
/project deps check student-portal

# 5. View the full picture
/project deps
```

## Integration with Other Commands

When a project has dependencies defined:
- `/design` automatically loads core project constraints
- `/evaluate` checks dependency alignment
- `/implement` generates integration code (API clients, event handlers)
- `/schema` shows shared vs local tables
- `/api` shows which endpoints come from core vs local
- `/security` validates auth alignment with core

## Examples
```
/project deps
/project deps university-core
/project deps set-core university-core
/project deps add student-portal --depends-on university-core
/project deps check student-portal
/project deps remove student-portal --depends-on university-core
```
