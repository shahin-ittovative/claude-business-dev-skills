<!--
CHUNK: 13
TITLE: Open Items & Clarifications
PROJECT: [Project Name]
VERSION: [X.X]
DEPENDS_ON: all preceding chunks (00 through 12)
PART OF: BRD - [Project Name]
PURPOSE: Output of the post-generation adversarial review. Captures gaps, missing scenarios, corner cases, and ambiguities flagged by a fresh-context reviewer. Each item has options where applicable, so stakeholders can choose rather than re-discover.
GENERATED_BY: brd-unifier post-generation reviewer (cleared-context subagent run after the main BRD body and Specs are complete).
SCOPE: The reviewer reads ALL preceding chunks INCLUDING the Specs chunk (12). Findings on the Specs synthesis (Mission too vague, Tech Stack rows missing version pins, Roadmap phasing inconsistent with FR groupings, Project Type justification unconvincing) are valid OI items.
-->

# Open Items & Clarifications

> **What this section is.** A structured backlog of concerns identified after the main BRD was authored, by a reviewer running with cleared context (so the review is independent rather than confirmatory). Items are not blockers in themselves — they are decisions the team needs to make before the BRD can be considered approved.
>
> **What this section is not.** It is not a list of `[NEEDS CLARIFICATION: ...]` markers found inside the body — those remain inline. This section is the reviewer's *external* findings: gaps the body did not mark, scenarios the body did not consider, corner cases the body did not test for, and inconsistencies between Specs (chunk 12) and the body it summarises.

---

## How to read each item

| Field | Meaning |
|-------|---------|
| **ID** | OI-NN. Stable across revisions. |
| **Where** | Section, FR ID, or "global" if cross-cutting. |
| **Type** | Gap / Missing scenario / Corner case / Ambiguity / Risk / Inconsistency / Specs-body mismatch. |
| **Concern** | One paragraph. What was missed and why it matters. |
| **Options** | Concrete choices, each with a one-line tradeoff. At least 2 options per item where a choice exists. |
| **Recommendation** | Reviewer's suggested option, with brief rationale. May be "no recommendation - stakeholder call." |
| **Status** | Open / Resolved (link to BRD update) / Deferred (with rationale). |

---

## Open Items

### OI-01: [Short title]

- **Where:** [Section name or FR ID, e.g., "FR-04 acceptance criteria" or "global - NFRs" or "Specs §3 Roadmap"]
- **Type:** [Gap | Missing scenario | Corner case | Ambiguity | Risk | Inconsistency | Specs-body mismatch]
- **Concern:** [One paragraph. What is missing or unclear, and why it matters for downstream design or implementation.]
- **Options:**
  - **A.** [Option A] — [one-line tradeoff].
  - **B.** [Option B] — [one-line tradeoff].
  - **C.** [Option C] — [one-line tradeoff]. *(Optional third option.)*
- **Recommendation:** [Reviewer's suggested option with brief rationale, or "no recommendation - stakeholder call."]
- **Status:** Open

---

### OI-02: [Short title]

- **Where:** [...]
- **Type:** [...]
- **Concern:** [...]
- **Options:**
  - **A.** [...] — [...].
  - **B.** [...] — [...].
- **Recommendation:** [...]
- **Status:** Open

---

<!-- Repeat the OI block for each open item. -->

---

## Resolution Log

<!-- When an open item is resolved, move its summary here with a pointer to the BRD update (chunk + heading). Keeps the audit trail. -->

| ID | Resolution Date | Resolved In | Outcome |
|----|----------------|-------------|---------|
| [OI-XX] | [YYYY-MM-DD] | [Chunk and section, e.g., "06a / FR-04 acceptance criteria"] | [Option A | Option B | Custom — short note] |

---

## Reviewer Notes

<!--
Optional. Free-form notes from the reviewer that did not crystallise into a numbered open item.
Examples: patterns observed across multiple FRs, stylistic concerns, suggestions for a future revision.
-->

- [Note 1]
- [Note 2]

<!-- MASTER: brd-master.md | PREV: 12-specs.md | NEXT: none -->
