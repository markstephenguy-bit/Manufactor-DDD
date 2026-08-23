# Phase 15 — Integration Validation

**Date:** 2026-08-22  
**Result:** PASS WITH WARNINGS  
**Phase status:** COMPLETE at current evidence level

## Scope

This audit validates the completed Phase 14 Queries and Application Use Cases against the canonical tactical behavior model and the whole Context Map / Integration Contract graph.

Validation covered:

- Query and Application Use Case schema compliance
- Command, Domain Event, Aggregate, Invariant, Policy, Repository, and Query referential integrity
- source-system authority
- cross-context read composition
- cross-context mutation/traceability boundaries
- duplicate or contradictory tactical artifacts
- application-orchestration leakage of domain rules
- unresolved rules that must remain explicit rather than inferred

## Canonical Phase 14/15 counts after reconciliation

- 10 candidate Bounded Contexts
- 14 candidate Aggregates
- 14 candidate aggregate-root Entities
- 8 Value Object candidates
- 29 first-class Invariants
- 30 Commands
- 30 Domain Events
- 2 Policies
- 0 Domain Services
- 14 Repository abstractions
- 0 Factories
- 13 Queries
- 14 Application Use Cases
- 9 Context Map relationships
- 9 Integration Contracts
- 9 open nonblocking issues

## Referential integrity corrections

### Workforce Availability

The Phase 15 audit found two stale/missing invariant references around coverage decisions.

Corrections:

- `command.workforce-availability.select-replacement` now references the existing canonical `invariant.coverage.requires-qualified-and-available` rather than the non-existent `invariant.coverage.replacement-must-be-qualified-and-available`.
- `invariant.coverage.need-is-explicit` is now first-class because the Coverage Plan already requires the staffing need/job/time context to remain explicit and `CreateOperationalCoveragePlan` depends on that rule.
- `event.workforce-availability.replacement-selected` provenance now references the canonical qualification/availability invariant.
- ISSUE-0011 evidence now uses canonical invariant IDs.

These changes do not alter the confirmed business decision that qualification and availability are prerequisite facts while final ability-to-cover selection remains supervisor judgment.

### Quality Verification tactical reconciliation

Phase 14 confirmed two additional candidate Aggregate boundaries:

- `aggregate.quality-verification.quality-concern`
- `aggregate.quality-verification.applicable-quality-target`

The Aggregate index now contains these directly and explicitly marks the earlier "Applicable Quality Target is not yet an aggregate" challenge conclusion as superseded by the Phase 14 authority/history evidence.

The six Quality Verification Commands and six Domain Events introduced during Phase 14 are now merged into the canonical command/event indexes. Temporary command/event sidecars were removed to prevent duplicate IDs.

The two Phase 14 Repository records were normalized to the normative Repository schema (`aggregate`, not non-normative `aggregate_root`).

## Context Map and Integration Contract validation

The canonical graph contains 9 unique relationships and 9 unique contracts.

### Quality Verification -> Corrective Action

`rel.quality-verification-to-corrective-action` / `contract.quality-verification-to-corrective-action` now cover both supported evidence paths:

- retained Quality Concern evidence when sufficient basis may exist for explicit Nonconformity admission;
- Quality Check evidence/result where a formal check exists.

The contract does not admit a Nonconformity automatically. Corrective Action owns explicit Nonconformity acceptance and CAPA lifecycle semantics.

### Corrective Action -> Quality Verification

`rel.corrective-action-to-quality-verification` / `contract.corrective-action-to-quality-verification` provide accepted Nonconformity identity/reference for Quality Concern traceability.

Quality Verification does not copy, infer, or own CAPA status, routed-work execution state, or closure state.

### Redundant Phase 15 sidecars

Prior Phase 15 sidecars split Quality Concern evidence into an additional forward relationship/contract and duplicated the reverse relationship/contract. They were removed because the canonical indexed contracts already express the same semantic boundaries without creating duplicate integration identities.

## Cross-context read composition

PASS.

Canonical cross-context reads remain explicit composition:

- Kiln Operations + ProTrack moisture through `contract.protrack-moisture-to-kiln-operations`
- Asset Lifecycle + MP2 WR/WO/hierarchy facts through `contract.mp2-maintenance-to-asset-lifecycle`
- Reliability Verification + Asset identity through `contract.asset-lifecycle-to-reliability-verification`
- Workforce Availability + Operational Qualification through `contract.training-qualification-to-workforce-availability`
- Workforce Availability + HR/timekeeping facts through `contract.hr-timekeeping-to-workforce-availability`
- Quality Concern + accepted Nonconformity reference through `contract.corrective-action-to-quality-verification`

No cross-context read is represented as a shared Aggregate or shared domain object graph.

## Source authority validation

PASS.

- ProTrack remains authoritative for its moisture measurements.
- MP2 remains authoritative for its hierarchy and WR/WO transaction facts.
- learning systems remain authoritative for their learning-completion records.
- HR/timekeeping sources remain authoritative for their personnel/absence facts.
- Training & Competency owns Operational Qualification semantics.
- Workforce Availability owns the retained staffing decision, not source qualification/absence facts.
- Corrective Action owns Nonconformity/CAPA lifecycle semantics.
- Quality Verification owns Quality Concern, Quality Check, and locally maintained quality-target parameters.

Reading, correlating, or retaining a reference does not transfer source authority.

## Application orchestration validation

PASS.

The 14 Application Use Cases orchestrate existing domain behavior and do not redefine:

- Nonconformity admission
- Quality Concern formalization or closure
- quality-target authority/history rules
- Reliability Verification -> Asset state transition rules
- qualification evidence/expiration rules
- LTV completion/sign-off rules
- Kiln Run -> ProTrack correlation rules
- CAPA external-work ownership or closure semantics

The LTV use case was deliberately narrowed to currently modeled publication/generation/issuance behavior rather than encoding ISSUE-0007 as orchestration logic.

## Remaining warnings

The following remain explicit, nonblocking domain-rule issues:

- ISSUE-0004 — failed Quality Check -> Nonconformity exact admission rule
- ISSUE-0005 — Reliability Verification Result -> Asset state transition rule
- ISSUE-0006 — Operational Qualification evidence/expiration/withdrawal rules
- ISSUE-0007 — process-specific LTV completion/approval/sign-off transitions
- ISSUE-0008 — Kiln Run -> ProTrack correlation identifiers/rules
- ISSUE-0009 — controlled Asset condition/lifecycle vocabulary
- ISSUE-0010 — child Corrective Action identity and detailed CAPA closure rules
- ISSUE-0011 — whether vacation planning later warrants a distinct Aggregate
- ISSUE-0012 — independent Quality Concern closure rule after disposition/escalation

None is encoded by inference.

## Conclusion

**PASS WITH WARNINGS**

Specification Phase 15 is complete at the current evidence level. The model remains **Strategic Stable / Tactical Candidate**. Remaining work is targeted business-rule refinement, not another missing whole-map specification pass.
