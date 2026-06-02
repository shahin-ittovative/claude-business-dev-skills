<!--
CHUNK: 15
TITLE: Open Items & Clarifications
PROJECT: [Project Name]
VERSION: [X.X]
DEPENDS_ON: all preceding SDD chunks
PART OF: SDD - [Project Name]
PURPOSE: Output of the post-generation cleared-context reviewer pass. Captures architecture-level gaps, missing scenarios, integration corner cases, and ADR ambiguities flagged by an independent reviewer. Each item carries options so architects/stakeholders can decide.
GENERATED_BY: sdd-unifier post-generation reviewer (cleared-context subagent run after the main SDD generation completes).
-->

# Open Items & Clarifications

> **What this section is.** A structured backlog of architectural concerns identified after the main SDD was authored, by a reviewer running with cleared context. Items are not blockers in themselves — they are decisions the architect needs to make before the SDD can be considered approved.
>
> **What this section is not.** It is not a list of inline `[NEEDS CLARIFICATION: ...]` markers found in the body — those remain inline. This section is the reviewer's *external* findings: gaps the body did not mark, scenarios the body did not consider, corner cases the body did not test for.

---

## How to read each item

| Field | Meaning |
|-------|---------|
| **ID** | OI-NN. Stable across revisions. |
| **Where** | Section number (e.g., §6, §13.2.1), service name, or "global" if cross-cutting. |
| **Type** | Architecture gap / Missing scenario / Corner case / Ambiguity / Risk / Inconsistency / NFR shortfall / ADR needed. |
| **Concern** | One paragraph. What was missed and why it matters for downstream LLD or implementation. |
| **Options** | At least 2 concrete choices, each with a one-line tradeoff. |
| **Recommendation** | Reviewer's suggested option with brief rationale, or "no recommendation - architect call." |
| **Status** | Open / Resolved (link to SDD update) / Deferred (with rationale). |

---

## Open Items

### OI-01: [Short title]

- **Where:** [§N or service name or "global"]
- **Type:** [Architecture gap | Missing scenario | Corner case | Ambiguity | Risk | Inconsistency | NFR shortfall | ADR needed]
- **Concern:** [One paragraph.]
- **Options:**
  - **A.** [Option A] — [one-line tradeoff].
  - **B.** [Option B] — [one-line tradeoff].
  - **C.** [Option C] — [one-line tradeoff]. *(Optional.)*
- **Recommendation:** [Reviewer's suggested option with rationale.]
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

<!-- When an open item is resolved, move its summary here with a pointer to the SDD update (chunk + heading). Audit trail. -->

| ID | Resolution Date | Resolved In | Outcome |
|----|----------------|-------------|---------|
| [OI-XX] | [YYYY-MM-DD] | [Chunk and section] | [Option chosen — short note] |

---

## Reviewer Notes

<!-- Optional. Free-form notes that did not crystallise into a numbered open item. -->

- [Note 1]
- [Note 2]

<!-- MASTER: sdd-master.md | PREV: 14-appendix-and-wishlist.md | NEXT: none -->
