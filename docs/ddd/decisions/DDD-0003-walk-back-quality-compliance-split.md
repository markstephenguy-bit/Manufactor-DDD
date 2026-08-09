# DDD-0003 — Walk back the confident three-way split from DDD-0001

## Status
Accepted

## Context
DDD-0001 split `subdomain.quality-compliance` into three confident,
`core`-classified subdomains (Safety Compliance & Procedure Sign-Off,
Corrective & Preventive Action, Environmental Compliance Reporting) on the
finding that no shared vocabulary or rule structure links them. External
audit (ChatGPT) correctly identified that this converted one unproven claim
("they belong together") into another unproven claim ("they are three
separate things") — absence of evidence for a grouping is not evidence for a
specific alternative structure. It also created three single-capability
Subdomains, which the prior process correction explicitly said not to do
merely because grouping evidence is weak.

## Evidence
Same evidence as DDD-0001 — re-read, not re-gathered. The evidence supports
"we don't know how these relate," not "we know they don't relate."

## Decision
Remove all three subdomain records. `cap.safety-compliance-signoff`,
`cap.corrective-preventive-action`, and `cap.environmental-compliance` are
left unplaced in `subdomains/index.yaml`, the same way `cap.project-tracking`
and the excluded `cap.risk-management` are already handled. The actual
grouping question is recorded as `ISSUE-0002` and escalated to Mark rather
than guessed.

## Alternatives Considered
Re-merge into one subdomain — rejected, same unproven-structure problem in
the other direction. Leave the three-way split but mark all three
`unresolved` instead of `candidate`/`core` — rejected in favor of not
creating the subdomain records at all, since even an "unresolved three-way
split" still asserts a specific structure (three) the evidence doesn't
support.

## Consequences
`subdomains/index.yaml` now contains only the four subdomains not challenged
this round (Asset & Reliability, Production Operations, People & Workforce,
Identity & Access). Bounded Context discovery (Phase 5) should not proceed
for the compliance-shaped capabilities until `ISSUE-0002` is answered.

## Affected Model Objects
`docs/ddd/subdomains/index.yaml` (three records removed),
`docs/ddd/issues/unresolved.yaml` (`ISSUE-0002` added).

## Unresolved Questions
`ISSUE-0002` itself.
