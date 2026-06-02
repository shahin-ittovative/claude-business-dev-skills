# Chunking Strategy

The CHUNKS mode produces multiple `.md` files, one per logical template-section grouping. This file defines what a chunk is, the canonical chunk map, when to deviate, and how merge handling works.

The chunk skeletons are embedded in this skill folder under `chunks/` — they are the authoritative source for section structure inside each chunk.

---

## Principles

1. **A chunk is a unit of reading, not a unit of storage.** Each chunk groups sections that a reader would read in one sitting, usually to answer one class of question.
2. **Never split by size.** Line count is irrelevant to chunking. A 30-line chunk and a 600-line chunk can both be correct.
3. **Related sections stay together.** Glossary + Assumptions + Facts + Challenges + Dependencies all answer "what should I know before reading the rest?" — they belong in one chunk.
4. **Large semantic sections may get multiple chunks.** Detailed FRs are the common case — if the BRD has four tiers of FRs, each tier gets its own chunk. If the BRD has 25 independent FRs with no tiering, cluster them by theme into 3–5 chunks.
5. **Every chunk is self-describing.** The first lines of every chunk are an HTML comment block identifying it.
6. **The embedded skeletons in `chunks/` define section structure, not content.** Copy the structure when generating; replace placeholders with project content.

---

## Canonical chunk map

Use this as the default. Numbering is stable — `07-` is always "Integrations" regardless of how many FR-detail chunks precede it — because stable numbering makes the merge step trivial.

| # | Filename | Embedded skeleton | Content (template sections) | Typical size |
|---|---|---|---|---|
| 00 | `00-cover-and-changelog.md` | `chunks/00-cover-and-changelog.md` | Title block (name, version, author, date, status), Changes Log, Table of Contents placeholder, Figures index, Tables index. | Small |
| 01 | `01-executive-summary-and-context.md` | `chunks/01-executive-summary-and-context.md` | Executive Summary, Background and Context / Problem Statement, Business Objectives. | Small–Medium |
| 02 | `02-glossary-assumptions-facts.md` | `chunks/02-glossary-assumptions-facts.md` | Glossary, Assumptions / Constraints, Facts, Challenges (incl. challenge evidence table), Dependencies. | Medium |
| 03 | `03-definitions-and-domain-concepts.md` | `chunks/03-definitions-and-domain-concepts.md` | Definitions & Important Details — the deep-dive section. Often the longest chunk; if >600 lines, split into `03a-`, `03b-`. | Medium–Large |
| 04 | `04-scope-and-personas.md` | `chunks/04-scope-and-personas.md` | Project Scope (narrative + In Scope / Out of Scope), Personas / Actors. | Small |
| 05 | `05-fr-overview.md` | `chunks/05-fr-overview.md` | Functional Requirements introductory narrative, Summarized Workflow (Miro link), High Level tier table, High Level FR summary table. **No FR detail blocks here.** | Medium |
| 06a, 06b, … | `06a-fr-[tier-slug].md` | `chunks/06a-fr-detailed.md` | Detailed FR blocks, grouped by tier or by theme. One chunk per tier, or per 4–8 FRs if no tiering. | Medium–Large each |
| 07 | `07-integrations.md` | `chunks/07-integrations.md` | Integrations — systems and integration patterns. | Small–Medium |
| 08 | `08-reporting-and-analytics.md` | `chunks/08-reporting-and-analytics.md` | Reporting / Analytics. | Small |
| 09 | `09-nfrs.md` | `chunks/09-nfrs.md` | Non-Functional Requirements. | Small–Medium |
| 10 | `10-summary-uiux-tech.md` | `chunks/10-summary-uiux-tech.md` | Summary, UI/UX Expectations, Technical Implementation Expectations. | Small |
| 11 | `11-appendix-and-wishlist.md` | `chunks/11-appendix-and-wishlist.md` | Appendix, Wishlist. | Small |
| 12 | `12-specs.md` | `chunks/12-specs.md` | **Specs** — constitution-grade summary, authored AFTER the body so it can synthesise from completed Executive Summary, FRs, NFRs, and Technical Implementation Expectations. Sub-sections: Mission, Tech Stack, Roadmap, Project Type. Used by speckit `/constitution` and as steering input for `sdd-unifier`. Always present, always concise. **Must come after the FR/NFR body; never first.** | Small |
| 13 | `13-open-items-and-clarifications.md` | `chunks/13-open-items-and-clarifications.md` | **Open Items & Clarifications** — output of the post-generation cleared-context reviewer pass. Captures gaps, missing scenarios, corner cases, with concrete options per item. Generated *after* the body AND the Specs chunk; never authored by the same context that wrote them. The reviewer reads chunk 12 too and may flag Specs-body mismatches. | Small–Medium |

Total typical chunk count: **13–18** depending on how many FR tiers / FR-detail chunks exist.

`brd-master.md` (also in `chunks/`) is a master index pointing at the chunks. Regenerate it per project so it links to that project's chunks specifically.

---

## Chunk naming

- Two-digit prefix with optional letter suffix (`06a`, `06b`) for multi-chunk sections.
- Slug in kebab-case, all lowercase, no special characters.
- `.md` extension.

Examples:
- `00-cover-and-changelog.md`
- `06a-fr-wallet-core.md`
- `06b-fr-recharge-and-payments.md`

---

## Chunk header (mandatory)

Every chunk begins with this HTML comment block (invisible when rendered, parseable on merge):

```markdown
<!--
CHUNK: 06a
TITLE: Detailed FRs — Wallet Core
PROJECT: Wallet Management Service
VERSION: 1.0
PART OF: BRD — Wallet Management Service
-->

# Detailed FRs — Wallet Core

...
```

After the comment, the chunk's top-level heading begins at `#`. Sub-sections use `##`, `###`. Heading levels are scoped per chunk. The merge step does not demote headings — each chunk's `#` becomes a distinct major section of the merged doc.

---

## When to deviate from the canonical map

Deviate, and note the deviation in the final handoff summary, when:

1. **A template section is genuinely empty** (e.g., a pure infrastructure BRD has no Reporting / Analytics). Keep the chunk, but make it minimal: the heading plus "Not applicable for this release."
2. **Definitions & Important Details spans more than one substantial domain concept** and exceeds ~600 lines. Split into `03a-definitions-[concept-1].md`, `03b-definitions-[concept-2].md`.
3. **FR count is very high (>30) without natural tiering.** Group by theme (e.g., "auth", "ledger", "reporting") and produce one chunk per theme: `06a-fr-auth.md`, `06b-fr-ledger.md`, `06c-fr-reporting.md`.
4. **FR count is very low (≤6) with no tiering.** Collapse `05-fr-overview.md` and `06a-fr-*.md` into one chunk, `05-functional-requirements.md`.

Do NOT deviate because a chunk "looks too short" — short chunks are fine when the template section is short. Do NOT deviate to merge semantically distinct chunks for the sake of fewer files.

---

## Skip rules (sections that are optional per the template)

- **Figures / Tables index** on the cover chunk — include an empty skeleton; populate as Miro diagrams and tables are added across other chunks.
- **Challenge evidence table** in Challenges — include only if evidence examples were provided.
- **Future Enhancements per FR** — always include; if genuinely nothing, write `- None identified at this time.`
- **UI/UX per FR** — include the heading; if no wireframe yet, reference the global UI/UX Expectations and write `No wireframe required for this FR.` or `Wireframe pending — see global UI/UX standards in chunk 10.`

---

## Merge handling (chunks → combined)

On merge, chunks are concatenated in numeric order (00, 01, 02, 03, 03a, 03b, 04, 05, 06a, 06b, …, 11, 12, 13). The merge step:

1. Strips the `<!-- CHUNK: ... -->` comment from each chunk.
2. Concatenates with a single blank line between chunks (no extra `---` separators unless the template calls for one).
3. Regenerates the Table of Contents in chunk 00 against the merged heading outline.
4. Regenerates the Figures and Tables indices if they exist.
5. Writes to `BRD-[ProjectName]-v[X.X]-MERGED.md` alongside the chunks.

Original chunks are kept — the merged file is a consolidated view.

---

## Re-chunk handling (combined → chunks)

When asked to split a combined BRD into chunks:

1. Read the combined file fully.
2. Identify section boundaries by `# ` and `## ` headings matching the canonical template order.
3. Group sections per the chunk map above.
4. For each output chunk:
   a. Prepend the `<!-- CHUNK: NN ... -->` comment block.
   b. Promote the first matching `## ` heading to `# ` (since chunks are scoped to a single major section).
   c. Adjust deeper heading levels accordingly (was `###` in combined → becomes `##` in chunk).
5. Write each chunk to `./brd-[slug]/NN-*.md`.
6. Keep the original combined file.
