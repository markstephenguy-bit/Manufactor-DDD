# DDD-0005 — CAPA defined by ISO 9001:2015's Nonconformity trigger; Project Tracking is distinct

## Status
Accepted

## Context
`ISSUE-0001` asked whether CAPA-tracked work is distinct from Project
Tracking. Mark had already twice pointed at the answer — confirming CAPA as
"a quality aspect" (`ISSUE-0002`) and directing that ISO 9001 be consulted
for what CAPA is — before this was correctly applied.

## Evidence
Mark, direct: "capa is a quality aspect" + explicit direction to consult
ISO 9001. ISO 9001:2015 clause 10.2: a Nonconformity is any failure to meet
a requirement; Corrective Action eliminates its root cause to prevent
recurrence, distinct from an immediate Correction.

## Decision
CAPA is defined by the ISO 9001 Nonconformity trigger — work responding to a
failure against a requirement. Project Tracking is planned/capital work not
triggered by a Nonconformity. This is the distinguishing test between the two
capabilities, resolved from Mark's direction plus the standard, not invented.

## Alternatives Considered
None — Mark directly supplied both the domain confirmation and the standard
to apply; this was a matter of correctly using given information, not a
choice among competing readings.

## Consequences
`cap.project-tracking` moves from `unresolved` to `candidate`.
`cap.corrective-preventive-action` and `subdomain.quality-capa` gain
Nonconformity/Correction/Corrective Action as their real vocabulary.
`ISSUE-0001` is resolved and removed from `unresolved.yaml`.

## Affected Model Objects
`docs/ddd/discovery/capability-candidates.yaml`,
`docs/ddd/subdomains/index.yaml`, `docs/ddd/issues/unresolved.yaml`.

## Unresolved Questions
None for this question. `ISSUE-0003` (Vehicle Management merge) remains
open, non-blocking.
