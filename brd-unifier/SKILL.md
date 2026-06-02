---
name: brd-unifier
description: Generate, transform, or reformat Business Requirements Documents (BRDs and BRD-HLDs) into the user's standardised template. ALWAYS trigger when the user asks to create, generate, author, draft, write, or build a BRD, BRD-HLD, Business Requirements Document, or High-Level Design. ALSO trigger when asked to transform, convert, reformat, standardise, unify, or migrate a Scope of Work (SoW), Statement of Work, project brief, product spec, RFP scope, or an existing BRD into this template. Output is Markdown only. Accepts an explicit mode argument: `brd-unifier chunks` produces the multi-file chunked layout; `brd-unifier combined` produces a single monolithic BRD; `brd-unifier` with no argument prompts the user to choose. The skill can either generate from scratch (using context, conversation, or a SoW) or transform an existing document (old-format BRD, single-file BRD, scattered notes) into the unified template. Every BRD ends with a constitution-grade `Specs` section (chunk 12 — Mission, Tech Stack, Roadmap, Project Type) authored AFTER the body so it can synthesise from completed FRs/NFRs/Tech Expectations. The Specs section is intended for speckit `/constitution` and as steering input for sdd-unifier. Every generation run ends with a post-generation cleared-context reviewer pass (chunk 13) producing an `Open Items & Clarifications` section that captures gaps the body did not flag inline, including any Specs-body mismatches. All diagrams are authored on a Miro board via the Miro MCP and referenced by link, never inlined as Mermaid or ASCII unless explicitly requested.
---

# BRD Unifier

Author, transform, and unify Business Requirements Documents (BRDs / BRD-HLDs) into the user's standardised template. This skill encapsulates the section structure, the `What / Why / How / Constraints / Acceptance Criteria / Future Enhancements / UI/UX` FR block convention, the chunking model, the SoW-and-BRD transformation rules, and the Miro-first diagram policy.

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
2. **Markdown only.** No `.docx`, `.pdf`, `.html` unless the user explicitly asks in a follow-up.
3. **Mode is explicit.** Either it comes from the argument or from the interactive prompt. Never guess silently.
4. **Chunks are semantic, not size-based.** Never split by line count. Split where the reader naturally changes gear.
5. **Diagrams live in Miro.** Any time the template asks for a diagram, create it on a Miro board via the Miro MCP and reference the board URL. See `miro-diagrams.md`.
6. **Flag gaps explicitly.** Where source material doesn't cover something the template requires, insert `**[NEEDS CLARIFICATION: <specific question>]**`. Never paper over gaps with plausible-sounding invention.
7. **Quality over theatre.** FR blocks are substantive — see `fr-quality.md` for the bar.
8. **Generate or transform — detect, don't ask twice.** See `transform-detection.md`.

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
- **Mode confirmation** — only if step 1 left ambiguity (e.g., user said "do both" — pick one and produce the other on follow-up).

If an answer is already in the conversation, do not re-ask.

### 3a. Project Type early ask (only if it would change body generation)

If the source does not state whether this is **Greenfield** or **Brownfield**, ask this single question now via **AskUserQuestion** (load via ToolSearch with `select:AskUserQuestion` if deferred):

> "Greenfield (new product, no existing code) or Brownfield (extending an existing codebase)? If brownfield, where is the codebase?"

This is asked early because Brownfield typically requires extra Existing System Context content in the BRD body (assumptions about constraints inherited from the existing system, integration boundaries already in place). Other Specs fields (Mission, Tech Stack, Roadmap) are derived later from the completed body — see Step 6a.

### 4. Plan internally

Enumerate which sections (combined) or chunks (chunked) will exist and which diagrams will go to Miro. The canonical chunk list is in `chunking.md`.

### 5. Create the Miro board (once, up-front)

Before generating any section that references a diagram:

- Load Miro tools via `tool_search` (Miro tools are deferred).
- Create or reuse a board named `BRD — [Project Name] — Diagrams`.
- Record the board URL.

See `miro-diagrams.md` for the diagram-by-diagram playbook.

### 6. Generate / transform output

**CHUNKS mode:**

- Use `chunks/*.md` (embedded in this skill folder) as the section skeleton.
- Write output to `./brd-[project-slug]/` (relative to the working directory) unless the user specifies a different path.
- Each chunk starts with the self-describing comment block (see `chunking.md`).

**COMBINED mode:**

- Use `TEMPLATE-COMBINED.md` (embedded) as the structure.
- Write output to `./BRD-[ProjectName]-v[X.X].md` unless the user specifies a different path.

**TRANSFORM intent (either mode):**

- Read the source document fully before writing anything.
- Map content to template sections per `sow-transformation.md`.
- Preserve verbatim numbers, dates, and named commitments.
- Flag every gap with `**[NEEDS CLARIFICATION: ...]**`.

### 6a. Synthesise the Specs chunk (mandatory, AFTER the body is complete)

The body is now written: chunks 00 through 11 in chunks mode, or sections through Wishlist in combined mode. Now synthesise the `Specs` chunk (`12-specs.md` / `# Specs` section in combined mode, placed after Wishlist).

The Specs section is **derived synthesis**, not new authoring. Pull each sub-section from the completed body:

1. **Mission** — distil from chunk 01 Executive Summary. 2-3 sentences. Strip business framing; keep the core idea — what is this product, who is it for, what is the single outcome. If you cannot fit it in 3 sentences, the Executive Summary itself was too vague — flag with `[NEEDS CLARIFICATION: tighten Executive Summary so Mission can distil cleanly]`.
2. **Tech Stack** — consolidate from chunk 10 "Technical Implementation Expectations" and any tech-pinning rows in chunk 09 NFRs. One bullet per tier (Backend, Frontend, Mobile, Data, Messaging). If a tier is not mentioned anywhere in the body, write "Not applicable." If a tier is mentioned without version pin, **invoke AskUserQuestion** for the version (CLAUDE.md defaults are reasonable but must be confirmed, not assumed).
3. **Roadmap** — group the FRs from chunks 05/06a/06b/... into 3-6 delivery phases. Each phase: short label + one-line scope + which FR IDs it covers. The grouping is your inference; if the FR list does not have natural phase breaks, **invoke AskUserQuestion**: "How would you slice these FRs into delivery phases?"
4. **Project Type** — already determined in Step 3a. Carry through verbatim with the justification line.

Tone: short, precise, clear, simple. Constitution voice — declarative, no marketing adjectives, no narrative. The reviewer in Step 7 will flag any Specs-body mismatch.

### 7. Post-generation review (mandatory, cleared-context)

After the body of the BRD AND the Specs chunk are written but **before** presenting to the user, run an adversarial review pass that produces the `Open Items & Clarifications` chunk (`13-open-items-and-clarifications.md` / `# Open Items & Clarifications` section in combined mode).

**Why cleared context.** The reviewer must be independent. The same context that authored the body anchors on what was written and tends to confirm rather than challenge. The reviewer's job is gap-finding, not validation.

**How to run it.**

1. Use the `Agent` tool with `subagent_type: general-purpose` (or `comprehensive-review:full-review` if appropriate for the project complexity). The subagent starts with no conversation memory, which is the point.
2. Pass the subagent:
   - Absolute paths to all generated chunks (or the combined file), **including chunk 12 Specs**.
   - The path to this BRD's templates so it knows the expected structure.
   - The brief: identify gaps, missing scenarios, corner cases, ambiguities, risks, inconsistencies, AND any Specs-body mismatches (e.g., Specs.Tech Stack pins a version not mentioned anywhere in chunk 10; Specs.Roadmap phases reference FR IDs that do not exist; Mission contradicts Executive Summary). For each finding, propose 2-3 concrete options with one-line tradeoffs and a recommendation. Output goes into the chunk/section using the schema in `chunks/13-open-items-and-clarifications.md`.
   - Constraint: the reviewer captures **external** findings only — gaps the body did not flag inline. Inline `[NEEDS CLARIFICATION: ...]` markers stay where they are; they do not move into Open Items.
3. The subagent writes directly to `13-open-items-and-clarifications.md` (chunks mode) or appends to the `# Open Items & Clarifications` section (combined mode).
4. Verify the output: at least one OI item per major risk area (scope, NFRs, integrations, security, multi-tenancy if applicable, data lifecycle). If the reviewer returned zero OIs, push back — that almost always means the review was confirmatory rather than adversarial; re-dispatch with a stronger adversarial framing.

**Reviewer prompt skeleton (adapt per project):**

> You are an independent adversarial reviewer for a Business Requirements Document. You have no memory of how this document was authored. Your job is to find what is missing, ambiguous, or risky — not to confirm what is present.
>
> Read these files: [paths]. Use [TEMPLATE-COMBINED.md path] as the structural reference.
>
> For each gap, missing scenario, corner case, ambiguity, risk, or inconsistency you find, write an OI entry following the schema in [chunks/12-open-items-and-clarifications.md path]. Each entry must include: Where, Type, Concern (one paragraph), Options (at least 2 with tradeoffs), Recommendation, Status: Open.
>
> Cover at minimum: scope edges, NFR gaps, integration error paths, multi-tenancy implications if relevant, data lifecycle and retention, failure modes the body assumes away, regulatory or compliance hooks not addressed, conflicts between sections, AND Specs-body mismatches (Mission vs Executive Summary, Tech Stack vs Technical Implementation Expectations, Roadmap FR IDs vs actual FRs).
>
> Do not echo what the document says. Do not confirm. Find what is missing. Write directly to [output path].

### 8. Present

After writing both the body and the Open Items chunk, surface the output to the user with:

- Project name, version, mode (chunks / combined), file paths.
- Number of chunks (if chunks mode) or section count (if combined).
- Number of Miro diagrams created and the board URL.
- Count of inline `[NEEDS CLARIFICATION: ...]` markers — explicit body-level gap inventory.
- Count of Open Items (OI-NN) — reviewer-level findings inventory.
- Specs sub-section status: Mission ✓ / Tech Stack ✓ / Roadmap ✓ / Project Type ✓ (or flag any that fell back to placeholders).
- One-line offer: "Want me to switch to the other mode?" / "Want me to merge the chunks?" / "Want me to re-chunk this combined file?"

### 9. Cross-mode conversion (on explicit request)

| User says | Action |
|---|---|
| "merge", "consolidate", "single file", "full doc" (after chunks exist) | Concatenate chunks per `chunking.md` § Merge handling. Write to `./BRD-[ProjectName]-v[X.X]-MERGED.md`. Keep originals. |
| "split into chunks", "re-chunk this", "chunk this BRD" (when a combined file exists) | Read the combined file, slice by template sections per `chunks/*.md` skeleton, write chunked output. Keep the original combined file. |
| "regenerate chunk N", "update section X" | Targeted regeneration of one chunk or section, leaving the rest untouched. |

---

## Reference files (read these when the situation calls for them)

- `TEMPLATE-COMBINED.md` — the single-file template. Read at the start of any COMBINED-mode generation.
- `chunks/*.md` — the per-chunk template skeletons. Read at the start of any CHUNKS-mode generation.
- `chunking.md` — canonical chunk map, naming convention, merge rules.
- `modes.md` — chunks vs combined behavioural details.
- `transform-detection.md` — rules for deciding generate vs transform.
- `sow-transformation.md` — how to map SoW or existing-BRD content into this template.
- `miro-diagrams.md` — Miro MCP usage for every diagram the template implies.
- `fr-quality.md` — what makes a substantive FR vs a thin one.

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
- Never inlines diagrams as Mermaid, ASCII art, or base64 images unless the user asks for inline (`render inline` / `don't use Miro` / `embed the diagram`).
- Never invents NFR targets, SLAs, latencies, or counts to fill a table. Missing target → `[NEEDS CLARIFICATION: ...]`.
- Never produces a "here's a summary, let me know if you want the full version" preview. Generate the actual deliverable.
- Never silently drops template sections. Empty sections keep their heading and write `Not applicable for this release.` (with a clarification flag if surprising).
- Never modifies the embedded templates (`TEMPLATE-COMBINED.md` or `chunks/*.md`) during a generation run — they are read-only references.
