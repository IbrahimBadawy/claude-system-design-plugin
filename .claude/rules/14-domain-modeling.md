---
description: Model the business domain before choosing technology. Use DDD concepts for complex systems.
globs: "*"
---

# Rule: Domain Modeling First (DDD)

For any system with complex business logic, model the domain BEFORE designing the architecture:

1. **Identify Bounded Contexts**: Each context = a service boundary with its own language
2. **Define Aggregates**: Consistency boundaries. Transactions must NOT cross aggregate boundaries
3. **Map Domain Events**: How contexts communicate (OrderPlaced, PaymentCompleted, UserRegistered)
4. **Distinguish Entities vs Value Objects**: Entities have identity, Value Objects are immutable (Money, Email, Address)
5. **Establish Ubiquitous Language**: Same vocabulary between developers and domain experts per context
6. **Draw Context Map**: Relationships between contexts (Shared Kernel, Customer-Supplier, Anticorruption Layer)

## When to Apply Full DDD
- System has complex business rules (not just CRUD)
- Multiple teams or bounded contexts
- Domain experts are available for collaboration
- Business logic changes frequently

## When Simple Modeling is OK
- Pure CRUD applications
- Simple data pipelines
- Small team, small scope
- Prototypes / MVPs

## Domain Layer Rules (from Clean Architecture)
- Domain layer has ZERO external dependencies
- Domain entities contain business rules, not just data
- Repository interfaces defined in domain, implemented in infrastructure
- Domain events are the primary communication mechanism between contexts
