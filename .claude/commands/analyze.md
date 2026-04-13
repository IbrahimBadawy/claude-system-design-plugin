# /analyze - Code Analysis & Reverse Engineering

Analyze existing codebases, detect technical debt, and reverse-engineer architecture.

## Usage
```
/analyze codebase              # Scan code, extract architecture understanding
/analyze debt                  # Automated tech debt detection
/analyze coverage              # Test coverage gap analysis
/analyze dependencies          # Dependency health (outdated, vulnerable)
/analyze architecture          # Reverse-engineer architecture from code
```

## /analyze codebase
Scan the project and generate understanding:
1. Read package.json/requirements.txt/go.mod for dependencies
2. Map directory structure to architectural layers
3. Identify frameworks, ORMs, auth patterns used
4. Count files, lines, functions per module
5. Identify entry points (main files, route definitions)
6. Generate architecture summary

Output: `docs/ARCHITECTURE-ANALYSIS.md`

## /analyze debt
Detect tech debt:
- **Code smells**: Long functions (>50 lines), large files (>500 lines), deep nesting (>4 levels)
- **Dependencies**: Outdated packages, deprecated APIs, security vulnerabilities
- **Architecture**: Circular dependencies, god classes, missing abstractions
- **Testing**: Low coverage areas, missing test files, broken tests
- **Docs**: Outdated documentation, missing README sections
- **Config**: Hardcoded values, missing env vars, committed secrets

Tools: `npm audit`, `ruff check`, SonarQube metrics, custom grep patterns

## /analyze coverage
Identify what's NOT tested:
- Map all API endpoints -> check which have integration tests
- Map all business rules -> check which have unit tests
- Map all user journeys -> check which have e2e tests
- Highlight critical untested paths (auth, payment, data mutation)

## /analyze dependencies
Health check all dependencies:
- Outdated: current vs latest version
- Vulnerable: known CVEs (npm audit, pip audit, Snyk)
- Unused: imported but never used
- Heavy: large packages that could be replaced
- License: check for GPL/AGPL in commercial projects

## /analyze architecture
Reverse-engineer the architecture:
- Generate component diagram from code structure
- Map data flow from route handlers through services to DB
- Identify design patterns in use
- Compare against Clean Architecture layers
- Generate Mermaid architecture diagram

## Examples
```
/analyze codebase
/analyze debt
/analyze dependencies
/analyze architecture
```
