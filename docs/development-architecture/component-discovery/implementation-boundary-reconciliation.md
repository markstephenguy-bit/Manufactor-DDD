# ManuFactor DDD-to-Implementation Boundary Reconciliation

**Status:** Candidate development-architecture baseline  
**Date:** 2026-08-24  
**Authority:** The canonical ManuFactor DDD remains authoritative for domain semantics. Existing `Manufactor` and `ManuFactor-arch` repositories are implementation/architecture evidence and constraints, not DDD authority.

## Purpose

Reconcile the whole-DDD reusable component map with the architecture and implementation structure already present in ManuFactor, so development does not create a competing architecture or equate Bounded Contexts with deployables, projects, schemas, or services.

## Sources reconciled

- Canonical ManuFactor DDD and `whole-ddd-component-map.md`
- `markstephenguy-bit/Manufactor` current development plan and solution structure
- `markstephenguy-bit/ManuFactor-arch` current architecture kernel, component inventory, port inventory, and current architecture drawing

## Core conclusion

The current DDD does **not** justify one deployment unit, service, database, PostgreSQL schema, or vertical implementation slice per Bounded Context.

The existing compact ManuFactor topology remains compatible with the DDD provided that semantic ownership is enforced logically in code, persistence access, integration contracts, and write paths.

The governing distinction is:

```text
Bounded Context = semantic ownership boundary
Component/Module = implementation organization
Port = reusable technical/application capability boundary
Deployable = process/operational boundary
Database/schema = persistence topology
```

None of those are automatically equivalent.

---

# 1. Deployment boundary decision

## Decision

**Preserve the existing ManuFactor process topology. Do not create a deployable per Bounded Context.**

The DDD component pass discovered substantial reusable mechanics across contexts. Splitting each Bounded Context into an independently deployable service would duplicate those mechanics, introduce network boundaries that the domain does not require, and turn semantic boundaries into operational boundaries without evidence.

Current implementation architecture already separates interactive application behavior, shared technical capabilities, and scheduled/background execution. That process topology remains the default.

## Consequences

- A Bounded Context may be implemented as one or more in-process modules/components.
- Multiple Bounded Contexts may execute in the same process.
- A shared reusable technical component may service several Bounded Contexts from the same process or shared service boundary.
- Background execution is placed in an existing worker/process because of execution characteristics, not because a Bounded Context needs its own service.
- A new deployable requires an independent operational driver such as isolation, scaling, failure containment, security boundary, incompatible runtime, or independently managed availability. Semantic distinctness alone is not enough.

## Guardrail

In-process calls do not permit one Bounded Context to mutate another Bounded Context's aggregate directly. Co-location removes network cost; it does not remove ownership.

---

# 2. Persistence topology decision

## Decision

**Preserve the existing PostgreSQL topology and current normalized database approach. Do not create a database or PostgreSQL schema per Bounded Context by default.**

Physical persistence isolation is not required to preserve DDD boundaries at the current scale. Logical ownership must instead be explicit and testable.

## Required ownership rules

Every persisted table or write model must have exactly one of these ownership classes:

1. **Bounded-Context-owned** — authoritative state for one DDD Bounded Context.
2. **Shared-technical-owned** — platform mechanics such as document storage, reference mapping, audit/history mechanics, projection metadata, notification mechanics, or integration checkpoints.
3. **Projection/read-owned** — denormalized or composed data that has no independent write authority.
4. **External-source copy/cache** — retained source facts whose external authority and provenance remain explicit.

A table must not be treated as shared domain state merely because several components can query it.

## Write rule

Only the owning Bounded Context or shared technical component may perform authoritative writes to its owned tables.

Another Bounded Context may:

- call the owner's application contract/use case;
- consume an explicit event/contract;
- read an authorized projection;
- retain a stable reference to the owner;
- compose the owner's facts in a read model.

It may not write the owner's tables directly as a shortcut.

## Relational reference rule

Cross-context references should default to stable identifiers/reference records rather than ORM object graphs.

Database foreign keys are a physical integrity tool, not proof of shared domain ownership. Where a cross-context FK is used, it must not create:

- cascade mutation across Bounded Contexts;
- aggregate-spanning ORM navigation used for writes;
- cross-context lifecycle coupling;
- an assumption that one transaction should update both sides.

Shared technical records such as Profile, stored evidence, or reference mappings may legitimately be FK targets because they are platform-owned mechanics rather than another Bounded Context's aggregate internals.

---

# 3. Transaction boundary decision

## Decision

**Transactions follow aggregate consistency boundaries, not the physical database boundary.**

The fact that several Bounded Contexts share PostgreSQL must not produce broad transactions that atomically mutate unrelated aggregates or contexts merely because SQL makes that convenient.

## Rules

- One aggregate mutation and its required retained history/evidence linkage may be one transaction when required by the aggregate invariant.
- Multiple aggregates inside one Bounded Context are not automatically one transaction.
- Cross-context workflows do not gain a shared ACID transaction merely because both contexts use the same database.
- Cross-context coordination uses explicit application orchestration, contracts, routing/reference records, or asynchronous/event-driven mechanics where required.
- Failure between independently owned operations must remain visible and recoverable rather than hidden inside a cross-context transaction.

## DDD protection

This prevents application orchestration from silently becoming the place where cross-context business invariants are invented.

---

# 4. Shared Data Port versus domain repositories

The existing architecture already exposes a reusable `Data` capability over PostgreSQL/EF Core. The DDD adds an important constraint:

**The Data Port is persistence mechanics, not a generic domain repository.**

Each canonical DDD Repository still represents access to an aggregate owned by one Bounded Context. The implementation may reuse the same EF/Npgsql infrastructure, DbContext infrastructure, transaction plumbing, and migration tooling without collapsing repository meaning.

Therefore:

```text
Shared Data mechanics
    |
    +-- Asset repository implementation
    +-- Quality Check repository implementation
    +-- Quality Concern repository implementation
    +-- Nonconformity repository implementation
    +-- Kiln Schedule repository implementation
    +-- Kiln Run repository implementation
    +-- etc.
```

is valid.

A universal repository exposing arbitrary tables/entities across contexts is not.

---

# 5. Identity and reference-resolution reconciliation

The DDD candidate `Identity & Reference Resolution` should **not automatically become a new service**.

Existing architecture already contains relevant mechanics:

- Catalog
- stable UUID identity
- hierarchy/reference registration
- `thing_links`
- external identifier fields/mappings
- Profile/identity infrastructure

## Decision

Treat the DDD candidate as a **capability assembled from existing platform mechanics first**. Add a new component only if the current Catalog/reference mechanisms cannot satisfy a concrete DDD requirement.

## Required semantic split

- **Identity.Login / Profile** answers who the application user is.
- **Catalog/reference mechanics** can resolve technical subject identities and cross-links.
- **Bounded Contexts** own the domain identity semantics of Asset, Project, Nonconformity, Qualification, Quality Concern, LTV instance, etc.

`thing_links` or Catalog membership must never imply that linked things share lifecycle ownership.

## Asset-specific requirement

Asset Lifecycle requires a stable ManuFactor Asset identity while MP2 hierarchy mappings may change. Therefore the reference mechanism must support versioned/provenanced external mappings without replacing the stable Asset identity when MP2 restructures its hierarchy.

---

# 6. Retained-history implementation direction

## Decision

**Use shared retained-history mechanics on PostgreSQL; do not introduce event sourcing merely because many contexts require history.**

Current evidence requires reliable retained state/history, not reconstruction of every aggregate exclusively from an event stream.

The reusable mechanism should support, at minimum:

- subject identity/reference;
- owning Bounded Context;
- recorded time;
- effective time when distinct;
- actor/responsible identity;
- reason;
- prior/new technical revision or state snapshot/reference;
- evidence references;
- provenance/source where applicable;
- correction/supersession linkage.

## Domain guardrail

The history infrastructure stores the fact that an accepted transition/change occurred. The owning Bounded Context still determines whether the transition was legal and what it means.

## No universal status table

The presence of shared history does not justify a shared domain status model. `Recorded`, `Qualified`, `Closed`, `Operating`, `Retired`, `Cancelled`, etc. retain context-specific meaning.

---

# 7. Evidence/document handling reconciliation

The existing `Storage` and `Documents.Compose` Ports already cover much of the DDD candidate `Evidence & Document Handling`.

## Decision

**Reuse those existing capabilities rather than create a new evidence service.**

Extend them only for mechanics demonstrated by the DDD, such as:

- immutable evidence reference;
- exact version/checksum where material;
- association to an owning domain record;
- scan/upload metadata;
- source/provenance metadata;
- linked external document support where the document remains outside ManuFactor.

## Guardrail

The storage/document layer must never determine whether evidence is sufficient to:

- mark an LTV `Recorded`;
- establish Operational Qualification;
- complete a Reliability Verification;
- accept a Nonconformity;
- close CAPA;
- evaluate a Quality Check.

Those decisions remain domain-owned.

---

# 8. Integration substrate reconciliation

The DDD strongly validates the existing `Integration` concept but requires it to be decomposed by source semantics.

## Shared mechanics

Reusable integration mechanics may own:

- connectivity;
- schedules/cursors/checkpoints;
- retry;
- source health;
- raw source identity;
- observation/import timestamps;
- provenance;
- unresolved mapping state.

## Source-specific translators remain separate

At minimum, the current DDD requires distinct translations for:

- MP2 -> Asset Lifecycle;
- ProTrack -> Kiln Operations;
- learning source -> Operational Training and Qualification;
- HR/timekeeping -> Operational Workforce Availability.

The generic integration substrate must not normalize these into one generic external-record model.

## No fabricated lineage

ProTrack moisture data has no kiln-run identifier. No integration, Catalog, projection, AI, or correlation mechanism may create individual run-to-moisture lineage from statistical similarity.

---

# 9. Projection and analytics boundary

The existing Analytics/Superset direction is compatible with the DDD provided that analytical data remains a projection.

## Decision

Separate three read concerns explicitly:

1. **BC-local query models** — projections of one context's authoritative data.
2. **External-source projections** — source-system facts retained/read with provenance.
3. **Cross-context analytical/composite projections** — deliberate joins across independently authoritative sources.

## Guardrails

- Superset/dashboard state is never authoritative domain state.
- A denormalized analytical row is not a new master record.
- Cross-context projection fields retain source/owner metadata where material.
- Stale or unavailable external state stays stale/unavailable; the composer does not invent a current value.
- Statistical inference is labeled as analysis and does not become source lineage.

---

# 10. Authorization reconciliation

The DDD candidate `Authorization / Responsibility Enforcement` maps partially to existing Identity/Permissions infrastructure, but the DDD shows that simple global RBAC is insufficient by itself.

## Decision

Use shared identity/permission mechanics to answer technical questions such as actor identity, application tier, scoped grants, and delegated responsibility.

The owning Bounded Context supplies the business requirement being checked.

Examples:

- Quality defines that salaried management may create/change Quality targets.
- Reliability defines maintenance-supervisor promotion authority.
- LTV defines Safety Office responsibility for returned-form recording.
- Training defines supervisor/designated trainer authority for qualification.
- Workforce defines responsible-supervisor ownership of the coverage judgment.

The platform enforces those requirements; it does not invent them.

---

# 11. Workflow-engine reconciliation

Existing architecture has an `Integration.Workflows` Port and Elsa capability. The DDD permits this only as workflow mechanics.

## Decision

**Do not model Bounded Context aggregate lifecycles as Elsa-owned business state machines by default.**

Elsa may coordinate long-running application/integration sequences, waiting, routing, timers, notifications, and external acknowledgements.

Aggregate lifecycle validity remains in the owning domain model.

Example:

```text
Elsa/application workflow:
  route corrective work
  wait for external completion evidence
  request effectiveness verification

Corrective Action domain:
  decides whether the Nonconformity may close
```

Elsa must not become the authority for what `Closed` means.

---

# 12. Reusable component-to-existing architecture mapping

| DDD-derived reusable capability | Existing architecture fit | Architecture action |
|---|---|---|
| Identity & Reference Resolution | Catalog + UUID/reference mechanics + `thing_links` + Profile where user identity is involved | Reuse/extend; do not create a new service yet |
| Retained History / Audit Journal | Data/PostgreSQL mechanics; domain tables already retain some lifecycle information | Add shared history mechanics/pattern; no event-store requirement |
| Evidence & Document Handling | Storage + Documents.Compose | Reuse/extend evidence metadata and linked-document mechanics |
| Effective-Dated Parameter Mechanics | Data; currently strongest DDD consumer is Quality Verification | Implement reusable mechanics only when concrete consumer shape is built; no universal Parameter domain |
| External Source Adapter Framework | Integration + wrappers/adapters | Reuse substrate; create source-specific adapters/translators |
| Reference Translation / ACL | Integration + Catalog/reference mappings | Explicit translator per integration contract |
| Projection / Query Infrastructure | Data + Analytics | Reuse; distinguish transactional projections from analytical copies |
| Cross-Context Read Composer | No need for a new domain component | Application/read composition over explicit contracts/projections |
| Authorization / Responsibility | Identity.Permissions + Profile/Identity mechanics | Extend enforcement model with BC-defined responsibility requirements |
| Work Routing & External Work Reference | Integration.Workflows + `thing_links`/reference mechanics | Reuse mechanics; reject shared Work aggregate |
| Workflow State-Machine Mechanics | Integration.Workflows / Elsa | Restrict to orchestration mechanics; domain transitions stay in BCs |
| Notification / Attention | Notification | Reuse as delivery mechanism |
| Business Subject Reference Picker / Resolver | Catalog/read models/UI | Reuse application/UI mechanics without semantic ownership |

---

# 13. Architectural questions resolved by this reconciliation

The original component map listed ten implementation questions. Current architecture plus the DDD now resolves or narrows several:

| Question | Current resolution |
|---|---|
| Deployment granularity | Preserve current compact multi-process topology; no BC-per-service default |
| Persistence isolation | One PostgreSQL topology/current normalized approach; enforce logical ownership instead of BC-per-schema/database |
| History implementation | Shared PostgreSQL retained-history mechanics; no event sourcing requirement |
| Integration execution | Adapter-specific execution using existing Integration/worker substrate; no one execution pattern forced across all sources |
| Projection consistency | Allow both transactional/local and asynchronous analytical projections; consistency must be explicit per query |
| Cross-context query composition | Use explicit read composition/projections; never direct cross-context write coupling |
| Reference resolution topology | Reuse Catalog/reference/thing-link mechanics first; no new centralized identity service justified yet |
| Evidence storage | Reuse existing Storage/Documents capabilities; extend metadata/link support rather than introduce another store |
| Authorization source | Reuse existing Identity/Profile/Permissions mechanics; BCs define business responsibility requirements |
| Workflow mechanics | Existing workflow engine is orchestration infrastructure only; domain lifecycle rules stay in BCs |

---

# 14. New implementation fitness rules

The DDD-to-architecture boundary should be mechanically challenged with these rules during development:

1. **No BC-per-deployable assumption.** A new process boundary needs an operational driver.
2. **One authoritative writer per persisted domain state.**
3. **No cross-context direct table mutation.**
4. **No cross-context ORM aggregate navigation used for writes.**
5. **No shared transaction merely because tables share PostgreSQL.**
6. **Every external copy/reference retains source authority/provenance.**
7. **No fabricated source lineage.**
8. **Shared workflow/history/evidence infrastructure cannot approve domain transitions.**
9. **Read composition cannot create write authority.**
10. **Reusable technical mechanics must survive materially different context tests before being generalized.**
11. **Existing Ports/components are checked before introducing another implementation capability, preventing duplicate mechanisms.**
12. **A database FK, shared table, common C# type, or shared UI control never establishes DDD semantic ownership by itself.**

---

# 15. Implementation sequence implication

The next implementation architecture work should not start by building ten Bounded Context slices independently.

The better sequence is:

```text
existing platform/ports
        |
        +-- strengthen shared reference + provenance mechanics
        +-- strengthen retained-history mechanics
        +-- strengthen evidence metadata/link mechanics
        +-- establish BC write-ownership rules
        +-- establish projection/read-composition pattern
        |
        +-- implement domain slices using those mechanics
```

This preserves the current compact architecture while allowing the DDD semantic model to control domain behavior.

## Current result

No business-domain contradiction was exposed. No DDD Bounded Context needs to be reopened.

The primary architectural correction produced by this pass is not a new technology or service. It is the explicit separation of **semantic ownership** from the existing **shared process and persistence topology**.
