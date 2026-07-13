# Modes — Chunks vs Combined

The skill produces output in one of two shapes. The shape is determined either by the argument passed (`sdd-unifier chunks` / `sdd-unifier combined`) or, when no argument is passed, by the interactive mode prompt with CHUNKS as the default.

---

## CHUNKS mode (default)

**Output:** A folder of `.md` files, one per logical template-section grouping, written to `./sdd-[project-slug]/`.

**Skeleton source:** `chunks/*.md` (embedded in this skill folder).

**File list (canonical):**

```
sdd-[project-slug]/
├── 00-cover-and-changelog.md
├── 01-executive-summary-scope-risks.md
├── 02-ecosystem-overview.md
├── 03-users-and-use-cases.md
├── 04-architecture-style-and-diagrams.md
├── 05-workflows-and-sequences.md
├── 06-principles-and-decisions.md
├── 07-cross-cutting-concerns.md
├── 08-integrations.md
├── 09-services-summary.md
├── 10-events-hub.md                # centralized event hub: catalog + payload contracts
├── 10a-service-[svc-1-slug].md     # one chunk per service
├── 10b-service-[svc-2-slug].md
├── 10c-service-[svc-N-slug].md
├── 11-centralized-user-roles.md    # platform-wide roles & authorities
├── 12-performance-and-capacity.md
├── 13-environments.md
├── 14-operations-runbook.md
├── 15-appendix-and-wishlist.md
├── 16-e2e-system-design.md         # end-to-end system map (authored last)
├── 17-open-items-and-clarifications.md   # reviewer output (post-generation)
└── sdd-master.md                   # master index (regenerated per project)
```

`sdd-master.md` (in `chunks/`) is the master index. Regenerate it per project so it links to that project's chunks specifically.

**Each chunk starts with** the self-describing HTML comment block:

```markdown
<!--
CHUNK: 04
TITLE: Architecture Style & Diagrams
PROJECT: [Project Name]
VERSION: [X.X]
PART OF: SDD — [Project Name]
-->
```

See `chunking.md` for chunking strategy and merge handling.

**When to prefer:**

- Multi-service systems where each service deserves its own detailed-spec chunk.
- Teams editing different sections in parallel (architects own arch chunks; SRE owns runbook; service teams own per-service chunks).
- The SDD is large (>800 lines is typical for multi-service systems).
- Per-service chunks need to be regenerated as services evolve.

---

## COMBINED mode

**Output:** A single `.md` file written to `./SDD-[ProjectName]-v[X.X].md`.

**Skeleton source:** `TEMPLATE-COMBINED.md` (embedded in this skill folder).

**Structure follows the template top-to-bottom (sections 1–23):**

1. Executive Summary
2. Scope (In / Out)
3. Assumptions
4. Risks
5. Glossary
6. Ecosystem Overview (incl. Architecture Doctrine: EDA + DDD + Hexagonal)
7. System Users & Use Cases (Actors + Use Case Diagram)
8. System Design / High-Level Architecture (Style, Context, HL Arch, Workflow, Sequence diagrams)
9. Architecture Principles
10. Architectural Decisions (ADR summary table)
11. Cross-Cutting Concerns (DB, Multi-tenancy, Deployment, Observability, Config, Security defaults)
12. Integrations
13. Services Decomposition (Summary)
14. Centralized Event Hub (Platform Event Catalog & Payload Contracts)
15. Detailed Service Specs (one 15.X block per service)
16. Centralized User Roles & Authorities (platform-wide)
17. Performance & Capacity Planning
18. Environments
19. Operations Runbook
20. Appendix
21. Wishlist
22. End-to-End System Design (authored last among body sections)
23. Open Items & Clarifications (reviewer output)

**No chunk comment blocks** in combined mode — the file is a single artefact.

**When to prefer:**

- Single-service systems (1 bounded context).
- Sharing as one attachment to a stakeholder, vendor, or external reviewer.
- Submitting for a formal architecture review pipeline that expects one file.
- Archiving a finalised version.

---

## Cross-mode conversion

The two modes are reversible.

### Chunks → Combined (merge)

When the user says "merge", "consolidate", "single file", "full doc" after a chunks-mode generation:

1. Read all `sdd-[slug]/NN-*.md` files in file-sort order (00 … 09, 10, 10a, 10b, …, 11 … 17).
2. Strip each chunk's `<!-- CHUNK: ... -->` HTML comment block.
3. Concatenate with a single blank line between chunks.
4. Deduplicate the repeated `# 15. Detailed Service Specs` parent heading (keep only the first) and renumber the §15.X service blocks if out of order.
5. Regenerate the Table of Contents in the cover section against the merged heading outline.
6. Regenerate the Figures and Tables indices.
7. Write to `./sdd-[slug]/SDD-[ProjectName]-v[X.X]-MERGED.md` (alongside the chunks).
8. Keep the original chunks.

### Combined → Chunks (re-chunk)

When the user says "split into chunks", "chunk this SDD", "re-chunk this":

1. Read the combined file fully.
2. Identify section boundaries by `# `, `## ` headings matching the template structure.
3. Group sections per the canonical chunk map (see `chunking.md`).
4. For section 15 (Detailed Service Specs), each `## 15.X` block becomes its own chunk file (`10a-service-*.md`).
5. For each chunk, prepend the `<!-- CHUNK: ... -->` comment block.
6. Heading levels stay as-is (the template uses absolute numbering like `# 1.`, `## 1.1`, so no demotion is needed).
7. Write each chunk file (plus a regenerated `sdd-master.md` index).
8. Keep the original combined file.

---

## Mode is independent of intent

Mode (CHUNKS / COMBINED) describes the **output shape**. Intent (GENERATE / TRANSFORM / DERIVE-FROM-BRD) describes the **input handling**. They are orthogonal:

|  | GENERATE | TRANSFORM | DERIVE-FROM-BRD |
|---|---|---|---|
| **CHUNKS** | Author fresh SDD as 18+ chunked files. | Re-shape source SDD into 18+ chunked files. | Generate skeleton SDD from BRD as 18+ chunked files; SDD-only sections are stubs with `[NEEDS CLARIFICATION: ...]`. |
| **COMBINED** | Author fresh SDD as a single file. | Re-shape source SDD into a single file. | Generate skeleton SDD from BRD as a single file; same flagging behaviour. |

See `transform-detection.md` for how to decide intent.
