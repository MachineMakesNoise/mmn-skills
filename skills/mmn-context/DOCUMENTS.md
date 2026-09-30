# Document routing

Use the smallest set that gives each established fact one authoritative home. Keep `README.md` as the concise overview and navigation hub; link to detail instead of repeating it. Match existing Markdown structure and voice.

Create a file only when it has substantive supported content beyond its title. Update an existing file only when the change corrects stale material, removes obsolete material, or adds confirmed value. Omit unsupported sections rather than adding TODO, TBD, unknown, or generated-content markers.

## Routing table

| Path | Create or update when | Put here |
| --- | --- | --- |
| `README.md` | Always ensure it is useful | Project purpose, intended users or use, current status and scope, verified quick start or usage, and links to deeper documents |
| `CONTEXT.md` | Project-specific domain language is established | Canonical domain terms, tight definitions, relationships, and vocabulary to avoid |
| `CONTEXT-MAP.md` | Several genuine bounded contexts have distinct language and ownership | Links to scoped context files and concise relationships between contexts |
| `ARCHITECTURE.md` | Current structure is evidenced or planned structure is explicitly confirmed | Components, boundaries, dependencies, data flow, runtime shape, and architectural constraints |
| `DESIGN.md` | Important behavioral or technical design detail does not belong in architecture or an ADR | User flows, algorithms, protocols, state models, and other cohesive design detail |
| `AGENTS.md` | The repository has verified project-specific agent rules | Commands, constraints, validation expectations, conventions, and links to detailed project guidance |
| `docs/adr/NNNN-slug.md` | A settled decision passes the ADR gate and appears in the approved file plan | The decision, its context, and why the chosen trade-off won |
| `DEVELOPMENT.md` | Local development needs more than a brief README section | Verified prerequisites, setup, build, test, lint, debug, and development commands |
| `CONTRIBUTING.md` | Contributor-facing policy or review workflow is established | Contribution boundaries, submission process, review expectations, and release responsibilities |
| `SECURITY.md` | Security support, reporting, or project-specific security boundaries are established | Supported versions, verified reporting route, sensitive boundaries, and response expectations |
| Other docs | A specification, runbook, API reference, or index has distinct lasting value | Only the material implied by that document's purpose |

## Placement rules

### Overview and links

Give `README.md` a title and a direct purpose statement. Include setup or usage only when repository evidence or confirmed plans support it. Link every generated detail document that a new reader should discover. Keep detailed architecture, vocabulary, procedures, and rationale in their dedicated files.

### Domain context

Most repositories use one root `CONTEXT.md`. Include only concepts specific to the project's domain, not general programming terminology. A compact form is:

```md
# {Context name}

{One or two sentences describing the context.}

## Language

**Canonical term**:
{One- or two-sentence definition.}
_Avoid_: {ambiguous or rejected synonyms}
```

Use a root `CONTEXT-MAP.md` only when evidence or confirmation establishes several bounded contexts. Each map entry links to the scoped `CONTEXT.md` and says what that context owns; a relationships section records meaningful interactions. Place context-specific ADRs under that context's `docs/adr/` and system-wide ADRs under the repository root `docs/adr/`.

### Current and planned design

Describe implemented structure as current. Put confirmed but unimplemented intent under an explicit `Planned` heading or equally clear label. `ARCHITECTURE.md` explains structural shape and boundaries; `DESIGN.md` explains cohesive behavior or mechanics. Put durable trade-off rationale in an ADR and link to it.

### Decision records

Create an ADR only when the decision is all three:

1. costly to reverse;
2. surprising without its rationale;
3. the result of a real trade-off.

The decision must be settled and listed in the approved write plan. Scan the destination ADR directory, increment its highest four-digit prefix, and use `NNNN-slug.md`. Start with the smallest useful record:

```md
# {Decision title}

{One to three sentences stating the context, decision, and reason.}
```

Add status, alternatives, or consequences only when they preserve material information.

### Operational guidance

Put verified developer commands in `DEVELOPMENT.md` when they would crowd the README. Put contributor policy in `CONTRIBUTING.md`, not in developer setup. Create `SECURITY.md` only from an established policy or confirmed reporting route; never invent a contact or promise.

A project `AGENTS.md` contains local execution guidance only. Include commands derived from manifests or configuration and constraints confirmed by the user. Link to project detail instead of copying global agent policy.

Create a documentation index only when the document set is large enough that README navigation is insufficient. Specialized specifications, runbooks, and API references remain lazy: create them when their distinct content and audience are established.

## File gate

For every candidate file, choose exactly one result:

- **Create:** enough supported content exists for a useful new document.
- **Update:** a targeted change corrects stale material, removes obsolete material, or adds confirmed value without replacing the document's voice or structure.
- **Skip:** the information is absent, uncertain, duplicated elsewhere, or too small to justify another file.

A complete file plan routes every accepted fact once, identifies links needed from `README.md`, separates current from planned content, and exposes every material conflict before writing.
