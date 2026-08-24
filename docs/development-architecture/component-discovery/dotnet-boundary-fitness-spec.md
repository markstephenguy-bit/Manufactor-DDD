# ManuFactor .NET Component-Boundary Fitness Specification

**Status:** Candidate enforcement specification  
**Date:** 2026-08-24  
**Applies to:** `markstephenguy-bit/Manufactor` current .NET solution  
**Semantic authority:** canonical ManuFactor DDD

## Purpose

Translate the DDD ownership rules and target component topology into mechanical architecture tests that can be enforced in the existing `ManuFactor.ArchitectureTests` project.

The current solution already has NetArchTest rules for:

- Contracts independence;
- Components not depending on Adapters;
- Components not referencing one another;
- inward-only Component Domain dependencies;
- WebApp as binding-only UI.

Those rules remain valid. This specification extends them to protect the newly reconciled DDD boundaries.

---

# Canonical implementation homes

The enforced domain-aligned Component namespace set should converge to:

- `ManuFactor.Components.AssetManagement` → Asset Lifecycle
- `ManuFactor.Components.Capa` → Corrective Action
- `ManuFactor.Components.KilnDrying` → Kiln Operations
- `ManuFactor.Components.OperationalReporting` → Operations Record
- `ManuFactor.Components.ProjectTracking` → Project Tracking
- `ManuFactor.Components.QualityVerification` → Quality Verification, when implemented
- `ManuFactor.Components.Afal` → Reliability Verification
- `ManuFactor.Components.LtvForms` → LTV Form Management
- `ManuFactor.Components.Training` → Operational Training and Qualification
- `ManuFactor.Components.Workforce` → Operational Workforce Availability

Legacy namespaces may temporarily remain, but new domain semantics must not be added to them when the canonical owner is one of the above.

---

# Required enforcement rules

## F-01 — No Component-to-Component implementation dependency

**Already present; retain.**

A domain-aligned Component may not reference another Component's Domain, Application, Infrastructure, entity, repository, or handler implementation.

Cross-context interaction must use explicit contracts/query composition/application orchestration.

## F-02 — Domain code cannot depend on shared implementation adapters

**Already present; retain and tighten where needed.**

Component Domain namespaces may depend on:

- their own Domain types;
- primitive/framework types that do not import infrastructure semantics;
- intentionally shared semantic-free contract/value types if approved.

They may not depend on:

- `ManuFactor.Adapters`;
- EF Core/Npgsql/SqlClient types;
- Elsa workflow types;
- Superset/ClickHouse client types;
- transport/client wrappers;
- WebApp.

## F-03 — WebApp is binding-only

**Already present; retain.**

The UI may construct Commands/Queries and render results. It may not reach repositories, DbContexts, domain internals, or adapter wrappers.

## F-04 — Only Adapters may reference external client libraries

Add/retain a mechanical rule that external technology client namespaces are confined to `ManuFactor.Adapters` or approved platform-host plumbing.

Examples:

- Npgsql / EF Core provider-specific implementation;
- Microsoft.Data.SqlClient;
- ClickHouse client;
- AD/LDAP client libraries;
- Elsa implementation APIs;
- external-system SDKs.

Contracts and Components must not reference these types directly.

## F-05 — No cross-context repository or persistence mutation

Because all components may share one physical PostgreSQL database, namespace/project tests alone are insufficient.

Enforce by convention plus architecture tests:

- each Component's Application/Domain code may invoke only its own write commands/repositories;
- cross-context orchestration uses Contracts;
- no Component may import another Component's persistence entity/configuration namespace;
- no navigation property or ORM relationship should expose another context's mutable entity object.

Foreign/reference identifiers across context boundaries are scalar/reference values, not ORM object graphs.

## F-06 — Shared technical tables cannot be domain owners

Shared mechanisms such as Catalog, Storage, profiles, publication checkpoints, `thing_links`, and audit/history infrastructure may be referenced through Ports/contracts.

Tests should prohibit domain code from treating shared infrastructure entity classes as aggregate roots.

Where possible, Component code receives IDs/reference DTOs rather than shared EF entities.

## F-07 — Analytics code cannot perform domain writes

`CronEtl`, Analytics handlers/adapters, ClickHouse/Data Lake code, and projection jobs must not reference Component write-command handlers or Component persistence mutation internals.

Allowed direction:

```text
Analytics/ETL -> read/export contract -> destination adapter
```

Prohibited direction:

```text
Analytics/ETL -> Component repository/domain mutation
```

End-of-Shift Reporting is the first concrete enforcement case.

## F-08 — Component domain transaction does not depend on analytical delivery

No Operations Record command handler may depend on a concrete MES-data-lake client or require analytical delivery success to commit the source record unless a future business rule explicitly changes this.

This can be partly enforced statically by prohibiting `OperationalReporting.Domain` and `OperationalReporting.Application` from referencing destination adapter namespaces.

## F-09 — Workflow runtime cannot define domain validity

Elsa/`Integration.Workflows` may live only outside Component Domain namespaces.

No Domain type should derive from or expose Elsa workflow state classes.

Workflow activities may dispatch owning-Component commands, but may not mutate context tables directly.

## F-10 — No universal status dependency across Component domains

A Component's Domain must not depend on a shared `Status`, `WorkflowStatus`, or `ThingStatus` type that carries cross-domain transition meaning.

Shared technical status cataloging is allowed only if the Component retains its own domain status vocabulary and invariant enforcement.

## F-11 — No generic Record/Task/Verification aggregate leakage

Architecture tests and code review should reject new shared Domain namespaces/types such as:

- `GenericRecord`;
- `GenericTask`;
- `GenericVerification`;
- `BusinessWorkflow`;
- `UniversalParameter`.

Shared transport/mechanical DTOs may be generic; domain aggregates may not.

## F-12 — Legacy component namespaces cannot silently become new domain owners

For these legacy Component names:

- `VehicleManagement`;
- `InspectionsCalibration`;
- `ParameterProcessChange`;
- `Environmental`;
- `Risk`;
- `CustomerComplaints`;
- `SupplierQuality` if present;

new Domain-layer types require explicit architecture review against the canonical DDD before addition.

This is best enforced initially by a deny-list test for new `.Domain` namespaces/types under those components, with narrow exceptions for already-existing code until migrated.

## F-13 — Quality Verification gets its own implementation namespace when built

Quality target/check/concern domain types must not be added under `InspectionsCalibration` or `ParameterProcessChange`.

When implementation begins, they belong under `ManuFactor.Components.QualityVerification`.

## F-14 — Vehicle lifecycle semantics belong to AssetManagement

New vehicle condition/lifecycle/domain-state types must not be added under `VehicleManagement.Domain`.

Vehicle-specific UI/filter/forms may remain outside the domain layer as feature organization if useful.

## F-15 — Cross-context reads remain read-only

Any read composer namespace must:

- depend on query contracts/read DTOs only;
- not depend on Component repositories;
- not expose write commands;
- not persist a composite record as domain truth merely for convenience.

---

# Suggested test organization

Extend `BoundaryTests.cs` or split into focused files:

```text
ManuFactor.ArchitectureTests/
  AssemblyBoundaryTests.cs
  ComponentIsolationTests.cs
  DomainPurityTests.cs
  ExternalClientEncapsulationTests.cs
  LegacyOwnershipGuardTests.cs
  AnalyticsBoundaryTests.cs
  ReadCompositionTests.cs
```

No new test project is required.

---

# Immediate tests justified now

The following can be implemented without waiting for additional business discovery:

1. external client libraries referenced only from Adapters/platform-approved hosts;
2. `CronEtl` must not depend on Component Domain/Infrastructure implementation namespaces;
3. no legacy `VehicleManagement.Domain` expansion;
4. no Quality domain under `InspectionsCalibration`/`ParameterProcessChange`;
5. no shared universal domain status/task/record/verification namespace;
6. cross-component reference remains contract-only;
7. destination-specific data-lake client types cannot appear in `OperationalReporting.Domain`.

The exact MES-data-lake client namespace cannot be added to the deny list until the destination technology is verified, but the architectural direction can already be tested by project/namespace dependency.

---

# Result

The existing architecture-test project is sufficient to enforce the DDD-aligned topology. No new testing framework or deployable is required.

The next implementation step should add these rules incrementally to `ManuFactor.ArchitectureTests` before domain code starts moving across reconciled boundaries.
