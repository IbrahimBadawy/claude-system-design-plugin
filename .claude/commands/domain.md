# /domain - Domain Model Analysis (DDD)

Analyze and model the business domain using Domain-Driven Design principles.

## Usage
```
/domain <system>                    # Full domain analysis
/domain contexts <system>           # Identify bounded contexts only
/domain events <system>             # Map domain events
/domain map <system>                # Draw context map
```

## Behavior

### Phase 1: Domain Discovery
1. Identify the core business processes
2. List all actors (users, systems, external services)
3. Map business workflows end-to-end
4. Identify the key business rules and invariants

### Phase 2: Bounded Contexts

Identify and define each bounded context:

```markdown
## Bounded Context: [Name]

**Purpose**: [what this context owns]
**Ubiquitous Language**: [key terms and their meaning in THIS context]
**Core Domain / Supporting / Generic**: [classification]

### Aggregates
- **[Aggregate Root]**: [description, invariants it enforces]
  - Entities: [child entities]
  - Value Objects: [value objects it contains]
  - Business Rules: [rules this aggregate enforces]

### Domain Events (Published)
- [EventName]: [when it occurs, what data it carries]

### Domain Events (Consumed)
- [EventName] from [Context]: [how it reacts]

### Commands
- [CommandName]: [what it does, who can invoke it]

### Queries
- [QueryName]: [what data it returns]
```

### Phase 3: Context Map

Define relationships between bounded contexts:

```
[Identity Context] <--- Upstream/Downstream ---> [Course Context]
                    Relationship: Customer-Supplier
                    Integration: REST API + Domain Events

[Course Context] <--- Anticorruption Layer ---> [External LMS]
```

**Relationship Types:**
- **Shared Kernel**: Two contexts share a small common model (tight coupling, use sparingly)
- **Customer-Supplier**: Upstream provides, downstream consumes (negotiate API)
- **Conformist**: Downstream conforms to upstream's model (no negotiation)
- **Anticorruption Layer**: Translate external models to internal (protect your domain)
- **Open Host Service**: Well-defined API for multiple consumers
- **Published Language**: Shared data format (JSON Schema, Protobuf)

### Phase 4: Event Storming Summary

Map the flow of events across the system:
```
[Command] -> [Aggregate] -> [Domain Event] -> [Policy/Handler] -> [Command] -> ...

Example:
SubmitOrder -> Order -> OrderSubmitted -> ProcessPayment -> Payment -> PaymentCompleted -> FulfillOrder
```

### Output
```markdown
# Domain Model: [System Name]

## Strategic Design (Big Picture)
### Bounded Contexts
[List all contexts with classification]

### Context Map
[Diagram of relationships]

## Tactical Design (Per Context)
### Context: [Name]
[Aggregates, entities, value objects, events, commands, queries]

## Domain Events Catalog
| Event | Source Context | Consumers | Data |
|-------|--------------|-----------|------|
| OrderPlaced | Orders | Payment, Notification | orderId, userId, items, total |

## Integration Patterns
[How contexts communicate: sync API, async events, saga, etc.]
```

## Examples
```
/domain university-management-system
/domain e-commerce-platform
/domain contexts payment-system
/domain events chat-application
```
