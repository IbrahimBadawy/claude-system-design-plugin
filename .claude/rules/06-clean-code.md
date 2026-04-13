---
description: Code quality standards from Clean Code, Code Complete, and Refactoring
globs: "*.{ts,js,py,java,go,rs,cpp,c,tsx,jsx}"
---

# Rule: Clean Code Standards

## Naming (Clean Code Ch.2)
- Use intention-revealing names
- Class names: nouns (UserService, PaymentGateway)
- Method names: verbs (createUser, processPayment)
- One word per concept (don't mix fetch/retrieve/get)
- Searchable names (no single-letter vars except loop counters in small scopes)

## Functions (Clean Code Ch.3)
- Small (ideally < 20 lines)
- Do one thing
- One level of abstraction per function
- Max 3 parameters (prefer 0-2)
- No side effects
- Command-Query Separation

## Error Handling (Clean Code Ch.7)
- Use exceptions, not error codes
- Don't return null - return empty collections or Optional
- Don't pass null
- Provide context in exception messages
- Wrap third-party exceptions

## Classes (Clean Code Ch.10)
- Single Responsibility Principle
- High cohesion
- Small and focused

## Code Smells to Watch For (Refactoring Ch.3)
- Duplicated Code -> Extract Function
- Long Function -> Extract Function
- Long Parameter List -> Introduce Parameter Object
- Feature Envy -> Move Function
- Primitive Obsession -> Replace Primitive with Object
- Repeated Switches -> Replace with Polymorphism

## Testing (Clean Code Ch.9)
- F.I.R.S.T: Fast, Independent, Repeatable, Self-Validating, Timely
- One concept per test
- Test boundary conditions
