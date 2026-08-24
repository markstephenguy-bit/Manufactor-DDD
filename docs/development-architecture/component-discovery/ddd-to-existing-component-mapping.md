# ManuFactor Canonical DDD to Existing Component Mapping

**Status:** Candidate architecture reconciliation  
**Date:** 2026-08-24  
**Authority:** Canonical DDD defines semantic ownership. Existing `ManuFactor-arch` Components and Ports are implementation architecture evidence and must be reconciled to the DDD rather than treated as equivalent to Bounded Contexts.

## Purpose

Map all 10 current Bounded Contexts to the existing implementation component inventory and identify where the older architecture component model aligns, overlaps, duplicates, or conflicts with the completed DDD.

This is deliberately not a one-to-one mapping exercise. A Bounded Context may consume multiple shared Ports/components, and an existing implementation component may need to be renamed, decomposed, merged, or retained only as an application feature grouping.

---

# Canonical mapping

| Canonical Bounded Context | Closest existing architecture component(s) | Fit | Required architecture treatment |
|---|---|---|---|
| **Asset Lifecycle** | `AssetManagement`; legacy `VehicleManagement` | Strong for AssetManagement; conflicting duplication for VehicleManagement | Make AssetManagement the implementation home for Asset Lifecycle semantics. VehicleManagement must not remain an independent domain owner because the canonical DDD explicitly merged vehicles into the physical Asset model. Vehicle-specific UI/workflows may remain as features under Asset Lifecycle if useful. |
| **Corrective Action** | `Capa` | Strong | Retain Capa as the implementation module for Nonconformity/CAPA semantics. Reconcile workflow usage so Elsa coordinates routing/waiting but does not own CAPA lifecycle validity. |
| **Kiln Operations** | `KilnDrying` | Strong | Retain as implementation module. Add explicit distinction between Kiln Schedule and Kiln Run aggregates and preserve ProTrack dimension-statistics/no-run-lineage rule. |
| **Operations Record** | `OperationalReporting` | Strong with naming/boundary correction | Treat OperationalReporting as the implementation family for business-need-specific operational records, not as a generic analytics/reporting component. Individual forms/records remain distinct business types inside the context. |
| **Project Tracking** | `ProjectTracking` | Strong | Retain. Project state remains independent from CAPA and MP2 work. Cross-links use reference/routing mechanics only. |
| **Quality Verification** | No exact current component; partial overlap with `InspectionsCalibration`, `ParameterProcessChange`, historical quality-shaped components | Weak / mismatch | Introduce an implementation module aligned to Quality Verification when development reaches this context. Do not force Quality targets/checks/concerns into ParameterProcessChange or InspectionsCalibration merely because they share parameters or verification mechanics. Shared technical mechanisms may be reused. |
| **Reliability Verification** | `Afal`; possible mechanical overlap with `InspectionsCalibration` | Strong for AFAL | Treat AFAL as the principal implementation slice/module for Reliability Verification. Any shared inspection/check mechanics remain technical reuse only; Reliability result semantics stay distinct from Quality Verification and Asset state. |
| **LTV Form Management** (`bc.safety-signoff`) | `LtvForms` | Strong | Retain. Update implementation lifecycle to canonical returned-paper receipt + scan + reliable match + `Recorded` semantics. Printing/issuance alone is not completion. |
| **Operational Training and Qualification** | `Training` | Strong | Retain. Moodle/learning completion remains evidence only; operational qualification belongs to this module/context. |
| **Operational Workforce Availability** | `Workforce` | Strong | Retain. HR/timekeeping absence facts and qualification are inputs; supervisor coverage judgment is the locally owned decision. |

---

# Existing architecture components not supported as current independent DDD Bounded Contexts

These components are not automatically wrong as UI/application modules, but the canonical DDD does not support treating them as independent semantic owners.

| Existing component | Canonical DDD implication | Treatment |
|---|---|---|
| `VehicleManagement` | Vehicles are part of Asset Lifecycle under current evidence | Remove independent domain ownership. Fold domain semantics into Asset Lifecycle; keep only vehicle-specific feature organization if it provides implementation value. |
| `ParameterProcessChange` | No current Bounded Context with this semantic boundary | Treat as an application/workflow feature only if concrete use cases remain. Do not let it become a universal parameter/change domain. Quality Target changes remain Quality Verification-owned; other process changes remain with their owning contexts. |
| `InspectionsCalibration` | No single canonical Bounded Context with this broad combined meaning | Decompose by actual semantic owner. Reliability checks belong to Reliability Verification; product-quality checks belong to Quality Verification; calibration mechanics may become a reusable/local helper only when concrete domain ownership is established. |
| `Risk` | No active current Bounded Context established by the canonical DDD | Do not treat as a current independent domain component unless new evidence establishes a ManuFactor-owned lifecycle. Existing UI/reporting can remain dormant/application-only. |
| `Environmental` | Environmental business area is real but deliberately deferred because no concrete ManuFactor gap currently establishes a Bounded Context | Keep deferred. Do not implement a domain model merely because an old component exists. |
| `SupplierQuality` | Not represented as a current independent Bounded Context | Do not preserve as independent semantic ownership without new evidence. Supplier-related quality facts may later map into Quality Verification or another future context if domain evidence supports it. |
| `CustomerComplaints` | Not represented as a current independent Bounded Context | Keep deferred/non-authoritative until a real ManuFactor-owned lifecycle is established. Do not infer a Quality context assignment merely from the name. |

---

# Important boundary corrections

## 1. AFAL is not Asset Lifecycle

AFAL/reliability verification may reference an Asset and produce a finding that influences Asset Lifecycle, but the verification result is not Asset condition/state.

Implementation implication:

```text
AFAL / Reliability Verification
    -> publishes/returns verification fact
    -> supervisor promotion decision where applicable
    -> explicit Asset Lifecycle command/use case

Asset Lifecycle
    -> decides accepted Asset state change under its own invariant
```

No direct update of Asset state from the AFAL persistence handler.

## 2. Quality Verification is not generic Inspections/Calibration

The canonical Quality Verification model includes:

- Quality Concern;
- Quality Check;
- measurement evidence;
- contextual Quality Target parameters;
- target applicability by mill/machine/process;
- retained historical evaluation basis.

A generic inspections component would erase these semantics. Reuse belongs below the domain boundary: forms, measurements, evidence, parameter mechanics, history, and query infrastructure.

## 3. Parameter/Process Change is not a universal change owner

The old architecture component can remain useful as a feature only if it represents concrete workflow. It must not become the owner of changes simply because they are “parameter” or “process” changes.

Examples:

- Quality Target change -> Quality Verification.
- Asset condition/lifecycle change -> Asset Lifecycle.
- Kiln schedule revision -> Kiln Operations.
- LTV template version change -> LTV Form Management.

Shared version/effective-date mechanics may still be reused.

## 4. Operational Reporting must not become analytics ownership

The canonical Operations Record context fills structured-record gaps. Superset/Analytics may consume those facts later.

Therefore:

```text
OperationalReporting implementation module
    owns capture/correction/history of business-defined operational records

Analytics / Superset
    reads/projections those facts
    does not own the record lifecycle
```

## 5. Vehicle-specific behavior may remain without a Vehicle domain boundary

The DDD merge does not prohibit a vehicle page, vehicle workflow, vehicle form, or vehicle filter.

It prohibits treating `Vehicle` as a separate domain lifecycle when the current evidence says equipment and vehicles share Asset Lifecycle semantics.

---

# Bounded Context to existing Port consumption

This table maps likely implementation reuse using the current Port inventory. It is not a domain dependency map.

| Bounded Context | Existing Ports/mechanics likely consumed |
|---|---|
| Asset Lifecycle | Data, Catalog/reference mechanics, Integration, Documents/Storage where evidence/history needs it, Analytics for projections |
| Corrective Action | Data, Catalog/reference mechanics, Integration.Workflows, Notification, Rules/authorization mechanics, Storage/Evidence |
| Kiln Operations | Data, Analytics, Integration for ProTrack/source adapters, Catalog/reference mechanics for Assets where needed |
| Operations Record | Data, Storage/Documents where record type requires artifacts, Catalog/reference picker, Analytics downstream |
| Project Tracking | Data, Catalog/reference mechanics, Notification as needed, Integration/reference routing when external work is linked |
| Quality Verification | Data, Storage/Evidence, Catalog/reference mechanics, Rules/authorization mechanics, Analytics/projections |
| Reliability Verification | Data, Catalog/reference mechanics, Storage/Evidence, Integration for MP2 references/routing, Documents.Compose where forms are required |
| LTV Form Management | Data, Storage, Documents.Compose, Printing, Catalog, Identity/Permissions, Notification where required |
| Operational Training and Qualification | Data, Integration to learning sources, Storage/Evidence, Analytics/projections |
| Operational Workforce Availability | Data, Integration to HR/timekeeping, Analytics/projections, Identity/Responsibility mechanics, Workflow only for application orchestration if required |

---

# Existing cross-cutting mechanisms after DDD reconciliation

## Catalog / `manufactor.thing` / `thing_links`

These remain valuable technical mechanics but must be constrained to reference/navigation/cross-link purposes.

They must not become a universal domain object model.

A link between a Nonconformity and Project, or between a Reliability Verification and Asset, carries reference semantics only. The linked records retain independent lifecycle ownership.

## Thing Status Catalog

The DDD rejects a universal domain status semantic.

If the existing Thing Status Catalog remains, it may provide technical cataloging/registration of allowed status codes for a given owning module, but each context owns:

- its status vocabulary;
- transition legality;
- status meaning;
- invariants triggered by status changes.

No common `Open/InProgress/Closed` vocabulary should be forced onto LTV, Asset, Qualification, CAPA, Quality Concern, Project, or Kiln simply for implementation uniformity.

## Cron/Jobs

Scheduling remains a technical execution mechanism. A scheduled job may trigger a query or command, but the job scheduler does not own the business deadline, due-state, qualification expiry rule, inspection rule, or workflow transition.

---

# Architecture actions resulting from the mapping

## Preserve as aligned implementation modules

- `LtvForms`
- `Afal`
- `OperationalReporting` with boundary/name discipline
- `Capa`
- `Training`
- `Workforce`
- `AssetManagement`
- `KilnDrying`
- `ProjectTracking`

## Reconcile before further implementation

- `VehicleManagement` -> fold domain ownership into Asset Lifecycle.
- `InspectionsCalibration` -> decompose by actual semantic owner; do not preserve as a generic verification domain.
- `ParameterProcessChange` -> prevent it from owning domain changes across contexts.
- quality functionality -> create a Quality Verification-aligned module/slice rather than reuse a semantically wrong existing component.

## Keep deferred unless new domain evidence appears

- `Environmental`
- `Risk`
- `CustomerComplaints`
- independent `SupplierQuality`

---

# Migration impact classification

No immediate code deletion or database migration is justified by this mapping alone.

The safe sequence is:

1. establish the target semantic ownership map in architecture artifacts;
2. add architecture fitness checks preventing new cross-boundary writes/duplicates;
3. when a reconciled component is next touched for development, move its ownership/code incrementally;
4. preserve stable data identifiers during moves;
5. migrate tables only when implementation pressure shows a real persistence mismatch;
6. never rewrite historical records solely to make old component names match new DDD names.

This keeps the DDD correction forward-compatible without a speculative rewrite.

---

# Result

The completed DDD and the existing ManuFactor architecture are broadly compatible, but they are **not semantically identical**.

Nine of the ten canonical contexts have a strong or usable existing implementation home. **Quality Verification is the primary missing direct implementation home.** The main existing architecture conflicts are older component boundaries that are too broad (`InspectionsCalibration`, `ParameterProcessChange`) or now duplicated by the canonical model (`VehicleManagement`).

No business question is required to resolve these architecture mismatches. They can be handled as implementation-boundary reconciliation while preserving the canonical DDD.
