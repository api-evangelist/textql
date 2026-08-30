---
name: textql-govern-an-ontology-change
description: Propose, review, approve, deny and restore a change to the TextQL Ontology — the one surface on this platform with a real review workflow and optimistic concurrency.
api: TextQL v2 API
base_url: https://app.textql.com/v2
operations:
  - v2.startSandbox
  - v2.ontologyDiff
  - v2.createOntologyChange
  - v2.listChanges
  - v2.getChange
  - v2.approveChange
  - v2.denyChange
  - v2.restoreChange
  - v2.stopSandbox
generated: '2026-08-30'
method: generated
source: >-
  openapi/textql-v2-openapi.yml +
  https://docs.textql.com/api-reference/v2/changes/restore-change +
  https://docs.textql.com/api-reference/v2/sandbox/create-ontology-change
---

# Govern an Ontology change

The Ontology is TextQL's governed semantic layer — metrics, entities and business logic stored as
files. It is never written directly. Edits are made in a sandcastle, proposed as a reviewable change,
and merged only when the folder's approval rule is satisfied.

This is the safest write surface on the platform, and the one an agent should prefer.

## Steps

1. **Start a sandcastle.** `v2.startSandbox` (`POST /v2/sandcastles`). Its Ontology mount is at
   `/sandbox/files/library`.

2. **Make the edits**, then **preview them without committing**.
   `v2.ontologyDiff` (`GET /v2/sandcastles/{id}/ontology/diff`) reports pending changes in the mount
   *without authoring a change*. This is a genuine dry run. Always call it before step 3 — it is free
   and it is the only look you get before the change becomes reviewable.

3. **Propose the change.** `v2.createOntologyChange`
   (`POST /v2/sandcastles/{id}/ontology/changes`) persists the edits back to the org's Ontology as a
   reviewable change in state `open`. Nothing is merged yet.

4. **Review.** `v2.listChanges` (scoped to your `OWNERS` read access) and `v2.getChange`, which
   returns the change with its diffs *and your capabilities on it* — read that before attempting a
   transition rather than discovering a 403.

5. **Decide.** All three transitions take an `ExpectedGitRefBody`: the git ref you expect the Ontology
   to be at. If it moved, you get `409 conflict` — re-read with `v2.getChange`, take the current ref,
   and reissue. Do not strip the ref to make the conflict go away; it is the concurrency guard.
   - `v2.approveChange` — records your approval and merges **once the folder's approval rule is
     satisfied**. Your approval alone may not merge it.
   - `v2.denyChange` — marks it denied. The author can deny their own change.

6. **Undo a denial.** `v2.restoreChange` (`POST /v2/changes/{id}/restore`) reopens a denied change
   back to `open`. Same authority as deny: the author, or a caller with `OWNERS` write authority on
   every changed path. Only a change in the `denied` state can be restored, and **no time limit on
   restoring is published** — do not promise a window to a user.

7. **Stop the sandcastle.** `v2.stopSandbox` destroys the gVisor session and everything in it.
   Irreversible by design — make sure step 3 succeeded first.

## Scopes

`ontology:read` and `ontology:write`; the sandcastle steps need `sandbox:read` and `sandbox:write`.
See `scopes/textql-scopes.yml`. Over the `ana` CLI these are the `ontology` and `sandcastle` tool
groups, both off by default.
