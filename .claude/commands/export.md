# /export - Export Designs to External Formats

Export designs, plans, and architecture to formats used by other tools and teams.

## Usage
```
/export openapi                # Export API design as OpenAPI 3.0 YAML
/export postman                # Export as Postman collection JSON
/export mermaid                # Export all diagrams as standalone .md files
/export terraform              # Export infrastructure as Terraform HCL
/export github-issues          # Create GitHub issues from plan tasks
/export docker                 # Generate docker-compose from architecture
```

## /export openapi
Reads the API design (from DESIGN.md or /api output) and generates an `openapi.yaml`:
- All endpoints with methods, paths, parameters
- Request/response schemas as JSON Schema
- Authentication schemes (Bearer JWT)
- Error response schemas
- Pagination parameters

## /export postman
Generates a Postman collection JSON:
- Organized by resource/feature folders
- Pre-configured auth (Bearer token variable)
- Example request bodies
- Environment variables (base_url, token)

## /export github-issues
Reads IMPLEMENTATION-ROADMAP.md and creates GitHub issues:
- One issue per roadmap step
- Labels: phase-1, phase-2, backend, frontend, etc.
- Milestones from plan phases
- Checklist items as task lists
- Uses: `gh issue create --title "..." --body "..." --label "..."`

## /export terraform
Generates Terraform HCL from architecture:
- VPC, subnets, security groups
- RDS (PostgreSQL), ElastiCache (Redis)
- ECS/Fargate or EC2 instances
- ALB, Route53, CloudFront
- S3 buckets, SQS queues

## /export docker
Generates docker-compose.yml from architecture components:
- App service with Dockerfile
- PostgreSQL with volumes
- Redis with health check
- Any message queues
- Development overrides

## Examples
```
/export openapi
/export github-issues
/export docker
```
