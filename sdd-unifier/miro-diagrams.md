# Miro Diagrams

All diagrams produced as part of a BRD live on a Miro board, accessed via the Miro MCP. The BRD references diagrams by link, never inlining them as Mermaid, ASCII, or image embeds.

## Why Miro-first

1. Diagrams evolve faster than text. A Miro link stays current as the board is edited; an inlined Mermaid block goes stale.
2. Reviewers prefer to discuss diagrams on Miro (comments, sticky notes, live edits) rather than in a Markdown file.
3. The BRD stays narrative-focused and readable end-to-end without requiring a Mermaid-capable renderer.

## Tool access

Miro tools are **deferred** — they don't appear in the initial tool list. Before calling any Miro tool, invoke `tool_search` with a query like `"miro diagram"` or `"miro board"` to load the Miro MCP tools into the available set. Only after that can `Miro:diagram_create`, `Miro:context_explore`, etc., be called.

## Board lifecycle

### Creating the board

At the start of BRD generation, before any chunk is written that will reference a diagram:

1. Load Miro tools via `tool_search`.
2. Check whether a board already exists for this BRD (`Miro:context_explore` on a board name like `BRD — [Project Name] — Diagrams`). If not, create one.
3. Record the board URL. It will appear in every BRD chunk that references a diagram and in the Figures index.

### Reusing the board

If the user is updating an existing BRD (e.g., "regenerate chunk 03 with the updated domain model"), reuse the existing Miro board rather than creating a new one. Ask the user for the board URL if it's not in context; don't guess.

## Which template sections need diagrams

| Template section | Diagram type | Miro tool |
|---|---|---|
| Executive Summary (optional, if architectural context helps) | Simple context diagram — boxes and arrows showing the system and its major neighbours | `Miro:diagram_create` |
| Background (optional) | Current-state diagram if the SoW describes an "as-is" worth visualising | `Miro:diagram_create` |
| Definitions & Important Details → data lifecycle / state machine | State diagram showing lifecycle states and transitions | `Miro:diagram_create` |
| Definitions & Important Details → data anatomy / entity structure | ERD or entity relationship diagram | `Miro:diagram_create` |
| Functional Requirements → Summarized Workflow | End-to-end workflow / sequence diagram | `Miro:diagram_create` |
| FR detail → sub-flow visualisation (when an FR's `How` is complex enough) | Sub-flow diagram | `Miro:diagram_create` |
| Integrations | Integration topology — target system in the middle, integrated systems around it, arrows labelled with mechanism | `Miro:diagram_create` |

**Do not** create a diagram for:
- Glossary, Assumptions, Dependencies, NFRs, UI/UX Expectations — these are tabular/list content.
- FRs whose `How` fits in 3–6 bullets — a diagram adds no information.
- Sections where the user has said the diagram is out of scope or will be added later.

## Diagram authoring

Diagrams are generated using Miro's DSL (Domain-Specific Language) for the `Miro:diagram_create` tool. Before producing a diagram:

1. Call `Miro:diagram_get_dsl` with the diagram type to confirm the current DSL syntax.
2. Compose the DSL describing nodes, edges, labels, and (where supported) swimlanes/groups.
3. Call `Miro:diagram_create` with the board ID and the DSL.
4. Capture the returned diagram identifier and the board URL.

For long-form structured content that is not a diagram (e.g., a table of state-transition rules), prefer `Miro:doc_create` if it makes sense to keep it alongside the diagrams on the same board; otherwise leave it as a Markdown table in the BRD.

## Referencing the diagram from the BRD

Wherever the BRD would have included the diagram inline, use this pattern instead:

```markdown
**Figure 3 — Notification Lifecycle State Machine.** See the Miro board for the live diagram: [BRD Diagrams — Notification Lifecycle](<board-url>#frame-<frame-id>).

> **Summary:** A notification moves through the states `accepted → validated → routed → dispatched → delivered`, with branching into `rejected`, `quarantined`, and `failed` at defined decision points. Transitions are audited; final states are terminal.
```

The **summary** block is mandatory. A reader with no Miro access should still understand what the diagram says at a conceptual level from the BRD alone — the Miro link is the authoritative visual, the summary is the fallback.

## The Figures index

Chunk 00 (`00-cover-and-changelog.md`) contains a Figures index. Every time a chunk references a Miro figure, also add an entry to the Figures index:

```markdown
| Figure # | Title | Location |
|---|---|---|
| Figure 1 | System Context Diagram | [Miro — Context](<url>) |
| Figure 2 | Notification Lifecycle | [Miro — Lifecycle](<url>) |
| Figure 3 | Integration Topology | [Miro — Integrations](<url>) |
```

Figure numbering is sequential across the whole BRD — not restarted per chunk.

## Naming convention on the Miro board

- Board name: `BRD — [Project Name] — Diagrams`
- Frame names inside the board mirror the BRD figure titles: `Figure 1 — System Context`, `Figure 2 — Notification Lifecycle`, etc.
- Keep the frame titles short but identifying — they'll be linked from the BRD.

## When the Miro MCP is unavailable

If `tool_search` does not return Miro tools (the MCP isn't enabled in this session), do **not** silently fall back to inline Mermaid. Instead:

1. Generate the BRD chunks as normal.
2. Where a diagram would be referenced, use a placeholder: `**Figure N — [Title].** [NEEDS MIRO: diagram to be authored on the project's Miro board.]`
3. At the end of the handoff summary, list the expected diagrams and tell the user the Miro MCP was not available so they can either enable it and ask for a re-run of the diagrammatic chunks, or author the diagrams manually.

Do not inline Mermaid as an "interim fallback" unless the user explicitly asks for it.
