# Platform creation and cleanup

Use this branch only when invocation supplied one platform and an unambiguous destination. The platform is the user's system of record; platform duplicate hygiene remains the user's responsibility.

## Process cohorts in dependency order

Process prerequisite cohorts before dependents. Default to one cohort at a time; a user-requested larger batch still has independent transaction and status reporting per cohort. Use sequential item creation unless the platform offers a reliable bulk operation with a durable result for each item.

Map canonical relationships to native platform vocabulary when known. When no faithful native relationship exists, put a short durable-ID relationship list in the item and disclose the limitation. A dependent waits for known prerequisite IDs. Cyclic or otherwise unavoidable relationships may be patched after item creation, but the affected cohort remains incomplete until required patches succeed.

Do not run broad similarity or fingerprint searches for duplicates. When an annotation contains an explicit platform reference, resolve that reference: if it already tracks the work, show it to the user rather than creating another item. For a completed referenced item, ask whether to leave the annotation or treat it as cleanup-only.

## Preview one transaction

Before mutation, show:

- exact destination and visibility when known;
- every lightweight item payload;
- native or generic relationships;
- exact local cleanup diff for every owned annotation.

Each payload contains only a concise imperative title, one short summary derived from the annotation and nearby context, source locations, and minimal relationship references. Include a short excerpt only to resolve ambiguity. For each group containing questions, preserve them in the payload and require their answers as part of the work. For each group containing `RESEARCH`, also include its verified research outcome carrying the completion contract defined in `SKILL.md`. These are the only exceptions to the lightweight payload. Omit all other acceptance criteria, implementation guidance, test plans, and instructions to remove the marker. The location is provenance after cleanup; a compact note may state that the tracking annotation was removed during item creation.

Get explicit approval for the combined transaction: create these items, establish their relationships, then apply these local cleanup edits. Group approval during triage is not creation authorization.

## Create, then clean immediately

For every group in the cohort:

1. Create or resolve the platform item and obtain its durable ID or URL.
2. Establish every relationship required before this cohort can complete.
3. Once all groups owning an annotation have successful durable items and relationships, remove that annotation and its associated text.
4. Make only the smallest punctuation, spacing, blank-line, or empty-comment repair needed. Preserve encoding, line endings, indentation, comment style, unrelated whitespace, and existing working-tree changes.
5. Re-scan edited locations for the intended markers and inspect the diff for unrelated changes. Run lightweight formatting or diagnostics when available; reserve broad tests for edits that can affect executable text.

An annotation shared by several groups or markers is removed once, only after all owners succeed. Cleanup edits the working tree only; staging, commits, and branches require a separate user request.

If cleanup of executable text or structured data is unsafe, pause and ask the user to specify or perform it. Continue only after the user either confirms cleanup or explicitly accepts responsibility for the remaining marker and its duplicate risk.

**Cohort complete:** every selected group has a durable item, required relationships are established, every safely owned annotation is removed, and each user-owned cleanup exception is explicitly acknowledged.

## Fail closed

If platform access or destination resolution fails when first needed, stop. A supplied platform never falls back to list-only mode.

If an item is created but a relationship or cleanup fails, retain the durable ID, preserve every annotation whose ownership transaction is incomplete, and show retry, skip, or abort choices. Do not delete created items automatically. Stop before unrelated later cohorts unless the user explicitly chooses to continue.

Completed cohorts are naturally resumable because their markers are gone. Deferred cleanup, interrupted shared cohorts, and partial creation are not reliably resumable: remaining markers may produce duplicate items in another run. Report that risk prominently with every known durable ID. Do not write hidden state or checkpoints.

## Final status

Report:

- created or resolved durable IDs/URLs;
- relationships established or pending;
- annotations removed and cleanup-only edits;
- user-owned unresolved cleanup;
- skipped, deferred, and rejected groups;
- platform or local failures;
- remaining markers and duplicate risks in the requested scope.

A platform run succeeds when every selected cohort meets its completion criterion. Skipped or deferred groups may remain, but the summary must account for them.
