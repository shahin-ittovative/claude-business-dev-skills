# Apply and Verify

## Apply (per point, immediately after the decision)

1. **Enumerate affected docs before editing.** Start from the tracker's
   Target doc(s) cell, then sweep: which other chain documents restate,
   cite, count, or depend on the changed content? Include downstream BRD
   and SDD chunk files that cite the changed sections or decision IDs.
2. **Edit all affected docs in the same step.** Counts (services, phases,
   batches, milestones), ID lists, tables, and cross-citations must be
   consistent chain-wide before the point is marked Applied.
3. **Supersession notes.** When a decision overrides an earlier statement
   in another doc, state the supersession on BOTH sides rather than
   silently editing one.
4. **Update the tracker row in the same step**: Status (Applied /
   Partially applied / Rejected / Deferred) and the Decision cell stating
   what was actually done. Recompute the Progress line.
5. **Partial acceptance**: apply the accepted parts, leave declined parts
   untouched, and name the declined parts in the Decision cell.

## Verify (after all points are closed)

Dispatch one fresh cleared-context subagent (`Agent`,
`subagent_type: general-purpose`). Brief:

> All decisions in [tracker path] have been applied to [doc paths]. Hunt
> for stale remnants of those changes: counts and enumerations that no
> longer add up; batch/phase/milestone numbers not updated everywhere;
> section and table references that resolve wrongly; phrasing that
> reflects a superseded decision; citations to renamed files; scope
> statements contradicted by an applied decision. For each remnant:
> Where, what is stale, what it should say. Score overall chain
> consistency out of 10. Do not re-litigate the decisions themselves.

Fix every confirmed remnant, then append the **Verification pass** block
to the tracker: date, score, remnant count, one line per fix. If the
agent returns zero remnants on a session with structural changes, treat
it as suspect and re-dispatch once with the stale-remnant categories
spelled out.

## Versioning checklist (after verification)

- [ ] Bump the in-document version of every edited doc.
- [ ] Add a changelog entry to each edited doc header naming the review
      session date and `review-comments-tracker.md` as the decision log.
- [ ] Rename files whose filename carries the version; note in headers
      that historical in-text citations to the old version remain valid
      if section numbering is unchanged.
- [ ] Sweep ALL project docs (including BRD/SDD chunk folders) for
      citations to the renamed files and update them.
- [ ] Append the **Versioning** block to the tracker.
- [ ] Present the close-out summary: points by status, structural
      decisions list, files touched, new versions.
