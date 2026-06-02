# BRD → SDD Derivation

This file defines how to derive a Solution Design Document from a Business Requirements Document. It is the new bit specific to `sdd-unifier` — it does not exist in `brd-unifier`.

The default workflow shape is **SoW → BRD → SDD**, with one SDD per BRD per SoW. This file covers the BRD → SDD step specifically.

---

## Specs-driven steering (read this BEFORE walking the field mapping)

If the BRD contains a `Specs` section (chunked: `12-specs.md`; combined: `# Specs`), it is the highest-fidelity steering signal in the entire BRD. It must be read first, and its four sub-sections steer the rest of the derivation:

| BRD Specs sub-section | SDD destination | How to apply |
|---|---|---|
| **1. Mission** | §1 Executive Summary opening paragraph | The Specs.Mission is constitution-grade and intentionally lossy. Translate it into the SDD's technical opening: "what this system *is* technically" framed by the same core idea. Do not paraphrase verbosely — keep the SDD opening as terse as the Mission. |
| **2. Tech Stack** | §6 Ecosystem Overview (Backend/Frontend/Mobile/Data/Messaging rows) | **Verbatim copy with version pins.** This OVERRIDES CLAUDE.md defaults — if the BRD Specs commits to a stack, that stack is authoritative. CLAUDE.md defaults only fill rows the Specs leaves empty (e.g., service mesh, secrets manager). Note any divergence from CLAUDE.md in the handoff summary. |
| **3. Roadmap** | §13.1 Services Decomposition (phasing column) + §1 Executive Summary "Phased Delivery" note | Each Roadmap phase corresponds to a delivery slice. Add a `Phase` column to the §13.1 services table. Mapping phases to services: a phase typically delivers 1-N services or 1-N service capabilities. Cross-check the FR-IDs in the Roadmap against the FR-IDs each service owns (per §13.1). Note phase ordering in the Executive Summary so the architect designs deployable slices, not big-bang launches. |
| **4. Project Type** | §1 Executive Summary metadata line + behaviour gate for the entire SDD | **Greenfield:** standard SDD generation; all per-service detailed specs scaffolded fresh; ADRs flagged as "to be decided." **Brownfield:** add a `## Existing System Context` sub-section under §1 listing the pre-existing codebase reference; cross-cutting concerns in §11 must be explicitly checked against the existing codebase ("inherit" / "override" / "new"); per-service §13.2.X specs are flagged as "extending existing service `<name>`" with a confirmation prompt. |

**If the BRD has no Specs section** (legacy BRD, or pre-Specs version), proceed with the field mapping below treating the BRD body as the only source. Note "no Specs section present" in the handoff summary so the architect knows to back-fill it via `brd-unifier` if downstream consumption (speckit `/constitution`) is planned.

**Specs override resolution (when fields conflict):**

- Specs.Tech Stack vs §10 BRD "Technical Implementation Expectations": Specs wins (it is the authoritative summary).
- Specs.Tech Stack vs CLAUDE.md defaults: Specs wins.
- Specs.Roadmap vs FR tier table: cross-validate. Mismatch is a flag, not an override — `[NEEDS CLARIFICATION: BRD Specs.Roadmap phases do not match FR tier groupings; confirm phase-to-tier mapping.]`
- Specs.Project Type vs scope narrative: Project Type wins.

---

## What derivation is, and is not

**Derivation IS:**

- Reading the BRD in full.
- Auto-filling SDD sections that have direct BRD analogues (Glossary, Personas → Actors, Integrations, Scope, NFRs context).
- Producing an SDD skeleton with all section headings present, in template order.
- Flagging SDD-only sections (Architecture Style, ADRs, Cross-cutting overrides per service, Operations Runbook procedures) with `[NEEDS CLARIFICATION: ...]` markers naming the specific decision needed.
- Falling back to user CLAUDE.md defaults (Java 21, Spring Boot 3.5+, PostgreSQL 17+, UUIDv7, Kafka, Keycloak, microservices-first, Angular 17+ standalone) for the Ecosystem Overview when the BRD doesn't override.

**Derivation IS NOT:**

- Inventing technical decisions the architect hasn't made.
- Producing per-service detailed specs from FRs alone. FRs describe behaviour; per-service detailed specs describe implementation choices (DB schema, API list, event model). The leap requires architect input.
- Fabricating performance numbers from NFR text. NFR-stated availability and latency targets carry over; the per-service throughput targets in §14.2 still need architect input.
- A finished SDD. The output is an architect-ready skeleton.

---

## Detecting BRD input form

The BRD can arrive in two forms:

### Chunked form (brd-unifier output)

The user points the skill at a folder. Recognise it by:

- Folder path matches `brd-*/` (kebab-case slug after `brd-`).
- Contains 11+ files with names like `00-cover-and-changelog.md`, `01-executive-summary-and-context.md`, `02-glossary-assumptions-facts.md`, `03-definitions-and-domain-concepts.md`, `04-scope-and-personas.md`, `05-fr-overview.md`, `06a-fr-*.md` (one or more), `07-integrations.md`, `08-reporting-and-analytics.md`, `09-nfrs.md`, `10-summary-uiux-tech.md`, `11-appendix-and-wishlist.md`.
- Each file begins with `<!-- CHUNK: NN ... PART OF: BRD — ... -->`.

**Reading order:** numeric (00, 01, 02, 03, 03a, 03b, 04, 05, 06a, 06b, 06c, 07, 08, 09, 10, 11). Multi-letter chunks (`06a`, `06b`) are read alphabetically within their numeric prefix.

**Treat as one logical BRD.** Do not produce one SDD per chunk. Read all chunks first, build a unified mental map, then write one SDD.

### Combined form (single BRD .md file)

The user points the skill at a single `.md` file. Recognise it by:

- Filename starts with `BRD-` (e.g., `BRD-WalletManagement-v1.0.md`) OR the file's section headings match the BRD template (5+ of: Executive Summary, Background, Business Objectives, Glossary, Assumptions / Constraints, Facts, Challenges, Dependencies, Definitions & Important Details, Project Scope, Personas, Functional Requirements, Integrations, Reporting / Analytics, Non-Functional Requirements, Summary, UI/UX Expectations, Technical Implementation Expectations).

**Read in full** before writing anything.

### When both forms exist for the same project

Prefer the chunked form (it is the editable source-of-truth in the user's workflow). If both are clearly the same BRD at the same version, use the chunked one. If the versions differ, use the higher version and note the discrepancy in the handoff.

---

## Field mapping table

This is the authoritative mapping. Each row says: BRD source section → SDD destination section + a note on transformation.

| BRD section | SDD destination | Transformation note |
|---|---|---|
| Cover & Changelog | §0 Cover & Changelog (regenerated) | New SDD has its own version (v1.0). Add a "Related BRD" line pointing at the source BRD's filename / chunk folder. New Changes Log entry: "Initial SDD draft, derived from BRD v[X.X] via sdd-unifier." |
| Executive Summary (BRD) | §1 Executive Summary (SDD) | Recast as **technical** summary. Drop business framing; lead with what the system *is technically*, the architecture style at a glance (placeholder if undecided), and the key technology pillars. The BRD's exec summary becomes input, not output. |
| Background and Context / Problem Statement | §1 Executive Summary context paragraph | Optional — only carry if it informs technical decisions. Otherwise leave to the Related-BRD link in the cover. |
| Business Objectives | §1 Executive Summary "Key technical bets and trade-offs" | Translate each business objective into a technical implication (e.g., "300+ tenants by Y2" → "horizontal scalability is a primary NFR; multi-tenancy strategy is a critical decision"). |
| Glossary | §5 Glossary | **Verbatim copy.** Add SDD-specific terms (architecture style names, infra components) as new rows. |
| Assumptions / Constraints | §3 Assumptions | Verbatim copy. Some entries may also surface in §4 Risks if they're more accurately described as risks. |
| Facts | Distributed: §1 Executive Summary, §6 Ecosystem Overview, §8.1 Architecture Style.Why | Facts often carry technical implications. Classify each into the section it most informs. |
| Challenges | §4 Risks | Convert each challenge to a risk row: assign Likelihood (L/M/H), Impact (L/M/H), Mitigation (paraphrase from BRD if stated; otherwise `[NEEDS CLARIFICATION: mitigation strategy for R-NN]`), Owner (`[NEEDS CLARIFICATION: risk owner]`). |
| Dependencies | §12 Integrations + §4 Risks (for hard dependencies) | Each external dependency becomes an Integration row. If the BRD marked a dependency as Hard with a real concern, also add it to Risks. |
| Definitions & Important Details | Distributed: §6 Ecosystem Overview hints, §13 Services per-service Business Logic & DB Modeling cues | This is the BRD's deep-dive section and the highest-value source for derivation. Domain concepts often map to bounded contexts (services). State machines map to per-service `Business Logic → State machine`. Data lifecycles inform per-service DB Modeling. |
| Project Scope (In Scope / Out of Scope) | §2 Scope (In / Out) | Verbatim copy, with a one-line preamble noting "Solution-design-level scope" framing. Some BRD scope items may be re-grouped under technical headings. |
| Personas / Actors | §7.1 Actors | Each persona becomes an Actor row. Add `Type (Human / System)` classification — usually all BRD personas are Human; supplement with System actors derived from Integrations (each external system that calls in is also a System actor). |
| Functional Requirements (overview narrative) | §7.2 Use Case Diagram input | The BRD's FR overview narrative seeds the use case diagram. Each FR (or FR tier) becomes a candidate Use Case (UC-NN). |
| Functional Requirements (tier table) | §13.1 Services Decomposition (often) | If the BRD's FR tiers map cleanly to services (one tier per service), seed §13.1 with one row per tier. If not, flag: `[NEEDS CLARIFICATION: map FR tiers to services — current FR tier table doesn't match a 1:1 service decomposition.]` |
| Functional Requirements (detailed FR blocks) | Distributed: §13.2.X per-service Business Logic, API list, Event Model | Each FR's `How` informs the Business Logic section of the service that owns it. The FR's Acceptance Criteria translate to API contract notes (request/response shapes) and possibly event names. **This is the heaviest derivation work** and produces partial content + many flags. |
| Integrations (BRD) | §12 Integrations (SDD) | Verbatim copy. Enrich each row with `Timeout`, `Rate Limit`, `Retries & Backoff`, `Fallback` columns — these are SDD-only fields not in the BRD. Each becomes `[NEEDS CLARIFICATION: ...]` if the BRD doesn't state them. |
| Reporting / Analytics | Distributed: §13.X per-service Output (where the report is served from), §14 Performance & Capacity context | Each report becomes an Output row in the service that produces it. Reporting volumes inform §14 load estimates. |
| Non-Functional Requirements | §14 Performance & Capacity (partial) + §11 Cross-Cutting Concerns context | NFRs about performance / availability / latency feed §14. NFRs about security / observability / multi-tenancy inform §11 defaults. NFRs about specific concrete targets carry verbatim. |
| Summary (BRD) | §1 Executive Summary closing paragraph (optional) | Often redundant; only carry if it adds a technical angle. |
| UI/UX Expectations | §6 Ecosystem Overview "Frontend Stack" row + §11 Cross-Cutting Concerns notes (UX standards) | Frontend tech (Angular, Tailwind, PrimeNG) → Ecosystem Overview. UX standards (validation patterns, error envelopes) → §11 Cross-Cutting (UX standards aren't a separate SDD section but inform Cross-Cutting where they touch backend, e.g., error envelope schema). |
| Technical Implementation Expectations | §6 Ecosystem Overview (verbatim) | This BRD section is the highest-fidelity input for the Ecosystem Overview. Carry every named technology, version pin, and architectural rule across. |
| Appendix | §17 Appendix | Carry references; add SDD-specific rows (OpenAPI specs path, event schemas, ADR repo, threat model, capacity plan). |
| Wishlist | §18 Wishlist | Verbatim copy, retitled "Future architectural enhancements". |

---

## SDD-only sections (always need architect input)

These sections have no BRD analogue and always produce `[NEEDS CLARIFICATION: ...]` markers when deriving from a BRD alone:

### §6 Ecosystem Overview (partial — see CLAUDE.md fallback)

The BRD's Technical Implementation Expectations may pre-fill some rows. Rows the BRD typically doesn't have:

- Service Mesh / Ingress
- Caching tier (the BRD might mention "Redis" but rarely commits to topology)
- Object Storage
- Secrets Management specifics
- API Gateway specifics
- CI/CD platform
- Observability stack specifics

For each missing row, fall back to user CLAUDE.md defaults if applicable, otherwise: `[NEEDS CLARIFICATION: <component> + version + topology]`.

### §8.1 Architecture Style

`[NEEDS CLARIFICATION: name the architecture style — microservices with event-driven async backbone (CLAUDE.md default), modular monolith, layered, hexagonal. Confirm or override.]`

### §8.2 / §8.3 / §8.4 / §8.5 Diagrams

The BRD's Summarized Workflow may seed §8.4 Workflow Diagrams. Otherwise:

- §8.2 Context Diagram: derivable from Integrations + Personas → flag for verification on Miro.
- §8.3 High-Level Architecture: needs architect input. `[NEEDS CLARIFICATION: layer composition, primary components, async backbone topology.]`
- §8.5 Sequence Diagrams: needs architect input per critical interaction. List each candidate sequence as `[NEEDS CLARIFICATION: sequence diagram for <flow name> — see BRD FR-NN.]`

### §10 Architectural Decisions (ADRs)

Always `[NEEDS CLARIFICATION: ...]` per intended ADR. List candidate decisions:

- Choice of architecture style (linked to §8.1).
- Choice of message broker (Kafka vs SNS+SQS vs RabbitMQ).
- Choice of multi-tenancy strategy.
- Choice of API style (REST vs gRPC vs GraphQL) per service.
- Choice of synchronous vs event-driven for cross-service calls.

### §11 Cross-Cutting Concerns (defaults)

Many of these have CLAUDE.md defaults — apply them and note "default per CLAUDE.md". For per-service overrides under §13.2.X:

`[NEEDS CLARIFICATION: any service-specific override of the default DB engine / multi-tenancy strategy / deployment strategy / observability instrumentation.]`

### §13.2.X per-service detailed specs

The biggest gap. From the BRD, for each service identified in §13.1:

- **Boundaries** — derive from FR ownership; flag for confirmation.
- **Input** — derive from Integrations and FRs (REST endpoints, events consumed); always partial.
- **Business Logic** — derive from FR `How` blocks; usually substantial but lacks state-machine detail. Flag state machines explicitly.
- **Output** — derive from FRs and Reporting; partial.
- **Integrations (per service)** — derive from system-level Integrations table; flag direction and failure handling.
- **DB Modeling** — `[NEEDS CLARIFICATION: ERD, table list, column types, constraints, indexes, retention. The BRD describes data conceptually but does not commit to a relational schema.]`
- **API Standards + List of APIs** — `[NEEDS CLARIFICATION: full API list in OpenAPI form. The BRD's FRs imply APIs but do not specify request/response shapes.]`
- **Event Model + Messaging Infra** — `[NEEDS CLARIFICATION: event names, producers, consumers, schema, delivery guarantee. The BRD's FRs imply events but do not commit to topic / payload structure.]`
- **Constraints** — derive from BRD Assumptions + FR Constraints.
- **Error Handling** — `[NEEDS CLARIFICATION: error envelope schema, validation/domain/auth/server error policies, async consumer poison-message strategy.]`
- **Observability** — apply CLAUDE.md defaults; flag service-specific metrics.
- **Compliance** — derive from BRD Challenges and any explicit compliance language; flag GDPR/PCI/ISO applicability per service.
- **Deployment Strategy** — apply CLAUDE.md defaults; flag service-specific overrides.
- **Future Enhancements** — carry from BRD's per-FR Future Enhancements where the FR maps to this service.

### §14 Performance & Capacity

- §14.1 Load Estimates: derivable from BRD NFRs + Reporting volumes; partial. Flag missing year-over-year projections.
- §14.2 Throughput Targets: `[NEEDS CLARIFICATION: per-service sustained RPS, peak RPS, p50/p95/p99 latency targets. NFRs alone don't give per-service granularity.]`
- §14.3 Peak Scenarios: `[NEEDS CLARIFICATION: peak scenario triggers + multipliers + duration. Likely linked to BRD Business Objectives but not directly stated.]`
- §14.4 Stress Testing Strategy: `[NEEDS CLARIFICATION: tooling, environments, scenario set, acceptance criteria, cadence.]`

### §15 Environments

`[NEEDS CLARIFICATION: per-environment data refresh policy, sizing rules, feature flag defaults, DNS naming, secrets strategy. The BRD does not specify the environment model.]`

### §16 Operations Runbook

Always entirely `[NEEDS CLARIFICATION: ...]`. The BRD never specifies operational procedures. Produce the section heading and the standard sub-sections (Restart, Clear Cache, Replay DLQ, Rotate Secrets, DB Failover, Tenant Incident Response) as empty templates with the marker.

---

## Workflow when deriving from BRD

1. **Detect the BRD form** (chunked / combined) per "Detecting BRD input form" above.
2. **Read the BRD in full.** For chunked: read all chunks in numeric order. For combined: read end-to-end.
3. **Build a unified mental map.** What's the system? What does it do? What are the bounded contexts (likely services)? What technologies are pinned? What NFRs apply?
4. **Identify the service decomposition.** This is the most-important inferred decision. From FR tiers / themes, propose a service list. If the mapping is clean (one tier ≈ one service), use it. If not, flag and ask once: "Proposed service decomposition: [list]. Confirm or revise before I generate per-service chunks?"
5. **Apply CLAUDE.md defaults** to Ecosystem Overview rows the BRD doesn't override.
6. **Walk the field mapping table** above, filling each SDD section.
7. **Produce SDD-only section skeletons** with `[NEEDS CLARIFICATION: ...]` markers per the "SDD-only sections" list above.
8. **Generate Miro figures** for derivable diagrams (Context, parts of HL Architecture, Workflow per critical FR flow). Flag undecided diagrams as `[NEEDS MIRO: ...]` placeholders.
9. **Write output** per the chosen mode (chunks / combined).
10. **Surface a structured handoff summary**: file paths, chunk count, Miro board URL, count of `[NEEDS CLARIFICATION: ...]` markers grouped by section, list of service-decomposition assumptions made, list of CLAUDE.md defaults applied.

---

## Service decomposition heuristics

The BRD almost never names "services" explicitly. Inferring the service list is the highest-value derivation step.

**Strong signals (a service likely exists):**

- An FR tier with a clear bounded-context name ("Wallet Management", "Notification Dispatch", "Billing").
- A domain concept in §3 that owns its own data lifecycle.
- An external integration that owns a clear surface (e.g., "SMSC integration" → likely a dedicated dispatcher service).
- An NFR that targets one specific area (e.g., "p99 < 50ms for ledger writes" → ledger is its own service).

**Weak signals (might not be a service):**

- A single FR (one feature ≠ one service).
- A domain concept that's a value object (no lifecycle, no ownership).
- A reporting requirement (often consumed-from rather than owned-by a service).

**When unclear:** propose a decomposition based on bounded contexts in §3 Definitions & Important Details, and explicitly flag it as a derivation assumption in the handoff: "Proposed services: [list]. This was inferred from §3 domain concepts and FR tier groupings. Confirm or revise."

Default to **fewer**, larger services rather than premature micro-decomposition. Per the user's CLAUDE.md: "Reach for a modular monolith only when the bounded context is genuinely small and stable" — but also "default to microservices for new services". The tension is resolved by starting with one service per stable bounded context, not one service per FR.

---

## Concrete example

Given a brd-unifier output at `./brd-wallet-management/` containing:

- 11 chunks following the canonical BRD chunk map.
- `02-glossary-assumptions-facts.md` defining "Wallet", "Reseller", "Tenant", "Ledger".
- `03-definitions-and-domain-concepts.md` describing wallet lifecycle, reseller hierarchy, ledger anatomy.
- `05-fr-overview.md` listing T1 (Core Wallet — FR-01..04), T2 (Reseller Mgmt — FR-05..07), T3 (Reporting — FR-08..09).
- `06a-fr-core-wallet.md`, `06b-fr-reseller-mgmt.md`, `06c-fr-reporting.md` with detailed FRs.
- `07-integrations.md` listing payment gateway, notification service, audit log sink.
- `09-nfrs.md` with availability, latency, multi-tenancy targets.
- `10-summary-uiux-tech.md` pinning Java 21, Spring Boot 3.5+, PostgreSQL 17+, Angular 17+.

Derived SDD service decomposition (proposed, flagged for confirmation):

- **wallet-core** (owns Wallet, Ledger; absorbs T1).
- **reseller-management** (owns Reseller hierarchy; absorbs T2).
- **reporting-aggregator** (owns reporting projections; absorbs T3 — likely a read-side service consuming events from the other two).

§13.2.1 wallet-core gets:
- Boundaries (derived from T1 FRs).
- Business Logic (derived from FR `How` blocks for FR-01..04).
- Constraints (derived from BRD Assumptions + per-FR Constraints).
- DB Modeling: `[NEEDS CLARIFICATION: full ERD and tables for wallet, ledger entry, reseller-association.]`
- API list: `[NEEDS CLARIFICATION: REST endpoints with OpenAPI shapes — derived from FR-01..04 acceptance criteria but not concretely specified.]`
- Event Model: `[NEEDS CLARIFICATION: events emitted by wallet-core (e.g., wallet.created, ledger.entry.recorded). The BRD implies async behaviour but does not name events.]`

…and so on for each service.
