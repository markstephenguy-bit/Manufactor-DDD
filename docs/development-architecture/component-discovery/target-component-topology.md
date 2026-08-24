# ManuFactor Target Component Topology

**Status:** Candidate development-architecture target  
**Date:** 2026-08-24  
**Semantic authority:** Canonical DDD under `docs/ddd`  
**Implementation constraint:** Preserve current compact ManuFactor deployment and shared-Port architecture unless a separate scaling/failure/security driver later requires change.

## Purpose

Define the target application/component topology after reconciling the completed DDD with the existing `Manufactor` implementation and `ManuFactor-arch` substrate.

This topology is not a service decomposition. A Bounded Context is a semantic ownership boundary. Multiple implementation modules may execute in the same process and persist in the same physical PostgreSQL database while remaining logically isolated.

---

# Target domain-aligned application modules

| Target implementation module | Canonical Bounded Context | Existing architecture lineage | Target status |
|---|---|---|---|
| `AssetManagement` | Asset Lifecycle | `AssetManagement` + vehicle-related domain behavior currently separated in `VehicleManagement` | Retain as domain-aligned module; absorb vehicle lifecycle semantics |
| `Capa` | Corrective Action | `Capa` | Retain |
| `KilnDrying` | Kiln Operations | `KilnDrying` | Retain; enforce Schedule vs Run distinction and ProTrack lineage limits |
| `OperationalReporting` | Operations Record | `OperationalReporting` | Retain implementation name for now, but semantic purpose is structured Operations Records rather than analytics ownership |
| `ProjectTracking` | Project Tracking | `ProjectTracking` | Retain |
| `QualityVerification` | Quality Verification | No clean existing component | **Add when this context enters implementation** |
| `Afal` | Reliability Verification | `Afal` | Retain as the current principal implementation slice; domain terminology remains Reliability Verification |
| `LtvForms` | LTV Form Management | `LtvForms` | Retain; canonical lifecycle includes returned paper receipt/scan/match/Recorded |
| `Training` | Operational Training and Qualification | `Training` | Retain |
| `Workforce` | Operational Workforce Availability | `Workforce` | Retain |

These ten modules are the implementation homes for the ten current canonical semantic ownership boundaries. Their names do not have to exactly equal the Bounded Context names, but their responsibilities must.

---

# Existing components that change role

## `VehicleManagement`

**Target role:** feature grouping inside or adjacent to `AssetManagement`, not an independent domain owner.

Allowed to retain:

- vehicle-specific pages;
- checklists/forms;
- vehicle filters and views;
- vehicle-specific external integrations;
- feature-specific application commands that ultimately act through Asset Lifecycle-owned behavior.

Must not own independently:

- Asset identity;
- condition/lifecycle state;
- conflicting vehicle status semantics;
- a second asset history model.

No immediate code deletion is required. The first time vehicle functionality is changed, new domain behavior must be routed through the Asset Lifecycle implementation boundary.

## `InspectionsCalibration`

**Target role:** decomposed feature family, not a generic verification domain.

Possible destinations depend on the actual subject of each feature:

- reliability/equipment verification -> `Afal` / Reliability Verification;
- product/process quality measurement -> `QualityVerification`;
- calibration-specific mechanics -> local helper/reusable technical mechanism until concrete business ownership establishes otherwise.

Do not create a shared `Inspection` aggregate merely to preserve this old component boundary.

## `ParameterProcessChange`

**Target role:** application feature/workflow grouping only where a concrete use case still exists.

It must not own all parameter/process changes. The owning module remains authoritative:

- Quality Target -> `QualityVerification`;
- Asset state -> `AssetManagement`;
- Kiln schedule revision -> `KilnDrying`;
- LTV template version -> `LtvForms`;
- other future changes -> their own owning context.

Shared effective-date/version/routing mechanics may be reused below these boundaries.

## `Environmental`, `Risk`, `CustomerComplaints`, `SupplierQuality`

**Target role:** deferred/non-authoritative application placeholders unless future domain evidence establishes an owned ManuFactor lifecycle.

Do not delete solely because current DDD lacks a Bounded Context. Also do not allow their existence in the architecture inventory to establish semantic authority.

---

# Shared technical substrate

The domain-aligned modules consume shared technical capabilities. These are not Bounded Contexts.

```text
ManuFactor Application
|
+-- Domain-aligned modules
|   +-- AssetManagement              -> Asset Lifecycle
|   +-- Capa                         -> Corrective Action
|   +-- KilnDrying                   -> Kiln Operations
|   +-- OperationalReporting         -> Operations Record
|   +-- ProjectTracking              -> Project Tracking
|   +-- QualityVerification          -> Quality Verification
|   +-- Afal                         -> Reliability Verification
|   +-- LtvForms                     -> LTV Form Management
|   +-- Training                     -> Operational Training and Qualification
|   +-- Workforce                    -> Operational Workforce Availability
|
+-- Shared application/platform capabilities
|   +-- Identity.Login
|   +-- Profile / Permissions enforcement
|   +-- Data
|   +-- Catalog / reference resolution
|   +-- Storage / evidence documents
|   +-- Documents.Compose
|   +-- Printing
|   +-- Rules mechanics
|   +-- Notification
|   +-- Integration
|   +-- Integration.Workflows
|   +-- Analytics
|   +-- Cross-context read composition
|   +-- retained-history mechanics
|
+-- External/source-specific adapters
    +-- MP2
    +-- ProTrack
    +-- Learning/Moodle
    +-- HR/timekeeping
    +-- future explicitly evidenced sources
```

---

# Write-boundary rules

## Rule 1 — one semantic writer

Each piece of domain state has one owning Bounded Context/module.

Examples:

- Asset condition/lifecycle -> `AssetManagement`;
- Nonconformity/CAPA -> `Capa`;
- Quality Target / Quality Check / Quality Concern -> `QualityVerification`;
- Qualification -> `Training`;
- coverage judgment -> `Workforce`.

Shared Data access does not alter this rule.

## Rule 2 — cross-context requests go through application contracts

A module must not mutate another module's tables/repositories because they are in the same DbContext or database.

Use:

- command/application contract;
- explicit integration contract;
- routed external-work reference;
- asynchronous signal/event where appropriate.

## Rule 3 — external facts do not become local state automatically

Examples:

- MP2 work facts do not automatically define Asset state;
- ProTrack dimension statistics do not identify a Kiln Run;
- learning completion does not grant Qualification;
- HR/timekeeping absence does not decide staffing coverage.

The owning module performs the business interpretation.

---

# Read topology

Three read modes are explicitly distinct.

## Local operational read

A module reads its own authoritative model/projection.

Example: Project Status from `ProjectTracking`.

## Cross-context operational read

An application query composes independently authoritative results.

Example:

```text
Who Can Cover This Job Now
    Workforce contribution     -> coverage facts/judgment
    Training contribution      -> current qualification
    HR/timekeeping contribution-> availability/absence facts
```

The composer owns only the response assembly, provenance, and freshness semantics.

## Analytical read

Data is projected/copied into Analytics/Data Lake and visualized through Superset or other analytical tooling.

This is appropriate for trends, aggregates, dashboards, benchmarking, and historical analysis. It is not the default source for operational commands or current business-state authority.

---

# Quality Verification insertion point

Quality Verification is the only canonical Bounded Context without a clean current implementation home.

The target module should be introduced as `QualityVerification` when implementation reaches this context. It should initially own only the canonical concepts already established:

- Quality Concern;
- Quality Check;
- Applicable Quality Target Parameter;
- contextual applicability by mill/machine/process/time;
- retained evaluation basis and measurement evidence;
- explicit escalation/admission interface to Corrective Action.

It may reuse:

- Data;
- Catalog/reference resolution;
- Storage/evidence;
- retained-history mechanics;
- Rules/permission enforcement mechanics;
- Cross-context Read Composition;
- Analytics downstream.

It must not be implemented as a rename of `InspectionsCalibration` or `ParameterProcessChange`, because neither existing component owns the full semantic boundary.

No new deployable, database, workflow engine, or generic verification framework is required to introduce it.

---

# Persistence topology

Target physical persistence remains compact:

```text
PostgreSQL / ManuFactor database
    domain-owned tables
        Asset Lifecycle-owned
        Corrective Action-owned
        Kiln Operations-owned
        Operations Record-owned
        Project Tracking-owned
        Quality Verification-owned
        Reliability Verification-owned
        LTV-owned
        Training-owned
        Workforce-owned

    shared-technical tables
        Profile/permission data
        Catalog/reference data
        Storage/evidence metadata/content
        workflow-runtime tables
        technical job/outbox/history metadata as adopted

    projection/read tables
        denormalized operational read projections
        integration/cache/projection tables where explicitly classified
```

Table naming/schema layout may remain physically normalized as already chosen. Ownership must be documented and enforced in code/tests rather than inferred from database schema boundaries.

---

# Fitness checks to add to implementation architecture

The following are suitable for automated architecture tests or code-review gates:

1. Component/domain code cannot reference another component's repository/entity namespace for writes.
2. A cross-context command must depend on a contract/application boundary, not another module's persistence class.
3. External client libraries are reachable only through designated wrappers/adapters.
4. `QualityVerification` domain code cannot depend on `Capa` persistence or vice versa.
5. `Afal` cannot directly set Asset state.
6. `Training` source adapters cannot directly grant Operational Qualification.
7. `Workforce` source adapters cannot directly create coverage decisions.
8. Workflow runtime code cannot directly mark CAPA/LTV/Qualification/Asset domain states valid without invoking the owning application/domain behavior.
9. Analytics/Data Lake models are not referenced by command/domain code as authoritative state.
10. New reusable components/Ports must pass the materially-different-consumer test before acceptance.

---

# Migration strategy

No speculative rewrite is justified.

Apply topology incrementally:

1. mark semantic ownership in architecture artifacts now;
2. add fitness rules before broad new implementation;
3. introduce `QualityVerification` only when its slice is scheduled;
4. when `VehicleManagement` is touched, route new lifecycle behavior to `AssetManagement`;
5. when `InspectionsCalibration` is touched, classify each capability by real owner before modifying it;
6. when `ParameterProcessChange` is touched, prevent new universal ownership and route domain changes to the owning module;
7. preserve stable IDs and historical records during any later table/code movement;
8. perform physical table migrations only when actual implementation pressure warrants them.

---

# Result

The target ManuFactor topology is a **compact modular application with ten domain-aligned semantic owners and a reusable shared technical substrate**, not ten microservices and not one generic workflow/data model.

The major structural action is additive and controlled: introduce `QualityVerification` as the missing domain-aligned implementation module when needed, while gradually constraining or decomposing older component boundaries that no longer match the canonical DDD.

No new business question is required for this target topology.