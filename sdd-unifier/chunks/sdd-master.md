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

> **Related BRD:** See [../brd/brd-master.md](../brd/brd-master.md) for the corresponding Business Requirements Document.

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
| 6. Ecosystem Overview (full tech stack table) | [02-ecosystem-overview.md](./02-ecosystem-overview.md) | Implementation Constitution & Planned specs |
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

## Services

| Section | Chunk |
|---------|-------|
| 13.1 Services Decomposition (summary table) | [09-services-summary.md](./09-services-summary.md) |
| 13.2 Detailed Service Spec (full template) | [10a-service-detailed-template.md](./10a-service-detailed-template.md) |

<!-- When a real SDD has multiple services, add rows here:
| 13.2.2 [Service 2 Name] - Detailed Spec | [10b-service-[slug].md](./10b-service-[slug].md) |
| 13.2.3 [Service 3 Name] - Detailed Spec | [10c-service-[slug].md](./10c-service-[slug].md) |
-->

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
| Event-Driven Architecture | Event model, messaging infra, DLQ |
| Constraints | Service-specific constraints |
| Error Handling | Sync + async error strategies |
| Observability & Monitoring | Logging, metrics, tracing |
| Developer Notes | Patterns, anti-patterns, test strategy |
| Service-Level Diagrams | Flow charts, sequence diagrams |
| Compliance | GDPR, PCI-DSS, ISO, local regs |
| Deployment Strategy | Replicas, strategy, health, rollback |
| Future Enhancements | Known gaps and planned improvements |

## Performance & Operations

| Section | Chunk |
|---------|-------|
| 14.1 Load Estimates | [11-performance-and-capacity.md](./11-performance-and-capacity.md) |
| 14.2 Throughput Targets | [11-performance-and-capacity.md](./11-performance-and-capacity.md) |
| 14.3 Peak Scenarios | [11-performance-and-capacity.md](./11-performance-and-capacity.md) |
| 14.4 Stress Testing Strategy | [11-performance-and-capacity.md](./11-performance-and-capacity.md) |
| 15. Environments (Dev/SIT/UAT/Prod) | [12-environments.md](./12-environments.md) |
| 16.1 Common Operations (runbook procedures) | [13-operations-runbook.md](./13-operations-runbook.md) |
| 16.2 Diagnostics Cheatsheet | [13-operations-runbook.md](./13-operations-runbook.md) |
| 16.3 On-Call | [13-operations-runbook.md](./13-operations-runbook.md) |

## Appendices

| Section | Chunk |
|---------|-------|
| 17. Appendix (references, specs, schemas) | [14-appendix-and-wishlist.md](./14-appendix-and-wishlist.md) |
| 18. Wishlist (platform-level future enhancements) | [14-appendix-and-wishlist.md](./14-appendix-and-wishlist.md) |

## Review Output

| Section | Chunk |
|---------|-------|
| Open Items & Clarifications | [15-open-items-and-clarifications.md](./15-open-items-and-clarifications.md) |

> Generated *after* the main SDD by a cleared-context reviewer. Captures architecture-level gaps, missing scenarios, ADR ambiguities. Each item carries options.

---

## Chunk Dependency Graph

```
sdd-master.md (you are here)
|
+-- 00-cover-and-changelog.md ........... metadata, version history
+-- 01-executive-summary-scope-risks.md . why + what + boundaries + risks + glossary
+-- 02-ecosystem-overview.md ............ tech stack (single source of truth)
+-- 03-users-and-use-cases.md ........... actors + use case diagram
+-- 04-architecture-style-and-diagrams.md architecture style + context + HLA
+-- 05-workflows-and-sequences.md ....... end-to-end flows + sequence diagrams
+-- 06-principles-and-decisions.md ...... governance: principles + ADRs
+-- 07-cross-cutting-concerns.md ........ platform defaults (DB, tenancy, deploy, observability, config)
+-- 08-integrations.md .................. external system connections
+-- 09-services-summary.md .............. decomposition overview table
|   +-- 10a-service-detailed-template.md  full spec for service 1
|   +-- [10b, 10c, ...] ................. one chunk per additional service
+-- 11-performance-and-capacity.md ...... load, throughput, peaks, stress testing
+-- 12-environments.md .................. Dev / SIT / UAT / Prod
+-- 13-operations-runbook.md ............ procedures, diagnostics, on-call
+-- 14-appendix-and-wishlist.md ......... references + future platform enhancements
+-- 15-open-items-and-clarifications.md . reviewer findings (post-generation)
```

### Reading Order by Task

| Agent Task | Start With | Then |
|------------|-----------|------|
| Understand the system | 01 | 04, 02 |
| Design a new service | 07, 02 | 10a (copy as template), 09 |
| Review architecture | 04, 06 | 05, 07 |
| Add an integration | 08 | 10a (service integrations sub-section) |
| Define DB schema | 07 (defaults) | 10a (DB Modeling sub-section) |
| Plan capacity / NFRs | 11 | 12, 01 (risks) |
| Write runbook procedures | 13 | 02 (ecosystem), 07 (observability) |
| Audit completeness | 00 (ToC) | all chunks sequentially |
| Check cross-cutting standards | 07 | 06 (principles), 02 (ecosystem) |
| Map BRD FRs to services | [../brd/05-fr-overview.md](../brd/05-fr-overview.md) | 09, then 10a+ |
| Read BRD Specs (constitution-grade summary) | [../brd/12-specs.md](../brd/12-specs.md) | 02 (Ecosystem) for Tech Stack, 09 (Services) for Roadmap phasing |
| Triage reviewer findings | 15 | the chunk(s) referenced by each open item |

### Cross-Document Navigation (BRD <-> SDD)

| BRD Chunk | Related SDD Chunk(s) | Relationship |
|-----------|---------------------|--------------|
| [brd/00a - Specs](../brd/12-specs.md) | [sdd/02 - Ecosystem](./02-ecosystem-overview.md), [sdd/01 - Executive Summary](./01-executive-summary-scope-risks.md), [sdd/09 - Services Summary](./09-services-summary.md) | BRD Specs.Tech Stack steers Ecosystem Overview; Specs.Mission feeds Executive Summary; Specs.Roadmap informs Services phasing; Specs.Project Type (greenfield/brownfield) gates SDD generation behaviour |
| [brd/01 - Executive Summary](../brd/01-executive-summary-and-context.md) | [sdd/01 - Executive Summary](./01-executive-summary-scope-risks.md) | BRD states business problem; SDD states technical solution |
| [brd/04 - Scope & Personas](../brd/04-scope-and-personas.md) | [sdd/03 - Users & Use Cases](./03-users-and-use-cases.md) | BRD personas become SDD actors |
| [brd/05 - FR Overview](../brd/05-fr-overview.md) | [sdd/09 - Services Summary](./09-services-summary.md) | FRs map to service responsibilities |
| [brd/06a - FR Detailed](../brd/06a-fr-detailed.md) | [sdd/10a - Service Detailed](./10a-service-detailed-template.md) | FR logic becomes service business logic |
| [brd/07 - Integrations](../brd/07-integrations.md) | [sdd/08 - Integrations](./08-integrations.md) | BRD lists systems; SDD details protocol/auth/retries |
| [brd/09 - NFRs](../brd/09-nfrs.md) | [sdd/11 - Performance](./11-performance-and-capacity.md) | NFR targets become throughput/latency targets |
| [brd/10 - UI/UX & Tech](../brd/10-summary-uiux-tech.md) | [sdd/02 - Ecosystem](./02-ecosystem-overview.md) | BRD tech expectations become SDD ecosystem |
