# [Project Name] - Business Requirements & High-Level Design

**Version:** [X.X]
**Author:** [Author Name]
**Date:** [YYYY-MM-DD]
**Status:** [Draft | In Review | Approved]

---

## Changes Log

| Version | Updated Date | Updated By | Reviewed/Approved By | Update Summary |
|---------|-------------|------------|---------------------|----------------|
| 1.0     | YYYY-MM-DD  | [Name]     |                     | Initial draft. |

---

## Table of Contents

<!-- Auto-generated or manually maintained. Include Figures and Tables indices if the document is large. -->

**Figures**

| Figure # | Title | Section |
|----------|-------|---------|
| Figure 1 | [Description] | [Section] |

**Tables**

| Table # | Title | Section |
|---------|-------|---------|
| Table 1 | Glossary Table | [Section] |

---

# Executive Summary

<!-- 2-4 paragraphs. What is this system? What business problem does it solve? What are its core capabilities at a glance? -->

[System Name] is [brief description of the system and its purpose].

The primary business problem it solves is [problem statement], replacing it with [solution summary].

Core capabilities:
- [Capability 1]
- [Capability 2]
- [Capability 3]

---

# Background and Context / Problem Statement

<!-- Why does this project exist? What is the current state (manual process, legacy system, gap)? Include the current behavior if relevant. -->

[Describe the current state, pain points, and the trigger for this initiative.]

---

# Business Objectives

<!-- Quantified where possible. What does success look like? -->

- [Objective 1 - with measurable KPI if available, e.g., "460x faster processing"]
- [Objective 2]
- [Objective 3]

---

# Glossary

| Term | Definition |
|------|-----------|
| [Term 1] | [Definition] |
| [Term 2] | [Definition] |

---

# Assumptions / Constraints

<!-- Numbered list. Each item should be clear and testable. -->

1. **[Short Label]**; [Detailed assumption or constraint description].
2. **[Short Label]**; [Detailed assumption or constraint description].

---

# Facts

<!-- Known truths that drive design decisions. -->

1. [Fact 1]
2. [Fact 2]

---

# Challenges

<!-- Known risks, data quality issues, integration pain points. Include examples where helpful. -->

1. [Challenge 1]
2. [Challenge 2]

<!-- Optional: Challenge evidence table -->

| Challenge ID | Source(s) & Evidence |
|-------------|---------------------|
| [ID]        | [Example data showing the challenge] |

---

# Dependencies

<!-- External systems, teams, data sources, third-party services. Use "NA" if none. -->

| Dependency | Type | Owner | Status | Notes |
|-----------|------|-------|--------|-------|
| [System/Team] | [Hard/Soft] | [Owner] | [Confirmed/Pending] | [Description] |

---

# Definitions & Important Details

<!-- Deep-dive into domain concepts critical for the team to understand before reading the FRs. -->

## [Domain Concept 1]

### Overview

[Explain the concept, its role in the system, and how it flows through the platform.]

### [Sub-concept / Data Lifecycle]

[Detailed explanation with states, transitions, examples.]

<!-- Include diagrams/figures where applicable -->

### [Sub-concept / Anatomy / Structure]

[Break down the internal structure - e.g., how a record is composed, what entities it maps to.]

## [Domain Concept 2 - e.g., Suppliers, Integrations]

### [Mapping / Configuration Details]

<!-- Optional: Per-variant mapping tables for integration-heavy projects. -->

| Source Field | Internal Field | Notes |
|-------------|---------------|-------|
| [External] | [Internal] | [Transform logic] |

---

# Project Scope

<!-- Short narrative of what the user needs to accomplish end-to-end. -->

[1-2 paragraph narrative of the end-to-end user journey.]

## In Scope

- [Scope item 1]
- [Scope item 2]

## Out of Scope

- [Out-of-scope item 1 - with brief justification or deferral note]
- [Out-of-scope item 2]

---

# Personas / Actors

<!-- Different actors that operate the system. Each persona should describe their role, goals, and access level. -->

| Persona | Role | Key Goals | Access Level |
|---------|------|-----------|-------------|
| [Persona 1] | [Role description] | [What they need to accomplish] | [Admin / Operator / Viewer / etc.] |
| [Persona 2] | [Role description] | [What they need to accomplish] | [Access level] |

---

# Functional Requirements

<!-- Start with a narrative that describes the primary user workflow end-to-end. -->

[1-2 paragraphs describing the primary happy-path workflow from the user's perspective.]

## Summarized Workflow

<!-- Optional: A simplified visual or step-by-step summary of the key workflow(s). Include diagrams. -->

[Workflow description with numbered steps or a diagram reference.]

## High Level

<!-- Tier groupings + summary table of all FRs. -->

| Tier | FRs | Description |
|------|-----|-------------|
| T1 - [Tier Name] | FR-01 - FR-04 | [Tier description] |
| T2 - [Tier Name] | FR-05 - FR-08 | [Tier description] |

<!-- Detailed high-level summary table -->

| FR ID | Requirement | Description |
|-------|------------|-------------|
| **[Category Header]** | | |
| FR-01 | [Short Title] | [1-3 sentence summary] |
| FR-02 | [Short Title] | [1-3 sentence summary] |

## Detailed

All detailed FRs follow this structure:

- **What**: Definition.
- **Why**: Purpose / business justification.
- **How**: Steps / sub-tasks / acceptance criteria.
- **Constraints**: Conditions / pre-conditions / limitations.
- **Acceptance Criteria**: Testable conditions that confirm the FR is complete.
- **Future Enhancements**: Low-complexity items developers can optionally include.
- **UI/UX**: Wireframes or references to approved Figma designs.

---

### FR-01: [Feature Title]

#### What

[Clear, concise definition of what this feature does.]

#### Why

[Business justification - why does the business need this?]

#### How

[Step-by-step description of the feature behavior. Use sub-sections (FR-01.1, FR-01.2) for complex features with multiple sub-flows.]

##### FR-01.1: [Sub-flow Title]

[Detailed steps for this sub-flow.]

##### FR-01.2: [Sub-flow Title]

[Detailed steps for this sub-flow.]

#### Constraints

[List of constraints, pre-conditions, or limitations. Use "Not applicable." if none.]

#### Acceptance Criteria

- [ ] [Testable condition 1 - e.g., "Given X, when Y, then Z"]
- [ ] [Testable condition 2]
- [ ] [Testable condition 3]

#### Future Enhancements

- [Enhancement 1]
- [Enhancement 2]

#### UI/UX

<!-- Reference wireframes, mockups, or Figma links. Use placeholder images during early drafts. -->

[Figure reference or Figma link]

---

<!-- Repeat the FR block (What/Why/How/Constraints/Acceptance Criteria/Future Enhancements/UI/UX) for each functional requirement -->

---

# Integrations

<!-- Systems that will be integrated with & the ways of integration. -->

| System | Direction | Protocol | Data Format | Auth | SLA |
|--------|-----------|----------|-------------|------|-----|
| [External System 1] | [Inbound / Outbound / Bidirectional] | [REST / SFTP / Kafka / Webhook] | [JSON / CSV / XML] | [API Key / OAuth2 / mTLS] | [e.g., 99.9% uptime, <200ms] |
| [External System 2] | [Direction] | [Protocol] | [Format] | [Auth] | [SLA] |

---

# Reporting / Analytics

<!-- Expected reports, table views, charts, dashboards, and data exports. -->

| Report / View | Data Source | Refresh | Audience | Format |
|--------------|------------|---------|----------|--------|
| [Report 1] | [Table / API / Aggregation] | [Real-time / Daily / On-demand] | [Admin / Manager / Tenant] | [Table / Chart / Export CSV] |
| [Report 2] | [Data Source] | [Refresh] | [Audience] | [Format] |

---

# Non-Functional Requirements

| NFR ID | Category | Requirement | Target |
|--------|----------|------------|--------|
| NFR-01 | [Performance / Security / Scalability / Availability / Usability] | [Requirement name] | [Concrete, measurable target] |
| NFR-02 | [Category] | [Requirement name] | [Concrete, measurable target] |

---

# Summary

<!-- 1-2 paragraphs summarizing what the system is and the core problem it solves. Acts as a quick refresher. -->

[System Name] is [short summary restating the purpose and key value proposition].

---

# UI/UX Expectations

<!-- Global UI/UX standards that apply across all pages. -->

- **Primary Color**: [Primary Color Hex].
- **Data Tables**: [Sorting, pagination: default 20 rows/ page, export (csv & excel), filtering standards]
- **Filtration**: [Standardized filter patterns]
- **Error Messages**: All API error responses include a machine-readable `error_code` and a human-readable `message`. UI surfaces the human-readable message to users.
- **Responsive Design**: Tenant portal must be usable on desktop, tablet and mobile screen sizes as a minimum.
- [Other global UX rules]

---

# Technical Implementation Expectations

<!-- Global Implementation notes. -->

* **Backend**: Java 21, Spring Boot 3.4+
  * layered implementation: Controller, Service, Service Implementation, Repo, Entity.
  * DTOs to utilize Java records.
  * No business logic in controller.
  * DRY, SOLID principles & appropriate design patterns (if needed)
* **Database**: PostgreSQL LTS.
* **ID Strategy**: UUID v7 (`uuid_generate_v7()`) - time-ordered for efficient indexing
* **TimeZone:** UTC for all datetime related.

---

# Appendix

| File Description | File (Attached) |
|-----------------|-----------------|
| [Description] | [Filename or link] |

---

# Wishlist

*Next features (after future enhancements section of each FR)*

1. [Feature 1]
   - [Sub-detail]
2. [Feature 2]
3. [Feature 3]

---

# Specs

<!--
Constitution-grade summary, authored AFTER the body so it can synthesise from completed Executive Summary, FRs, NFRs, and Technical Implementation Expectations.
Sub-sections 1, 2, and 3 are intended as direct inputs for speckit `/constitution`. Sub-section 2 (Tech Stack) and sub-section 4 (Project Type) also steer downstream sdd-unifier / lld-unifier behaviour.
Tone: short, precise, clear, simple. No narrative. No marketing. Each sub-section is one screen at most.
This section MUST come after the FR/NFR body, never before.
-->

## 1. Mission

<!-- 2-3 sentences. Distilled from the Executive Summary above. Core idea only. No metrics, no features list. -->

[2-3 sentences. Core idea only.]

## 2. Tech Stack

<!-- Consolidated from "Technical Implementation Expectations" above and any tech-pinning rows in NFRs. One bullet per tier. If a tier is N/A, write "Not applicable." — do not delete the row. -->

- **Backend:** [Language + framework + version, e.g., Java 21, Spring Boot 3.5+]
- **Frontend:** [Language + framework + version, e.g., Angular 17+ standalone, Tailwind, PrimeNG] | Not applicable.
- **Mobile:** [Platform + framework, e.g., Flutter 3.x] | Not applicable.
- **Data:** [Primary store + version, e.g., PostgreSQL 17+]
- **Messaging:** [If event-driven, e.g., Kafka / SQS+SNS] | Not applicable.

## 3. Roadmap

<!-- Phases derived from the FR list above. Each phase: short label + one-line scope + which FR IDs it covers. 3-6 phases is typical. -->

| Phase | Scope (one line) | FR IDs |
|-------|------------------|--------|
| P1 - [Foundation label] | [What this phase delivers] | FR-01, FR-02 |
| P2 - [Phase 2 label] | [What this phase delivers] | FR-03, FR-04 |
| P3 - [Phase 3 label] | [What this phase delivers] | FR-05, FR-06 |

## 4. Project Type

<!-- Pick exactly one. The choice gates downstream skills (sdd-unifier, lld-unifier from-code vs from-sdd). -->

- [ ] **Greenfield** - new product, no pre-existing codebase to honour.
- [ ] **Brownfield** - extending or re-architecting an existing codebase. Cite the codebase reference: [path or repo URL].

**Selected:** [Greenfield | Brownfield]

**Justification (one line):** [Why this classification.]

---

# Open Items & Clarifications

<!--
Output of the post-generation cleared-context reviewer pass. Captures gaps, missing scenarios, corner cases that the body did not flag inline. Each item has options where applicable.
This section is not a list of `[NEEDS CLARIFICATION: ...]` markers — those stay inline. This section is the reviewer's external findings.
-->

## How to read each item

| Field | Meaning |
|-------|---------|
| **ID** | OI-NN. Stable across revisions. |
| **Where** | Section, FR ID, or "global". |
| **Type** | Gap / Missing scenario / Corner case / Ambiguity / Risk / Inconsistency. |
| **Concern** | One paragraph. What was missed and why it matters. |
| **Options** | Concrete choices, each with a one-line tradeoff. At least 2 where a choice exists. |
| **Recommendation** | Reviewer's suggested option with rationale, or "no recommendation - stakeholder call." |
| **Status** | Open / Resolved (link to BRD update) / Deferred (with rationale). |

## Open Items

### OI-01: [Short title]

- **Where:** [Section name or FR ID]
- **Type:** [Gap | Missing scenario | Corner case | Ambiguity | Risk | Inconsistency]
- **Concern:** [One paragraph.]
- **Options:**
  - **A.** [Option A] — [one-line tradeoff].
  - **B.** [Option B] — [one-line tradeoff].
- **Recommendation:** [Reviewer's suggested option with rationale.]
- **Status:** Open

<!-- Repeat OI block for each open item. -->

## Resolution Log

| ID | Resolution Date | Resolved In | Outcome |
|----|----------------|-------------|---------|
| [OI-XX] | [YYYY-MM-DD] | [Section / FR ID] | [Option chosen — short note] |

## Reviewer Notes

<!-- Optional. Free-form notes that did not crystallise into a numbered open item. -->

- [Note 1]
- [Note 2]
