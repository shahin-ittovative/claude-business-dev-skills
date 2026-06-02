# SDD → LLD Field Mapping (FROM-SDD direction)

This file defines how to derive a Low-Level Design from a Solution Design Document. It is the equivalent of `brd-to-sdd.md` in `sdd-unifier`, but one stage further down the chain: SoW → BRD → SDD → **LLD**.

The default workflow shape is **SoW → BRD → SDD → LLD**, with one LLD per SDD.

---

## BRD Specs inheritance (read this BEFORE the SDD)

If the SDD's master index links to a BRD that contains a `Specs` section (chunked: `12-specs.md`; combined: `# Specs`), the LLD MUST inherit two of its sub-sections directly:

| BRD Specs sub-section | LLD destination | How to apply |
|---|---|---|
| **2. Tech Stack** | LLD §0 metadata "Tech Stack snapshot" + §3 Architecture (Runtime Stack) + per-service §7.X (Class & Interface Map type choices) | The BRD Specs.Tech Stack is the authoritative version pin for the entire LLD. When SDD's §6 Ecosystem Overview disagrees with BRD Specs, BRD Specs wins (it was the original commitment). When CLAUDE.md defaults disagree with BRD Specs, BRD Specs wins. Pattern selection in `pattern-rules.md` reads the resolved Tech Stack to choose stack-appropriate patterns (e.g., Spring Boot vs Quarkus vs Express). |
| **4. Project Type** | LLD §0 metadata "Project Type" + gates the entire direction decision (see `transform-detection.md`) | **Greenfield:** standard from-sdd direction generates implementation-ready specs from the SDD. **Brownfield:** the user should normally pick `from-code` or `hybrid` direction (the LLD then describes what exists, not what was designed). If the user picks `from-sdd` for a brownfield project, surface a friction prompt explaining the disconnect — proceeding will produce a *target-state* LLD that may not reflect reality. |

**Specs.Mission** and **Specs.Roadmap** are not directly inherited by the LLD body — they are too coarse-grained for implementation-level documentation. They remain visible in the LLD master index as a "see BRD Specs" pointer but do not drive section content.

**If the BRD has no Specs section** (legacy / pre-Specs version), proceed with the SDD as the only source. CLAUDE.md defaults fill what the SDD does not pin.

---

## What derivation IS

- Reading the SDD in full (and BRD if linked).
- Auto-filling LLD sections that have direct SDD analogues (Bounded Context, Architecture overview, Data Model skeleton, API endpoints, Event topics).
- Applying CLAUDE.md design rules aggressively per `pattern-rules.md` (constructor injection, records DTOs, idempotency on money writes, outbox for state changes, sagas for cross-service, Resilience4j, multi-tenant indexes, RFC 9457 errors, OpenAPI versioning, Flyway migrations).
- Producing a per-service implementation chunk (`04-implementation/<service>.md`) that is **implementation-ready** for an AI implementer to pick up.
- Flagging gaps with `> Confirm: ...` (medium confidence) or `> TODO: <best-guess> — verify` (low confidence).

## What derivation IS NOT

- Inventing class names that aren't implied by the SDD or by CLAUDE.md naming conventions.
- Inventing performance numbers from thin air. SLO targets carry from SDD §14; if the SDD didn't pin them, the LLD flags them.
- Inventing concrete OpenAPI schemas if the SDD didn't pin request / response shapes. The LLD proposes shapes per CLAUDE.md REST conventions and flags them for confirmation.
- Producing complete pseudocode for every method. Only non-trivial methods get pseudocode; CRUD methods are described by signature alone.

---

## Detecting SDD input form

The SDD can arrive in two forms:

### Chunked form (sdd-unifier output)

Recognise it by:

- Folder path matches `sdd-*/`.
- Contains files named `00-cover-and-changelog.md`, `01-executive-summary-scope-risks.md`, `02-ecosystem-overview.md`, `03-users-and-use-cases.md`, `04-architecture-style-and-diagrams.md`, `05-workflows-and-sequences.md`, `06-principles-and-decisions.md`, `07-cross-cutting-concerns.md`, `08-integrations.md`, `09-services-summary.md`, `10a-service-*.md` (one per service), etc.
- Each file begins with `<!-- CHUNK: NN ... PART OF: SDD — ... -->`.

**Reading order:** numeric (00, 01, 02, …), with multi-letter chunks read alphabetically within their numeric prefix.

**Treat as one logical SDD.** Read all chunks first, build a unified mental map, then write one LLD.

### Combined form (single SDD .md file)

Recognise by `SDD-` filename prefix or matching template structure.

Read in full before writing anything.

---

## Field mapping table

This is the authoritative mapping. Each row says: SDD source section → LLD destination chunk + transformation note.

| SDD section | LLD destination | Transformation note |
|---|---|---|
| 00 Cover & Changelog | `00-metadata.md` | New LLD has its own version (v1.0). Add a "Related SDD" line pointing at the SDD's filename / chunk folder. New Changes Log entry: "Initial LLD draft, derived from SDD v[X.X] via lld-unifier. Mode: from-sdd." |
| 01 Executive Summary | `01-purpose-and-scope.md` § 1 Purpose | Recast as **implementation-purpose** statement: what the LLD enables a developer to do (not the technical-summary framing of the SDD). |
| 01 Scope (In/Out) | `01-purpose-and-scope.md` § 2 Scope | Verbatim copy. May narrow if the LLD covers a subset of services. |
| 01 Assumptions | `01-purpose-and-scope.md` § 3 Assumptions | Verbatim copy. Add LLD-specific implementation assumptions if any. |
| 01 Risks | (Not directly carried — surface in `15-open-questions.md` if any risk affects implementation choices) | The LLD does not duplicate the SDD risk register. |
| 01 Glossary | `01-purpose-and-scope.md` § 4 Glossary | Verbatim copy + LLD-specific terms (pattern names, transaction-policy names, class-suffix conventions). |
| 02 Ecosystem Overview | `03-architecture.md` § 6.3 Runtime Stack | Verbatim carry of named technologies and version pins. |
| 03 Actors / Use Cases | (Implicit — informs `04-implementation/<service>.md` § 7.8 Use Case Workflows naming) | Each use case becomes a `### UC-NN` block in the owning service's file. |
| 04 Architecture Style | `03-architecture.md` § 6.4 Architectural Style — As Operationalised | The LLD doesn't restate the style; it operationalises it (concrete topic naming convention, schema registry choice, outbox-table convention, saga style per case). |
| 04 Context Diagram | `02-context.md` § 5.4 Cross-Service Dependencies | Convert to a Mermaid `graph LR` showing services + external systems. Optional Miro link if the SDD's Context Diagram is on Miro. |
| 04 High-Level Architecture | `03-architecture.md` § 6.1 Component Topology | Convert to Mermaid `graph TB`. |
| 05 Workflows | `04-implementation/<service>.md` § 7.8 Use Case Workflows (per service) | The SDD describes flows at system level; the LLD refines per service with idempotency points, outbox emission points, retry/timeout choices, sequence diagrams (Mermaid). |
| 05 Sequence Diagrams | `04-implementation/<service>.md` § 7.8 (sequence subsection per use case) | Convert to inline Mermaid `sequenceDiagram`. Optional Miro link if the SDD's diagram is on Miro. |
| 06 Architecture Principles | (Inherited — referenced from `03-architecture.md`) | The LLD doesn't restate principles; it follows them. |
| 06 Architectural Decisions (ADRs) | `16-references.md` § 19.2 (cross-link only) | The LLD links to ADRs but doesn't duplicate them. |
| 07 Cross-Cutting Concerns | `09-cross-cutting.md` (entire chunk) | Each SDD default expands into the concrete LLD configuration: e.g., "Multi-tenancy: schema-per-tenant for high-volume" → `09-cross-cutting.md` § 12.1 + concrete index strategy in `05-data-model.md` § 8.4. |
| 08 Integrations | `06-api-contracts.md` (downstream REST) + `07-event-contracts.md` (downstream events) + `04-implementation/<svc>.md` (per-service Resilience4j config in `09-cross-cutting.md` § 12.3) | Each integration row becomes either an outbound API call (with timeout, retry, circuit breaker config) or an event subscription. |
| 09 Services Decomposition | `lld-master.md` (table of services) + one `04-implementation/<service>.md` file per row | This is **the** structural mapping: one SDD service = one LLD per-service file. |
| 10a (per service) Boundaries | `04-implementation/<svc>.md` § 7.1 Responsibility | Reframe from boundary statement to responsibility statement. |
| 10a Input | `06-api-contracts.md` (REST inbound) + `07-event-contracts.md` (event consumers) | Inbound REST and event consumers each become rows in the contracts chunks; cross-referenced from the per-service file. |
| 10a Business Logic | `04-implementation/<svc>.md` § 7.2 Class & Interface Map + § 7.3 Method Pseudocode + § 7.4 Design Patterns Applied | The SDD's business-logic prose becomes the LLD's class/interface map and pattern application. **This is the heaviest derivation step.** |
| 10a State Machine (Business Logic subsection) | `08-state-and-rules.md` § 11.1 Aggregate State Machines | Convert to Mermaid `stateDiagram-v2`. |
| 10a Output | `06-api-contracts.md` (REST outbound) + `07-event-contracts.md` (event producers) | Same as Input but for outputs. |
| 10a Integrations (per service) | `04-implementation/<svc>.md` (cross-reference to `06-api-contracts.md` outbound calls) + Resilience4j config in `09-cross-cutting.md` § 12.3 | |
| 10a DB Modeling | `05-data-model.md` § 8.2 Tables (in this service's schema) + § 8.3 Indexes + § 8.5 Migration Plan + § 8.6 Retention & Archival + § 8.7 Encryption | Direct lift; the SDD's ERD becomes the LLD's Mermaid `erDiagram`. |
| 10a Multi-Tenancy Specifications | `05-data-model.md` § 8.4 Multi-Tenancy Strategy (per-service row) | |
| 10a API Standards + List of APIs | `06-api-contracts.md` § 9.1 Endpoint Inventory + § 9.2 Request/Response Shapes + § 9.5 OpenAPI snippets | Per-service endpoint table; OpenAPI generation is downstream (LLD references `[path/openapi.yaml]` and lists snippets). |
| 10a Event Model + Messaging Infra | `07-event-contracts.md` § 10.1 Topic Inventory + § 10.2 Event Schemas + § 10.3 Producer Specs + § 10.4 Consumer Specs | Per-topic detail. |
| 10a Constraints | `04-implementation/<svc>.md` § 7.7 Error Handling (constraints that surface as exceptions) + `09-cross-cutting.md` (constraints that are platform rules) | |
| 10a Error Handling | `04-implementation/<svc>.md` § 7.7 Error Handling (per-exception RFC 9457 mapping) | Each error becomes a row in the per-service error table. |
| 10a Observability | `10-operations.md` § 13.3 Metrics (per-service custom metrics) | |
| 10a Developer Notes | `04-implementation/<svc>.md` § 7.4 Design Patterns Applied + `13-testing.md` § 16 (test strategy) | |
| 10a Service-Level Diagrams (Flow Chart, Sequence) | `04-implementation/<svc>.md` § 7.8 Use Case Workflows (Mermaid sequence per use case) | |
| 10a Compliance | `11-security.md` § 14.6 Compliance | |
| 10a Deployment Strategy | `03-architecture.md` § 6.2 Deployment Topology (per-service replicas / strategy if overrides exist) | |
| 10a Future Enhancements | (Carried — surface in `15-open-questions.md` § 18.4 Decisions Pending if any inform near-term implementation) | |
| 11 Performance & Capacity | `12-performance.md` (entire chunk) | Targets carry verbatim; LLD adds the *meeting-the-targets* detail (caching, hot-path indexes, bulkhead sizes). |
| 12 Environments | `10-operations.md` § 13.1 Configuration | Per-environment config rows. |
| 13 Operations Runbook | `10-operations.md` § 13.8 Runbook Procedures | The SDD's runbook procedures carry; the LLD enriches with concrete commands once code exists. From SDD alone, runbook procedures may be skeleton-form with `> TODO: concrete commands once code exists — verify`. |
| 14 Appendix | `16-references.md` (entire chunk) | Carry references; add LLD-specific rows. |

---

## LLD-only sections (always need architect / SDD-blind input)

These sections have no direct SDD analogue and always produce `> TODO: <best-guess> — verify` markers when deriving from SDD alone:

### `04-implementation/<service>.md` § 7.2 Class & Interface Map (concrete names)

The SDD describes business logic; class names are an implementation choice. Apply CLAUDE.md naming defaults:

- Controllers: `<Domain>Controller`
- Service interfaces: `<Domain>Service`
- Service impls: `<Domain>ServiceImpl`
- Repositories: `<Domain>Repository`
- Domain types: records (CLAUDE.md), suffixed `Dto` (inbound), `Response` (outbound), or unsuffixed (entity).

Flag with `> Confirm: class names follow CLAUDE.md conventions; verify with team`.

### `04-implementation/<service>.md` § 7.3 Method-Level Pseudocode

Only non-trivial methods get pseudocode. The SDD's `Business Logic → How` paragraphs seed the pseudocode. Flag low-confidence inferences with `> TODO: <pseudocode best-guess> — verify with FR-NN`.

### `04-implementation/<service>.md` § 7.6 Transaction Boundaries

Defaults per CLAUDE.md (constructor injection, `@Transactional` REQUIRED, READ_COMMITTED). Per-method overrides require code-or-architect input — flag with `> Confirm: transaction propagation default applied; verify per method`.

### `06-api-contracts.md` § 9.2 Request / Response Shapes

The SDD's "List of APIs" gives method + path + summary. Concrete request / response shapes need either OpenAPI source or architect input — propose a shape per CLAUDE.md REST conventions (records, validation annotations) and flag.

### `12-performance.md` § 15.2 Caching Strategy

The SDD names hot-path concerns; the LLD chooses the cache. From SDD alone, propose a cache only if a SDD performance target requires one (e.g., GET p99 < 50ms with 1000 RPS implies a cache). Otherwise: `> TODO: caching strategy — verify`.

### `12-performance.md` § 15.5 Peak Scenarios

Carry from SDD §14.3 if pinned; otherwise: `> TODO: peak scenarios — verify with SDD §14.3`.

### `14-frontend.md` (if applicable)

The SDD's UI/UX expectations seed the frontend chunk. Concrete component tree, signal/store boundaries, PrimeNG component selections need either existing code or architect input — propose per CLAUDE.md frontend defaults and flag.

---

## Workflow when deriving from SDD

1. **Detect the SDD form** (chunked / combined) per "Detecting SDD input form" above.
2. **Read the SDD in full.** Numeric order for chunks, end-to-end for combined.
3. **Read the BRD if linked** (only for purpose / scope / glossary supplementary content).
4. **Build a unified mental map.** What's the system? What services exist? What patterns are pinned? What are the performance targets?
5. **Walk the field mapping table** above, filling each LLD chunk.
6. **Apply CLAUDE.md design rules** per `pattern-rules.md`. Every applied pattern carries the triggering rule + rationale.
7. **Generate `04-implementation/<service>.md`** per service. This is the heaviest work.
8. **Apply confidence rules** per `confidence-rules.md`. Every inference carries `> Confirm:` (medium) or `> TODO: <best-guess> — verify` (low).
9. **Index every flag** in `15-open-questions.md`.
10. **Write output** per the chosen shape (chunks / combined).
11. **Surface a structured handoff summary**: file paths, chunk count, service count, pattern application count, confidence flag counts (high / medium / low).
