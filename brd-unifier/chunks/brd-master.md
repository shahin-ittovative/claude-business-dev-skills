<!--
TYPE: Master Index
PROJECT: [Project Name]
VERSION: [X.X]
PART OF: BRD - [Project Name]
PURPOSE: Navigation graph for AI agents and human readers. Each node links to a self-describing chunk. Load this file first, then follow links to the chunks you need.
VERSIONING: All chunks share the BRD version number. When any chunk is updated, bump the BRD version in this master and in the updated chunk(s).
MAINTENANCE: When adding or removing chunks, update the tables below, the dependency graph, and the reading-order table.
LANGUAGE: The whole BRD is business language only - the WHAT. Technical stack, terminology, and the HOW are owned by the SDD (sdd-unifier).
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
| Glossary (business terms) | [02-glossary-assumptions-facts.md](./02-glossary-assumptions-facts.md) |
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

## User Journeys & Use Cases

| Section | Chunk |
|---------|-------|
| User Journeys (per persona) | [05-user-journeys-overview.md](./05-user-journeys-overview.md) |
| Summarized Workflow | [05-user-journeys-overview.md](./05-user-journeys-overview.md) |
| Use Case Summary Table | [05-user-journeys-overview.md](./05-user-journeys-overview.md) |
| Detailed Use Cases (per persona; Actor/Goal/Why/Main Flow/Alt Flows/Rules/Acceptance/Future/UI) | [06a-use-cases-detailed.md](./06a-use-cases-detailed.md) |
| Users & Use Cases Matrix (who may do what) | [07-users-use-cases-matrix.md](./07-users-use-cases-matrix.md) |

<!-- When a real BRD has multiple personas, add rows here:
| Detailed Use Cases - [Persona 2] | [06b-use-cases-[persona-slug].md](./06b-use-cases-[persona-slug].md) |
| Detailed Use Cases - [Persona 3] | [06c-use-cases-[persona-slug].md](./06c-use-cases-[persona-slug].md) |
-->

## Integrations & Data

| Section | Chunk |
|---------|-------|
| Integrations (business-level) | [08-integrations.md](./08-integrations.md) |
| Reporting / Analytics | [09-reporting-and-analytics.md](./09-reporting-and-analytics.md) |

## Quality & Standards

| Section | Chunk |
|---------|-------|
| Non-Functional Requirements (business language) | [10-nfrs.md](./10-nfrs.md) |
| Summary | [11-summary-and-uiux.md](./11-summary-and-uiux.md) |
| UI/UX Expectations | [11-summary-and-uiux.md](./11-summary-and-uiux.md) |

## Appendices

| Section | Chunk |
|---------|-------|
| Appendix (incl. Technical Inputs for the SDD, parked verbatim) | [12-appendix-and-wishlist.md](./12-appendix-and-wishlist.md) |
| Wishlist | [12-appendix-and-wishlist.md](./12-appendix-and-wishlist.md) |

## Review Output

| Section | Chunk |
|---------|-------|
| Open Items & Clarifications | [13-open-items-and-clarifications.md](./13-open-items-and-clarifications.md) |

> Generated *after* the main BRD by a cleared-context reviewer. Captures gaps, missing scenarios, corner cases the body did not flag inline. Every item carries a **Recommended Answer** with the **Why** behind it (evidence + tradeoff), ready to apply; the skill walks the user through each item for acceptance, then reflects accepted answers into the body and logs them in the Resolution Log.

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
+-- 05-user-journeys-overview.md .... journeys & use case summary
|   +-- 06a-use-cases-detailed.md .... detailed use cases per persona
|   +-- [06b, 06c, ...] .... additional persona chunks
+-- 07-users-use-cases-matrix.md .... who is allowed to do what
+-- 08-integrations.md .... business integrations (what, not how)
+-- 09-reporting-and-analytics.md .... outputs & dashboards
+-- 10-nfrs.md .... business-language quality expectations
+-- 11-summary-and-uiux.md .... summary & UX standards
+-- 12-appendix-and-wishlist.md .... supporting files, parked technical inputs, future ideas
+-- 13-open-items-and-clarifications.md .... reviewer findings with recommended answers
```

### Reading order by task

| Agent Task | Start With | Then |
|------------|-----------|------|
| Understand the project | 01 | 02, 03 |
| Write or review use cases | 05 | 06a+, 07 |
| Check who may do what | 07 | 04, 06a+ |
| Derive an SDD (`sdd-unifier`) | brd-master | all chunks; technical inputs parked in 12 |
| Check scope & boundaries | 04 | 01 |
| Audit completeness | 00 (ToC) | all chunks sequentially |
| Add integrations | 08 | 03, 05 |
| Define NFRs | 10 | 01 |
| Triage reviewer findings | 13 | the chunk(s) referenced by each open item |
