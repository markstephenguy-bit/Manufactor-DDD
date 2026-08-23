# Phase 14 — Use Cases and Queries Audit

**Date:** 2026-08-22  
**Result:** PASS WITH WARNINGS  
**Phase status:** COMPLETE at current evidence level

## Normative schema audit

The Phase 14 canonical artifacts were checked against `SPECIFICATION.md` sections 27 and 28.

### Queries

Canonical file: `docs/ddd/queries/index.yaml`

Count: **13**

Every promoted Query contains the required fields:

- `id`
- `name`
- `bounded_context`
- `purpose`
- `inputs`
- `result`
- `authoritative_source`
- `consistency_expectation`
- `status`
- `provenance`

The Query set remains read-only in domain intent. No Query is modeled as a state-changing operation.

### Application Use Cases

Canonical file: `docs/ddd/application-use-cases/index.yaml`

Count: **14**

Every promoted Application Use Case contains the required fields:

- `id`
- `name`
- `bounded_context`
- `goal`
- `actors`
- `trigger`
- `preconditions`
- `commands`
- `queries`
- `domain_services`
- `events_observed`
- `result`
- `failure_paths`
- `status`
- `provenance`

No Application Use Case introduces a Domain Service. Existing whole-map evaluation still supports zero Domain Services.

## Cross-context read composition

Cross-context Query composition is explicit rather than modeled as a shared domain model.

Confirmed examples:

- Kiln Run performance composes `bc.kiln-operations` with authoritative ProTrack moisture facts through `contract.protrack-moisture-to-kiln-operations`.
- Asset drill-through composes `bc.asset-lifecycle` with MP2 hierarchy/WR-WO facts through `contract.mp2-maintenance-to-asset-lifecycle`.
- Reliability history references Asset identity through `contract.asset-lifecycle-to-reliability-verification` while keeping verification-result meaning separate from Asset state.
- Workforce coverage composes Operational Qualification and HR/timekeeping facts through their explicit contracts while retaining the supervisor staffing decision in `bc.workforce-availability`.
- Quality Concern traceability to an accepted Nonconformity uses `contract.corrective-action-to-quality-verification`; CAPA lifecycle state is not imported into Quality Verification.

## Business-rule placement audit

Application orchestration was checked for invariant leakage.

Business rules remain in Aggregates, Invariants, Policies, and Commands. Use cases state orchestration preconditions/failure paths but do not redefine rules deciding:

- whether a Quality Concern automatically becomes a formal record — it does not;
- who may formally triage a Quality Concern;
- whether a failed Quality Check becomes a Nonconformity — it does not automatically;
- whether a quality-target change may destroy historical evaluation basis — it may not;
- whether a Reliability Verification directly changes Asset state — it does not;
- whether a staffing replacement satisfies qualification/availability prerequisites;
- who makes the final coverage judgment — the supervisor;
- whether routed CAPA work becomes Corrective Action-owned execution state — it does not.

## Tactical reconciliation performed during Phase 14

Confirmed Phase 14 business decisions required first-class tactical additions in Quality Verification.

### Quality Concern

Canonical artifacts now include:

- `docs/ddd/aggregates/index.yaml`
- `docs/ddd/commands/index.yaml`
- `docs/ddd/domain-events/index.yaml`
- `docs/ddd/concepts/phase14-quality-reconciliation.yaml`
- `docs/ddd/entities/phase14-quality-reconciliation.yaml`
- `docs/ddd/invariants/phase14-quality-reconciliation.yaml`
- `docs/ddd/repositories/phase14-quality-reconciliation.yaml`

Quality Concern is justified as a candidate Aggregate because it has independent identity, retained triage/disposition history, explicit commands, and an independent lifecycle from both Quality Check and Nonconformity.

### Applicable Quality Target Parameter

Phase 14 evidence changed the earlier tactical conclusion. ManuFactor owns target maintenance, salaried management controls create/change authority, contextual applicability is business-significant, and prior versions must remain historically resolvable for retained Quality Checks. The canonical Aggregate index now records the candidate Aggregate and explicitly marks the earlier contrary challenge conclusion as superseded.

The two Phase 14 Repository records were normalized to the normative Repository schema and use `aggregate`, not non-normative `aggregate_root`.

Temporary Phase 14 command/event sidecars were removed after their objects were merged into canonical indexes to avoid duplicate IDs.

## Use-case corrections made by audit

### Workforce Availability

`use-case.workforce-availability.fill-gap` now includes `command.workforce-availability.create-coverage-plan` and observes `event.workforce-availability.staffing-gap-identified` before replacement selection. `invariant.coverage.need-is-explicit` is first-class because the staffing need is part of the Coverage Plan consistency boundary.

### LTV Form Management

`use-case.ltv-form-management.manage-forms` was narrowed to currently modeled behavior: publish template, generate instance, and record issuance. Exact completion/sign-off transition rules remain ISSUE-0007 and are not invented in application orchestration.

### Quality Concern / Corrective Action boundary

Quality Verification concern disposition no longer reads CAPA state as if locally authoritative. The existing Quality Verification -> Corrective Action contract now covers both concern and Quality Check evidence for explicit Nonconformity admission evaluation. The reverse Corrective Action -> Quality Verification contract supports accepted-Nonconformity traceability only.

## Remaining warnings

Phase 14 is complete without resolving:

- ISSUE-0004 — exact failed Quality Check -> Nonconformity admission rule
- ISSUE-0005 — exact Reliability Verification Result -> Asset state transition rule
- ISSUE-0006 — qualification evidence/expiration/withdrawal details
- ISSUE-0007 — exact LTV completion/sign-off workflow states and transitions
- ISSUE-0008 — exact Kiln Run -> ProTrack correlation identifiers/rules
- ISSUE-0009 — exact Asset condition/lifecycle vocabulary
- ISSUE-0010 — child Corrective Action identity and CAPA closure detail
- ISSUE-0011 — whether vacation planning later warrants a distinct Aggregate
- ISSUE-0012 — independent Quality Concern closure rule after disposition/escalation

These remain explicit and do not invalidate the Phase 14 Query/Application Use Case model at its current candidate maturity.
