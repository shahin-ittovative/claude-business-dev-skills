# Research Orchestration — Multi-Agent Fan-Out

Fill the analysis-heavy frameworks with sourced findings by fanning out independent research subagents via the **Agent tool** (parallel, in one message). Each agent returns structured, sourced data; a synthesis step writes the chunks and propagates shared values into the Tier-5 scoreboard.

## Clusters (run concurrently)

1. **Market sizing agent → chunk 07.** Size TAM/SAM/SOM both ways. Top-down: global market → region share → % relevant to segment. Bottom-up: addressable customers × ARPU. Return every figure with a source and the assumption behind it.
2. **Competitor agent → chunk 06.** Identify the top 5 competing products. For each: provider, best-for, key features, pricing model, weaknesses, positioning, tech stack. For the feature tables, mark each feature **Available / Not available** per competitor.
3. **Macro agent → chunks 08, 09.** PESTLE factors (Political, Economic, Social, Technological, Legal, Environmental) and Porter's Five Forces, each force scored 1–3 with a one-line rationale.
4. **Factor agent → chunks 10, 11, 12.** EFAS (opportunities + threats with weight 0–1 and rating 1–5) and IFAS (strengths + weaknesses) → consolidated SWOT.

## Source rules
- Acceptable sources: industry reports (Statista, Gartner, IBISWorld), Crunchbase/PitchBook, Google Trends/Keyword Planner, government statistics portals, vendor sites for competitor facts.
- Every numeric claim cites a source. No source and not derivable → `[NEEDS CLARIFICATION: <question>]`, never a guessed number.
- Prefer recent data; record the year of each figure.

## Output schema per agent
Return JSON-like structured text: the framework's Answer slots, each with `value`, `source`, `year`, and `assumption` (where relevant). The synthesis step maps these into the chunk tables and computes derived values per `frameworks.md`.

## Synthesis and propagation
After all agents return: write chunks 06–12, compute derived totals (Porter's average, EFAS/IFAS weighted sums), then carry the propagation map in `frameworks.md` into the Tier-5 Executive Summary (22). Tiers 1, 3, 4 are authored from intake + the research bundle (they are framing/prioritization, not new web research).

## Optional accelerator
The same playbook can run under the Workflow tool for heavier deterministic pipelines, but only when the user explicitly opts into multi-agent orchestration. Default is Agent-tool fan-out.
