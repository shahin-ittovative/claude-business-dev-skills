# Frameworks - Fill Contract, Classification, Formulas, Propagation

Per-framework structure and guidance live in the embedded `chunks/*.md`. This file holds the cross-cutting rules.

## Fill contract
- Fill **Answer** slots only. Guidance/description/sample text is read-only context.
- Compute derived values from the formulas below - never leave a derived cell blank when its inputs exist, and never hardcode a derived value.
- Unverifiable inputs → `[NEEDS CLARIFICATION: <question>]`.

## Row behaviour (Markdown)

| Behaviour | Frameworks |
|---|---|
| Fixed rows (keep as-is) | PESTLE (08), Porter's (09), SWOT (12), MoSCoW (14), Ansoff (17), Product Lifecycle (20) |
| Variable rows (add as needed) | Market Comparison (06), EFAS (10), IFAS (11), RICE (13), OKRs (15), BCG (16), VRIO (18), Roadmap (21) |
| Per-persona (replicate block per persona) | Value Proposition Canvas (04), Empathy Map (05) |

## Formula catalog
- **Market Sizing (07):** Regional TAM = Global × region share. Serviceable TAM = regional × % relevant. Bottom-up TAM = addressable customers × ARPU. TAM = average(top-down, bottom-up). SAM = TAM × SAM%. SOM = SAM × SOM%.
- **Porter's (09):** each force scored 1 (low threat) to 3 (high threat); overall = average of the five.
- **EFAS (10) / IFAS (11):** weighted = weight × rating per factor; total = sum of weighted scores. Weights within a block sum to 1.0.
- **RICE (13):** score = (Reach × Impact × Confidence) / Effort.
- **Tier-5 Executive Summary (22):** five signals (Market Attractiveness, Problem/Solution Fit, Feasibility, Competitive Risk, Strategic Fit), each scored 1–5 with weight 0.2; composite = Σ(score × weight); recommendation derived from the composite band. Competitive Risk is inverted from Porter's threat (higher threat → lower score).
- **Investor Assessment (23):** seven aspects scored 0–10 by judgment from the source chunks (not derived from a single framework). Weights: Market opportunity 0.20, Problem/solution fit 0.15, Moat 0.15, Business model & economics 0.15, Go-to-market 0.10, Team & execution 0.10, Financial/return 0.15 (sum 1.0). Composite = Σ(score × weight); verdict bands Go ≥ 7.0, Conditional 5.0–6.9, No-Go < 5.0. The composite weighted average is computed; the per-aspect scores are the investor agent's judgment, each cited to its source chunk.

## Tier-5 propagation map (keep signals coherent)
| Tier-5 signal | Source |
|---|---|
| Market Attractiveness | SAM/SOM from 07 |
| Problem / Solution Fit | EFAS opportunity strength (10) |
| Feasibility | IFAS strength rating (11) |
| Competitive Risk | Porter's threat (09), inverted |
| Strategic Fit | EFAS net (10) + IFAS net (11) |

When filling 22, derive each signal from its source framework. If a source is missing or flagged, the corresponding Tier-5 signal inherits a `[NEEDS CLARIFICATION]`.

## Markdown vs Excel row behaviour (important)
The table above governs the **Markdown** output, where any framework may grow freely. The **Excel export** is more constrained - the reference workbook has fixed cross-sheet ranges and READ-ONLY sample blocks that row insertion can break. Therefore on export:
- **EFAS (10), IFAS (11), and VRIO (18) are fixed-row** - the export never inserts rows. EFAS/IFAS feed the scoreboard's fixed ranges (`SUM(EFAS!F2:F6)`, `'IFAS '!E2:E5`); VRIO has formulas in its sample block below the data. Keep the highest-priority items in the sheet and note overflow in the Open Items log.
- **RICE (13) is a single initiative in Excel** - the sheet has one editable column (D4:D7); columns E/F/G are READ-ONLY sample features. Additional RICE initiatives live in the Markdown and are noted as overflow.
- **Market Comparison (06) is fixed-cell** (blank answer blocks with set capacity). **BCG (16), OKRs (15), Roadmap (21)** can extend via `needed_rows`. All fill the provided rows first; overflow is noted. See `xlsx-export.md`.
