---
name: sdd-unifier
description: Generate, transform, or reformat Solution Design Documents (SDDs) into the user's standardised template. ALWAYS trigger when the user asks to create, generate, author, draft, write, or build an SDD, Solution Design Document, technical design document, system design document, HLD, or architecture design document. ALSO trigger when asked to transform, convert, reformat, standardise, unify, or migrate an existing SDD or technical design into this template. ALSO trigger when asked to derive, generate, or scaffold an SDD from a BRD (including the chunked or combined output of the brd-unifier skill). Output is Markdown only. Accepts an explicit mode argument: `sdd-unifier chunks` produces the multi-file chunked layout; `sdd-unifier combined` produces a single monolithic SDD; `sdd-unifier` with no argument defaults to chunks (Enter / empty / `y` / `chunks` all confirm). Default mode is CHUNKS. The skill can generate from scratch, transform an existing SDD into this template, or derive an SDD skeleton from one BRD (chunked folder or single file). When the source BRD contains a `Specs` section (Mission, Tech Stack, Roadmap, Project Type), the skill consumes it as the highest-fidelity steering signal — Tech Stack overrides CLAUDE.md defaults in §6 Ecosystem Overview, Roadmap informs §13.1 service phasing, and Project Type (greenfield/brownfield) gates the entire SDD generation behaviour. Every generation run ends with a post-generation cleared-context reviewer pass producing an `Open Items & Clarifications` section. All architectural diagrams are authored on a Miro board via the Miro MCP and referenced by link, never inlined as Mermaid or ASCII unless explicitly requested.
---

# SDD Unifier

Author, transform, and unify Solution Design Documents (SDDs) into the user's standardised template. This skill encapsulates the full SDD section structure, the per-service spec block convention, the chunking model, the BRD→SDD derivation rules, and the Miro-first diagram policy.

The embedded templates in this skill folder are the authoritative source — `TEMPLATE-COMBINED.md` for the single-file layout and `chunks/*.md` for the chunked layout. Both were lifted verbatim from the user's working templates at `W:\ITV\STANDARDZzzz\`.

This skill is a sibling of `brd-unifier` — they work in concert when the workflow is SoW → BRD → SDD.

---

## Argument parsing — do this first

The skill is invoked with an optional argument: `sdd-unifier [chunks|combined]`.

| Argument received | Meaning | Action |
|---|---|---|
| `chunks` (or empty / Enter / `y` / `yes` / `default`) | Produce multi-file chunked output | Proceed with CHUNKS mode (the default). |
| `combined` (or `c` / `single` / `one file` / `merged`) | Produce a single consolidated `.md` file | Proceed with COMBINED mode. |
| Anything else | Unrecognised | Re-prompt once with the same question; if still unclear, default to CHUNKS and note the fallback in the handoff summary. |

### Interactive mode prompt (when no arg passed)

**CHUNKS is the default.** Ask one question, accept Enter / empty / "y" as confirmation of the default. Do not ramble:

> **Output format?** [chunks / combined] — default `chunks` (press Enter to accept).
>
> - **`chunks`** (default) — multi-file layout; one `.md` per template section grouping. Matches the embedded `chunks/*.md` skeleton.
> - **`combined`** — single monolithic `.md` file matching `TEMPLATE-COMBINED.md`.

Interpretation rules:

- Empty reply / Enter / `""` / `y` / `yes` / `chunks` / `default` → **CHUNKS mode**.
- `combined` / `c` / `single` / `one file` / `merged` → **COMBINED mode**.
- Anything else → re-prompt once; if still unclear, default to CHUNKS and note the fallback.

If the user has already implied a mode in their request ("give me the full SDD as one file" → `combined`; "split it into chunks" → `chunks`), do NOT ask — proceed with the implied mode and confirm in one short line.

---

## Core principles

1. **Templates are authoritative.** `TEMPLATE-COMBINED.md` and the files under `chunks/` define the section order, naming, and structure. Section headings are never silently renamed.
2. **Markdown only.** No `.docx`, `.pdf`, `.html` unless the user explicitly asks.
3. **Mode is explicit.** Either from argument or interactive prompt. Never guess silently.
4. **Chunks are semantic, not size-based.** Never split by line count. The detailed-service chunk (`10a-service-*.md`) splits by service, not by length.
5. **Diagrams live in Miro.** Architecture, context, workflow, sequence, and ER diagrams all go on a Miro board referenced by link. See `miro-diagrams.md`. The embedded templates include Mermaid alternatives — those are reference only; the Miro board is the source of truth.
6. **Flag gaps explicitly.** Where source material doesn't cover something the template requires, insert `**[NEEDS CLARIFICATION: <specific question>]**`. Never paper over gaps with plausible-sounding invention.
7. **One SDD per SoW (default).** A single SDD covers a single SoW / single BRD by default. Multi-BRD rollup is out of scope for this skill version.
8. **Use platform defaults from CLAUDE.md when source is silent.** Java 21 / Spring Boot 3.5+, PostgreSQL 17+, UUIDv7, Kafka, Keycloak, microservices-first, Angular 17+ standalone — these are the user's standing technical defaults. The Ecosystem Overview falls back to these unless the BRD or SoW overrides them.
9. **Generate, transform, or derive — detect, don't ask twice.** See `transform-detection.md`.

---

## Workflow

### 1. Resolve mode

Per the Argument parsing section above. Default is CHUNKS.

### 2. Resolve intent

Three possible intents:

- **GENERATE** — fresh SDD from a SoW, conversation, or topic seed. The architect supplies all technical decisions (or accepts CLAUDE.md defaults).
- **TRANSFORM** — re-shape an existing SDD (vendor template, IEEE 1016 style, prior in-house format) into this template.
- **DERIVE-FROM-BRD** — generate an SDD skeleton from a BRD (chunked folder or combined file), auto-filling BRD-derivable sections and flagging SDD-only sections with `[NEEDS CLARIFICATION: ...]` markers.

See `transform-detection.md` for the decision rules.

### 3. Intake (short — not a clarification storm)

Ask at most **three** questions before starting:

- **Project / system name** — if not stated.
- **Source material** — fresh? Existing SDD to migrate? BRD to derive from?
- **Mode confirmation** — only if step 1 left ambiguity.

If an answer is in the conversation, do not re-ask.

### 3a. Read BRD Specs (mandatory when DERIVE-FROM-BRD)

If intent is DERIVE-FROM-BRD, locate and read the BRD's `Specs` section before anything else:

- **Chunked BRD:** `12-specs.md` (the Specs chunk lives at the END of the BRD, after the Wishlist and before the Open Items chunk).
- **Combined BRD:** the `# Specs` top-level section.

The Specs section steers downstream generation behaviour aggressively. See `brd-to-sdd.md` § Specs-driven steering for the complete mapping. Key gates:

- **Specs.Tech Stack** is verbatim-authoritative for §6 Ecosystem Overview. It overrides CLAUDE.md defaults.
- **Specs.Project Type = Brownfield** activates the brownfield flow: add §1 Existing System Context sub-section, mark cross-cutting concerns as inherit/override/new, flag per-service specs as extending existing services.
- **Specs.Roadmap** adds a Phase column to §13.1 Services Decomposition and seeds §1 Executive Summary phased-delivery note.

If the BRD has no Specs section (legacy / pre-Specs version), note "no Specs section present" in the handoff summary and proceed with the standard field mapping; downstream speckit `/constitution` consumers will need to back-fill via `brd-unifier`.

### 4. Plan internally

Enumerate which sections (combined) or chunks (chunked) will exist, which diagrams will go to Miro, and which sections need `[NEEDS CLARIFICATION: ...]` markers. The canonical chunk list is in `chunking.md`.

### 5. Create the Miro board (once, up-front)

Before generating any chunk that references a diagram:

- Load Miro tools via `tool_search`.
- Create or reuse a board named `SDD — [Project Name] — Diagrams`.
- Record the board URL.

See `miro-diagrams.md`. SDDs typically need *more* diagrams than BRDs: System Context, High-Level Architecture, Workflow per critical flow, Sequence per critical interaction, plus per-service ERDs and Sequence diagrams.

### 6. Generate / transform / derive output

**CHUNKS mode (default):**

- Use `chunks/*.md` as the section skeleton.
- Write output to `./sdd-[project-slug]/`.
- Each chunk starts with the self-describing comment block (see `chunking.md`).
- For section 13 (Services), produce one chunk per service: `10a-service-[slug].md`, `10b-service-[slug].md`, ...

**COMBINED mode:**

- Use `TEMPLATE-COMBINED.md` as the structure.
- Write output to `./SDD-[ProjectName]-v[X.X].md`.

**TRANSFORM intent:**

- Read the source SDD fully.
- Map content to template sections per `source-transformation.md`.
- Preserve verbatim numbers, dates, named technologies, version pins.
- Flag every gap with `**[NEEDS CLARIFICATION: ...]**`.

**DERIVE-FROM-BRD intent:**

- Read the BRD fully (chunked folder OR combined file — see `brd-to-sdd.md` § Detecting BRD input form).
- Map BRD content to SDD sections per the table in `brd-to-sdd.md`.
- For SDD-only sections (Architecture Style, ADRs, Cross-cutting overrides, Runbook procedures), produce the heading + structure with `[NEEDS CLARIFICATION: ...]` markers naming the specific decisions the architect must make.
- For Ecosystem Overview, fall back to user's CLAUDE.md defaults unless the BRD's "Technical Implementation Expectations" section overrides them.
- The output is intentionally an architect-ready skeleton, not a finished SDD.

### 7. Post-generation review (mandatory, cleared-context)

After the body of the SDD is written but **before** presenting to the user, run an adversarial review pass that produces the `Open Items & Clarifications` chunk (`15-open-items-and-clarifications.md` / `# 19. Open Items & Clarifications` section in combined mode).

**Why cleared context.** The same context that authored the SDD anchors on its own architectural choices. The reviewer's job is to find what is missing or risky, not to confirm what was decided.

**How to run it.**

1. Use the `Agent` tool with `subagent_type: comprehensive-review:full-review` (preferred for SDDs given the multi-dimensional review surface) or `general-purpose`. The subagent starts with no conversation memory.
2. Pass the subagent:
   - Absolute paths to all generated SDD chunks (or the combined file).
   - The path to the source BRD (chunks folder or combined file) so the reviewer can cross-check against requirements.
   - The path to this skill's templates.
   - The brief: identify architecture-level gaps, missing scenarios, ADR ambiguities, NFR shortfalls, integration corner cases, multi-tenancy implications, and inconsistencies. For each finding, propose 2-3 concrete options with one-line tradeoffs and a recommendation. Output goes into the chunk/section using the schema in `chunks/15-open-items-and-clarifications.md`.
   - Constraint: the reviewer captures **external** findings only — gaps the body did not flag inline. Inline `[NEEDS CLARIFICATION: ...]` markers stay where they are.
3. The subagent writes directly to `15-open-items-and-clarifications.md` (chunks mode) or appends to the `# 19. Open Items & Clarifications` section (combined mode).
4. Verify coverage: at minimum one OI per major risk surface (architecture style, ADRs, cross-cutting concerns, per-service contracts, NFRs, integrations, security, observability). Zero OIs typically means a confirmatory review — re-dispatch with stronger adversarial framing.

**Reviewer prompt skeleton (adapt per project):**

> You are an independent adversarial reviewer for a Solution Design Document. You have no memory of how this document was authored. Your job is to find what is missing, ambiguous, or risky in the architecture — not to confirm what is present.
>
> Read these files: [SDD paths]. Cross-reference against the source BRD: [BRD paths]. Use [TEMPLATE-COMBINED.md path] as the structural reference.
>
> For each architecture-level gap, missing scenario, corner case, ambiguity, risk, NFR shortfall, or inconsistency, write an OI entry following the schema in [chunks/15-open-items-and-clarifications.md path]. Each entry must include: Where (§N or service), Type, Concern (one paragraph), Options (at least 2 with tradeoffs), Recommendation, Status: Open.
>
> Cover at minimum: architecture style fit, ADR completeness, cross-cutting concerns coverage, per-service contract completeness, NFR realisability, integration error paths and timeouts, multi-tenancy edge cases, observability gaps, security/compliance hooks, deployment failure modes, runbook completeness, BRD-to-SDD traceability gaps.
>
> Do not echo what the document says. Do not confirm. Find what is missing. Write directly to [output path].

### 8. Present

After writing both the body and the Open Items chunk, surface to the user:

- Project name, version, mode (chunks / combined), file paths.
- Number of chunks (if chunks mode) or section count (if combined).
- Number of Miro diagrams created and the board URL.
- Count of inline `[NEEDS CLARIFICATION: ...]` markers — explicit body-level gap inventory, with a list of the SDD-only sections that need the architect's input.
- Count of Open Items (OI-NN) — reviewer-level findings inventory.
- BRD Specs consumption status (when DERIVE-FROM-BRD): "Mission ✓ / Tech Stack ✓ / Roadmap ✓ / Project Type ✓" or flag any sub-section not present in the source BRD.
- One-line offer: "Want me to switch modes?" / "Want me to merge?" / "Want me to fill in section X now that you have decisions?"

### 9. Cross-mode conversion (on explicit request)

| User says | Action |
|---|---|
| "merge", "consolidate", "single file", "full doc" (after chunks exist) | Concatenate per `chunking.md` § Merge handling. Write to `./SDD-[ProjectName]-v[X.X]-MERGED.md`. Keep originals. |
| "split into chunks", "re-chunk this" (when a combined file exists) | Slice by template section per `chunks/*.md` skeleton. Keep the original combined file. |
| "regenerate chunk N", "update section X", "fill in section Y now that I have decisions" | Targeted regeneration of one chunk or section, leaving the rest untouched. Bump the Changes Log. |

---

## Reference files (read these when the situation calls for them)

- `TEMPLATE-COMBINED.md` — the single-file template. Read at the start of any COMBINED-mode generation.
- `chunks/*.md` — the per-chunk template skeletons. Read at the start of any CHUNKS-mode generation.
- `chunking.md` — canonical chunk map, naming convention, merge rules.
- `modes.md` — chunks vs combined behavioural details.
- `transform-detection.md` — rules for deciding generate / transform / derive-from-BRD.
- `source-transformation.md` — how to map SoW or existing-SDD content into this template.
- `brd-to-sdd.md` — explicit BRD→SDD field mapping (the new bit; read this when the source is a BRD).
- `sdd-quality.md` — what makes a substantive section vs a thin one (analogous to fr-quality.md in brd-unifier).
- `miro-diagrams.md` — Miro MCP usage for every diagram the template implies.

---

## Output conventions

- **Project slug**: kebab-case, lowercased, derived from project name.
- **Chunked output folder**: `./sdd-[project-slug]/`.
- **Chunked filenames**: `NN-short-title.md` (two-digit prefix, optional letter for service splits like `10a`, `10b`). See `chunking.md`.
- **Combined output filename**: `SDD-[ProjectName]-v[X.X].md` (PascalCase project name).
- **Merged-from-chunks filename**: `SDD-[ProjectName]-v[X.X]-MERGED.md`.
- **Encoding**: UTF-8, LF line endings.
- **Tables**: pipe-table format, no hard line wrap.

---

## Things this skill never does

- Never emits `.docx`, `.pdf`, `.xlsx`, or any non-Markdown output unless the user explicitly asks.
- Never inlines diagrams as Mermaid as the *primary* artefact unless the user explicitly asks. The embedded templates include Mermaid alternatives as reference, but the Miro board is authoritative.
- Never invents performance targets, version pins, technology choices, or capacity numbers to fill a table. Missing target → `[NEEDS CLARIFICATION: ...]`.
- Never produces a "here's a summary, let me know if you want the full version" preview. Generate the actual deliverable.
- Never silently drops template sections. Empty sections keep their heading and write `Not applicable for this release.` (with a clarification flag if surprising).
- Never modifies the embedded templates (`TEMPLATE-COMBINED.md` or `chunks/*.md`) during a generation run — they are read-only references.
- Never writes a per-service detailed spec from the BRD alone. The BRD doesn't have enough technical specificity; per-service detailed specs (DB Modeling, API list, Event Model) need architect input or are flagged.
