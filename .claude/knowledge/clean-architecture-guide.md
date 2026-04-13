# Clean Architecture & Code Quality Guide

Principles from Clean Architecture, Clean Code, Design Patterns (GoF), Refactoring, and Code Complete.

## Clean Architecture - The Dependency Rule

```
[Frameworks & Drivers] -> [Interface Adapters] -> [Use Cases] -> [Entities]
       (outer)                                                    (inner)
```

Source code dependencies point ONLY inward. Inner circles know nothing about outer circles.

- **Entities**: Business rules, domain objects (most stable)
- **Use Cases**: Application-specific business rules
- **Interface Adapters**: Controllers, presenters, gateways (convert data formats)
- **Frameworks & Drivers**: Web, DB, external services (most volatile)

The database is a DETAIL. The web is a DETAIL. Business rules are the CENTER.

## SOLID Principles

| Principle | Rule | Violation Sign |
|-----------|------|---------------|
| **S**ingle Responsibility | One reason to change per class | Class changed for multiple actors |
| **O**pen/Closed | Open for extension, closed for modification | Changing existing code to add features |
| **L**iskov Substitution | Subtypes substitutable for base types | Special-case handling for subtypes |
| **I**nterface Segregation | No unused dependencies | Client depends on methods it doesn't use |
| **D**ependency Inversion | Depend on abstractions, not concretions | High-level imports low-level directly |

## Component Principles

**Cohesion** (what goes together):
- REP: Reuse/Release Equivalence - releasable together
- CCP: Common Closure - change together (SRP for components)
- CRP: Common Reuse - used together (ISP for components)

**Coupling** (relationships):
- ADP: No cycles in dependency graph
- SDP: Depend toward stability
- SAP: Stable = abstract, volatile = concrete

## GoF Design Patterns - When to Use

### Creational
- **Factory Method**: When class can't anticipate what objects to create
- **Abstract Factory**: When need families of related objects
- **Builder**: When constructing complex objects step by step
- **Singleton**: When exactly one instance needed (use sparingly)
- **Prototype**: When creating objects by cloning

### Structural
- **Adapter**: When integrating incompatible interfaces (third-party)
- **Bridge**: When abstraction and implementation vary independently
- **Composite**: For tree structures (part-whole hierarchies)
- **Decorator**: Add responsibilities dynamically without subclassing
- **Facade**: Simplify complex subsystem interface
- **Proxy**: Control access (remote, virtual, protection)

### Behavioral
- **Strategy**: Interchangeable algorithms
- **Observer**: Event notification to multiple listeners
- **Command**: Encapsulate actions (undo/redo, queuing)
- **State**: Object behavior changes with state
- **Template Method**: Algorithm skeleton with customizable steps
- **Chain of Responsibility**: Pass request through handler chain
- **Iterator**: Sequential access without exposing internals
- **Mediator**: Centralize complex communication

## Code Smells -> Refactoring (Top 10)

| Smell | Refactoring |
|-------|-------------|
| Duplicated Code | Extract Function, Pull Up Method |
| Long Function | Extract Function |
| Long Parameter List | Introduce Parameter Object |
| Feature Envy | Move Function |
| Primitive Obsession | Replace Primitive with Object |
| Repeated Switches | Replace Conditional with Polymorphism |
| Divergent Change | Extract Class |
| Shotgun Surgery | Move Function, Combine into Class |
| Data Clumps | Introduce Parameter Object, Extract Class |
| Large Class | Extract Class, Extract Superclass |

## Kent Beck's Four Rules of Simple Design
1. Runs all the tests
2. Contains no duplication
3. Expresses the intent of the programmer
4. Minimizes the number of classes and methods

## Defensive Programming (Code Complete)
- Validate all inputs at system boundaries
- Assertions for conditions that should NEVER occur
- Barricade pattern: validate at boundaries, trust internally
- Error handling for anticipated abnormal situations
- Exceptions for truly exceptional conditions only

## Testing Strategy
- **Unit tests**: Fast, isolated, test domain logic and use cases
- **Integration tests**: Test infrastructure (DB, API, external)
- **E2E tests**: Critical user journeys
- **F.I.R.S.T**: Fast, Independent, Repeatable, Self-Validating, Timely
- No single technique exceeds ~75% defect detection; use combinations
