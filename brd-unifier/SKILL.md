---
name: brd-unifier
description: Generate, transform, or reformat Business Requirements Documents (BRDs and BRD-HLDs) into the user's standardised template. ALWAYS trigger when the user asks to create, generate, author, draft, write, or build a BRD, BRD-HLD, Business Requirements Document, or High-Level Design. ALSO trigger when asked to transform, convert, reformat, standardise, unify, or migrate a Scope of Work (SoW), Statement of Work, project brief, product spec, RFP scope, or an existing BRD into this template. Output is Markdown only. Accepts an explicit mode argument; `brd-unifier chunks` produces the multi-file chunked layout; `brd-unifier combined` produces a single monolithic BRD; `brd-unifier` with no argument prompts the user to choose. The skill can either generate from scratch (using context, conversation, or a SoW) or transform an existing document (old-format BRD, single-file BRD, scattered notes) into the unified template. The BRD is business-language only; it states the WHAT — user journeys, per-user use cases with detailed steps (UC-NN blocks), a Users & Use Cases permission matrix, business-level integrations (e.g., "Integration with Payment Gateway"), and NFRs phrased as business expectations (highly available, scalable). No technical stack, no technical terminology — the HOW is owned by sdd-unifier; the constitution-grade Specs section is owned by lld-unifier. Every generation run ends with a post-generation cleared-context reviewer pass (chunk 13) producing an `Open Items & Clarifications` section where every item carries a concrete Recommended Answer; the skill then walks the user through each item to accept, adjust, or defer, and reflects accepted answers into the BRD body. All diagrams are authored as inline Mermaid by default; Miro boards are produced only on explicit request.
---

# BRD Unifier

Author, transform, and unify Business Requirements Documents (BRDs / BRD-HLDs) into the user's standardised template. This skill encapsulates the section structure, the per-persona use-case convention (`Actor & Goal / Why / Preconditions / Main Flow / Alternate & Exception Flows / Business Rules / Acceptance Criteria / Future Enhancements / UI/UX`), the Users & Use Cases Matrix, the chunking model, the SoW-and-BRD transformation rules, and the inline-Mermaid-first diagram policy.

The embedded templates in this skill folder are the authoritative source — `TEMPLATE-COMBINED.md` for the single-file layout and `chunks/*.md` for the chunked layout.

---

## Argument parsing — do this first

The skill is invoked with an optional argument: `brd-unifier [chunks|combined]`.

| Argument received | Meaning | Action |
|---|---|---|
| `chunks` | Produce multi-file chunked output | Skip the mode prompt; proceed with CHUNKS mode. |
| `combined` | Produce a single consolidated `.md` file | Skip the mode prompt; proceed with COMBINED mode. |
| (empty / nothing passed) | User has not chosen a mode | Run the **interactive mode prompt** below. |
| Anything else | Unrecognised | Tell the user the valid options and ask them to re-invoke, or treat as `(empty)` and prompt. |

### Interactive mode prompt (when no arg passed)

**CHUNKS is the default.** Ask one question, accept Enter / empty / "y" as confirmation of the default. Do not ramble:

> **Output format?** [chunks / combined] — default `chunks` (press Enter to accept).
>
> - **`chunks`** (default) — multi-file layout; one `.md` per template section grouping. Matches the embedded `chunks/*.md` skeleton.
> - **`combined`** — single monolithic `.md` file matching `TEMPLATE-COMBINED.md`.

Interpretation rules:

- Empty reply / Enter / `""` / `y` / `yes` / `chunks` / `default` → **CHUNKS mode**.
- `combined` / `c` / `single` / `one file` / `merged` → **COMBINED mode**.
- Anything else → re-prompt once with the same question; if still unclear, default to CHUNKS and note the fallback in the handoff summary.

If the user has already implied a mode in their request ("give me the full doc in one file" → `combined`; "split it into chunks" → `chunks`), do NOT ask — proceed with the implied mode and confirm in one short line in the handoff summary.

---

## Core principles

1. **Templates are authoritative.** `TEMPLATE-COMBINED.md` and the files under `chunks/` define the section order, naming, and structure. Section headings are never silently renamed.
2. **Business language only — the BRD states the WHAT.** No technology names, protocols, frameworks, or implementation terminology anywhere in the body. NFRs are business expectations ("highly available", "handles seasonal peaks") with business measures; integrations name the business partner and purpose ("Integration with Payment Gateway"), not the mechanism. The HOW — tech stack, architecture, technical targets — is owned by `sdd-unifier`; the constitution-grade Specs section is owned by `lld-unifier`. Technical mandates found in source material are parked **verbatim** in Appendix § Technical Inputs for the SDD so nothing is lost.
3. **User-journey first.** Requirements are expressed as per-persona use cases (UC-NN) with detailed numbered steps, alternate and exception flows — not abstract feature statements. Every persona gets a journey narrative, a use-case chunk, and a column in the Users & Use Cases Matrix.
4. **Markdown only.** No `.docx`, `.pdf`, `.html` unless the user explicitly asks in a follow-up.
5. **Mode is explicit.** Either it comes from the argument or from the interactive prompt. Never guess silently.
6. **Chunks are semantic, not size-based.** Never split by line count. Split where the reader naturally changes gear.
7. **Diagrams are inline Mermaid by default.** Any time the template asks for a diagram, author it as an inline Mermaid block with a 1-2 sentence prose summary. Miro boards are produced only when the user explicitly asks. See `mermaid-diagrams.md`.
8. **Flag gaps explicitly.** Where source material doesn't cover something the template requires, insert `**[NEEDS CLARIFICATION: <specific question>]**`. Never paper over gaps with plausible-sounding invention.
9. **Quality over theatre.** Use-case blocks are substantive — see `use-case-quality.md` for the bar.
10. **Generate or transform — detect, don't ask twice.** See `transform-detection.md`.
11. **One fact, one home (no duplication).** The BRD is the single home for business facts: UC-NN blocks, the Users & Use Cases Matrix, personas, business NFRs, business integrations. Downstream documents (SDD, LLD) reference them by ID and link — so IDs and names must stay stable across revisions (renaming a UC or persona breaks the chain; add a mapping note in the Changes Log if unavoidable). Within the BRD, no content is restated across chunks — cross-reference with a link. Restated content is a review defect (OI Type: Duplication).

---

## Workflow

### 1. Resolve mode

Per the Argument parsing section above. Do not skip this — it determines output shape.

### 2. Resolve intent: generate vs transform

See `transform-detection.md` for the decision rules. In short:

- **GENERATE** — fresh BRD from a SoW, conversation, or topic seed.
- **TRANSFORM** — re-shape an existing document (an old-format BRD, a flat scope doc, a single-file BRD that needs chunking, or a chunked BRD that needs combining) into this template.

Both intents end with the same output shape (CHUNKS or COMBINED, per step 1) — the difference is in step 3.

### 3. Intake (short — not a clarification storm)

Ask at most **three** questions before starting, only those that genuinely block quality:

- **Project / system name** — if not stated.
- **Source material** — SoW attached? Existing BRD to migrate? Conversation context only? From scratch?
- **Personas** — if the source does not make clear who the users are, ask for the user types once; personas drive the use-case chunks and the matrix.

If an answer is already in the conversation, do not re-ask.

### 4. Plan internally

Enumerate which sections (combined) or chunks (chunked) will exist — including one use-case chunk per persona — and which Mermaid diagrams each will carry. The canonical chunk list is in `chunking.md`.

### 5. Diagram policy (inline Mermaid; Miro on demand)

All diagrams (persona journey summaries, summarized workflows, context sketches) are authored as **inline Mermaid** blocks, each followed by a 1-2 sentence prose **Summary** so the content reads without a renderer. Keep BRD diagrams business-language only — swimlanes and steps named after personas and business actions, never components or protocols. Validate each Mermaid block parses; on failure fall back to a text description + a clarification flag.

**Miro only on explicit request:** if the user asks for a board, create/reuse `BRD — [Project Name] — Diagrams` via the Miro MCP and append `> Miro: <url>` links below the corresponding Mermaid blocks — the inline Mermaid stays authoritative. See `mermaid-diagrams.md`.

### 6. Generate / transform output

**CHUNKS mode:**

- Use `chunks/*.md` (embedded in this skill folder) as the section skeleton.
- Write output to `./brd-[project-slug]/` (relative to the working directory) unless the user specifies a different path.
- Each chunk starts with the self-describing comment block (see `chunking.md`).
- Detailed use cases: one chunk per persona — `06a-use-cases-[persona-slug].md`, `06b-use-cases-[persona-slug].md`, … in the persona order of chunk 05. UC IDs are sequential across the whole BRD.

**COMBINED mode:**

- Use `TEMPLATE-COMBINED.md` (embedded) as the structure.
- Write output to `./BRD-[ProjectName]-v[X.X].md` unless the user specifies a different path.

**TRANSFORM intent (either mode):**

- Read the source document fully before writing anything.
- Map content to template sections per `sow-transformation.md`.
- Preserve verbatim numbers, dates, and named commitments.
- Park any technical mandates verbatim in Appendix § Technical Inputs for the SDD — never spread them into the body.
- Flag every gap with `**[NEEDS CLARIFICATION: ...]**`.

### 6a. Build the Users & Use Cases Matrix (mandatory, AFTER the use-case chunks)

The matrix (`07-users-use-cases-matrix.md` / `# Users & Use Cases Matrix` section) is **derived**, not authored independently. Build it from the completed use cases:

1. Columns = every persona from chunk 04, in the same order. Rows = every UC ID from chunk 05, in order.
2. Cell = `Yes` where the persona is the UC's Primary or Supporting Actor; `-` otherwise.
3. Conditional access (own records only, requires approval, limited amounts) gets a numbered footnote — never a bare `Yes`.
4. Cross-check both directions: every actor named in a UC has a `Yes`; every `Yes` traces to a UC actor field. A mismatch means the UC or the matrix is wrong — fix the source of truth (the UC) first.
5. Red-flag review: a persona column with no `Yes`, or a matrix where everyone can do everything, means the personas or UC actors need another pass.

### 7. Post-generation review (mandatory, cleared-context)

After the body of the BRD is written but **before** presenting to the user, run an adversarial review pass that produces the `Open Items & Clarifications` chunk (`13-open-items-and-clarifications.md` / `# Open Items & Clarifications` section in combined mode).

**Why cleared context.** The reviewer must be independent. The same context that authored the body anchors on what was written and tends to confirm rather than challenge. The reviewer's job is gap-finding, not validation.

**How to run it.**

1. Use the `Agent` tool with `subagent_type: general-purpose` (or `comprehensive-review:full-review` if appropriate for the project complexity). The subagent starts with no conversation memory, which is the point.
2. Pass the subagent:
   - Absolute paths to all generated chunks (or the combined file).
   - The path to this BRD's templates so it knows the expected structure.
   - The brief: identify gaps, missing scenarios, corner cases, ambiguities, risks, and inconsistencies — including matrix inconsistencies (a UC actor without a matrix `Yes`, a persona with no use cases) and any technical language that leaked into the body. For each finding, propose 2-3 concrete options with one-line tradeoffs AND a **Recommended Answer** (the concrete resolution text, written so it can be pasted into the BRD as-is — the exact step, rule, row, or wording) AND a **Why** (REQUIRED: the reason that option wins — the evidence behind it and the tradeoff accepted; never empty). Output goes into the chunk/section using the schema in `chunks/13-open-items-and-clarifications.md`.
   - Constraint: the reviewer captures **external** findings only — gaps the body did not flag inline. Inline `[NEEDS CLARIFICATION: ...]` markers stay where they are; they do not move into Open Items.
3. The subagent writes directly to `13-open-items-and-clarifications.md` (chunks mode) or appends to the `# Open Items & Clarifications` section (combined mode).
4. Verify the output: at least one OI item per major risk area (scope, use-case exception coverage, matrix consistency, NFRs, integrations, security/privacy, data lifecycle). Every OI must have a non-empty Recommended Answer AND a non-empty Why. If the reviewer returned zero OIs, push back — that almost always means the review was confirmatory rather than adversarial; re-dispatch with a stronger adversarial framing.

**Reviewer prompt skeleton (adapt per project):**

> You are an independent adversarial reviewer for a Business Requirements Document. You have no memory of how this document was authored. Your job is to find what is missing, ambiguous, or risky — not to confirm what is present.
>
> Read these files: [paths]. Use [TEMPLATE-COMBINED.md path] as the structural reference.
>
> For each gap, missing scenario, corner case, ambiguity, risk, or inconsistency you find, write an OI entry following the schema in [chunks/13-open-items-and-clarifications.md path]. Each entry must include: Where, Type, Concern (one paragraph), Options (at least 2 with tradeoffs), **Recommended Answer (the concrete resolution text, ready to paste into the BRD)**, **Why (the reason that option wins over the alternatives — evidence + tradeoff accepted; never empty)**, Status: Open.
>
> Cover at minimum: scope edges, use-case exception flows the body assumes away, Users & Use Cases Matrix consistency (every UC actor has a Yes; every persona has use cases), NFR gaps, integration failure scenarios from the user's point of view, multi-tenancy implications if relevant, data lifecycle and retention, regulatory or compliance hooks not addressed, conflicts between sections, any technical/implementation language that leaked into the business text, and duplication (the same fact stated in two chunks, or source content restated where a cross-reference belongs — one fact, one home).
>
> Do not echo what the document says. Do not confirm. Find what is missing. Write directly to [output path].

### 8. Open Items review & acceptance loop (mandatory)

The Open Items are not left for the user to discover — walk them through each item and get a decision:

1. Present the OI list compactly (ID, title, one-line concern, the Recommended Answer and its Why).
2. Ask the user to decide per item, batched via **AskUserQuestion** (load via ToolSearch if deferred; up to 4 items per call); the recommended option's description carries its Why so the user decides with the reason in view. Options per item: **Accept recommendation** (recommended, listed first) / **Choose option [B/C]** / **Defer** / user types their own answer via "Other".
3. For every **accepted** (or user-adjusted) item:
   - Apply the Recommended Answer (or the adjusted text) to the referenced chunk(s)/section(s) — it was written to be paste-ready.
   - If the change touches actors or permissions, re-verify the Users & Use Cases Matrix (step 6a rules).
   - Set the OI's Status to `Accepted - applied` (or `Adjusted - applied`), add a Resolution Log row, and bump the Changes Log in chunk 00 once for the batch.
4. **Deferred / Rejected** items keep their entry with the new status and rationale; they are not applied.
5. If the user says "later" / "I'll review offline", leave all items `Open` and note in the handoff that the acceptance loop is pending — do not apply anything without an explicit decision.

### 9. Present

After the body, the Open Items chunk, and the acceptance loop, surface the output to the user with:

- Project name, version, mode (chunks / combined), file paths.
- Number of chunks (if chunks mode) or section count (if combined), including the persona count and use-case count.
- Count of inline Mermaid diagrams generated (and the Miro board URL, only if one was requested).
- Count of inline `[NEEDS CLARIFICATION: ...]` markers — explicit body-level gap inventory.
- Open Items summary: total, accepted & applied, adjusted, deferred, rejected, still open.
- Matrix status: personas × use cases covered, plus any footnoted conditional cells.
- Chain handoff check: UC IDs, persona names, and integration IDs are stable and internally consistent (downstream `sdd-unifier` references them by ID); no content restated across chunks.
- One-line offer: "Want me to switch to the other mode?" / "Want me to merge the chunks?" / "Want me to re-chunk this combined file?"

### 10. Cross-mode conversion (on explicit request)

| User says | Action |
|---|---|
| "merge", "consolidate", "single file", "full doc" (after chunks exist) | Concatenate chunks per `chunking.md` § Merge handling. Write to `./BRD-[ProjectName]-v[X.X]-MERGED.md`. Keep originals. |
| "split into chunks", "re-chunk this", "chunk this BRD" (when a combined file exists) | Read the combined file, slice by template sections per `chunks/*.md` skeleton, write chunked output. Keep the original combined file. |
| "regenerate chunk N", "update section X" | Targeted regeneration of one chunk or section, leaving the rest untouched. If use cases change, re-derive the matrix (step 6a). |

---

## Reference files (read these when the situation calls for them)

- `TEMPLATE-COMBINED.md` — the single-file template. Read at the start of any COMBINED-mode generation.
- `chunks/*.md` — the per-chunk template skeletons. Read at the start of any CHUNKS-mode generation.
- `chunking.md` — canonical chunk map, naming convention, merge rules.
- `modes.md` — chunks vs combined behavioural details.
- `transform-detection.md` — rules for deciding generate vs transform.
- `sow-transformation.md` — how to map SoW or existing-BRD content into this template.
- `mermaid-diagrams.md` — inline Mermaid conventions for every diagram the template implies, plus the Miro-on-demand flow.
- `use-case-quality.md` — what makes a substantive use case vs a thin one, and the matrix consistency rules.

---

## Output conventions

- **Project slug**: kebab-case, lowercased, derived from the project name (e.g., "Wallet Management Service" → `wallet-management-service`).
- **Chunked output folder**: `./brd-[project-slug]/`.
- **Chunked filenames**: `NN-short-title.md` (two-digit prefix, optional letter for splits like `06a`, `06b`). See `chunking.md`.
- **Combined output filename**: `BRD-[ProjectName]-v[X.X].md` (PascalCase project name, no spaces).
- **Merged-from-chunks filename**: `BRD-[ProjectName]-v[X.X]-MERGED.md`.
- **Encoding**: UTF-8, LF line endings.
- **Tables**: pipe-table format, no hard line wrap.

---

## Things this skill never does

- Never emits `.docx`, `.pdf`, `.xlsx`, or any non-Markdown output unless the user explicitly asks.
- Never puts technical stack, technical terminology, protocols, or implementation detail in the BRD body. Source-stated technical mandates go verbatim into Appendix § Technical Inputs for the SDD; everything else technical is left to `sdd-unifier`. The Specs section (Mission, Tech Stack, Roadmap, Project Type) is owned by `lld-unifier`, not this skill.
- Never creates a Miro board unless the user explicitly asks. Inline Mermaid is the authoritative diagram medium; Miro links are additive. BRD Mermaid diagrams stay business-language (personas and actions, never components or protocols).
- Never invents NFR measures, counts, or business targets to fill a table. Missing measure → `[NEEDS CLARIFICATION: ...]`.
- Never writes a matrix cell that contradicts a use case's actor fields — the UC is the source of truth; fix it first.
- Never applies an Open Item to the body without the user's explicit acceptance in the review loop.
- Never produces a "here's a summary, let me know if you want the full version" preview. Generate the actual deliverable.
- Never silently drops template sections. Empty sections keep their heading and write `Not applicable for this release.` (with a clarification flag if surprising).
- Never modifies the embedded templates (`TEMPLATE-COMBINED.md` or `chunks/*.md`) during a generation run — they are read-only references.
