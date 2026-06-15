<!--
PRE-BRD CHUNK: 22
TITLE: Executive Summary Scoreboard
TIER: Tier 5: Synthesis
PART OF: PRE-BRD Master
-->

# Executive Summary Scoreboard

A go / no-go synthesis. It aggregates five signals (market attractiveness, problem / solution fit, feasibility, competitive risk, strategic fit) from the analysis tiers above into a single weighted composite and a recommendation. Generate it only after its dependencies are filled.

## Dependencies to verify before scoring

| Dependency | Source file | Ready? |
|---|---|---|
| Product name | 01 Concept Sheet / 02 Product Charter |  |
| Market Sizing (SAM / SOM) | 07 Market Sizing and Analysis |  |
| EFAS ratings & weights | 10 EFAS |  |
| IFAS ratings & weights | 11 IFAS |  |
| Porter's threat levels | 09 Porter's Five Forces |  |

## Method

Each score is derived from the analysis files (not entered by hand). Scale is 1 to 5: 5 = strong / favorable, 1 = weak / unfavorable. Competitive Risk is inverted (high threat maps to a low score). Composite = sum of (score x weight). Verdict thresholds: 4.0 and above = Go; 3.0 to 3.99 = Conditional Go; below 3.0 = No-Go. Weights must sum to 1. End with a one-line **sensitivity check**: state whether a single ±1 notch on any one signal would flip the verdict band, so the recommendation's robustness (or fragility) is visible rather than implied by suspiciously tidy scores.

## Scoreboard

| Signal dimension | Source signal | Score (1 to 5) | Weight | Weighted | Rationale |
|---|---|---|---|---|---|
| Market Attractiveness | Market Sizing (SAM / SOM) |  | 0.2 |  |  |
| Problem / Solution Fit | EFAS opportunity strength |  | 0.2 |  |  |
| Feasibility | IFAS strength rating |  | 0.2 |  |  |
| Competitive Risk | Porter's threat (inverted) |  | 0.2 |  |  |
| Strategic Fit | EFAS + IFAS blended (VRIO context) |  | 0.2 |  |  |
| **Composite** |  |  | **1.0** |  | Weights sum to 100% |

## Result

| Composite score | Recommendation |
|---|---|
|  |  |

## Conditions to resolve before full commitment

Add prioritized conditions derived from the weakest signals (for example: thin competitive moat per VRIO, modest market ceiling per Market Sizing, prioritization inputs not yet populated per RICE).

| # | Condition | Source signal |
|---|---|---|
|  |  |  |

## Sources

Market Sizing (SAM / SOM), EFAS (opportunities), IFAS (strengths), Porter's Five Forces, VRIO / SWOT. Update any source file and regenerate this scoreboard.
