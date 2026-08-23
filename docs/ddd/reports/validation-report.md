# ManuFactor DDD Validation Report

**Model version:** Current canonical model through Specification Phase 15 Integration Validation  
**Date:** 2026-08-23  
**Maturity state:** **Strategic Stable / Tactical Candidate**  
**Overall result:** **PASS WITH WARNINGS**

## Scope

This report evaluates the canonical DDD repository through Phase 15, including Domain, Subdomains, Bounded Contexts, Context Map, Integration Contracts, Ubiquitous Language, Domain Concepts, Aggregates, Entities/Value Objects, Invariants, Commands, Domain Events, Policies, Domain Service evaluation, Repositories, Factory evaluation, Queries, Application Use Cases, unresolved issues, and whole-map integration consistency.

## Current model summary

- 1 top-level Domain: ManuFactor
- Major active Subdomains: Operations, Quality, Maintenance / Reliability, Safety
- Environmental retained as a real business area but deferred until a concrete ManuFactor process/gap list exists
- Human Resources rejected as the active Subdomain for known operational qualification/workforce gaps
- Identity & Access treated as shared platform infrastructure, not a business Subdomain
- 10 active candidate Bounded Contexts
- 9 unique Context Map relationships
- 9 unique Integration Contracts
- 14 candidate Aggregates
- 14 candidate aggregate-root Entities
- 8 Value Object candidates
- 29 first-class Invariants
- 31 Commands
- 31 Domain Events
- 3 supported Policies
- 0 Domain Services currently justified
- 14 technology-independent Repository abstractions
- 0 Factories currently justified
- 13 canonical Queries
- 15 canonical Application Use Cases
- 4 open nonblocking tactical/domain-rule issues

---

## Gate A — Domain completeness: PASS

The top-level Domain remains consistent with ManuFactor's role as gap filler, source-system reader/aggregator/correlator, and owner of missing workflows/records only where real gaps exist. No Phase 14/15 change expands ManuFactor into wholesale ERP, MES, CMMS, HR, QMS, or analytics ownership.

## Gate B — Bounded Context validity: PASS WITH WARNINGS

The ten supported Bounded Contexts remain semantically distinct. No new Bounded Context was created merely because a department, application, query, workflow, or integration exists.

Warnings remain because most contexts are still `candidate` and several exact tactical transition rules remain unresolved.

## Gate C — Context Map validity: PASS WITH WARNINGS

The canonical map contains 9 unique relationships backed by 9 Integration Contracts.

Quality integration is represented by:

1. `rel.quality-verification-to-corrective-action` — Quality Concern or Quality Check evidence may be supplied for explicit Corrective Action admission evaluation.
2. `rel.corrective-action-to-quality-verification` — accepted Nonconformity identity/reference may be consumed for Quality Concern traceability without importing CAPA lifecycle ownership.

The ProTrack -> Kiln Operations integration is explicitly statistical rather than traceability-based: ProTrack records moisture by lumber dimension (thickness x width x length) and does not provide a Kiln Run/Charge identifier. The contract therefore forbids fabricated run-level lineage and supports dimension-level moisture statistics for drying-performance interpretation.

Redundant Phase 15 Quality Concern relationship/contract sidecars were removed after their semantics were reconciled into the canonical indexes. Several DDD `relationship_type` values remain deliberately `unresolved`; organizational relationship patterns are not guessed.

## Gate D — Language validity: PASS

Critical distinctions remain explicit:

- Quality Concern != Quality Check != Nonconformity
- Failed Quality Check != Nonconformity
- Failed Reliability Verification != Asset Condition
- Asset Condition != Asset Lifecycle State
- Operating / Degraded / Down are functional condition values
- In Service / Out of Service / Retired are lifecycle-state values
- Operational Qualification != Operational Availability
- qualification + availability prerequisites != supervisor's final coverage judgment
- ProTrack Moisture Measurement != Kiln Operations interpretation
- ProTrack dimension-level moisture statistics != individual Kiln Run/Charge traceability
- LTV Form Template != LTV Printed Instance != Sign-Off

## Gate E — Aggregate validity: PASS WITH WARNINGS

The canonical aggregate index contains 14 candidate Aggregates.

Phase 14 evidence justified:

- **Quality Concern** — independent retained identity, triage/disposition history, and formal-record references.
- **Applicable Quality Target Parameter** — ManuFactor-owned contextual parameter identity/version history with confirmed salaried-management create/change authority.

The earlier conclusion that Applicable Quality Target lacked evidence for an independent Aggregate is explicitly marked superseded in the canonical Aggregate index.

Post-Phase-15 hardening resolved ISSUE-0011: vacation coverage remains inside the Operational Coverage Plan aggregate at current evidence level. A separate vacation aggregate is not justified unless future evidence introduces an independent lifecycle or schedule-wide invariant.

Asset lifecycle hardening resolved ISSUE-0009 without creating a new aggregate: condition and lifecycle position remain Value Objects inside the existing Asset aggregate.

Exact Quality Concern closure after escalation and exact target retirement/supersession behavior remain unresolved and are not invented.

## Gate F — Entity / Value Object validity: PASS WITH WARNINGS

Quality Concern and Applicable Quality Target Parameter have explicit aggregate-root identity continuity.

Asset Value Objects now have controlled vocabularies:

- `value-object.asset-lifecycle.asset-condition`: Operating, Degraded, Down
- `value-object.asset-lifecycle.lifecycle-state`: In Service, Out of Service, Retired

The distinction is intentional: Down is a functional condition and is not synonymous with Out of Service; Retired is a lifecycle position and not a condition.

## Gate G — Behavior validity: PASS WITH WARNINGS

Canonical behavior includes:

- 29 first-class Invariants
- 31 Commands
- 31 Domain Events
- 3 Policies
- 0 Domain Services

The Policy set includes `policy.corrective-action.determine-nonconformity-admission`. This requires an authorized reviewer to explicitly determine from retained evidence that an identified applicable requirement was not fulfilled. A Quality Concern or failed Quality Check alone remains insufficient, and a Quality Check is not a mandatory gate.

Reliability hardening clarified that there is no deterministic Reliability Verification Result -> Asset State mapping. A millwright records the finding; the maintenance supervisor decides whether to promote it; Asset Lifecycle owns any resulting state transition.

ISSUE-0009 hardening completed previously dangling Asset lifecycle behavior:

- `command.asset-lifecycle.change-lifecycle-state`
- `event.asset-lifecycle.lifecycle-state-changed`
- `use-case.asset-lifecycle.change-lifecycle-state`

The transition uses the controlled lifecycle vocabulary and preserves history under the existing Asset invariants. MP2 status or Reliability Verification result alone cannot silently drive the transition.

Phase 15 corrected stale Workforce Availability references:

- `SelectReplacement` references canonical `invariant.coverage.requires-qualified-and-available`.
- `invariant.coverage.need-is-explicit` is first-class because the staffing need/job/time context is already an Aggregate consistency rule required by Coverage Plan creation and replacement selection.
- workforce Domain Event provenance uses canonical invariant IDs.

## Gate H — Query and Application Use Case validity: PASS

### Queries

All 13 canonical Queries match the normative schema and remain read-only.

The prior `query.kiln-operations.run-moisture` identity was retired because the source data cannot support run-level correlation. It is replaced by `query.kiln-operations.dimension-moisture-statistics`, which reads ProTrack moisture behavior by thickness x width x length and explicitly preserves the absence of run-level lineage.

### Application Use Cases

There are now 15 canonical Application Use Cases. The additional Asset lifecycle-state use case explicitly orchestrates `ChangeAssetLifecycleState` without collapsing condition and lifecycle semantics.

Other corrections include:

- Workforce coverage creates the explicit Coverage Plan/staffing need before replacement selection.
- LTV orchestration is limited to currently modeled template publication, instance generation, and issuance rather than inventing completion/sign-off rules.
- Quality Concern review retains only local concern/check queries; accepted Nonconformity traceability is handled through an explicit integration boundary.
- Kiln run history remains authoritative locally, while downstream moisture review is statistical by lumber dimension and must not be presented as individual kiln-run traceability.

Application orchestration does not redefine Aggregate invariants or unresolved business rules.

## Gate I — Boundary leakage and source authority: PASS

No direct cross-context Aggregate/Entity sharing or Shared Kernel is modeled.

Cross-context compositions include:

- Kiln Operations + ProTrack dimension-level moisture statistics
- Asset Lifecycle + MP2 hierarchy/WR-WO facts
- Reliability Verification + Asset identity/context
- Workforce Availability + Operational Qualification + HR/timekeeping facts
- Quality Concern + accepted Corrective Action Nonconformity reference

Source facts remain authoritative in their owning system/context. Reading, correlating, statistically comparing, or retaining a reference does not transfer source authority.

## Gate J — Repository / tactical schema validity: PASS WITH WARNINGS

The Phase 14 Quality Concern and Applicable Quality Target Repository candidates use the normative Repository schema and remain technology-independent.

## Gate K — Provenance: PASS

Phase 14/15 and post-Phase-15 changes distinguish domain-expert evidence from modeling inference. Stable IDs were preserved except where modeled identity actually changed or a previously missing behavior was introduced.

## Gate L — Unresolved questions: PASS WITH WARNINGS

Open nonblocking issues in `docs/ddd/issues/unresolved.yaml`:

- ISSUE-0006 — Operational Qualification evidence/review/expiration/withdrawal rules; grant authority is already known
- ISSUE-0007 — process-specific LTV completion/approval/sign-off rules
- ISSUE-0010 — child Corrective Action identity and detailed CAPA closure rules
- ISSUE-0012 — independent Quality Concern closure rule after Nonconformity escalation

Resolved during post-Phase-15 hardening:

- ISSUE-0004 — Nonconformity admission now has an explicit domain Policy.
- ISSUE-0005 — no deterministic Reliability Result -> Asset State mapping exists; maintenance-supervisor promotion is the gate.
- ISSUE-0008 — ProTrack has no kiln-run identifier; moisture analysis is statistical by thickness x width x length and must not fabricate run-level lineage.
- ISSUE-0009 — Asset condition/lifecycle vocabularies and lifecycle transition behavior are now explicit.
- ISSUE-0011 — no separate vacation-planning Aggregate is justified at current evidence level.

None of the remaining issues blocks Phase 14/15 completion because unsupported transitions remain unmodeled rather than guessed.

---

# Phase 14 assessment — Use Cases and Queries: PASS

The reconciled discovery candidates were normalized into canonical Queries and Application Use Cases. Tactical Quality Concern and Applicable Quality Target behavior was reconciled where confirmed business decisions required first-class model objects.

Detailed audit: `docs/ddd/reports/phase14-use-case-query-audit.md`.

**Specification Phase 14 — COMPLETE at current evidence level.**

---

# Phase 15 assessment — Integration Validation: PASS WITH WARNINGS

Whole-map integration validation found and corrected stale command/invariant references, a superseded Aggregate challenge conclusion, redundant Quality integration sidecars, and cross-context concern/Nonconformity traceability semantics.

The resulting graph has 9 unique Context Map relationships and 9 unique Integration Contracts. No significant cross-context interaction used by current artifacts is hidden as shared domain state.

Detailed audit: `docs/ddd/reports/phase15-integration-validation.md`.

**Specification Phase 15 — COMPLETE at current evidence level.**

---

# Post-Phase-15 targeted hardening

The targeted hardening pass reviewed whether unresolved questions were already answerable from canonical evidence or direct domain clarification.

- ISSUE-0011 closed: Operational Coverage Plan already owns vacation/sick-call staffing coverage; no independent vacation lifecycle/invariant justifies another Aggregate.
- ISSUE-0004 closed: Nonconformity admission is an explicit Policy decision.
- ISSUE-0005 closed: Reliability Verification does not deterministically map to Asset State; supervisor promotion is the gate.
- ISSUE-0008 closed: ProTrack has no kiln-run identifier; moisture interpretation is statistical by thickness x width x length.
- ISSUE-0009 closed: Asset Condition is Operating/Degraded/Down; Asset Lifecycle State is In Service/Out of Service/Retired; lifecycle-state transition behavior is now explicit and history-preserving.
- ISSUE-0006 wording is narrowed so grant authority is no longer presented as unresolved.
- ISSUE-0007 wording distinguishes completion, approval, and sign-off transitions from printing/issuance.
- ISSUE-0012 is narrowed to the genuinely unresolved case: closure after Nonconformity escalation.

---

# Maturity assessment

## Strategic Stable: PASS

Major Subdomains, Bounded Contexts, contextual language, source authority, and integration boundaries are established with no known blocking strategic ambiguity.

## Tactical Candidate: PASS

The tactical model is complete through Specification Phase 15 but remains Candidate rather than Stable because four domain-specific lifecycle/rule questions remain open.

---

# Overall conclusion

**PASS WITH WARNINGS**

> **Strategic Stable / Tactical Candidate**

Specification Phases 1-15 are complete at the current evidence level. Remaining work is targeted domain-rule refinement against four explicit unresolved issues, not continuation of a missing specification phase.
