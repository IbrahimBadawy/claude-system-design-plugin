# /cicd - CI/CD Pipeline Design

Design the CI/CD pipeline, deployment strategy, and release management.

## Usage
```
/cicd <system>                      # Full CI/CD design
/cicd pipeline                      # Pipeline stages design
/cicd deploy-strategy               # Deployment strategy selection
```

## Pipeline Stages
```
Commit -> Lint -> Unit Test -> Build -> SAST Scan -> Integration Test
-> Deploy Staging -> E2E Test -> Manual Approval -> Deploy Production
-> Smoke Test -> Monitor
```

### Stage Details
| Stage | Tools | Fail Action |
|-------|-------|-------------|
| Lint | ESLint/Ruff | Block merge |
| Unit Test | Jest/Pytest | Block merge |
| Build | Docker | Block deploy |
| SAST | Semgrep/CodeQL | Block if critical |
| SCA (deps) | Snyk/Dependabot | Warn (block if critical) |
| Integration | Testcontainers | Block deploy |
| Deploy Staging | ArgoCD/GitHub Actions | Rollback |
| E2E | Playwright/Cypress | Block production |
| Deploy Production | Blue-Green/Canary | Auto-rollback on error spike |
| Smoke Test | k6/curl | Rollback |

### Deployment Strategies
- **Blue-Green**: Two environments, instant switch. Safe but 2x cost.
- **Canary**: Gradual rollout (1% -> 5% -> 25% -> 100%). Monitor at each step.
- **Rolling**: Replace instances one by one. Zero downtime, slower rollback.
- **Feature Flags**: Deploy dark, enable gradually. Most flexible.

### Environment Promotion
```
dev (auto-deploy on push) -> staging (auto-deploy on merge to main)
-> production (manual approval or auto after E2E pass)
```

### Key Practices
- Trunk-based development, short-lived branches (< 1 day)
- Immutable artifacts (same Docker image dev -> staging -> prod)
- Infrastructure as Code (Terraform/Pulumi)
- Automated rollback on error rate > threshold
- Secret management (not in CI config, use vault)

**Tools:** GitHub Actions, GitLab CI, ArgoCD, Terraform, Docker, k6

## Examples
```
/cicd university-system
/cicd pipeline
/cicd deploy-strategy
```
