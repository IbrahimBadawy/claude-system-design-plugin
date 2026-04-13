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

## Architecture Core Insights (from deep read)

**Architecture's real purpose**: Minimize human resources to build/maintain the system. NOT about behavior.
**The Tortoise wins**: Making messes is ALWAYS slower than staying clean, at every timescale.
**Behavior vs Structure**: Architecture (keeping software changeable) > Function (making it work).
**Defer decisions**: Database, web server, REST vs SOA - the longer you wait, the more info you have.
**Screaming Architecture**: Top-level structure should scream "Health Care System" not "Rails" or "Spring".

## Three Paradigms -> Three Architecture Concerns
- **Structured** (removes goto) = algorithmic foundation of modules
- **OOP** (removes function pointers) = polymorphism to cross architectural boundaries
- **Functional** (removes assignment) = discipline on data location/access, eliminates race conditions

**OOP's only real contribution for architects**: Safe polymorphism = absolute control over source code dependency direction = plugin architecture where high-level policy is independent of low-level detail.

**Functional programming insight**: All race conditions, deadlocks, concurrent update problems are due to mutable variables. Segregate mutability - push processing into immutable components. Event sourcing = CR not CRUD.

## Humble Object Pattern (at every boundary)
- **Presenter/View**: View is humble (just loads ViewModel data). Presenter formats (Date->String, sets flags).
- **Database Gateways**: Gateway impl is humble (SQL). Use case interactors are testable (stubs).
- **ORMs are "data mappers"**: Not true ORM. Objects != data structures. Belong in database layer.
- **Service Listeners**: Format/send data (humble) vs use simple data structures (testable).

## Main is a Plugin
- Main creates Factories, injects dependencies, loads config - the "dirtiest" module
- DI frameworks confined to Main; distribute dependencies normally after injection
- Multiple Main components: one for Dev, Test, Production, per-country

## Services are NOT Architecture (The Kitty Problem)
- Architecture is defined by boundaries WITHIN services, not between them
- Cross-cutting concerns (new features) force changes across ALL microservices
- Solution: SOLID principles with polymorphism inside services (OCP)
- "Architectural boundaries run THROUGH services, not between them"

## Four Packaging Approaches (Simon Brown's Missing Chapter)
1. **Package by Layer**: web/service/repository - doesn't scream the domain
2. **Package by Feature**: all orders code together - better domain visibility
3. **Ports and Adapters**: inside=domain, outside=infrastructure
4. **Package by Component**: bundles logic+persistence behind clean interface - stepping stone to microservices
- Access modifiers make ALL the difference - with all public, approaches are identical
- **Compiler-enforced architecture** > discipline > static analysis

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

## GoF Two Foundational Principles
1. **"Program to an interface, not an implementation"** - never declare variables as concrete classes
2. **"Favor object composition over class inheritance"** - inheritance breaks encapsulation; designers OVERUSE it

## Eight Causes of Redesign -> Pattern Solutions
| Cause | Patterns |
|-------|----------|
| Creating by specifying class | Abstract Factory, Factory Method, Prototype |
| Dependence on specific operations | Chain of Responsibility, Command |
| Platform dependence | Abstract Factory, Bridge |
| Algorithmic dependencies | Strategy, Template Method, Iterator, Visitor |
| Tight coupling | Facade, Mediator, Observer, Bridge, Command |
| Extending via subclassing | Bridge, Composite, Decorator, Observer, Strategy |
| Can't alter classes conveniently | Adapter, Decorator, Visitor |

## MVC is Three Patterns Combined
- **Observer**: Model notifies views on change
- **Composite**: Nested views treated uniformly
- **Strategy**: Controller is swappable input-handling strategy

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

## Refactoring Principles (Fowler - Deep Read)

### When NOT to Refactor
- **Easier to rewrite**: Code so tangled you can't make incremental working changes
- **Not in your way**: Ugly code behind a clean API that nobody touches - leave it
- **Close to a deadline**: Productivity gains come later, not now

### Key Mechanics
- **Extract Function**: Name by WHAT not HOW. Variables only read = parameters. Modified + used after = return it.
- **Replace Temp with Query**: Only when assigned once + no side effects. Refactor first, cache later.
- **Change Function Declaration (published API)**: Keep old as delegation to new. Deprecate. Migrate callers. Remove.

### Database Refactoring (Expand-Contract)
1. Add new column (expand)
2. Write to both old + new columns
3. Migrate all readers to new column
4. Backfill historical data
5. Remove old column (contract)
Each phase = separate deployment. Database always works between steps.

### Architecture Relationship
- YAGNI + Refactoring = flexible architecture (defer decisions because change is cheap)
- **Branch by Abstraction**: Create abstraction over old code -> migrate callers -> create new impl -> switch -> remove abstraction
- **Strangler Fig**: Gradually replace legacy while old system runs

### Testing Philosophy
- Test parts most likely to break, not 100% coverage
- Test boundaries: 0, 1, many elements. Edge cases. Empty strings.
- Bug report -> write failing test FIRST -> then fix
- Don't test implementation details. Test behavior.
- "Probing": Introduce a bug deliberately. If no test fails, you're missing a test.

## Kent Beck's Four Rules of Simple Design
1. Runs all the tests
2. Contains no duplication
3. Expresses the intent of the programmer
4. Minimizes the number of classes and methods

## Objects vs Data Structures (Clean Code Ch.6)
- Objects: hide data, expose behavior. Data structures: expose data, no behavior.
- **Virtual opposites**: Procedural = easy to add functions, hard to add types. OO = easy to add types, hard to add functions.
- **"Everything is an object" is a myth** - sometimes data structures + procedures are better.
- **Law of Demeter**: Talk to friends, not strangers. Don't chain: a.getB().getC().getD()
- **Hybrids are worst of both worlds** - half object, half data structure.
- **Active Records are data structures**, not objects. Don't put business rules in them.

## Concurrency (Clean Code Ch.13)
- Concurrency decouples WHAT from WHEN
- Two threads on one line of Java: 12,870 possible execution paths
- **Defense**: SRP for concurrency code, limit shared data scope, use copies, independent threads
- **Three models**: Producer-Consumer, Readers-Writers, Dining Philosophers
- **Testing**: Treat spurious failures as threading issues, test on multiple platforms, instrument with jiggling
- **Graceful shutdown is specifically hard** - plan for it early

## Defensive Programming (Code Complete)
- Validate all inputs at system boundaries
- Assertions for conditions that should NEVER occur
- Barricade pattern: validate at boundaries, trust internally
- Error handling for anticipated abnormal situations
- Exceptions for truly exceptional conditions only

## Code Complete - Additional Insights

### Variable Management
- **Span** (lines between references) and **Live Time** (first to last reference) - minimize both
- **Binding Time Spectrum**: code-time -> compile-time -> load-time -> instantiation -> just-in-time
- Earlier binding = less flexibility + less complexity. Only add what requirements demand.

### Table-Driven Methods
- Replace complex if-chains with array lookups: `charType = charTypeTable[inputChar]`
- Three access: Direct (index), Indexed (intermediate table), Stair-Step (range thresholds)
- Put knowledge in DATA rather than LOGIC

### Performance (Code Complete Ch.25-26)
- Less than 4% of code = more than 50% of runtime (Knuth)
- Performance hierarchy: Requirements > Architecture > Algorithm > OS > Compiler > Hardware > Code tuning
- "Fewer lines = faster" is FALSE - 10 individual assignments were 60-74% faster than a loop
- Always MEASURE - results vary wildly across languages and compilers
- Sentinel values in loops: 23-65% savings. Loop unrolling: 16-43% savings.

### Formatting and Self-Documenting Code
- **Newspaper metaphor**: Name at top, high-level concepts, details increase downward
- Significant systems built from ~200 line files (FitNesse avg: 65 lines)
- **Self-documenting code > comments**: Programming style IS documentation
- McCabe's complexity: 0-5 fine, 6-10 simplify, 10+ break apart

### Professional Character
- 10:1 ratio in programmer effectiveness (creation time, debug time, error rate)
- Great programmers realize how small their brains are -> all practices compensate for limited cognition
- Level 4 programmer: "15% communicating with computer, 85% communicating with people"
- "If you work 10 years, do you get 10 years experience or 1 year 10 times?"

## Testing Strategy
- **Unit tests**: Fast, isolated, test domain logic and use cases
- **Integration tests**: Test infrastructure (DB, API, external)
- **E2E tests**: Critical user journeys
- **F.I.R.S.T**: Fast, Independent, Repeatable, Self-Validating, Timely
- No single technique exceeds ~75% defect detection; use combinations
