<!--
TYPE: Master Index
PROJECT: [Project Name]
VERSION: [X.X]
PART OF: SDD - [Project Name]
PURPOSE: Navigation graph for AI agents and human readers. Each node links to a self-describing chunk. Load this file first, then follow links to the chunks you need.
VERSIONING: All chunks share the SDD version number. When any chunk is updated, bump the SDD version in this master and in the updated chunk(s).
MAINTENANCE: When adding or removing chunks (especially 10x service chunks), update the tables below, the dependency graph, and the reading-order table.
-->

# SDD Master Index - [Project Name]

> **How to use:** This file is the entry point for the Solution Design Document. Each section below maps to a chunk file containing the full template content. Links are relative to this directory. An AI agent should load this file first, identify which chunk(s) are relevant to the task, and navigate to only those chunks.

> **Related BRD:** See [../brd-[project-slug]/brd-master.md](../brd-[project-slug]/brd-master.md) for the corresponding Business Requirements Document.

> **Specs note:** The constitution-grade `Specs` chunk (Mission, Tech Stack, Roadmap, Project Type) is owned by `lld-unifier` and lives with the LLD (`../lld-[project-slug]/17-specs.md`), synthesised from this SDD's body. The SDD carries no Specs chunk.

---

## Document Metadata & History

| Section | Chunk |
|---------|-------|
| Title block, Version, Author, Reviewers, Approvers, Status | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |
| Related BRD reference | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |
| Changes Log | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |
| Table of Contents, Figures & Tables indices | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |

## Strategic Context & Risk

| Section | Chunk |
|---------|-------|
| 1. Executive Summary (technical) | [01-executive-summary-scope-risks.md](./01-executive-summary-scope-risks.md) |
| 2. Scope (In / Out) | [01-executive-summary-scope-risks.md](./01-executive-summary-scope-risks.md) |
| 3. Assumptions | [01-executive-summary-scope-risks.md](./01-executive-summary-scope-risks.md) |
| 4. Risks (likelihood, impact, mitigation) | [01-executive-summary-scope-risks.md](./01-executive-summary-scope-risks.md) |
| 5. Glossary | [01-executive-summary-scope-risks.md](./01-executive-summary-scope-risks.md) |

## Technology Foundation

| Section | Chunk | Beneficial/ used for |
|---------|-------|-------|
| 6. Ecosystem Overview (full tech stack table, incl. Architecture Doctrine: EDA + DDD + Hexagonal defaults) | [02-ecosystem-overview.md](./02-ecosystem-overview.md) | Implementation Constitution & Planned specs |
| Ecosystem-level rules | [02-ecosystem-overview.md](./02-ecosystem-overview.md) | Implementation Constitution & Planned specs |

## Actors & Use Cases

| Section | Chunk |
|---------|-------|
| 7.1 Actors | [03-users-and-use-cases.md](./03-users-and-use-cases.md) |
| 7.2 Use Case Diagram | [03-users-and-use-cases.md](./03-users-and-use-cases.md) |

## System Architecture

| Section | Chunk |
|---------|-------|
| 8.1 Architecture Style (What / Why / How) | [04-architecture-style-and-diagrams.md](./04-architecture-style-and-diagrams.md) |
| 8.2 Context Diagram | [04-architecture-style-and-diagrams.md](./04-architecture-style-and-diagrams.md) |
| 8.3 High-Level Architecture Diagram | [04-architecture-style-and-diagrams.md](./04-architecture-style-and-diagrams.md) |
| 8.4 Workflow Diagrams | [05-workflows-and-sequences.md](./05-workflows-and-sequences.md) |
| 8.5 Sequence Diagrams | [05-workflows-and-sequences.md](./05-workflows-and-sequences.md) |

## Governance & Decisions

| Section | Chunk |
|---------|-------|
| 9. Architecture Principles (AP-01 to AP-12) | [06-principles-and-decisions.md](./06-principles-and-decisions.md) |
| 10. Architectural Decisions (ADRs) | [06-principles-and-decisions.md](./06-principles-and-decisions.md) |

## Cross-Cutting Concerns

| Section | Chunk |
|---------|-------|
| 11.1 DB Modeling defaults | [07-cross-cutting-concerns.md](./07-cross-cutting-concerns.md) |
| 11.2 Multi-Tenancy defaults | [07-cross-cutting-concerns.md](./07-cross-cutting-concerns.md) |
| 11.3 Deployment defaults | [07-cross-cutting-concerns.md](./07-cross-cutting-concerns.md) |
| 11.4 Observability defaults | [07-cross-cutting-concerns.md](./07-cross-cutting-concerns.md) |
| 11.5 Configuration Management defaults | [07-cross-cutting-concerns.md](./07-cross-cutting-concerns.md) |
| 11.6 Security defaults | [07-cross-cutting-concerns.md](./07-cross-cutting-concerns.md) |

## Integrations

| Section | Chunk |
|---------|-------|
| 12. Integrations (protocol, auth, retries, rate limits) | [08-integrations.md](./08-integrations.md) |

## Services & Platform Contracts

| Section | Chunk |
|---------|-------|
| 13. Services Decomposition (summary table) | [09-services-summary.md](./09-services-summary.md) |
| 14. Centralized Event Hub (Platform Event Catalog & Payload Contracts) | [10-events-hub.md](./10-events-hub.md) |
| 15.1 Detailed Service Spec (full template) | [10a-service-detailed-template.md](./10a-service-detailed-template.md) |
| 16. Centralized User Roles & Authorities (platform-wide) | [11-centralized-user-roles.md](./11-centralized-user-roles.md) |

<!-- When a real SDD has multiple services, add rows here:
| 15.2 [Service 2 Name] - Detailed Spec | [10b-service-[slug].md](./10b-service-[slug].md) |
| 15.3 [Service 3 Name] - Detailed Spec | [10c-service-[slug].md](./10c-service-[slug].md) |
-->

> **Contract consistency:** chunk 10 (event hub) is the platform contract registry - topic names, event names, and payload contracts in every `10x` chunk must match it verbatim; chunk 11 is the same for roles and permission tokens. Divergences are flagged in the registries' consistency/drift sections, never silently reconciled.

### Service Spec Sub-Sections (within each 10x chunk)

Each `10x` service chunk contains these sub-sections in order:

| Sub-Section | Description |
|-------------|-------------|
| What | Service definition & bounded context |
| Boundaries | Owns / does not own / upstream / downstream |
| Input | Inbound triggers (REST, events, schedules) |
| Business Logic | Core logic + state machines |
| Output | Outbound responses and events |
| Integrations | Per-service integration details |
| DB Modeling | ERD, tables, retention, archival, encryption |
| Multi-Tenancy Specifications | Overrides to platform defaults |
| API Standards | Style, versioning, auth, idempotency, API list |
| Event-Driven Architecture | Published + consumed events (must match chunk 10), messaging infra, DLQ |
| Constraints | Service-specific constraints |
| Error Handling | Sync + async error strategies |
| Observability & Monitoring | Logging, metrics, tracing |
| Developer Notes | Patterns, anti-patterns, test strategy |
| Service-Level Diagrams | Flow charts, sequence diagrams (inline Mermaid) |
| Compliance | GDPR, PCI-DSS, ISO, local regs |
| Deployment Strategy | Replicas, strategy, health, rollback |
| Future Enhancements | Known gaps and planned improvements |

## Performance & Operations

| Section | Chunk |
|---------|-------|
| 17.1 Load Estimates | [12-performance-and-capacity.md](./12-performance-and-capacity.md) |
| 17.2 Throughput Targets | [12-performance-and-capacity.md](./12-performance-and-capacity.md) |
| 17.3 Peak Scenarios | [12-performance-and-capacity.md](./12-performance-and-capacity.md) |
| 17.4 Stress Testing Strategy | [12-performance-and-capacity.md](./12-performance-and-capacity.md) |
| 18. Environments (Dev/SIT/UAT/Prod) | [13-environments.md](./13-environments.md) |
| 19.1 Common Operations (runbook procedures) | [14-operations-runbook.md](./14-operations-runbook.md) |
| 19.2 Diagnostics Cheatsheet | [14-operations-runbook.md](./14-operations-runbook.md) |
| 19.3 On-Call | [14-operations-runbook.md](./14-operations-runbook.md) |

## Appendices & End-to-End View

| Section | Chunk |
|---------|-------|
| 20. Appendix (references, specs, schemas) | [15-appendix-and-wishlist.md](./15-appendix-and-wishlist.md) |
| 21. Wishlist (platform-level future enhancements) | [15-appendix-and-wishlist.md](./15-appendix-and-wishlist.md) |
| 22. End-to-End System Design (services · topics · producers · consumers) | [16-e2e-system-design.md](./16-e2e-system-design.md) |

> Chunk 16 is authored LAST among body chunks: it consolidates the final reconciled state of chunks 09, 10, 10x, and 11 into one self-contained system map.

## Review Output

| Section | Chunk |
|---------|-------|
| 23. Open Items & Clarifications | [17-open-items-and-clarifications.md](./17-open-items-and-clarifications.md) |

> Generated *after* the main SDD body by a cleared-context reviewer. Captures architecture-level gaps, missing scenarios, ADR ambiguities, and cross-chunk contract mismatches. Every item carries a **Recommended Answer** with the **Why** behind it (evidence + tradeoff), ready to apply; the skill walks the user through each item for acceptance, then reflects accepted answers into the body and logs them in the Resolution Log.

---

## Chunk Dependency Graph

```
sdd-master.md (you are here)
|
+-- 00-cover-and-changelog.md ........... metadata, version history
+-- 01-executive-summary-scope-risks.md . why + what + boundaries + risks + glossary
+-- 02-ecosystem-overview.md ............ tech stack + architecture doctrine (single source of truth)
+-- 03-users-and-use-cases.md ........... actors + use case diagram
+-- 04-architecture-style-and-diagrams.md architecture style + context + HLA
+-- 05-workflows-and-sequences.md ....... end-to-end flows + sequence diagrams
+-- 06-principles-and-decisions.md ...... governance: principles + ADRs
+-- 07-cross-cutting-concerns.md ........ platform defaults (DB, tenancy, deploy, observability, config)
+-- 08-integrations.md .................. external system connections
+-- 09-services-summary.md .............. decomposition overview table
|   +-- 10-events-hub.md ................ platform event catalog + payload contracts (contract registry)
|   +-- 10a-service-detailed-template.md  full spec for service 1 (events must match chunk 10)
|   +-- [10b, 10c, ...] ................. one chunk per additional service
|   +-- 11-centralized-user-roles.md .... platform-wide roles & authorities catalogue
+-- 12-performance-and-capacity.md ...... load, throughput, peaks, stress testing
+-- 13-environments.md .................. Dev / SIT / UAT / Prod
+-- 14-operations-runbook.md ............ procedures, diagnostics, on-call
+-- 15-appendix-and-wishlist.md ......... references + future platform enhancements
+-- 16-e2e-system-design.md ............. end-to-end system map (authored last; consolidates 09/10/10x/11)
+-- 17-open-items-and-clarifications.md . reviewer findings with recommended answers (post-generation)
```

### Reading Order by Task

| Agent Task | Start With | Then |
|------------|-----------|------|
| Understand the system | 16 (e2e) | 01, 04, 02 |
| Design a new service | 07, 02 | 10 (event contracts), 10a (copy as template), 09 |
| Review architecture | 04, 06 | 05, 07, 16 |
| Add an integration | 08 | 10a (service integrations sub-section) |
| Define DB schema | 07 (defaults) | 10a (DB Modeling sub-section) |
| Add / change an event | 10 (registry first) | the producing + consuming 10x chunks, then 16 |
| Define roles / permissions | 11 | 03, the affected 10x chunks |
| Plan capacity / NFRs | 12 | 13, 01 (risks) |
| Write runbook procedures | 14 | 02 (ecosystem), 07 (observability) |
| Audit completeness | 00 (ToC) | all chunks sequentially |
| Check cross-cutting standards | 07 | 06 (principles), 02 (ecosystem) |
| Check contract consistency | 10 §14.8, 11 §16.12 | every 10x Event Model, 16 |
| Map BRD use cases to services | [../brd-[project-slug]/05-user-journeys-overview.md](../brd-[project-slug]/05-user-journeys-overview.md) | 09, then 10a+ |
| Feed speckit `/constitution` | ../lld-[project-slug]/17-specs.md (LLD-owned) | 02, 09 |
| Steer `lld-unifier` (tech choices, contracts) | 02, 10 | 09, 11 |
| Triage reviewer findings | 17 | the chunk(s) referenced by each open item |

### Cross-Document Navigation (BRD <-> SDD)

| BRD Chunk | Related SDD Chunk(s) | Relationship |
|-----------|---------------------|--------------|
| [brd/01 - Executive Summary](../brd-[project-slug]/01-executive-summary-and-context.md) | [sdd/01 - Executive Summary](./01-executive-summary-scope-risks.md) | BRD states business problem; SDD states technical solution |
| [brd/04 - Scope & Personas](../brd-[project-slug]/04-scope-and-personas.md) | [sdd/03 - Users & Use Cases](./03-users-and-use-cases.md) | BRD personas become SDD actors |
| [brd/05 - User Journeys Overview](../brd-[project-slug]/05-user-journeys-overview.md) | [sdd/09 - Services Summary](./09-services-summary.md) | Use cases map to service responsibilities |
| [brd/06a - Use Cases Detailed](../brd-[project-slug]/06a-use-cases-detailed.md) | [sdd/10a - Service Detailed](./10a-service-detailed-template.md) | UC main/exception flows become service business logic and error handling |
| [brd/07 - Users & Use Cases Matrix](../brd-[project-slug]/07-users-use-cases-matrix.md) | [sdd/11 - Centralized User Roles](./11-centralized-user-roles.md), [sdd/03 - Users & Use Cases](./03-users-and-use-cases.md) | Matrix drives the platform role catalogue and per-service authorization rules |
| [brd/08 - Integrations](../brd-[project-slug]/08-integrations.md) | [sdd/08 - Integrations](./08-integrations.md) | BRD names partners and purpose; SDD details protocol/auth/retries |
| [brd/10 - NFRs](../brd-[project-slug]/10-nfrs.md) | [sdd/12 - Performance](./12-performance-and-capacity.md), [sdd/04 - Architecture](./04-architecture-style-and-diagrams.md) | Business expectations are quantified into technical targets and architecture drivers |
| [brd/12 - Appendix (Technical Inputs for the SDD)](../brd-[project-slug]/12-appendix-and-wishlist.md) | [sdd/02 - Ecosystem](./02-ecosystem-overview.md) | Source technical mandates (parked verbatim in the BRD) seed the SDD ecosystem selection and override CLAUDE.md defaults |
