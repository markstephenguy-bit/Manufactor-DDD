# ManuFactor Reusable Capability to Existing Port Gap Analysis

**Status:** Candidate development-architecture reconciliation  
**Date:** 2026-08-24  
**Semantic authority:** `docs/ddd`  
**Architecture evidence:** current `ManuFactor` implementation and `ManuFactor-arch` substrate

## Purpose

Test the whole-DDD reusable capability map against the existing ManuFactor Port/shared-service architecture before adding any new Port, service, or framework.

The existing architecture rule remains: do not create duplicate capability. A DDD-derived reusable need therefore does **not** automatically imply a new Port. First determine whether an existing Port or shared mechanic already supplies the technical responsibility without taking domain ownership.

---

## Capability reconciliation matrix

| DDD-derived reusable capability | Existing architecture mechanism | Coverage | Required treatment |
|---|---|---|---|
| Identity & Reference Resolution | `Catalog`, UUID identity, `manufactor.thing`, `thing_links`, Data | Partial | Reuse first. Add only the missing mechanics for external-reference namespace, mapping provenance, unresolved mapping state, and mapping history where concrete integrations require them. Do not create an enterprise master-entity domain model. |
| Retained History / Audit | `Data` + ordinary PostgreSQL tables; logging exists separately | Partial but structurally sufficient | Do not create an event store by default. Establish a shared history persistence pattern/technical helper usable by BC-owned tables. Each BC retains lifecycle/transition semantics. A dedicated Port is justified only if callers need behavior that Data cannot express without duplication. |
| Evidence & Document Handling | `Storage`, `Documents.Compose`, Data metadata | Strong but incomplete | Reuse. Close the already-known linked-document gap and add integrity/version/provenance metadata only as required. Storage does not determine evidence sufficiency. |
| Effective-Dated / Contextual Parameters | `Data`; possible `Catalog`; `Rules` | Weak as a reusable abstraction | Keep Quality Target mechanics domain-local initially, implemented using Data. Do not add a generic Parameter Port until at least one other materially different BC proves the same mechanics are reusable. |
| External Source Adapter Runtime | `Integration`; Cron/Jobs; source wrappers | Partial | Preserve one integration capability family, but concrete adapters must be source-specific. Current generic `Integration` evidence is MP2-heavy and cannot define translation semantics for ProTrack, Moodle/learning, HR/timekeeping, etc. |
| Translation / Anti-Corruption Mechanics | `Integration`, Catalog/reference mappings | Partial | Provide common mapping/error/provenance mechanics where useful, but translators remain per source/context contract. No universal canonical source model. |
| Projection / Query Infrastructure | `Data`, `Analytics`, Superset/Data Lake | Partial | Local application queries can use Data. Analytics/Superset serves analytical projections. Do not force operational application read models through the Data Lake. |
| Cross-Context Read Composition | No exact current Port; Data/Integration/Analytics cover pieces | **Gap** | Add an application/read-composition pattern, not automatically a new deployable. It must compose independently authoritative query results with provenance/freshness and expose no write capability. |
| Authorization / Responsibility Enforcement | `Identity.Login`, `Identity.Permissions`, Profile data, `Rules` | Strong conceptually; architecture records are inconsistent | Reconcile the driver/source model. Current implementation plan says ManuFactor owns Profile role/tier and Windows username resolves the Profile, while older Port inventory still describes `Identity.Permissions` as AD-driven. Business-action authority remains BC-owned. |
| Work Routing / External Work Reference | `Integration.Workflows`, `thing_links`, Catalog/reference mechanics | Strong for mechanics | Reuse routing/reference mechanics. Do not introduce shared Work/Task domain ownership. External destination state remains external unless explicitly returned by contract. |
| Workflow Execution Mechanics | `Integration.Workflows` / Elsa | Strong | Keep Elsa as orchestration infrastructure only. BC command/domain logic approves transitions; workflow coordinates waiting, routing, timers, notifications, and signals. |
| Notification / Attention | `Notification` | Strong | Reuse. Domain determines when/why/who; Notification owns delivery mechanics and retry/queue semantics only. |
| Business Subject Reference Picker / Resolver | `Catalog`, Data/read queries, UI mechanisms | Strong enough | Treat as application/UI reuse over reference/query infrastructure. No separate domain or Port required now. |

---

# Material gaps

## 1. Cross-context application read composition

This is the clearest missing reusable capability.

Canonical queries already require examples such as:

- Quality Concern + related Nonconformity/CAPA status;
- Asset state/history + MP2 references/facts;
- Kiln plan/run data + ProTrack dimension-level moisture statistics;
- Workforce coverage + Training qualification + HR/timekeeping facts;
- Reliability Verification + referenced Asset facts.

Neither `Analytics` nor Superset is the correct default solution for these application reads:

- Analytics is optimized for aggregate/analytical views and Data Lake projections;
- these queries often require current operational state and explicit source authority;
- some must display independently authoritative contributions with different freshness;
- composing them must never create write authority.

### Target mechanic

```text
Application Query / Read Composer
    -> calls owning BC query handlers/read models
    -> calls external-source read adapters where the contract requires it
    -> joins contributions for presentation
    -> retains source/provenance/freshness metadata where material
    -> never calls repositories or domain write handlers directly
    -> never persists a composite record as a new source of truth merely for convenience
```

This can initially be an in-process application pattern/library using the existing dispatch/contracts architecture. It does **not** require a new service or database.

A prebuilt integration projection is allowed later when scale/performance demands it, provided field authority and refresh semantics remain explicit.

---

## 2. Identity/Permissions source-of-truth synchronization

There is a current architecture-record inconsistency:

- older `ManuFactor-arch` Port inventory describes `Identity.Permissions` with `DirectoryServicesWrapper` / AD as its driver;
- the current implementation plan states that ManuFactor owns each Profile's app role/tier and does not consult AD groups for those permissions;
- Windows authentication supplies a trusted Windows username used to resolve the ManuFactor Profile.

This does not require a DDD change. It requires the architecture substrate to distinguish:

```text
Identity.Login
    authority: Windows/Negotiate authentication
    result: authenticated Windows identity / username

Profile / app authorization data
    authority: ManuFactor
    result: role/tier, grants, app-specific responsibility metadata

BC business authority
    authority: owning Bounded Context rules
    result: whether this actor may perform this business action in this circumstance
```

Do not allow generic RBAC to replace rules such as salaried-management control of Quality targets, maintenance-supervisor promotion of reliability findings, Safety Office LTV recording, or qualification authority.

---

## 3. End-of-Shift Reporting → MES data lake publication

This is now the preferred concrete integration forcing case.

New domain-expert evidence establishes that End-of-Shift Reporting needs to get into the MES data lake. The canonical Operations Record context already supports downstream analytical composition while retaining authority for the submitted record and its correction history.

The architecture must therefore prove:

- stable source record/revision identity;
- explicit provenance;
- idempotent delivery/replay;
- correction-safe downstream representation;
- separate domain and delivery transactions;
- destination-specific adapter isolation;
- delivery checkpoint and failure state;
- no transfer of Operations Record write authority to Analytics or the data lake.

The detailed forcing pass is recorded in `end-of-shift-mes-data-lake-pass.md`.

This flow should test the existing `Data` + `Analytics.ETL` + scheduling/integration substrate before any new Port is introduced.

---

## 4. External reference mapping completeness — deferred from first forcing case

`Catalog`, UUIDs, and `thing_links` remain a useful base for future external-reference mappings, but MP2 → Asset Lifecycle is **not ready** to define the generic contract.

That integration requires substantially more work first, including:

- inspection of the real MP2 asset/equipment hierarchy and identifiers;
- identification of stable versus mutable identifiers;
- understanding how hierarchy restructuring appears in MP2;
- WR/WO correlation and equipment-reference behavior;
- bootstrap rules for ManuFactor Asset identity;
- unmatched and ambiguous cases;
- field-by-field source authority;
- mapping history/provenance only after the above is known.

Therefore no generic external-reference mapper should be shaped from MP2 assumptions at this stage.

The shared capability remains a candidate, but its contract must wait for sufficient concrete source evidence.

---

## 5. Evidence storage completeness

Storage is already the correct shared capability. The DDD adds requirements that should be checked against the existing contract as contexts are implemented:

- stored document or first-class external/reference link;
- checksum/integrity metadata where evidence integrity matters;
- source/provenance;
- version or immutable-reference semantics where historical evaluation depends on exact evidence;
- retained association from domain record to evidence reference.

The domain record, not Storage, owns whether that evidence permits a transition.

---

# Existing Ports that must not expand into domain owners

## `Catalog`

May own technical registration, hierarchy, lookup, identifiers, cross-links, and reference metadata.

Must not own Asset, Person, Quality Target, Qualification, Project, Nonconformity, or other domain lifecycles.

## `Rules`

May execute technical rule-evaluation mechanisms when useful.

Must not become the repository of all domain invariants. Aggregate/domain behavior remains authoritative for business validity.

## `Integration.Workflows`

May own workflow runtime state.

Must not own the validity of `Closed`, `Recorded`, `Qualified`, `Retired`, `Operating`, etc.

## `Analytics`

May own ETL, analytical projections, time-series/OLAP stores, and Superset integration.

Must not become the operational write model or the default mechanism for current cross-context application composition.

For End-of-Shift Reporting specifically, Analytics may publish a downstream copy/projection to the MES data lake while Operations Record remains authoritative for the source report and correction history.

## `Data`

May provide persistence mechanics.

Must not imply that one DbContext/table set has semantic permission to mutate another context's state.

---

# Port creation test for this phase

A new reusable Port should be added only when all are true:

1. a concrete technical responsibility is required by multiple materially different consumers;
2. no current Port supplies that responsibility without semantic distortion;
3. the contract can be defined without importing one BC's domain language as universal language;
4. the capability has a clear termination/driver or a stable in-process implementation boundary;
5. adding it removes real duplication or coupling rather than adding taxonomy completeness;
6. ownership and failure behavior are explicit.

Under this test, the current pass does **not** justify a large new Port expansion.

The strongest new architecture concept is **Cross-Context Read Composition**, and it should begin as an application/read pattern using existing contracts rather than as a new network service.

The first concrete integration proof should be **End-of-Shift Reporting → MES data lake**, using the existing Analytics/ETL substrate unless the real destination contract proves a missing capability.

---

# Result

The existing ManuFactor shared-service/Port substrate covers most of the reusable mechanics found by the completed DDD. The DDD primarily tightens **ownership and guardrails**, rather than demanding a new infrastructure stack.

Current actionable architecture gaps are:

1. explicit cross-context application read composition;
2. reconciliation of Identity.Login versus ManuFactor-owned Profile/permission authority;
3. concrete End-of-Shift Reporting → MES data lake publication contract and destination discovery;
4. future external-reference mapping/provenance design after sufficient source-specific discovery, with MP2 → Asset Lifecycle explicitly deferred as a forcing case;
5. completion of evidence/document reference and integrity mechanics.

No domain contradiction was found. No new Bounded Context is required by these gaps.