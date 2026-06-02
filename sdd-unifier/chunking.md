# Chunking Strategy

The CHUNKS mode produces multiple `.md` files, one per logical template-section grouping. This file defines the canonical chunk map, when to deviate, and how merge handling works.

The chunk skeletons are embedded in this skill folder under `chunks/` — they are the authoritative source for section structure inside each chunk.

---

## Principles

1. **A chunk is a unit of reading, not a unit of storage.** Each chunk groups sections that a reader would read in one sitting.
2. **Never split by size.** Line count is irrelevant. A 30-line chunk and a 600-line chunk can both be correct.
3. **Related sections stay together.** Executive Summary + Scope + Risks all answer "what are we building and what could go wrong?" — they belong in one chunk (chunk 01).
4. **Per-service detailed specs are split by service.** This is the SDD's biggest variable — split point is `13.2.X` per service. One chunk per service: `10a-service-[slug].md`, `10b-service-[slug].md`, etc.
5. **Every chunk is self-describing.** First lines are an HTML comment block identifying it.

---

## Canonical chunk map

| # | Filename | Embedded skeleton | Content (template sections) | Typical size |
|---|---|---|---|---|
| 00 | `00-cover-and-changelog.md` | `chunks/00-cover-and-changelog.md` | Title block (name, version, status, author, reviewers, approvers, date, related BRD), Changes Log, Table of Contents, Figures index, Tables index. | Small |
| 01 | `01-executive-summary-scope-risks.md` | `chunks/01-executive-summary-scope-risks.md` | §1 Executive Summary, §2 Scope (In/Out), §3 Assumptions, §4 Risks (with likelihood/impact/mitigation/owner), §5 Glossary. | Medium |
| 02 | `02-ecosystem-overview.md` | `chunks/02-ecosystem-overview.md` | §6 Ecosystem Overview — full platform stack table (compute, runtime, mesh, RDBMS, cache, broker, storage, IAM, secrets, gateway, CI/CD, observability, backend, frontend, BI). Plus ecosystem-level rules (timezone, ID strategy, service auth, secrets handling). | Medium |
| 03 | `03-users-and-use-cases.md` | `chunks/03-users-and-use-cases.md` | §7 Actors table, Use Case Diagram (Miro link). | Small |
| 04 | `04-architecture-style-and-diagrams.md` | `chunks/04-architecture-style-and-diagrams.md` | §8.1 Architecture Style (What/Why/How), §8.2 Context Diagram, §8.3 High-Level Architecture Diagram. | Medium |
| 05 | `05-workflows-and-sequences.md` | `chunks/05-workflows-and-sequences.md` | §8.4 Workflow Diagrams (one per critical flow), §8.5 Sequence Diagrams (one per critical interaction). | Medium–Large |
| 06 | `06-principles-and-decisions.md` | `chunks/06-principles-and-decisions.md` | §9 Architecture Principles table, §10 Architectural Decisions (ADR summary table). | Medium |
| 07 | `07-cross-cutting-concerns.md` | `chunks/07-cross-cutting-concerns.md` | §11 Cross-Cutting Concerns defaults: DB Modeling, Multi-Tenancy, Deployment, Observability, Configuration, Security. | Medium |
| 08 | `08-integrations.md` | `chunks/08-integrations.md` | §12 Integrations table — every external integration with protocol, mode, trigger, auth, timeout, rate limit, retries, fallback. | Small–Medium |
| 09 | `09-services-summary.md` | `chunks/09-services-summary.md` | §13.1 Services Decomposition summary table (one row per service). | Small |
| 10a, 10b, … | `10a-service-[slug].md` | `chunks/10a-service-detailed-template.md` | §13.2.X Detailed Service Spec — one chunk per service. Each chunk contains the full per-service block (Boundaries, Input, Business Logic, Output, Integrations, DB Modeling with ERD/Tables/Migrations/Retention/Archival/Encryption, Multi-tenancy specs, API standards + List of APIs, Event Model + Messaging Infra, Constraints, Error Handling, Observability, Developer Notes, Service-Level Diagrams, Compliance, Deployment Strategy, Future Enhancements). | Large each |
| 11 | `11-performance-and-capacity.md` | `chunks/11-performance-and-capacity.md` | §14 Performance & Capacity (Load Estimates, Throughput Targets per service, Peak Scenarios, Stress Testing Strategy). | Medium |
| 12 | `12-environments.md` | `chunks/12-environments.md` | §15 Environments table (Dev, SIT, UAT, Prod) + per-environment specifics. | Small |
| 13 | `13-operations-runbook.md` | `chunks/13-operations-runbook.md` | §16 Operations Runbook (Common Operations procedures, Diagnostics Cheatsheet, On-Call). | Medium |
| 14 | `14-appendix-and-wishlist.md` | `chunks/14-appendix-and-wishlist.md` | §17 Appendix (BRD link, OpenAPI specs, event schemas, ADR repo, threat model, capacity plan, runbooks, diagrams source), §18 Wishlist. | Small |
| 15 | `15-open-items-and-clarifications.md` | `chunks/15-open-items-and-clarifications.md` | **Open Items & Clarifications** — output of the post-generation cleared-context reviewer pass. Architecture-level gaps, missing scenarios, ADR ambiguities. Each item carries options. Generated *after* the body by an independent reviewer; never authored by the same context that wrote the SDD. | Small–Medium |

Total typical chunk count: **15 + N services** (so 17–21 for a typical multi-service system).

`sdd-master.md` (in `chunks/`) is a master index pointing at the chunks. Regenerate it per project.

---

## The 10a chunk pattern (per-service detailed spec)

Section 13.2 of the SDD is the heaviest section — it contains a full detailed spec block for every service in the system. Each spec is roughly 200–400 lines.

In CHUNKS mode, each service gets its own file:

```
10a-service-wallet-core.md
10b-service-payment-processor.md
10c-service-notification-dispatcher.md
...
```

Naming:
- Two-character prefix (`10a`, `10b`, `10c`, …, `10z`, then `10aa`, `10ab` if you have more than 26 services — extremely unlikely).
- Slug in kebab-case, derived from the service name.
- The file `chunks/10a-service-detailed-template.md` is the **template** for any service spec — copy its structure when adding a new service.

Order: services are numbered in the order they appear in the §13.1 Services Decomposition summary table (chunk 09).

---

## Chunk header (mandatory)

Every chunk begins with this HTML comment block:

```markdown
<!--
CHUNK: 10a
TITLE: Service Detailed Spec — Wallet Core
PROJECT: Wallet Management Service
VERSION: 1.0
PART OF: SDD — Wallet Management Service
-->

# 13.2.1 Wallet Core

...
```

Heading levels inside a chunk preserve the template's numbering (`# 13.2.1`, `## Boundaries`, etc.) so merge handling is trivial — no demotion or promotion needed.

---

## When to deviate from the canonical map

Deviate, and note the deviation in the final handoff summary, when:

1. **A template section is genuinely empty** for this project (e.g., a backend-only system has no Frontend Stack row in Ecosystem Overview, or a single-service system has no §13.1 decomposition table). Keep the section, write `Not applicable for this release.`
2. **Workflow / sequence chunk explodes.** If §8.4 + §8.5 together exceed ~600 lines, split: `05a-workflows.md` and `05b-sequences.md`.
3. **A single service is so complex it warrants splitting.** Rare, but if one service's detailed spec exceeds ~800 lines (large state machines, many APIs, many events), split into `10a1-service-foo-data.md` and `10a2-service-foo-events.md`. Note in the handoff summary.
4. **Operations Runbook becomes a living artefact.** If the runbook is under heavy active iteration during incidents, split: `13a-runbook-procedures.md` and `13b-runbook-diagnostics.md`.

Do NOT deviate to merge per-service chunks for the sake of fewer files. Per-service chunks are the most-edited files in an SDD; keeping them separate is the whole point.

---

## Skip rules (sections that are optional per the template)

- **Figures / Tables index** on the cover chunk — include an empty skeleton; populate as Miro diagrams and tables are added.
- **Mermaid alternative blocks** in the diagram sections — keep them in the chunks as a fallback but make clear the Miro board is authoritative. See `miro-diagrams.md`.
- **Compliance sub-section per service** — include the heading; if no specific regulation applies (`GDPR`, `PCI-DSS`, `ISO 27001`), write "Not applicable for this service." Don't omit silently.
- **State machine** under Business Logic — only include if the service is genuinely stateful with named states. Stateless services don't need this sub-section.
- **Event-Driven Architecture sub-section per service** — include the heading; if the service is purely synchronous, write "Not applicable; this service does not produce or consume events."

---

## Merge handling (chunks → combined)

On merge, chunks are concatenated in numeric order: 00, 01, 02, 03, 04, 05, 06, 07, 08, 09, 10a, 10b, 10c, …, 11, 12, 13, 14, 15.

Steps:

1. Strip the `<!-- CHUNK: ... -->` comment from each chunk.
2. Concatenate with a single blank line between chunks.
3. Regenerate the Table of Contents in chunk 00 against the merged heading outline.
4. Regenerate the Figures and Tables indices.
5. Renumber the §13.2.X service blocks if they got out of order (they should match chunk-letter order: 10a → §13.2.1, 10b → §13.2.2, …).
6. Write to `SDD-[ProjectName]-v[X.X]-MERGED.md` alongside the chunks.

Original chunks are kept.

---

## Re-chunk handling (combined → chunks)

When asked to split a combined SDD into chunks:

1. Read the combined file fully.
2. Identify section boundaries by `# 1.`, `# 2.`, … headings.
3. Group sections per the chunk map.
4. **Section 13 needs special handling**: identify each `### 13.2.X` block and split into a separate chunk file `10a-service-[slug].md`, `10b-service-[slug].md`, …
5. For each chunk, prepend the `<!-- CHUNK: ... -->` comment block.
6. Heading levels stay as-is (the template uses absolute numbering like `# 1.`, `## 1.1`, so no demotion is needed).
7. Write each chunk file.
8. Keep the original combined file.
