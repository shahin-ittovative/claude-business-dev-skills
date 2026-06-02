# Source Transformation — SoW, Existing BRD, or Loose Spec → Unified BRD

This file defines how to translate a source document into the unified BRD template, regardless of whether the source is a SoW, an existing BRD in another format, or a loose product spec.

It exists because the field mapping is not always obvious, and getting it wrong produces a BRD that looks structured but is semantically empty.

For deciding **whether** transformation is the right intent, see `transform-detection.md`. This file covers the **how**.

---

## Overall approach

1. **Read the source in full before writing anything.** The BRD sections are interdependent; you need the whole picture before you can populate Glossary, Assumptions, and Scope coherently.
2. **Classify each source paragraph into a BRD target section.** Keep a running map: "paragraph 3 → Business Objectives; paragraph 4 → Assumptions item 2; paragraph 5 → FR-02.How".
3. **Paraphrase, don't copy.** Source docs are often written for procurement, legal, or vendor audiences. The BRD is written for the delivery team. Same facts, different voice.
4. **Flag gaps immediately.** Every gap becomes a `**[NEEDS CLARIFICATION: <specific question>]**` marker — never an invented plausible value.
5. **Preserve commitments verbatim.** Numbers, dates, percentages, SLAs, named integration points are carried across exactly. "11 source systems currently in SIT" stays "11 source systems currently in SIT", not "approximately a dozen".

---

## Field-by-field mapping (SoW source)

### "Overview" / "Background" / "About the project"

→ **Executive Summary** (top 2–4 paragraphs) AND **Background and Context / Problem Statement**.

Split: the *what this is* portion goes to Executive Summary; the *why this exists / what's broken today* portion goes to Background.

### "Objectives" / "Goals" / "Success criteria"

→ **Business Objectives** as a bulleted list.

If the source states measurable KPIs ("reduce notification latency by 40%", "onboard 30+ systems in Year 1"), carry them with the measurement intact. Don't invent numbers if the source is qualitative.

### "Definitions" / "Glossary" / scattered acronyms

→ **Glossary** table.

Also scan the body for acronyms used without expansion (common offenders: SMSC, IAM, SMPP, TADIG, CPaaS, NCA, PDPL, MSISDN, ICCID, KYC). Every acronym used in the BRD should be in the Glossary.

### "Assumptions" / "Constraints"

→ **Assumptions / Constraints** numbered list.

Source docs often mix assumptions and constraints. The template combines them into one list. Use the short-label format: `**Label**; description.`

### "Dependencies"

→ **Dependencies** table.

Classify each as Hard (blocks delivery) or Soft (affects quality but not blocking). Name the owner team or system where stated.

### "Scope"

The source's Scope section typically has three parts mapping to three BRD sections:

| Source content | BRD destination |
|---|---|
| "The vendor shall deliver..." list of deliverables | **Project Scope → In Scope** |
| Exclusions ("The following are explicitly out of scope...") | **Project Scope → Out of Scope** |
| Functional deliverables described as features/capabilities | **Functional Requirements** (tier + detailed FR blocks) |

The third row is the substantive work — see "FR extraction" below.

### "Deliverables" / "Features" / "Capabilities" / "Requirements"

→ **Functional Requirements**.

Rules:

1. **One FR per distinct capability**, not one FR per paragraph. A source paragraph may contain 3 capabilities; they become 3 FRs.
2. **If the source tiers or waves the deliverables**, carry the tiering into the BRD's tier table. If flat, thematically cluster the FRs and introduce tiers (Tier 1 = foundational, Tier 2 = extensions, Tier 3 = enhancements). Note tier introduction in the intake summary.
3. **For each FR**, populate all blocks per the template:
   - **What** — rewrite the source's statement of the capability as a clear, short definition.
   - **Why** — if the source gives rationale, paraphrase it. If not, derive from Business Objectives and say so, or flag `[NEEDS CLARIFICATION: business rationale for FR-NN]`.
   - **How** — expand the source's bullets into step-level behaviour. Where vague, either infer from domain knowledge (and mark inferred in `Constraints`) or flag gaps.
   - **Constraints** — pre-conditions, limits, assumptions specific to this FR.
   - **Acceptance Criteria** — testable conditions ("Given X, when Y, then Z").
   - **Future Enhancements** — usually not in the source; write `- None identified at this time.` unless the source hints at future waves.
   - **UI/UX** — Figma link, Miro frame link, or pending note.
4. **Preserve FR numbering from the source** if it numbers them (`FR-ENH-01`, etc.). Otherwise number `FR-01`, `FR-02`, … in tier order.

See `fr-quality.md` for what a substantive FR block looks like.

### "Technical requirements" / "Architecture expectations" / "Standards"

→ Distributed across multiple BRD sections:

| Source content | BRD destination |
|---|---|
| Architecture principles (Kafka-first, API-first, multi-tenant) | **Definitions & Important Details** OR **Technical Implementation Expectations** |
| Integration standards (OpenAPI, Avro, mTLS, OAuth) | **Integrations** AND **Technical Implementation Expectations** |
| Specific technology mandates (Java 21, Spring Boot, PostgreSQL, UUIDv7) | **Technical Implementation Expectations** |
| Observability requirements | Either a dedicated FR (if substantial) or **Non-Functional Requirements** |

### "Non-Functional Requirements" / "Performance" / "Availability" / "Security"

→ **Non-Functional Requirements** table.

Convert each NFR into a row: `NFR-NN | <category> | <name> | <target>`. Carry concrete targets verbatim. If the source names a requirement without a target ("the system shall be highly available"), flag it: `NFR-NN | Availability | Availability | [NEEDS CLARIFICATION: specific monthly availability target]`.

### "Integrations" / "Interfaces" / "Connected systems"

→ **Integrations** section.

List every system the target system must interact with, the integration mechanism (Kafka topic, REST endpoint, SMPP bind, SFTP, file drop), the direction (inbound / outbound / bidirectional), data format, auth mechanism, SLA, and status (live / in progress / planned).

### "Reporting" / "Dashboards" / "Analytics"

→ **Reporting / Analytics** section.

Convert reporting expectations into a list of named reports or dashboards with data source, audience, refresh frequency, and format. If the source only says "reporting shall be provided", flag it.

### "Personas" / "Users" / "Actors" (often implicit)

→ **Personas / Actors** section.

If not named explicitly, infer from role references scattered across the text ("operator", "campaign owner", "client admin", "external tenant"). Flag if no persona-level info available.

### "Timeline" / "Milestones" / "Phases"

Timelines typically do **not** go into the BRD — the BRD describes the system, not the programme plan. Exception: if a phase fundamentally changes system behaviour (e.g., "in Phase 2, multi-tenancy is enabled"), that's an FR or domain concept.

### "Commercial" / "Pricing" / "Payment terms"

These do **not** go into the BRD. Ignore for BRD purposes.

### "Risks" / "Issues"

→ **Challenges** section.

Paraphrase each risk as a challenge. If the source provides evidence (data samples, failure examples), lift it into the challenge evidence table.

---

## Field-by-field mapping (existing-BRD source — different format / template)

When the source is already a BRD but in a different format (vendor template, IEEE 830/29148 style, Volere, Notion-flavoured, custom), the mapping is more direct because the source already has structure. The job is re-architecting, not extraction.

### Approach

1. **Build a section crosswalk first.** Before writing, map each source section to a target template section. Note any source sections with no target (often: change history in non-standard format, sign-off pages, glossary in a non-tabular format).
2. **For sections present in both:** carry content across, restructure to match the template's expected sub-structure (e.g., FR blocks must use `What/Why/How/Constraints/Acceptance Criteria/Future Enhancements/UI/UX`).
3. **For target sections missing from source:** flag the entire section with `[NEEDS CLARIFICATION: section X is required by the template but not present in the source]` and produce a stub with the heading.
4. **For source sections with no target:** evaluate — is the content useful? If yes, find the closest target section. If not (vendor sign-off block, commercial appendix), drop it and note in the handoff.

### Common source-format peculiarities

| Source format | Peculiarity | Handling |
|---|---|---|
| IEEE 830 / 29148 | "Specific Requirements" with deeply nested numbering | Flatten to FR-NN; preserve the numbering in a `Source-ref:` line in `Constraints` if traceability is needed. |
| Volere | "Shells" per requirement with ratings/priorities | Map shell content to `What/Why/How`; rating becomes priority tier. |
| Vendor / RFP-derived BRD | Vendor-obligation language, sign-off blocks | Rewrite to system voice (see "Voice and audience" below). Drop sign-off. |
| Notion / Confluence export | Mixed concerns per page, embedded design references | Re-classify each page into the target sections. Keep design-tool links. |
| Old version of this same template | Stale section names or missing sub-sections | Migrate to current template; bump version in Changes Log with "Migrated to template version X.Y". |

---

## Field-by-field mapping (loose product spec / informal source)

When the source has no clear structure (Notion brain-dump, single-page brief, scattered notes):

1. **Read everything first.** Identify the dominant content types: features (→ FRs), constraints (→ Assumptions / Constraints), goals (→ Business Objectives), risks (→ Challenges).
2. **Classify paragraph by paragraph.** Heavier classification work than SoW transformation, because the source isn't pre-sorted.
3. **Expect more gaps.** Loose specs typically have no NFRs, no formal personas, no integration table. Each missing piece is a `[NEEDS CLARIFICATION: ...]` marker.
4. **The Glossary will need active construction.** Loose specs use jargon without defining it; you'll need to identify terms and either define them or flag for clarification.

---

## Handling voice and audience shift

Source docs are often written for one audience; the BRD targets the delivery team.

| Source voice | Target voice |
|---|---|
| "The Vendor shall implement X" (SoW) | "The system shall support X" or "X is implemented as follows: ..." |
| "The Supplier is responsible for ensuring Y" | "Y is ensured by ..." (active, system-as-subject) |
| "The user/client should be able to..." (vague capability) | "The system supports the following capability: ..." (concrete behaviour) |
| Passive contractual hedging ("It is intended that...") | Direct: "The system does X." |

Drop vendor-obligation scaffolding; keep the behavioural commitment.

---

## Gap inventory

At the end of any transformation, produce an internal count of `[NEEDS CLARIFICATION: ...]` markers and surface the total in the handoff summary.

Thresholds:

- **0–5 markers:** healthy. The source was rich; minimal follow-up needed.
- **6–14 markers:** typical. Recommend the user review and close before circulating.
- **15+ markers:** the source is significantly under-specified for this template. Recommend a clarification session **before** the BRD is circulated for review. Do not soften this recommendation — a BRD with 15+ open clarifications is not review-ready.

---

## RFP-specific notes

If the source is an RFP scope (the BRD is being derived from procurement language), the following do **not** carry into the BRD — they are RFP artefacts, not system requirements:

- Vendor Response Required bullets
- Eligibility criteria
- Evaluation methodology
- Commercial framework
- Bid bond / performance bond clauses
- Vendor company / capability sections

Only the system-behaviour scope of the RFP transfers.

---

## Sanity checks before completion

Before declaring the transformation done, verify:

- [ ] Every FR has all required sub-sections (`What/Why/How/Constraints/Acceptance Criteria/Future Enhancements/UI/UX`).
- [ ] The Glossary covers every acronym used in the BRD.
- [ ] The Changes Log has a row for this transformation (version bumped if migrating from a prior version).
- [ ] The Figures index lists every Miro figure referenced.
- [ ] No section was silently dropped — empty sections still have their heading plus an explanation.
- [ ] No invented numbers — every quantitative claim traces to source or carries a `[NEEDS CLARIFICATION: ...]` marker.
