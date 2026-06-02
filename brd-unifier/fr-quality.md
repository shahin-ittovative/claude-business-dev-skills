# FR Quality

The Functional Requirements section is where a BRD earns (or loses) its usefulness to the delivery team. This file captures what a good FR block looks like so the skill produces genuinely substantive FRs, not template-shaped filler.

## The FR block structure

Every FR block has five sub-sections in this order (per the template):

1. **What** — definition of the capability.
2. **Why** — business rationale.
3. **How** — behaviour, steps, sub-flows.
4. **Constraints** — conditions, pre-conditions, limitations.
5. **Future Enhancements** — near-term follow-ups that are in-scope-adjacent.
6. **UI/UX** — wireframe reference.

Each sub-section has a quality bar. The tests below help you recognise whether you've met that bar.

## What — the definition

**Good:** One to three sentences that an engineer new to the project can read and understand the scope of the capability.

> Example: "Extend UNC's Email channel to support the inclusion of file attachments in outbound email notifications. Source systems shall reference attachments by URL, by inline base64 payload, or by a pre-uploaded content reference identifier stored in UNC's content store."

**Bad:**
- Restating the FR title ("Email Attachment Support is the ability to support email attachments.")
- Vague aspirational language ("Provide a world-class attachment experience.")
- A single sentence that could apply to many different FRs ("Allow users to perform operations on attachments.")

**Test:** If you deleted the FR title and someone read only the `What`, could they tell what the FR is about? If not, rewrite.

## Why — the business rationale

**Good:** Connects the capability to a Business Objective or a specific business pain. References the Problem Statement / Background where relevant.

> Example: "Several source systems require the delivery of business documents (invoices, tickets, policy documents) alongside email content. Today these systems either bypass UNC or embed document links into the email body, reducing deliverability and undermining the unified-governance objective."

**Bad:**
- "To improve user experience." (Whose? In what way? Measurable how?)
- "Industry best practice." (Not a rationale. Why do *we* need it?)
- A mirror of the `What`. ("To enable email attachments, we support email attachments.")

**Test:** If the user reads the `Why` and asks "so what?", the `Why` hasn't done its job.

## How — the behaviour

This is where the FR is won or lost. The `How` is the contract between the BRD and the engineering team.

**Good:** Concrete, testable bullets describing what the system does, in the order it does it, with branching and error cases named. For complex FRs, split into numbered sub-flows (FR-01.1, FR-01.2, ...).

> Example bullets:
> - Support attachments on all Email notification API endpoints (Kafka and REST) with a versioned schema extension.
> - Accept attachments via URL reference, inline base64, or pre-uploaded content store reference.
> - Enforce per-client limits on: count per email, total size, individual size, allowed MIME types.
> - Virus-scan every attachment before dispatch; reject infected, oversize, or disallowed-MIME attachments with a descriptive error code and audit log entry.
> - Log attachment metadata (name, size, content-type, SHA-256 hash) in the notification audit trail.

**Bad:**
- "The system shall support X." (Restates the `What` as a bullet.)
- Implementation choices masquerading as behaviour. ("Use ClamAV for virus scanning" belongs in Constraints or in `Definitions & Important Details`, not in `How`.)
- Technology-only content. ("Kafka topic shall be `email.attachments`.") — that's a design decision, and belongs in a design document, not an FR.
- Three bullets for a complex capability. A meaningful `How` for a non-trivial FR is typically 6–12 bullets or a sub-flow per significant interaction.

**Test:** Could a QA engineer write test cases directly from your `How` without having to guess? If each bullet can map to 1–3 test cases, the `How` is well-sized.

## Constraints — conditions and limits

**Good:** Specific pre-conditions, external dependencies, volume limits, tenancy scoping, retention rules. Ideally, each constraint is a testable fact.

> Example:
> - Attachment retention window: configurable per client; default 7 calendar days.
> - Maximum individual attachment size: configurable per client; platform maximum 25 MB.
> - Cross-team dependency: stc SMTP relay must be configured to accept the maximum attachment size.

**Bad:**
- "Must be secure." (Vague.)
- "Must perform well." (Cite an NFR or give a number.)
- Repetition of the `How`.

**Test:** If the constraint were violated, would that be a bug? If yes, good constraint. If not — it's aspirational, not a constraint.

## Future Enhancements — near-term follow-ups

**Good:** Concrete, scoped items that a developer could optionally complete if there's time. Each is a candidate for the next release.

> Example:
> - Add support for inline-image attachments rendered in the email body (currently attachments only appear as standard MIME parts).
> - Support scheduled attachment expiry notifications to the source system.

**Bad:**
- Grand vision statements ("In the future, leverage AI to...").
- Items that are actually out-of-scope-forever, not enhancements.
- An empty list — if there really are no enhancements, write `- None identified at this time.`

**Test:** Could a developer pick up the enhancement in a sprint after the base FR ships? If yes, good enhancement. If no, it belongs in the Wishlist at the end of the BRD.

## UI/UX — wireframe references

**Good:** A Figma link (preferred), a Miro frame link to a wireframe sketch, or (if neither exists) a written reference to the global UI/UX Expectations section plus a note that a wireframe is pending.

**Bad:**
- ASCII wireframes.
- Leaving the section empty.
- "TBD" with no follow-up note.

**Test:** Can a UI developer start from this section and know where to look? If yes, good.

## FR granularity — when to split

Split one FR into multiple when any of these is true:

- The `How` has two or more genuinely distinct sub-flows that branch on different conditions.
- The FR covers two different actor types with significantly different behaviour.
- The FR covers both a synchronous and an asynchronous path, each non-trivial.

Merge what looks like two FRs into one when:

- The two FRs cannot be implemented or released independently.
- One is obviously a prerequisite for the other with no standalone value.

## FR numbering and IDs

- Numbering is sequential per BRD: `FR-01`, `FR-02`, …
- Sub-flows: `FR-01.1`, `FR-01.2`, …
- If the source (SoW, RFP, prior conversation) already uses a scheme like `FR-ENH-01`, keep it.
- Never renumber FRs mid-document if the user has seen the prior numbering — renumbering breaks cross-references. If numbering must change, flag it in the changelog.

## What about cross-cutting behaviour?

Observability, logging, audit trail, PDPL compliance, and similar cross-cutting concerns appear in many FRs. Don't repeat them in every `How`. Instead:

1. State the cross-cutting expectation once in `Definitions & Important Details` or `Non-Functional Requirements`.
2. In each FR's `How`, name only the FR-specific aspect ("log attachment metadata in the notification audit trail" — not "implement comprehensive audit logging per the audit framework").
3. In `Constraints`, reference the cross-cutting section if the compliance bound is FR-relevant ("subject to audit retention policy in NFR-07").

This keeps FRs readable while preserving the linkage.
