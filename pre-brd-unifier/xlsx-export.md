# Excel Export — On Demand, Post-Approval

Excel is produced only when the user explicitly approves the reviewed Markdown and asks for the sheet ("export excel" / "looks good, generate the sheet"). Never from the mode argument; never before review.

## How it works
The export clones `reference/PRE-BRD-v1.1.xlsx` and writes generated values into Answer cells only. The workbook's 81 live formulas, READ-ONLY sample columns, Calibri 11 styling, dark-blue header bands, and Executive Summary control panel are preserved. Writable cells are whitelisted in `reference/cell-map.json`; the engine (`scripts/export_xlsx.py`) refuses to write anywhere else.

## Procedure
1. Build the payload (below) from the approved Markdown chunks: for each framework, read the Answer values and map them to the cells the cell map declares writable.
2. Run the engine:

```powershell
python -c "import sys,json; sys.path.insert(0,r'C:\Users\negat\.claude\skills\pre-brd-unifier\scripts'); import export_xlsx as ex; ex.export(json.load(open(r'<payload.json>',encoding='utf-8')), r'<dst>\PRE-BRD-<ProjectName>-v1.1.xlsx')"
```

3. Report the output path and tell the user to open it in Excel (formulas recompute on open; the control-panel switch is set to YES so the scoreboard computes).

## Payload schema

```json
{
  "control_panel": true,
  "sheets": {
    "RICE Framework": { "answers": { "D4": 1000, "D5": 2, "D6": 0.8, "D7": 5 } },
    "Roadmap & project plan": {
      "needed_rows": 16,
      "answers": { "B3": "Q3-2026", "C3": "Launch MVP", "D3": "Core booking", "F3": "Eng", "G3": "Planned" }
    }
  }
}
```

- `answers`: `cell -> value`. The engine rejects any cell not whitelisted for that sheet.
- `needed_rows`: optional, and ONLY honoured for the genuinely extendable sheets — `BCG Matrix`, `Objectives Key Results (OKRs)`, `Roadmap & project plan`. The engine inserts rows there, cloning the template row's style/formula pattern. EFAS, IFAS, and VRIO are **fixed** (no insertion) and ignore `needed_rows` — see the limits below.
- `control_panel`: `true` flips the Executive Summary switch (`C5 → "YES"`) so the composite/recommendation compute.

### Answer cells are cleared before filling
The engine **blanks every whitelisted answer cell across ALL mapped sheets first, then writes the payload values.** This is deliberate: the reference workbook ships pre-filled with an example, and a global clear guarantees no leftover example value survives — not in a filled sheet, and not in a sheet the payload omits. Two consequences for the payload-builder:
1. **Build the payload from all 22 chunks.** Any sheet you leave out of the payload comes out blank in the workbook. To produce a complete pre-BRD, map every framework's answers — not just the analytical ones.
2. **Supply every real value for any row you use.** An omitted weight/rating leaves that cell blank and contributes 0 to the composite, so fill complete rows.

## Sheet-name reference
Use exact workbook sheet names in the payload. Note `IFAS ` has a **trailing space**. Other multi-word names: `Market Sizing & analysis`, `Porter's Five Forces`, `Objectives Key Results (OKRs)`, `Roadmap & project plan`, `Product Strategy Canvas`, `Value Proposition Canvas`.

## Guardrails and known limits
- Sheet names in the payload must match the workbook exactly; unknown sheet → `ExportError`.
- Do not write derived/formula cells — the engine raises if you try.
- The reference workbook and cell map are read-only inputs; never edit them to make a payload fit — fix the payload.
- **EFAS / IFAS / VRIO are fixed-row on export** (no insertion). EFAS and IFAS feed fixed cross-sheet ranges in the scoreboard, so inserting rows would drop factors from the go/no-go composite. VRIO's READ-ONLY sample block sits below its data and contains formulas that openpyxl cannot re-reference on insert. For all three, fill within the provided rows and record any overflow in the Open Items log.
- **Market Comparison is fixed-cell** (the four answer blocks ship blank with set row capacity). **BCG / OKRs / Roadmap** may insert rows via `needed_rows`. For any of these, if the Markdown has more competitors/features/initiatives than the sheet holds, fill the most material ones and note the overflow; never silently truncate.
