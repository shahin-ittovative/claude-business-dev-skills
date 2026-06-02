# Product Documentation Skill Suite

A set of four reusable skills that cover the full product documentation lifecycle, from idea validation through to implementation-ready design. Each skill owns one stage. Used in order, they take a raw idea and carry it to a buildable specification with consistent structure, numbering, and diagramming conventions across every stage.

```text
Idea  ->  pre-BRD  ->  BRD  ->  SDD  ->  LLD  ->  Build
          validate     what     how-      how-       code
                       /why     overall   detailed
```

| Stage              | Skill   | Question it answers               | Primary output                                    |
| ------------------ | ------- | --------------------------------- | ------------------------------------------------- |
| 1. Discovery       | pre-BRD | Is this worth building?           | Strategic framework set plus go / no-go verdict   |
| 2. Requirements    | BRD     | What are we building, and why?    | Functional plus non-functional requirements       |
| 3. Solution design | SDD     | How does the system fit together? | Architecture, services, cross-cutting concerns    |
| 4. Detailed design | LLD     | How is each service built?        | Schemas, APIs, sequence flows, class-level detail |

---

## Shared conventions

All four skills follow the same house style, so output is interchangeable and tool-friendly across stages.

- **Markdown only.** No `.docx` or `.pdf` unless explicitly requested.
- **No em dash characters.** Use commas, colons, parentheses, or sentence breaks instead.
- **Chunked by default.** Long deliverables are split into multiple context-scoped `.md` files, one per logical grouping, not one monolithic file. Merge into a single document only on request.
- **Stable two-digit numbering.** Files use the `NN-kebab-name.md` pattern so ordering is deterministic and cross-references stay valid.
- **Self-describing front-matter.** Each chunk opens with an HTML comment block (chunk number, title, tier or stage, parent document) so an agent can reassemble the set.
- **Diagrams via Miro MCP.** Architecture and flow diagrams are created directly on a Miro board and referenced by link. Diagrams are not inlined as Mermaid, ASCII, or images unless inline rendering is explicitly requested. Where a Mermaid fallback is provided (notably in the SDD template), it is offered as an alternative, not the default.
- **Tech defaults.** Java 21, Spring Boot 3.4+, PostgreSQL, Kafka, Angular 17+ with Tailwind and PrimeNG, UUID v7 primary keys, BIGINT minor units for money, UTC for all timestamps.

---

## 1. pre-BRD

**Purpose:** the discovery layer that runs before any requirements are written. It validates that an idea is worth building before effort is spent on a full BRD.

**Structure:** a five-tier framework workbook delivered as a master index plus 22 framework files.

1. Idea definition: Concept Sheet, Product Charter, Lean Canvas, Value Proposition Canvas, Empathy Map.
2. Market and competition: Market Comparison, Market Sizing, PESTLE, Porter's Five Forces, EFAS, IFAS, SWOT.
3. Prioritization: RICE, MoSCoW.
4. Strategy and planning: OKRs, BCG Matrix, Ansoff Matrix, VRIO, Product Strategy Canvas, Product Lifecycle, Roadmap and Project Plan.
5. Synthesis: Executive Summary Scoreboard with a composite score and go / no-go thresholds.

**Key behaviors:**

- The master file (`00-pre-brd-master.md`) links every framework by relative path and states a fill contract: empty cells in the Answer column (descriptive frameworks) or empty value cells (scoring tables) are where values get injected. Guidance and description columns are read-only context.
- Scoring and calculation rules are stated in prose so they can be computed deterministically: Porter's 1 to 3 average, EFAS and IFAS weight times rating with weights summing to 1, RICE = Reach times Impact times Confidence divided by Effort, BCG growth-rate and relative-share formulas, VRIO result legend, Ansoff decision scoring, the Market Sizing top-down vs bottom-up cross-check, and the Executive Summary composite with go / no-go thresholds.
- Fixed-row frameworks (PESTLE, Porter's, SWOT, MoSCoW, Ansoff, Product Lifecycle) ship with their rows already in place.

**Feeds into:** the BRD. A validated pre-BRD supplies the problem statement, target users, market context, and prioritized scope that the BRD turns into requirements.

---

## 2. BRD (`product-brd-unified-generator`)

**Purpose:** generate or update a Business Requirements Document, or a combined BRD-HLD, in the standardized house template.

**Triggers:** any request to create, generate, author, draft, write, or build a BRD, BRD-HLD, Business Requirements Document, High-Level Design, or product requirements document. Also triggers on requests to transform, convert, reformat, or standardize a Scope of Work (SoW), Statement of Work, project brief, product spec, or RFP scope into a BRD.

**Package contents:**

- `SKILL.md`: entry point with trigger description, workflow, and the mode matrix. The frontmatter description is kept under the 1024-character limit so it uploads cleanly to claude.ai; the longer detail lives in the body.
- `TEMPLATE.md`: the standard BRD-HLD template.
- `references/chunking.md`: the canonical 11 to 16 chunk map with stable two-digit numbering.
- `references/sow-transformation.md`: SoW-to-BRD field mapping.
- `references/miro-diagrams.md`: Miro tool access, board lifecycle, which sections warrant diagrams, DSL authoring, and graceful degradation when Miro is unavailable.
- `references/fr-quality.md`: pass and fail tests for each of the five FR sub-sections (What, Why, How, Constraints, UI/UX), plus guidance on handling cross-cutting concerns once rather than repeating them per FR.

**Modes:**

- **Chunked (default):** one `.md` file per logical section grouping.
- **Merged (on request):** triggers like "merge", "consolidate", "single file", or "full doc" concatenate the chunks into one unified BRD.

**Feeds into:** the SDD. Domain facts and behavioral constraints in the BRD become the functional scope the SDD designs against. When using SpecKit, behavioral requirements route to `spec.md`, technical constraints to `plan.md`, and stakeholder rationale stays in the BRD only.

---

## 3. SDD

**Purpose:** the Solution Design Document. Extends the BRD with technical architecture depth: how the system fits together at the design level.

**Two template variants:**

- `SDD-Template-Unified.md`: fully populated reference template with worked examples throughout. Used as a reference and onboarding aid.
- `SDD-Template-Unified-Empty.md`: clean fill-in version with placeholder brackets in every field and only structural scaffolding plus canonical defaults retained. This is the working starting point for each new SDD.

**Sections covered:** document metadata, executive summary, scope, assumptions, risks, glossary, ecosystem overview, system users with use case diagrams, high-level architecture (context, workflow, and sequence diagrams), architecture style and principles, an architectural decisions table, cross-cutting concerns (database modeling, multi-tenancy, deployment, observability, configuration management), an integrations table, detailed per-service specs (DB modeling, ERDs, API lists, EDA event models, error handling, observability, compliance, deployment, future enhancements), performance and capacity planning, environments, operations runbook, appendix, and wishlist.

**Diagrams:** dual format. A tool-agnostic step-by-step text description suitable for Miro, Lucidchart, or draw.io, plus a Mermaid code block as an alternative.

**Feeds into:** the LLD. Each service identified and bounded in the SDD becomes the subject of its own LLD.

---

## 4. LLD

**Purpose:** the Low-Level Design. Takes a single service or component that the SDD scoped and specifies it to an implementation-ready level of detail.

**Typical contents per service:**

- Database schema: full table definitions, keys, constraints (including database-enforced invariants such as balance floors), and ERDs.
- API design: endpoint-by-endpoint request and response contracts, status codes, and error catalog.
- Sequence diagrams for the critical flows, including the edge cases (race conditions, idempotency, expiry, retries).
- Concurrency and safety patterns: locking strategy (pessimistic, optimistic, or atomic conditional update), idempotency handling, atomic gating, and the outbox or CDC pattern where events are emitted.
- Class-level and component-level breakdown mapping the design onto the Java and Spring Boot reference structure.

**Relationship to the SDD:** the SDD and LLD are often authored together as a combined HLD plus LLD document for a single service, with the HLD section setting context (system context, architecture overview, tenant and domain model, technology stack) and the LLD section carrying the implementation detail above. The LLD inherits all shared conventions and tech defaults.

**Feeds into:** implementation, including SpecKit-driven and Claude Code-assisted builds.

---

## Installation and usage

These skills run in two environments.

### claude.ai (web or desktop)

Upload each skill through Settings, under Capabilities or Customize, then Skills. Each skill must be a folder containing its `SKILL.md` (plus any reference files), zipped and uploaded individually, then toggled on. Custom skills are private to your account; on Team or Enterprise plans an owner can optionally share them org-wide.

Note the 1024-character cap on the `description` frontmatter field: any skill whose description runs longer is rejected on upload, so keep trigger-rich detail in the body.

### Claude Code (CLI)

Install at user scope by extracting the skill into:

```text
C:\Users\<username>\.claude\skills\<skill-name>\
```

List installed skills with `/skills` inside a session, or this command in PowerShell outside one:

```powershell
dir $env:USERPROFILE\.claude\skills
```

Common failure causes if a skill does not appear: the session was not reloaded, Windows extraction created a double-nested folder, or the `SKILL.md` frontmatter is invalid. Two caveats for CLI use: the Miro MCP must be configured separately via `claude mcp add`, and any output paths that reference `/mnt/user-data/outputs/` are sandbox-specific and may need adjusting.

---

## Suggested workflow

1. Run **pre-BRD** to validate the idea and produce a go / no-go verdict.
2. If go, run the **BRD** skill to convert the validated concept (or an inbound SoW) into requirements.
3. Run **SDD** to design the system architecture against those requirements.
4. Run **LLD** per service to reach implementation-ready detail.
5. Hand the LLD to SpecKit and Claude Code for the build.
