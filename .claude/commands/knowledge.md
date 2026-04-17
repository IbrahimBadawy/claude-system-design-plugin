# /knowledge - Domain Knowledge Builder

Build, manage, and use custom knowledge bases for any domain or technology.
When working with unfamiliar domains (robotics, automation, medical, finance, etc.),
build knowledge FIRST, then design and implement with that knowledge.

## Two Knowledge Scopes

| Scope | Location | When to Use |
|-------|---------|------------|
| **Global** | `.claude/knowledge/domains/<topic>.md` | Knowledge reusable across ALL your projects (e.g., HL7 FHIR, FIX protocol, OPC-UA, Kubernetes patterns) |
| **Project** | `projects/<project>/knowledge/<topic>/` | Knowledge specific to ONE project (e.g., this client's legacy API, their internal protocol) |

**Lookup order when knowledge is needed:**
1. Active project's `projects/<project>/knowledge/` (most specific)
2. `.claude/knowledge/domains/` (global)
3. Build fresh if missing

## Usage
```
/knowledge build <topic>                   # Ask: global or project-specific? Then research & save.
/knowledge build <topic> --global          # Force global scope (shared across projects)
/knowledge build <topic> --project         # Force project scope (this project only)
/knowledge build <topic> --from <url>      # Build from a specific documentation URL
/knowledge list                            # List all knowledge (global + current project)
/knowledge list --global                   # List only global knowledge
/knowledge list --project                  # List only current project's knowledge
/knowledge show <topic>                    # Show a specific knowledge base
/knowledge update <topic>                  # Re-research and update existing knowledge
/knowledge add <topic> <notes>             # Manually add notes to a knowledge base
/knowledge search <query>                  # Search across all knowledge bases
/knowledge import <file-or-url>            # Import a PDF, doc, or URL
/knowledge docs <topic>                    # List all documentation files for a topic
/knowledge promote <topic>                 # Promote project knowledge to global
/knowledge copy <topic> --to <project>     # Copy global knowledge into a project
```

## Behavior

### /knowledge build <topic>

Before building, ASK the user (unless `--global` or `--project` was passed):

```
Where should I save this knowledge?

  1. Global   - Available to ALL your projects (recommended for standard tech/protocols)
  2. Project  - Only for the active project (recommended for client-specific systems)

Which scope?
```

Then:

1. **Deep Web Research** (automated):
   - WebSearch "<topic> documentation"
   - WebSearch "<topic> programming guide"
   - WebSearch "<topic> architecture"
   - WebSearch "<topic> API reference"
   - WebSearch "<topic> best practices 2026"
   - WebSearch "<topic> integration patterns"
   - WebSearch "<topic> common mistakes"

2. **Read Official Documentation**:
   - WebFetch the official docs pages found
   - Extract: concepts, APIs, protocols, data formats, terminology
   - Identify: key components, communication patterns, constraints

3. **Synthesize and Organize** into a structured knowledge file:

```markdown
# Knowledge: [Topic]

**Built**: [date]
**Scope**: Global | Project (<project-name>)
**Sources**: [list of URLs consulted]
**Last Updated**: [date]

## Overview
[What is this technology/domain? Brief description]

## Key Concepts
| Concept | Description |
|---------|-------------|
| [Term 1] | [What it means in this domain] |
| [Term 2] | [Explanation] |

## Architecture / How It Works

Show the diagram as ASCII in chat; persist the Mermaid version in this file (Rule 20):

\`\`\`mermaid
graph TB
    A[Component A] --> B[Component B]
    B --> C[Component C]
\`\`\`

## Programming / Configuration
### Language / Tools
[What languages, IDEs, tools are used]

### Key APIs / Interfaces
| API/Interface | Purpose | Protocol |
|--------------|---------|----------|
| [API 1] | [what it does] | [REST/OPC-UA/etc] |

### Code Examples
[Key code patterns, configurations, or snippets from docs]

## Communication Protocols
| Protocol | Used For | Port/Details |
|----------|---------|-------------|
| [Protocol 1] | [purpose] | [details] |

## Data Formats
[What data formats does this technology use? JSON, XML, binary, etc.]

## Integration Patterns
[How does this technology integrate with other systems?]

## Constraints & Limitations
[What can't it do? Performance limits? Hardware requirements?]

## Security Considerations
[Authentication, encryption, network security for this technology]

## Common Mistakes / Gotchas
[Things to watch out for based on community experience]

## Useful Resources
- [Official docs URL]
- [Best tutorial/guide]
- [Community forum/GitHub]
```

4. **Save Knowledge** at the chosen scope:
   - **Global**: `.claude/knowledge/domains/<topic>.md`
   - **Project**: `projects/<project>/knowledge/<topic>/<topic>.md`
     - Plus a `docs/` subfolder for user-provided files (PDFs, datasheets)
     - Plus a `urls.md` for bookmarked references

5. **Render the diagrams in chat** using Mermaid code blocks so the user sees them immediately.

### /knowledge build <topic> --from <url>

When building from a specific URL:
1. WebFetch the URL
2. Extract all technical content
3. Organize into the standard knowledge format
4. Can be chained: `/knowledge build kuka --from https://docs.kuka.com`

### /knowledge list

Show all available knowledge bases:
```
## Knowledge Bases

### Global (available in all projects)
Location: .claude/knowledge/

| Topic | Built | Updated | Size |
|-------|-------|---------|------|
| patterns | Built-in | - | 16 references |
| technology-guide | Built-in | 2026 | 2026 stack |
| distributed-systems | Built-in | - | DDIA |
| clean-architecture | Built-in | - | 8 books |

### Global Custom Domains
Location: .claude/knowledge/domains/

| Topic | Built | Sources |
|-------|-------|---------|
| kuka-robot | 2026-04-13 | 5 URLs |
| plc-siemens | 2026-04-13 | 8 URLs |
| opc-ua | 2026-04-13 | 4 URLs |

### Project: university-system
Location: projects/university-system/knowledge/

| Topic | Built | Sources |
|-------|-------|---------|
| moodle-api | 2026-04-12 | 3 URLs |
| client-legacy-sis | 2026-04-15 | Client docs + notes |
```

### /knowledge promote <topic>

Move a project-specific knowledge file to global scope (when you realize it's reusable):
1. Copy `projects/<project>/knowledge/<topic>/<topic>.md` → `.claude/knowledge/domains/<topic>.md`
2. Update the `Scope:` field to `Global`
3. Optionally remove from the project (ask user)

### /knowledge copy <topic> --to <project>

Copy a global knowledge file into a specific project (e.g., to customize it for that client):
1. Copy `.claude/knowledge/domains/<topic>.md` → `projects/<project>/knowledge/<topic>/<topic>.md`
2. Update the `Scope:` field to `Project (<project>)`
3. User can then edit the project copy without affecting global

### /knowledge update <topic>

Re-research the topic to find updates:
1. Re-run all searches
2. Compare with existing knowledge
3. Add new findings, mark outdated info
4. Update the "Last Updated" date

### /knowledge add <topic> <notes>

Manually add your own notes/knowledge:
```
/knowledge add kuka-robot "The KUKA.PLC mxAutomation interface allows direct PLC control of the robot via EtherNet/IP"
```
Appends to the knowledge file under a "Manual Notes" section.

### /knowledge search <query>

Search across ALL knowledge bases for a term (both global and project):
```
/knowledge search "OPC-UA"
```
Returns matching sections from all knowledge files, grouped by scope.

## Integration with Other Commands

When knowledge exists for a project's domain:
- `/design` references domain knowledge for architecture decisions (checks project first, then global)
- `/implement` uses API docs and code examples from knowledge
- `/schema` considers domain-specific data structures
- `/research` checks existing knowledge before searching online
- `/plan create` incorporates domain constraints into plans

## Example Workflows

### Global Knowledge (cross-project)
```
# Standard protocols — build globally once, use in all projects
/knowledge build opc-ua --global
/knowledge build hl7-fhir --global
/knowledge build fix-protocol --global
/knowledge list --global
```

### Project-Specific Knowledge
```
/project add client-xyz-erp
/knowledge build client-legacy-api --project    # This client's custom API
/knowledge build client-data-model --project    # Their specific data model
/knowledge list --project
```

### Industrial Automation ERP (mix of global + project)
```
/project add automation-erp

# Global: standard tech used in many projects
/knowledge build kuka-robot --global
/knowledge build siemens-plc --global
/knowledge build opc-ua --global

# Project: this client's specific config
/knowledge build client-plant-layout --project
/knowledge build client-existing-scada --project

# Now design with full domain knowledge (global + project)
"ابدا plan"
/design automation-erp
```

### Medical System
```
/project add hospital-system
/knowledge build hl7-fhir --global            # HL7 FHIR healthcare standard
/knowledge build dicom --global               # Medical imaging standard
/knowledge build hipaa-compliance --global    # HIPAA security requirements
/knowledge build hospital-existing-hims --project  # This hospital's legacy HIMS
```

### Financial System
```
/project add trading-platform
/knowledge build fix-protocol --global
/knowledge build market-data-feeds --global
/knowledge build regulatory-compliance --global
/knowledge build broker-api --project         # This broker's specific API
```

## Examples
```
/knowledge build kuka-robot --global
/knowledge build client-legacy-api --project
/knowledge build plc-siemens --from https://support.industry.siemens.com
/knowledge list
/knowledge show kuka-robot
/knowledge update opc-ua
/knowledge promote client-legacy-api
/knowledge add kuka-robot "KRL is the programming language for KUKA robots"
/knowledge search "EtherNet/IP"
```
