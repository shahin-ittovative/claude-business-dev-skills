---
name: lld-unifier
description: Generate, transform, or reformat Low-Level Design Documents (LLDs) into the user's standardised template. ALWAYS trigger when the user asks to create, generate, author, draft, write, build, or unify an LLD, Low-Level Design, low-level design document, implementation design, detailed design, or service design. ALSO trigger when asked to reverse-engineer an LLD from existing code (from-code mode), or to derive an LLD from an SDD (from-sdd mode), or to compare an SDD against existing code and produce a unified LLD with drift markers (hybrid mode). The skill is bidirectional — it can author the LLD before code exists (greenfield, from SDD/BRD) or after code exists (reverse-engineering). Output is Markdown only. Accepts an explicit shape argument: `lld-unifier chunks` produces multi-file chunked layout (default); `lld-unifier combined` produces a single monolithic LLD. The skill ALWAYS asks the user which mode (from-code / from-sdd / hybrid) at the start of any invocation. When a BRD with `12-specs.md` is reachable, the skill inherits Specs.Project Type (which adjusts the suggested direction) and Specs.Tech Stack (which steers pattern selection and version pinning). The skill orchestrates two specialist agents — `feature-dev:code-explorer` for structural discovery and `code-documentation:docs-architect` for narrative synthesis — to produce implementation-ready LLDs that any AI agent or developer can execute against. Every generation run ends with a post-generation cleared-context reviewer pass producing an `Open Items & Clarifications` chunk that complements the author-generated open-questions index. Diagrams are inline Mermaid by default; Miro links are optional for whiteboard-richer visuals.
---

# LLD Unifier

Author, transform, and unify Low-Level Design Documents (LLDs) into the user's standardised template. This skill encapsulates the full LLD section structure, the per-service implementation chunk convention, the chunking model, the bidirectional input handling (from-code reverse engineering, from-sdd forward design, hybrid drift detection), the design-pattern rationale model, and the inline-Mermaid-first diagram policy.

The embedded templates in this skill folder are the authoritative source — `TEMPLATE-COMBINED.md` for the single-file layout and `chunks/*.md` for the chunked layout.

This skill is the **fourth and final stage** of the user's documentation chain:

```
SoW → BRD (brd-unifier) → SDD (sdd-unifier) → LLD (lld-unifier) → implementation
```

It is the bridge from architectural intent to executable code: the LLD it produces is intended to be consumed directly by an AI implementer (or human developer) to write the source code.

---

## Argument parsing — do this first

The skill is invoked with an optional argument: `lld-unifier [chunks|combined]`.

| Argument received | Meaning | Action |
|---|---|---|
| `chunks` (or empty / Enter / `y` / `yes` / `default`) | Produce multi-file chunked output | Proceed with CHUNKS shape (the default). |
| `combined` (or `c` / `single` / `one file` / `merged`) | Produce a single consolidated `.md` file | Proceed with COMBINED shape. |
| Anything else | Unrecognised | Re-prompt once with the same question; if still unclear, default to CHUNKS and note the fallback in the handoff summary. |

**Important:** the argument controls only the *output shape* (chunks vs combined). The *direction* (from-code / from-sdd / hybrid) is asked separately — see step 2 of the workflow below.

### Interactive shape prompt (when no arg passed)

**CHUNKS is the default.** Ask one question, accept Enter / empty / "y" as confirmation:

> **Output shape?** [chunks / combined] — default `chunks` (press Enter to accept).
>
> - **`chunks`** (default) — multi-file layout; one `.md` per template section grouping. Matches the embedded `chunks/*.md` skeleton.
> - **`combined`** — single monolithic `.md` file matching `TEMPLATE-COMBINED.md`.

Interpretation rules:

- Empty / Enter / `""` / `y` / `yes` / `chunks` / `default` → **CHUNKS shape**.
- `combined` / `c` / `single` / `one file` / `merged` → **COMBINED shape**.
- Anything else → re-prompt once; if still unclear, default to CHUNKS and note the fallback.

If the user has already implied a shape ("give me the full LLD as one file" → `combined`; "split it into chunks" → `chunks`), do NOT ask — proceed with the implied shape and confirm in one short line.

---

## Core principles

1. **Templates are authoritative.** `TEMPLATE-COMBINED.md` and the files under `chunks/` define the section order, naming, and structure. Section headings are never silently renamed.
2. **Markdown only.** No `.docx`, `.pdf`, `.html` unless the user explicitly asks.
3. **Mode is always asked.** Direction (from-code / from-sdd / hybrid) is never silently inferred. See `transform-detection.md` for the prompt and acceptable answers.
4. **Bidirectional by design.** The same template fits whether the LLD is reverse-engineered from code (`from-code`), forward-designed from an SDD/BRD (`from-sdd`), or unified across both (`hybrid`).
5. **Implementation-ready.** The load-bearing chunk is `04-implementation/<service>.md`. Every per-service file must contain enough detail (class signatures, method-level pseudocode, design patterns with rationale, workflow steps) for an AI implementer to scaffold code without further questions.
6. **Design patterns are first-class.** Every applied pattern carries: name, triggering CLAUDE.md rule, roles played by classes/methods, one-line rationale, Mermaid class diagram, pseudocode skeleton. See `pattern-rules.md`.
7. **Diagrams are inline Mermaid by default.** Sequence, state, ERD, class, and pattern diagrams render inline. Miro links are an optional `> Miro: <url>` slot for whiteboard-richer visuals. See `mermaid-diagrams.md`.
8. **Confidence is tiered, not binary.** High-confidence inference: emit clean. Medium: emit + `> Confirm:` flag. Low: emit + `> TODO: <best-guess> — verify`. All flags indexed in `15-open-questions.md`. See `confidence-rules.md`.
9. **Drift is a feature, not a flaw.** In hybrid mode, divergences between SDD intent and code reality are explicitly marked with `⚠ drift` and a `> Drift note:` block. See `hybrid-drift.md`.
10. **Use platform defaults from CLAUDE.md when source is silent.** Java 21 / Spring Boot 3.5+, PostgreSQL 17+, UUIDv7, Kafka, Keycloak, microservices-first, Angular 17+ standalone, constructor injection, records for DTOs, idempotency on money writes, outbox for state-changing events, sagas for cross-service flows, Resilience4j, RFC 9457 errors. These are the user's standing technical defaults.
11. **Chunks are semantic, not size-based.** Never split by line count.
12. **Flag gaps explicitly.** Where the input doesn't cover something the template requires, use `> Confirm:` (medium-confidence inference) or `> TODO: <best-guess> — verify` (low-confidence inference). Never paper over gaps with plausible-sounding invention.

---

## Workflow

### 1. Resolve output shape

Per the Argument parsing section above. Default is CHUNKS.

### 2. Resolve direction (always ask)

Ask exactly this question:

> **Direction?** [from-code / from-sdd / hybrid]
>
> - **`from-code`** — reverse-engineer an LLD from existing source code (point me at a path). Uses `feature-dev:code-explorer` + `code-documentation:docs-architect`.
> - **`from-sdd`** — forward-design an LLD from an SDD (and BRD if linked). Greenfield, before any code exists.
> - **`hybrid`** — both inputs available and the code is complete. Two-pass: generate the from-sdd view, generate the from-code view, then unify into a single LLD with inline drift markers.

Acceptable shorthands:
- `code` / `from-code` / `reverse` / `1` → from-code.
- `sdd` / `from-sdd` / `design` / `forward` / `greenfield` / `2` → from-sdd.
- `hybrid` / `both` / `drift` / `compare` / `3` → hybrid.

If the user provides only a code path → still ask, but default the prompt to from-code.
If the user provides only an SDD path → still ask, but default the prompt to from-sdd.
If the user provides both → still ask, but default the prompt to hybrid.

See `transform-detection.md` for the full mode resolution rules, including partial-code handling.

### 3. Intake (short — not a clarification storm)

Ask at most **three** questions before starting:

- **Project / system name** — if not stated.
- **Source material** — code path? SDD path? both? Pull from arguments if provided.
- **Direction confirmation** — from step 2.

If an answer is in the conversation, do not re-ask.

### 3a. Read BRD Specs (when reachable)

Before walking the SDD or running code-explorer, check whether the SDD chain links to a BRD with a `Specs` section (chunked: `12-specs.md`; combined: `# Specs`):

- **Specs.Project Type** is consulted by `transform-detection.md` to adjust the suggested direction (greenfield/brownfield steering).
- **Specs.Tech Stack** is the authoritative version pin for the entire LLD. It overrides SDD §6 Ecosystem Overview when they disagree (Specs is the original commitment). It steers pattern selection in `pattern-rules.md`.
- **Specs.Mission** and **Specs.Roadmap** are coarse-grained for LLD purposes — they live in the master index as a pointer but do not drive section content.

See `sdd-to-lld.md` § BRD Specs inheritance for the complete rules.

If no BRD Specs is reachable, proceed with the SDD as the only source. CLAUDE.md defaults fill what the SDD does not pin.

### 4. Plan internally

Enumerate which chunks (or sections, in combined shape) will exist, which workflows will be documented, which services will get their own `04-implementation/<service>.md` chunk, which CLAUDE.md defaults apply, and which sections need confidence flags. The canonical chunk list is in `chunking.md`.

### 5. Dispatch agents (from-code and hybrid only)

For from-code or hybrid direction:

- **Phase 1 — Discovery:** dispatch `feature-dev:code-explorer` agent. Brief it with the target path and a request for: entry points, call graph, dependencies, data tables touched, Kafka topics produced/consumed, structural pattern detection (interface + multiple implementations + context class), and cross-cutting hooks (interceptors/aspects/filters/guards). Capture findings as structured notes with file:line citations.
- **Phase 2 — Synthesis:** dispatch `code-documentation:docs-architect` agent. Brief it with the Phase 1 findings + this skill's section schema. Request per-service narratives, sequence stories, design-pattern rationale (why this pattern, what it solves here), and workflow descriptions.

See `agent-orchestration.md` for the dispatch templates and confidence-weighting rules.

For from-sdd direction: skip Phase 1 + 2. Read the SDD chunks and apply the field mapping in `sdd-to-lld.md` directly.

### 6. Generate / transform / derive output

**CHUNKS shape (default):**

- Use `chunks/*.md` as the section skeleton.
- Write output to `./lld-[project-slug]/`.
- Each chunk starts with the self-describing comment block (see `chunking.md`).
- For chunk 04, produce one file per service: `04-implementation/[service-slug].md`. Cross-service sagas live with the orchestrator service's file.

**COMBINED shape:**

- Use `TEMPLATE-COMBINED.md` as the structure.
- Write output to `./LLD-[ProjectName]-v[X.X].md`.

**FROM-CODE direction:**

- Phase 1 + 2 outputs are template-fitted into the chunks per `code-extraction.md`.
- Structural claims (call graph, schema, topic names) default to high confidence.
- Semantic claims (business rule narrative, pattern rationale) default to medium confidence unless cross-validated.
- Every applied pattern annotated per `pattern-rules.md`.

**FROM-SDD direction:**

- Read the SDD chunks (and BRD if linked) per `sdd-to-lld.md`.
- Apply CLAUDE.md defaults aggressively (constructor injection, records DTOs, idempotency on money writes, outbox for state changes, sagas for cross-service, Resilience4j, multi-tenant indexes, RFC 9457 errors, OpenAPI versioning, Flyway migrations, etc.).
- Each pattern annotated with triggering CLAUDE.md rule + service-specific rationale + Mermaid class diagram + pseudocode skeleton.
- Sections that the SDD and CLAUDE.md cannot together fill (SLOs, threat notes, peak scenario multipliers) → `> TODO: <best-guess> — verify`.

**HYBRID direction:**

- Run from-sdd pass internally → "designed" view per section.
- Run from-code pass internally → "built" view per section.
- Section-by-section diff per `hybrid-drift.md`:
  - Match → no marker (✅ implicit).
  - Differ → emit reconciled content + `⚠ drift` marker + `> Drift note: SDD says X, code does Y.`
  - Code-only → `🆕 code-only` marker.
  - SDD-only → `⛔ sdd-only` marker.
- Emit single unified LLD (no separate `designed/` / `built/` folders).
- `15-open-questions.md` indexes every drift marker with location + severity.

**PARTIAL CODE + SDD:**

- Skill identifies which services have code (uses `code-explorer` to enumerate existing service modules).
- Run from-code pass on existing services.
- Missing services → `> TODO: not yet built (SDD-described — see SDD §13.2.X)` placeholder in `04-implementation/<service>.md`.
- Note the partial-code choice in `15-open-questions.md`.

### 7. Post-generation review (mandatory, cleared-context)

After the body of the LLD is written but **before** presenting to the user, run an adversarial review pass that produces the `Open Items & Clarifications` chunk (`17-open-items-and-clarifications.md` / `# 20. Open Items & Clarifications` section in combined shape).

**Why cleared context.** The author's `> Confirm:` and `> TODO:` flags (indexed in chunk 15) capture gaps the author *recognised*. The reviewer's job is to find gaps the author *did not recognise*: edge cases assumed away, error paths silently dropped, pattern applications that look wrong, concurrency hazards in the pseudocode, multi-tenancy leaks in the data access layer.

Chunk 15 (Open Questions) and chunk 17 (Open Items & Clarifications) coexist:

- **Chunk 15** = author-generated index of inline confidence flags. Lives alongside the body.
- **Chunk 17** = reviewer-generated external findings with options. Independent of the body.

**How to run it.**

1. Use the `Agent` tool with `subagent_type: comprehensive-review:full-review` (preferred, broad review surface) or `general-purpose`. Subagent starts with no conversation memory.
2. Pass the subagent:
   - Absolute paths to all generated LLD chunks (or the combined file), **including chunk 15** so the reviewer can see what the author already flagged and avoid duplicating.
   - Path to the source SDD (and BRD `12-specs.md` if reachable) for cross-validation.
   - Path to this skill's templates.
   - Path to CLAUDE.md so the reviewer can spot pattern misapplication and rule violations.
   - The brief: identify implementation-level gaps, missing edge cases, pattern misapplications, untested error paths, concurrency hazards, transaction boundary issues, idempotency gaps, multi-tenancy leaks, and test gaps. For hybrid mode, also flag drift between the from-sdd and from-code views that wasn't already captured. For each finding, propose 2-3 concrete options with one-line tradeoffs and a recommendation. Output goes into the chunk/section using the schema in `chunks/17-open-items-and-clarifications.md`.
   - Constraint: External findings only. Do not duplicate items already in chunk 15.
3. The subagent writes directly to `17-open-items-and-clarifications.md` (chunks shape) or the `# 20. Open Items & Clarifications` section (combined shape).
4. Verify coverage: at minimum one OI per major implementation risk surface (per service: error handling, transactions, idempotency, multi-tenancy, observability hooks, test coverage). Zero OIs typically means a confirmatory review — re-dispatch with stronger adversarial framing.

**Reviewer prompt skeleton (adapt per project):**

> You are an independent adversarial reviewer for a Low-Level Design Document. You have no memory of how this document was authored. Your job is to find what is missing, ambiguous, or risky in the implementation specification — not to confirm what is present.
>
> Read these files: [LLD paths, including chunk 15]. Cross-reference: [SDD paths] and [BRD Specs path if available]. Honour the rules in [CLAUDE.md path].
>
> Skip anything already flagged in chunk 15 (`> Confirm:` / `> TODO:` items). Your job is to find what the author *did not* flag.
>
> For each implementation gap, missing edge case, pattern misapplication, error path issue, concurrency hazard, transaction boundary problem, idempotency gap, multi-tenancy leak, or test gap, write an OI entry following the schema in [chunks/17-open-items-and-clarifications.md path]. Each entry must include: Where (service / sub-section), Type, Concern (one paragraph), Options (at least 2 with tradeoffs), Recommendation, Status: Open.
>
> Cover at minimum (per service): error envelope completeness vs RFC 9457, transaction propagation correctness, idempotency on money/wallet/external-side-effect operations, multi-tenancy filtering on every query, outbox correctness for state-changing events, saga compensation paths, retry/backoff correctness with Resilience4j, observability instrumentation completeness, contract/integration/e2e test coverage, OpenAPI/event-schema versioning. For hybrid mode also flag undocumented drift.
>
> Do not echo what the document says. Do not confirm. Find what is missing. Write directly to [output path].

### 8. Present

After writing both the body and the Open Items chunk, surface to the user:

- Project name, version, shape (chunks / combined), direction (from-code / from-sdd / hybrid / partial), file paths.
- Number of chunks (if chunks shape) or section count (if combined).
- Number of services covered + names.
- Count of inline Mermaid diagrams generated.
- Count of `⚠ drift` / `🆕 code-only` / `⛔ sdd-only` markers (hybrid only).
- Count of `> Confirm:` and `> TODO:` flags, indexed in `15-open-questions.md` (author-flagged).
- Count of Open Items (OI-NN) in `17-open-items-and-clarifications.md` (reviewer-flagged).
- BRD Specs inheritance status: "Project Type ✓ / Tech Stack ✓" or flag absence.
- One-line offer: "Want me to switch shape?" / "Want me to merge?" / "Want me to fill in section X now that you have decisions?" / "Want me to re-run from-code now that the missing services are scaffolded?"

### 9. Cross-shape conversion (on explicit request)

| User says | Action |
|---|---|
| "merge", "consolidate", "single file", "full doc" (after chunks exist) | Concatenate per `chunking.md` § Merge handling. Write to `./LLD-[ProjectName]-v[X.X]-MERGED.md`. Keep originals. |
| "split into chunks", "re-chunk this" (when a combined file exists) | Slice by template section per `chunks/*.md` skeleton. Keep the original combined file. |
| "regenerate chunk N", "update section X", "fill in service Y" | Targeted regeneration, leaving the rest untouched. Bump the Changes Log. |
| "re-run from-code" (after partial → full) | Re-dispatch agents on the now-complete code; re-fit; bump version. |

---

## Reference files (read these when the situation calls for them)

- `TEMPLATE-COMBINED.md` — the single-file template. Read at the start of any COMBINED-shape generation.
- `chunks/*.md` — the per-chunk template skeletons. Read at the start of any CHUNKS-shape generation.
- `chunks/lld-master.md` — the master index template; regenerate per project.
- `chunking.md` — canonical chunk map, naming convention, merge rules, per-service split.
- `modes.md` — chunks vs combined behavioural details.
- `transform-detection.md` — rules for asking the direction question and resolving partial-code cases.
- `sdd-to-lld.md` — explicit SDD→LLD field mapping (read in from-sdd direction).
- `code-extraction.md` — how to drive `code-explorer` + `docs-architect` to populate the LLD (read in from-code direction).
- `hybrid-drift.md` — two-pass orchestration + section-by-section diff rules (read in hybrid direction).
- `pattern-rules.md` — CLAUDE.md design rules → triggering conditions for from-sdd; pattern-detection heuristics for from-code.
- `confidence-rules.md` — high/medium/low tiering + structural-vs-semantic weighting.
- `lld-quality.md` — what makes a substantive section vs a thin one (analogous to `sdd-quality.md` in sdd-unifier).
- `mermaid-diagrams.md` — diagram conventions, which diagrams default to inline Mermaid vs Miro link.
- `agent-orchestration.md` — dispatch templates for `feature-dev:code-explorer` and `code-documentation:docs-architect`.

---

## Output conventions

- **Project slug**: kebab-case, lowercased, derived from project name.
- **Chunked output folder**: `./lld-[project-slug]/`.
- **Per-service implementation files**: `./lld-[project-slug]/04-implementation/[service-slug].md` — one per service. The folder is created even if there is only one service.
- **Chunked filenames**: `NN-short-title.md` (two-digit prefix). See `chunking.md`.
- **Combined output filename**: `LLD-[ProjectName]-v[X.X].md` (PascalCase project name).
- **Merged-from-chunks filename**: `LLD-[ProjectName]-v[X.X]-MERGED.md`.
- **Encoding**: UTF-8, LF line endings.
- **Tables**: pipe-table format, no hard line wrap.

---

## Things this skill never does

- Never emits `.docx`, `.pdf`, `.xlsx`, or any non-Markdown output unless the user explicitly asks.
- Never silently picks a direction (from-code / from-sdd / hybrid). Always asks per step 2.
- Never invents class names, method signatures, table columns, topic names, or version pins to fill a section. Missing detail → confidence flag.
- Never produces a "here's a summary, let me know if you want the full version" preview. Generates the actual deliverable.
- Never silently drops template sections. Empty sections keep their heading and write `Not applicable for this service.` (with a flag if surprising).
- Never modifies the embedded templates (`TEMPLATE-COMBINED.md` or `chunks/*.md`) during a generation run — they are read-only references.
- Never writes the conditional `14-frontend.md` chunk when the target has no UI surface. The chunk is omitted, not stubbed.
- Never blocks low-confidence sections from emitting. Per `confidence-rules.md`, low confidence emits with a `> TODO:` flag plus best-guess content. The reviewer decides what to keep.
- Never ignores CLAUDE.md defaults in from-sdd direction. If a CLAUDE.md rule applies, it is applied with attribution.
