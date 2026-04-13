# /knowledge - Domain Knowledge Builder

Build, manage, and use custom knowledge bases for any domain or technology.
When working with unfamiliar domains (robotics, automation, medical, finance, etc.),
build knowledge FIRST, then design and implement with that knowledge.

## Usage
```
/knowledge build <topic>              # Research a topic deeply and save knowledge
/knowledge build <topic> --from <url> # Build from a specific documentation URL
/knowledge list                       # List all knowledge bases (global + project)
/knowledge show <topic>               # Show a specific knowledge base
/knowledge update <topic>             # Re-research and update existing knowledge
/knowledge add <topic> <notes>        # Manually add notes to a knowledge base
/knowledge search <query>             # Search across all knowledge bases
/knowledge import <file-or-url>       # Import a PDF, doc, or URL into knowledge
/knowledge docs <topic>               # List all documentation files for a topic
```

## Behavior

### /knowledge build <topic>

When building knowledge for a new domain:

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
[System architecture, components, communication flow]
[Include Mermaid diagrams where helpful]

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
[Common integration architectures]

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

4. **Save Knowledge**:
   - **Project-level**: `.claude/projects/<project>/knowledge/<topic>.md`
     (used when building for a specific project)
   - **Global-level**: `.claude/knowledge/domains/<topic>.md`
     (used when the knowledge applies across projects)

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
| Topic | Built | Updated | Size |
|-------|-------|---------|------|
| patterns | Built-in | - | 16 references |
| technology-guide | Built-in | 2026 | 2026 stack |
| distributed-systems | Built-in | - | DDIA |
| clean-architecture | Built-in | - | 8 books |

### Custom Domains
| Topic | Built | Sources | Location |
|-------|-------|---------|----------|
| kuka-robot | 2026-04-13 | 5 URLs | domains/kuka-robot.md |
| plc-siemens | 2026-04-13 | 8 URLs | domains/plc-siemens.md |
| opc-ua | 2026-04-13 | 4 URLs | domains/opc-ua.md |

### Project: university-system
| Topic | Built | Location |
|-------|-------|----------|
| moodle-api | 2026-04-12 | projects/university-system/knowledge/ |
```

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

Search across ALL knowledge bases for a term:
```
/knowledge search "OPC-UA"
```
Returns matching sections from all knowledge files.

## Integration with Other Commands

When knowledge exists for a project's domain:
- `/design` references domain knowledge for architecture decisions
- `/implement` uses API docs and code examples from knowledge
- `/schema` considers domain-specific data structures
- `/research` checks existing knowledge before searching online
- `/plan create` incorporates domain constraints into plans

## Example Workflows

### Industrial Automation ERP
```
/project add automation-erp
/knowledge build kuka-robot           # KUKA robot programming & APIs
/knowledge build siemens-plc          # Siemens S7 PLC programming
/knowledge build opc-ua               # OPC-UA protocol for industrial comm
/knowledge build scada-systems        # SCADA architecture
/knowledge build mes-systems          # Manufacturing Execution Systems
/knowledge build industrial-iot       # IIoT sensors and protocols
# Now design with full domain knowledge
"ابدا plan"
/design automation-erp
```

### Medical System
```
/project add hospital-system
/knowledge build hl7-fhir             # HL7 FHIR healthcare standard
/knowledge build dicom                # Medical imaging standard
/knowledge build hipaa-compliance     # HIPAA security requirements
/knowledge build medical-devices-api  # FDA device integration
```

### Financial System
```
/project add trading-platform
/knowledge build fix-protocol         # FIX financial protocol
/knowledge build market-data-feeds    # Real-time market data
/knowledge build regulatory-compliance # Financial regulations
/knowledge build order-matching       # Order book algorithms
```

## Examples
```
/knowledge build kuka-robot
/knowledge build plc-siemens --from https://support.industry.siemens.com
/knowledge list
/knowledge show kuka-robot
/knowledge update opc-ua
/knowledge add kuka-robot "KRL is the programming language for KUKA robots"
/knowledge search "EtherNet/IP"
```
