---
name: pre-brd-unifier
description: Generate, transform, or reformat a pre-BRD (the discovery layer that runs before a full BRD) into the user's standardised 22-framework template across five tiers (Idea Definition, Market & Competition, Prioritization, Strategy & Planning, Synthesis). ALWAYS trigger when the user asks to create, generate, author, draft, write, or build a pre-BRD, PRE-BRD, discovery doc, market study, idea validation, or go/no-go analysis. ALSO trigger when asked to transform notes, an idea, a concept brief, or a Scope of Work into the pre-BRD template, or to run a competitor/market-sizing/PESTLE/SWOT/RICE analysis as part of pre-BRD discovery. Runs multi-agent web research (market sizing, competitor scan, macro and internal factor analysis) to fill the frameworks with sourced values. Output is Markdown only by default. Accepts an explicit mode argument: `pre-brd-unifier chunks` produces the multi-file chunked layout; `pre-brd-unifier combined` produces a single consolidated file; `pre-brd-unifier` with no argument prompts (chunks is the default). On demand AFTER the user reviews and approves the Markdown, it exports a styled .xlsx that clones the reference workbook PRE-BRD v1.1.xlsx with identical fonts, settings, sample columns, and live formulas. Excel is never produced from the argument and never before approval.
---

# Pre-BRD Unifier

Author, transform, and unify a pre-BRD: 22 analysis and market-study frameworks across five tiers, ending in a go/no-go Executive Summary Scoreboard. This is the discovery-layer companion to `brd-unifier` — it performs the analysis (market sizing, competitor scan, macro/competitive/internal factors) rather than templating requirements.

The embedded templates in `chunks/` are the authoritative section skeletons. The embedded `reference/PRE-BRD-v1.1.xlsx` is the authoritative styling + formula source for Excel export.

---

## Argument parsing — do this first

`pre-brd-unifier [chunks|combined]`.

| Argument | Action |
|---|---|
| `chunks` | CHUNKS mode, skip the prompt. |
| `combined` | COMBINED mode, skip the prompt. |
| (empty) | Run the interactive prompt below. |
| anything else | State valid options; treat as empty and prompt. |

**Interactive prompt (chunks is default):**

> **Output format?** [chunks / combined] — default `chunks` (press Enter to accept).

Empty / Enter / `y` / `chunks` → CHUNKS. `combined` / `c` / `single` / `one file` → COMBINED. If the user already implied a shape, do not ask.

Excel is NOT a mode. It is the on-demand step in `xlsx-export.md`, run only after the user reviews and approves the Markdown.

---

## Core principles

1. **Templates are authoritative.** `chunks/*.md` define section order and structure; never rename headings.
2. **Markdown only by default.** `.xlsx` only on explicit request after review; never `.docx`/`.pdf`.
3. **Mode is explicit** — from the argument or the prompt.
4. **Research is sourced.** Every researched figure carries a source (see `research-orchestration.md`). Unverifiable values → `[NEEDS CLARIFICATION: <question>]`. Never invent market numbers.
5. **Compute, do not hardcode.** Derived values (RICE, TAM→SAM→SOM, EFAS/IFAS weighted, Porter's averages, Tier-5 composite) come from the stated formulas in `frameworks.md`.
6. **Fill contract.** Write Answer cells only; guidance/sample content is read-only context.
7. **Coherent synthesis.** Tier-5 must follow from the tier-2/3/4 signals; do not contradict them.

---

## Workflow

1. **Resolve mode** (above) and **intent** — generate vs transform per `transform-detection.md`.
2. **Intake — ask at most 4 questions**, skipping anything already in context: product idea/concept; target segment/customer; geography/market footprint; currency + hard constraints.
3. **Research fan-out** per `research-orchestration.md` → a sourced research bundle.
4. **Fill the 22 chunks** tier by tier from the bundle, per `frameworks.md`. Replicate per-persona blocks. Add rows for variable-row frameworks. Flag gaps with `[NEEDS CLARIFICATION: ...]`.
5. **Reviewer pass** (cleared-context) → `23-open-items-and-assumptions-log.md` (chunks) or an appended section (combined). See "Reviewer pass" below.
6. **Present** the Markdown deliverable with an inventory: chunks/sections, `[NEEDS CLARIFICATION]` count, open-items count, source count. **Stop. Do not produce Excel.**
7. **On explicit approval** ("export excel" / "looks good, generate the sheet"): run the export per `xlsx-export.md`.

### Output shapes

- **CHUNKS:** write `./pre-brd-[project-slug]/NN-*.md` using `chunks/*.md` as skeletons; regenerate `00-pre-brd-master.md` as the index.
- **COMBINED:** concatenate the same content into `./PRE-BRD-[ProjectName]-v1.1.md` with a table of contents; no chunk comment blocks.

### Reviewer pass (cleared-context, mandatory)

Dispatch a subagent (`Agent`, `subagent_type: general-purpose`) with no authoring memory. Brief: find unsourced/shaky market numbers, competitor coverage gaps, cross-tier inconsistencies (do SAM/SOM, EFAS/IFAS, Porter's feed the Tier-5 scoreboard coherently?), and weak go/no-go logic. For each finding: Where, Type, Concern, Options (≥2 with tradeoffs), Recommendation, Status: Open. Write to the Open Items log. If it returns zero findings, re-dispatch with stronger adversarial framing.

---

## Output conventions

- Project slug: kebab-case from the project name.
- Chunked folder: `./pre-brd-[project-slug]/`; filenames `NN-short-title.md`.
- Combined: `./PRE-BRD-[ProjectName]-v1.1.md`. Excel: `./PRE-BRD-[ProjectName]-v1.1.xlsx`.
- Encoding UTF-8, LF. Tables are pipe-tables, no hard wrap.

## Things this skill never does

- Never emits Excel from the argument or before the user approves the Markdown.
- Never overwrites guidance or READ-ONLY sample cells, or hardcodes a value where the workbook has a formula.
- Never invents market figures without a source — unverifiable → `[NEEDS CLARIFICATION]`.
- Never modifies the embedded `chunks/*.md` or `reference/PRE-BRD-v1.1.xlsx` during a run.
- Never silently drops a framework; an empty framework keeps its heading with a flagged note.

## Reference files

- `modes.md` — chunks vs combined + conversion.
- `transform-detection.md` — generate vs transform.
- `frameworks.md` — fill contract, classification, formula catalog, Tier-5 propagation.
- `research-orchestration.md` — multi-agent research playbook.
- `xlsx-export.md` — payload schema and the on-demand export procedure.
