# /project template - Convert Project to Reusable Template

Convert a completed project into a reusable template that can be used to bootstrap similar future projects.

## Usage
```
/project template <project-name>                    # Convert project to template
/project template <project-name> --name <template>  # Convert with custom template name
/project template list                              # List all available templates
/project template use <template-name> <new-project> # Create new project from template
```

## Behavior

### /project template <project-name>

When converting a project to a template:

1. **Read the project** - Load PROJECT.md, DESIGN.md, STATUS.md, and all ADRs
2. **Extract the reusable parts**:
   - Architecture pattern (components, data flow, boundaries)
   - API design patterns (endpoint structure, auth, pagination)
   - Data model patterns (schema structure, relationships, indexes)
   - Technology stack decisions (with justification)
   - Scaling strategy
   - Monitoring and alerting setup
   - Security checklist applied
   - Infrastructure setup
3. **Generalize** - Replace project-specific names/values with placeholders:
   - `{{PROJECT_NAME}}` - Project name
   - `{{DOMAIN}}` - Business domain
   - `{{DAU}}` - Daily active users
   - `{{PRIMARY_ENTITY}}` - Main entity name
   - `{{DB_TYPE}}` - Database technology
   - `{{CACHE_TYPE}}` - Cache technology
   - `{{QUEUE_TYPE}}` - Queue technology
   - `{{AUTH_METHOD}}` - Authentication method
   - `{{DEPLOY_TARGET}}` - Deployment target (AWS/GCP/Azure)
4. **Save as template** in `.claude/templates/<template-name>/`:

```
.claude/templates/<template-name>/
  TEMPLATE.md          # Template metadata and usage instructions
  ARCHITECTURE.md      # Generalized architecture pattern
  SCHEMA.md            # Generalized data model
  API.md               # Generalized API design
  STACK.md             # Technology stack with justification
  INFRASTRUCTURE.md    # Infrastructure and deployment
  CHECKLIST.md         # Customized checklist for this pattern
```

### TEMPLATE.md Format
```markdown
# Template: {{TEMPLATE_NAME}}

**Created From**: [original project name]
**Date**: [date]
**Category**: [Web App | API Service | Real-time System | Data Pipeline | etc.]
**Complexity**: [Simple | Medium | Complex]

## Description
[What kind of system this template is for]

## Best Used For
- [Use case 1]
- [Use case 2]
- [Use case 3]

## Not Suitable For
- [Anti-use case 1]

## Core Components
- [Component 1]: [role]
- [Component 2]: [role]

## Technology Stack
| Layer | Technology | Why |
|-------|-----------|-----|
| Backend | {{BACKEND}} | [reason] |
| Database | {{DB_TYPE}} | [reason] |
| Cache | {{CACHE_TYPE}} | [reason] |
| Queue | {{QUEUE_TYPE}} | [reason] |

## Variables to Customize
| Variable | Description | Example |
|----------|-------------|---------|
| {{PROJECT_NAME}} | Your project name | university-system |
| {{DAU}} | Expected daily active users | 50,000 |
| ... | ... | ... |

## How to Use
1. Run `/project template use <this-template> <new-project-name>`
2. Answer the customization questions
3. Review the generated design
4. Start with `/implement`

## Architecture Decisions Carried Over
- ADR-001: [decision that applies to all similar projects]
- ADR-002: [another reusable decision]

## Lessons Learned (from original project)
- [Lesson 1]
- [Lesson 2]
```

### /project template use <template-name> <new-project>

When creating a project from a template:

1. **Load the template** from `.claude/templates/<template-name>/`
2. **Ask customization questions**:
   - What's the project name and domain?
   - What's the expected scale? (DAU, QPS)
   - Any different technology preferences?
   - What's the deployment target?
   - Any specific constraints?
3. **Generate the project** by:
   - Replacing all `{{PLACEHOLDERS}}` with user answers
   - Creating the full project structure in `.claude/projects/<new-project>/`
   - Pre-filling DESIGN.md from the template architecture
   - Copying relevant ADRs
   - Customizing the checklist
4. **Review with user** - Walk through the generated design, ask for adjustments
5. **Ready to go** - User can start with `/evaluate` or `/implement`

### /project template list

Show all available templates:
```
## Available Templates

| Template | Category | Source Project | Created | Description |
|----------|----------|---------------|---------|-------------|
| university-saas | Web App | university-system | 2026-04-13 | Multi-tenant university management |
| payment-gateway | API Service | payment-system | 2026-04-10 | Payment processing with PSP |
| real-time-chat | Real-time | chat-platform | 2026-04-08 | WebSocket-based chat |
```

## Examples
```
/project template university-system
/project template university-system --name university-saas
/project template list
/project template use university-saas new-college-system
```
