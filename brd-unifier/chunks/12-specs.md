<!--
CHUNK: 12
TITLE: Specs
PROJECT: [Project Name]
VERSION: [X.X]
DEPENDS_ON: 01-executive-summary-and-context.md, 06a-fr-detailed.md, 09-nfrs.md, 10-summary-uiux-tech.md
PART OF: BRD - [Project Name]
PURPOSE: Constitution-grade summary, authored AFTER the body so it can synthesise from completed Executive Summary, FRs, NFRs, and Technical Implementation Expectations. Source of truth for speckit `/constitution` and a steering input for sdd-unifier and lld-unifier. Tone: short, precise, clear, simple. No narrative. No marketing.
SPECKIT_NOTE: Sub-sections 1 (Mission), 2 (Tech Stack), and 3 (Roadmap) are intended as direct inputs for speckit's `/constitution`. Sub-section 4 (Project Type) gates downstream generation behaviour for SDD/LLD.
DERIVATION_NOTE: Mission distils from §1 Executive Summary; Tech Stack consolidates §10 Technical Implementation Expectations and any tech-pinning rows from §6 NFRs; Roadmap groups FRs from §6a (and 06b/06c if multi-tier) into delivery phases; Project Type is asked explicitly if not already stated in source material.
-->

# Specs

> **Purpose.** Constitution-grade summary of this BRD, written after the body is complete. Speckit's `/constitution` reads this verbatim. Sub-section 2 (Tech Stack) and sub-section 4 (Project Type) also steer SDD/LLD generation. Each sub-section is short by design.

---

## 1. Mission

<!--
2-3 sentences. Distilled from the Executive Summary in chunk 01. Core idea only.
Answer: what is this product, who is it for, what is the single core outcome.
No metrics, no features list, no fluff. If it cannot fit in 3 sentences, distil further.
Used directly by speckit /constitution.
-->

[2-3 sentences. Core idea only.]

---

## 2. Tech Stack

<!--
Consolidated from §10 Technical Implementation Expectations and any tech-pinning rows from §6 NFRs.
One bullet per tier, language + framework + key version.
If a tier is not applicable to this product, write "Not applicable." — do not delete the row.
Used by speckit /constitution AND read by sdd-unifier as a steering input.
-->

- **Backend:** [Language + framework + version, e.g., Java 21, Spring Boot 3.5+]
- **Frontend:** [Language + framework + version, e.g., Angular 17+ standalone, Tailwind, PrimeNG] | Not applicable.
- **Mobile:** [Platform + framework, e.g., Flutter 3.x, or iOS Swift 6 / Android Kotlin] | Not applicable.
- **Data:** [Primary store + version, e.g., PostgreSQL 17+]
- **Messaging:** [If event-driven, e.g., Kafka on prem / SQS+SNS on AWS] | Not applicable.

---

## 3. Roadmap

<!--
Phases derived from the FR list (chunks 05, 06a, 06b, 06c, ...). Each phase is a coherent slice of capability the team can ship and stakeholders can review.
3-6 phases is typical. More than 8 means the slicing is too fine; fewer than 2 means it is too coarse.
Each phase: short label + one-line scope + which FR IDs it covers.
Used by speckit /constitution to set ordering expectations and by execution agents to plan delivery sequences.
-->

| Phase | Scope (one line) | FR IDs |
|-------|------------------|--------|
| P1 - [Foundation label] | [What this phase delivers] | FR-01, FR-02, ... |
| P2 - [Phase 2 label] | [What this phase delivers] | FR-03, FR-04 |
| P3 - [Phase 3 label] | [What this phase delivers] | FR-05, FR-06 |

---

## 4. Project Type

<!--
Pick exactly one. The choice gates downstream skills (sdd-unifier, lld-unifier from-code vs from-sdd direction) and scopes the implementation expectations.
-->

- [ ] **Greenfield** - new product, no pre-existing codebase to honour.
- [ ] **Brownfield** - extending or re-architecting an existing codebase. Cite the codebase reference: [path or repo URL].

**Selected:** [Greenfield | Brownfield]

**Justification (one line):** [Why this classification.]

<!-- MASTER: brd-master.md | PREV: 11-appendix-and-wishlist.md | NEXT: 13-open-items-and-clarifications.md -->
