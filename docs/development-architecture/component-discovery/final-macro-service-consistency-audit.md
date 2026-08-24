# Final Whole-Model Macro-Service Consistency Audit

**Status:** discovery audit  
**Date:** 2026-08-24  
**Design gate:** CLOSED  
**Inputs:** canonical DDD; normalized macro-service inventory; live ManuFactor architecture workbook; live architecture textual substrate.

## Audit question

Is there any major macro-service discovery work left that could materially change the reusable-service map, or are the remaining items reconciliation defects inside an otherwise stable macro-service model?

## Result

**The macro-service category map is stable. No major reusable-service category remains undiscovered.**

The remaining work is **major architecture reconciliation**, not further macro-service category discovery. Because the user has required that no DDD-driven design begin while major work remains, the design gate remains closed until that reconciliation is completed and reviewed.

## Coverage audit

### Canonical DDD coverage

- 10/10 Bounded Contexts checked.
- 17/17 canonical Application Use Cases checked.
- Context-map integrations checked for source authority and translation implications.
- Cross-context operational read requirements checked.
- Retained-history requirements checked.
- Evidence/document requirements checked.
- Actor/authority requirements checked.
- Work-routing requirements checked.
- Operations-form evidence checked without promoting forms into domain types.

Result: **no uncovered recurring responsibility requires an additional macro-service family.**

## Reuse/abstraction audit

The following remain valid macro-service families:

1. Data & State Persistence
2. Catalog, Reference & Hierarchy
3. Forms & Structured Capture
4. Documents, Artifacts & Evidence
5. Identity, Access & Responsibility
6. Integration, Source Acquisition & Provenance
7. Operational Query & Read Composition
8. Analytics, Projection & Data Lake
9. Retained History
10. Work Routing & External Reference Coordination
11. Notification & Attention
12. Scheduling & Background Execution (platform)

Supporting capabilities such as Rules, workflow runtime, Search, Computation, Realtime, Storage, Compose, and Printing do not independently satisfy the macro-service test.

Ambient concerns such as Observability, technical Audit, Security, DevOps, Testing, and generic configuration/governance do not become DDD-derived macro services.

## Duplicate-capability audit

No duplicate macro-service family is required.

Potential duplicate/overlap zones and their resolved semantic boundary:

- **Integration vs Analytics.ETL** — Integration owns source/destination interaction and provenance mechanics; Analytics owns analytical transformation/projection/data-lake concerns.
- **Integration.Jobs/Cron-Jobs/Cron-ETL/Hangfire** — one Scheduling & Background Execution family; hosts/jobs do not create separate macro services.
- **Integration.Workflows/Elsa vs Work Routing** — Elsa is runtime/orchestration mechanics; routing/reference coordination is the reusable business-facing technical responsibility.
- **Observability.Audit vs Retained History** — technical audit is not retained business history.
- **Catalog vs Data.Reference vs Forms** — Catalog handles semantic/reference registry and hierarchy metadata; Data.Reference serves shared lookup datasets; Forms owns reusable capture/form-definition mechanics at macro-service level; none owns BC domain semantics.
- **Storage/Compose/Printing** — one Documents/Artifacts/Evidence family at macro level.
- **Rules vs Governance.Validation vs BC invariants** — shared mechanics only; business rules/invariants remain BC-owned.

## Rejected universal abstractions remain rejected

Workbook inspection does not justify restoring any of these:

- universal `Thing` domain model;
- universal Status domain semantics;
- GenericRecord;
- generic Form domain aggregate;
- GenericVerification;
- GenericTask / GenericWork aggregate;
- universal Parameter domain model;
- workflow engine as owner of domain lifecycle;
- Catalog as owner of every domain record.

## Live-workbook contradictions that remain major reconciliation work

### 1. Identity permission authority contradiction

The live `Ports` sheet says:

`Identity.Permissions -> DirectoryServicesWrapper -> AD`

but accepted ADR 117 says:

- ManuFactor owns role/tier assignment internally;
- no AD groups are consulted as permission source.

This is a direct internal contradiction in the live architecture database.

### 2. Universal Thing Status implication

The workbook retains a base status catalog (`Open`, `InProgress`, `Paused`, `Closed`, `Cancelled`) and attempts to map domain-specific states to it. The workbook itself demonstrates semantic mismatch (for example, `Scrapped` is not naturally `Cancelled`).

The architecture must distinguish any technical display/category mechanism from BC-owned status/state semantics.

### 3. Catalog ownership ambiguity

`Catalog.Entities`, `Catalog.Forms`, and the `Catalog Entities` sheet mix:

- registry/reference metadata;
- shared lookup data;
- domain objects;
- pre-DDD concepts.

The architecture must prevent this from being interpreted as Catalog ownership of domain lifecycle/persistence.

### 4. Pre-DDD component/rule/action residue

The workbook still contains architecture facts centered on:

- ParameterProcessChange;
- InspectionsCalibration;
- VehicleManagement;
- Risk;
- Environmental;
- QMS-like cross-cutting rules;
- old direct Component trigger/coupling ideas.

These must be clearly reconciled to canonical DDD or marked historical/deferred/non-authoritative before they can safely inform future design.

### 5. Missing macro responsibilities in live Port representation

The live Port table does not coherently represent:

- Forms & Structured Capture;
- Retained History;
- Operational Query & Read Composition;
- Work Routing & External Reference Coordination as distinct from workflow runtime.

This is not evidence that those macro services are invalid. The DDD + whole-model consumer audit establishes them; the live architecture inventory needs reconciliation.

### 6. Missing canonical Quality Verification implementation concept

The live Component inventory has no clean equivalent to canonical Quality Verification. `InspectionsCalibration` cannot be assumed equivalent because the DDD explicitly separates Quality Verification from Reliability Verification and from generic inspections/calibration semantics.

This is an architecture reconciliation gap, not a reason to invent implementation design now.

## Are these still major work?

**Yes.**

They are not major *category-discovery* work, but they are major enough to prevent a safe transition into DDD-driven design because the live architecture source still contradicts or misrepresents the stable macro-service map in several load-bearing places.

Therefore:

- macro-service discovery categories: **COMPLETE**;
- macro-service consumer/overlap discovery: **COMPLETE**;
- live-workbook verification: **COMPLETE**;
- live architecture reconciliation: **NOT COMPLETE**;
- design gate: **CLOSED**.

## Remaining discovery-phase work

The next pass should remain architecture reconciliation/discovery only:

1. Produce an explicit **workbook reconciliation ledger** for every affected live row/category, stating `keep`, `reclassify`, `constrain`, `defer`, `strike`, or `needs canonical DDD mapping`.
2. Reconcile the contradictory Identity.Permissions rows/ADR interpretation.
3. Reconcile Thing Status Catalog semantics.
4. Reconcile Catalog.Entities/Catalog.Forms/Data.Reference ownership wording.
5. Reconcile all pre-DDD Component rows against the 10 canonical BCs.
6. Reconcile Rules, Scheduled Actions, Notifications, Build Order, and Component-to-Port assignments that refer to outdated component semantics.
7. Verify that no live architecture row still asserts semantic ownership inconsistent with canonical DDD.
8. Only then present the discovery-completion gate for Mark's confirmation.

No implementation topology, code contract, persistence pattern, endpoint contract, or vertical-slice design should be advanced during those steps.
