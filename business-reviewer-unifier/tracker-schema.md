# Tracker Schema

File: `./review-comments-tracker.md` in the project root. Persistent across
sessions; updated after every applied point, never batched to session end.

## Template

```markdown
# Review Comments Tracker

**Created:** YYYY-MM-DD
**Source:** Multi-agent review of <doc list with versions at review time>
**Reviewers:** Business Owner (BO), <Domain> SME (SME), Product Manager (PM),
Principal Architect (PA), Document Consistency (DC)[, add-ons]
**SME domain:** <confirmed domain, e.g., residential compound and community operations>
**Status values:** Pending | Decided | Applied | Partially applied | Rejected | Deferred

| ID | Reviewer | Concern (short) | Target doc(s) | Status | Decision |
|----|----------|-----------------|---------------|--------|----------|
| BO-01 | Business Owner | <short concern> | 01 §7 | Pending | |

**Progress:** <N of M decision points resolved (R raw comments, K merges)>

**Verification pass (YYYY-MM-DD):** <score, remnants found and fixed>

**Versioning (YYYY-MM-DD):** <per-doc version bumps, renames, citation updates>

**Major structural decisions taken during this session:**
1. <decision>
```

## Column rules

- **ID**: `<ROLE>-NN`. Merged findings noted inside the Concern cell of the
  surviving row ("merged with PA-05") and the absorbed row is not listed
  separately.
- **Concern (short)**: one line, readable without the source documents.
- **Target doc(s)**: doc ids + sections, comma-separated; update if apply
  reveals more affected docs than the reviewer cited.
- **Status**: exactly one of the six values. Decided means the user chose
  but edits are not yet made; Applied means every affected doc reflects it.
- **Decision**: filled at decision time; states what was ACTUALLY decided
  (which may exceed or fall short of the recommendation). For Partially
  applied, name the declined parts. For Rejected/Deferred, one-line reason.

## Footer rules

- **Progress** line is recomputed at every update.
- **Verification pass** and **Versioning** blocks are appended by the
  `verify` phase only.
- **Major structural decisions** lists only decisions that changed the
  shape of the plan (phase moves, reclassifications, deployment-model
  corrections), not every applied edit.
