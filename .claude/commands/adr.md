# /adr - Architecture Decision Record

Create an Architecture Decision Record for a significant technical decision.

## Usage
```
/adr <title>
```

## Output Template

```markdown
# ADR-[number]: [Title]

**Date**: [today's date]
**Status**: Proposed | Accepted | Deprecated | Superseded by ADR-XX
**Deciders**: [list of people]

## Context
[What is the issue? What forces are at play? Why do we need to decide this now?]

## Decision Drivers
- [Driver 1: e.g., scalability requirement]
- [Driver 2: e.g., team expertise]
- [Driver 3: e.g., cost constraints]
- [Driver 4: e.g., time to market]

## Considered Options

### Option 1: [Name]
**Description**: [Brief description]
- Pros:
  - [Pro 1]
  - [Pro 2]
- Cons:
  - [Con 1]
  - [Con 2]
- Estimated Effort: [S/M/L/XL]
- Risk Level: [Low/Medium/High]

### Option 2: [Name]
**Description**: [Brief description]
- Pros:
  - [Pro 1]
  - [Pro 2]
- Cons:
  - [Con 1]
  - [Con 2]
- Estimated Effort: [S/M/L/XL]
- Risk Level: [Low/Medium/High]

## Decision
We chose **[Option X]** because [main reasons].

## Consequences

### Positive
- [Benefit 1]
- [Benefit 2]

### Negative
- [Trade-off 1]
- [Trade-off 2]

### Risks
- [Risk 1] -- Mitigation: [how we handle it]
- [Risk 2] -- Mitigation: [how we handle it]

## Follow-up Actions
- [ ] [Action 1] -- Owner: [name] -- Due: [date]
- [ ] [Action 2] -- Owner: [name] -- Due: [date]

## References
- [Link or reference 1]
- [Link or reference 2]
```

## Behavior
1. If the title suggests a common decision (DB choice, queue selection, etc.), pre-populate options from the knowledge base
2. Research current best practices if the technology is not in the knowledge base
3. Include real-world examples where relevant
4. Reference relevant case studies from Alex Xu's books

## Examples
```
/adr Choose message queue for order processing
/adr Database selection for user profiles
/adr Monolith vs microservices for MVP
/adr Authentication strategy for multi-tenant SaaS
```
