---
description: Design the system before writing implementation code
globs: "*.{ts,js,py,java,go,rs,cpp,c}"
---

# Rule: Design Before Code

For any component with non-trivial complexity, DESIGN FIRST:

1. Define the API contract (input/output)
2. Define the data model (schema, relationships, indexes)
3. Document the architecture (components, data flow)
4. Identify the critical path and potential bottlenecks
5. THEN implement

Exception: Simple bug fixes and minor refactoring can skip this.
