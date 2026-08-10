# DDD-0004 — Safety, CAPA, and Environmental confirmed as three separate subdomains

## Status
Accepted

## Context
DDD-0003 left `cap.safety-compliance-signoff`, `cap.corrective-preventive-action`,
and `cap.environmental-compliance` unplaced pending `ISSUE-0002`, since
repository evidence could not determine whether they were one domain, three,
or CAPA a cross-cutting process.

## Evidence
Mark, direct, answering `ISSUE-0002`: "separate. capa is a quality aspect."

## Decision
Three separate subdomains, restored to `subdomains/index.yaml` with
domain-expert provenance:
- `subdomain.safety-compliance-signoff`
- `subdomain.quality-capa` — named to carry Mark's framing that CAPA is
  specifically the quality-management aspect of the domain, not a generic
  catch-all
- `subdomain.environmental-compliance`

## Alternatives Considered
None — this is a direct domain-expert answer to a precisely scoped question,
not an inference with competing readings.

## Consequences
`ISSUE-0002` is resolved and removed from `docs/ddd/issues/unresolved.yaml`.
Bounded Context discovery may proceed for these three once the remaining
open issues (`ISSUE-0001`, `ISSUE-0003`) are also resolved or explicitly
deferred.

## Affected Model Objects
`docs/ddd/subdomains/index.yaml` (three records added),
`docs/ddd/discovery/capability-candidates.yaml` (evidence updated),
`docs/ddd/issues/unresolved.yaml` (`ISSUE-0002` removed).

## Unresolved Questions
None remaining for this specific question.
