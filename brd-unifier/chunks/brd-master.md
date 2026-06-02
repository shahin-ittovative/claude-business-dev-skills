<!--
TYPE: Master Index
PROJECT: [Project Name]
VERSION: [X.X]
PART OF: BRD - [Project Name]
PURPOSE: Navigation graph for AI agents and human readers. Each node links to a self-describing chunk. Load this file first, then follow links to the chunks you need.
VERSIONING: All chunks share the BRD version number. When any chunk is updated, bump the BRD version in this master and in the updated chunk(s).
MAINTENANCE: When adding or removing chunks, update the tables below, the dependency graph, and the reading-order table.
-->

# BRD Master Index - [Project Name]

> **How to use:** This file is the entry point. Each section below maps to a chunk file containing the full template content. Links are relative to this directory. An AI agent should load this file first, identify which chunk(s) are relevant to the task, and navigate to only those chunks.

---

## Document Metadata & History

| Section | Chunk |
|---------|-------|
| Title block, Version, Author, Date, Status | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |
| Changes Log | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |
| Table of Contents | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |
| Figures Index | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |
| Tables Index | [00-cover-and-changelog.md](./00-cover-and-changelog.md) |

## Strategic Context

| Section | Chunk |
|---------|-------|
| Executive Summary | [01-executive-summary-and-context.md](./01-executive-summary-and-context.md) |
| Background and Context / Problem Statement | [01-executive-summary-and-context.md](./01-executive-summary-and-context.md) |
| Business Objectives | [01-executive-summary-and-context.md](./01-executive-summary-and-context.md) |

## Domain Knowledge

| Section | Chunk |
|---------|-------|
| Glossary | [02-glossary-assumptions-facts.md](./02-glossary-assumptions-facts.md) |
| Assumptions / Constraints | [02-glossary-assumptions-facts.md](./02-glossary-assumptions-facts.md) |
| Facts | [02-glossary-assumptions-facts.md](./02-glossary-assumptions-facts.md) |
| Challenges (incl. evidence table) | [02-glossary-assumptions-facts.md](./02-glossary-assumptions-facts.md) |
| Dependencies | [02-glossary-assumptions-facts.md](./02-glossary-assumptions-facts.md) |
| Definitions & Important Details | [03-definitions-and-domain-concepts.md](./03-definitions-and-domain-concepts.md) |

## Scope & Actors

| Section | Chunk |
|---------|-------|
| Project Scope (In Scope / Out of Scope) | [04-scope-and-personas.md](./04-scope-and-personas.md) |
| Personas / Actors | [04-scope-and-personas.md](./04-scope-and-personas.md) |

## Functional Requirements

| Section | Chunk |
|---------|-------|
| FR Narrative & Summarized Workflow | [05-fr-overview.md](./05-fr-overview.md) |
| High-Level Tier Table | [05-fr-overview.md](./05-fr-overview.md) |
| High-Level FR Summary Table | [05-fr-overview.md](./05-fr-overview.md) |
| Detailed FRs (What/Why/How/Constraints/Future/UI) | [06a-fr-detailed.md](./06a-fr-detailed.md) |

<!-- When a real BRD has multiple FR tiers, add rows here:
| Detailed FRs - [Tier 2 Name] | [06b-fr-[tier-slug].md](./06b-fr-[tier-slug].md) |
| Detailed FRs - [Tier 3 Name] | [06c-fr-[tier-slug].md](./06c-fr-[tier-slug].md) |
-->

## Integrations & Data

| Section | Chunk |
|---------|-------|
| Integrations | [07-integrations.md](./07-integrations.md) |
| Reporting / Analytics | [08-reporting-and-analytics.md](./08-reporting-and-analytics.md) |

## Quality & Standards

| Section | Chunk |
|---------|-------|
| Non-Functional Requirements | [09-nfrs.md](./09-nfrs.md) |
| Summary | [10-summary-uiux-tech.md](./10-summary-uiux-tech.md) |
| UI/UX Expectations | [10-summary-uiux-tech.md](./10-summary-uiux-tech.md) |
| Technical Implementation Expectations | [10-summary-uiux-tech.md](./10-summary-uiux-tech.md) |

## Appendices

| Section | Chunk |
|---------|-------|
| Appendix | [11-appendix-and-wishlist.md](./11-appendix-and-wishlist.md) |
| Wishlist | [11-appendix-and-wishlist.md](./11-appendix-and-wishlist.md) |

## Specs (constitution-grade summary, authored last)

| Section | Chunk |
|---------|-------|
| Mission | [12-specs.md](./12-specs.md) |
| Tech Stack | [12-specs.md](./12-specs.md) |
| Roadmap | [12-specs.md](./12-specs.md) |
| Project Type (greenfield/brownfield) | [12-specs.md](./12-specs.md) |

> Synthesised from the completed body. Mission distils §1 Executive Summary, Tech Stack consolidates §10 Technical Implementation Expectations, Roadmap groups FR IDs into delivery phases, Project Type is asked explicitly. Sub-sections 1, 2, and 3 are direct inputs for speckit `/constitution`. Sub-section 2 (Tech Stack) and sub-section 4 (Project Type) also steer `sdd-unifier` and `lld-unifier`.

## Review Output

| Section | Chunk |
|---------|-------|
| Open Items & Clarifications | [13-open-items-and-clarifications.md](./13-open-items-and-clarifications.md) |

> Generated *after* the main BRD AND the Specs chunk by a cleared-context reviewer. Captures gaps, missing scenarios, corner cases the body did not flag inline, plus any Specs-body mismatches. Each item carries options so stakeholders can choose.

---

## Chunk Dependency Graph

```
brd-master.md (you are here)
|
+-- 00-cover-and-changelog.md .... metadata, version history
+-- 01-executive-summary-and-context.md .... why this project exists
+-- 02-glossary-assumptions-facts.md .... prerequisite knowledge
+-- 03-definitions-and-domain-concepts.md .... deep domain models
+-- 04-scope-and-personas.md .... boundaries & actors
+-- 05-fr-overview.md .... what the system does (summary)
|   +-- 06a-fr-detailed.md .... how each FR works (detail)
|   +-- [06b, 06c, ...] .... additional FR tier chunks
+-- 07-integrations.md .... external system connections
+-- 08-reporting-and-analytics.md .... outputs & dashboards
+-- 09-nfrs.md .... performance, security, scalability
+-- 10-summary-uiux-tech.md .... standards & tech stack
+-- 11-appendix-and-wishlist.md .... supporting files & future ideas
+-- 12-specs.md .... constitution-grade summary (synthesised from body)
+-- 13-open-items-and-clarifications.md .... reviewer findings (post-generation)
```

### Reading order by task

| Agent Task | Start With | Then |
|------------|-----------|------|
| Understand the project | 01 | 02, 03 |
| Feed speckit `/constitution` | 12 | (only this) |
| Steer `sdd-unifier` (tech choices, project type) | 12 | 09, 10 |
| Write or review FRs | 05 | 06a+ |
| Assess technical feasibility | 10 | 09, 07 |
| Check scope & boundaries | 04 | 01 |
| Audit completeness | 00 (ToC) | all chunks sequentially |
| Add integrations | 07 | 03, 05 |
| Define NFRs | 09 | 10, 01 |
| Triage reviewer findings | 13 | the chunk(s) referenced by each open item |
