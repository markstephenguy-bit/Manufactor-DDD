# Live ManuFactor Architecture Substrate — Macro-Service Reconciliation

**Status:** Discovery reconciliation; no implementation/design decisions  
**Date:** 2026-08-24  
**Semantic authority:** `docs/ddd`  
**Architecture evidence:** live `ManuFactor-arch` Drawings/Kernel/Taxonomy. The binary workbook remains the architecture repository's declared live database, but this reconciliation uses the live textual substrate that the workbook is intended to summarize/derive alongside.

## Purpose

Reconcile the DDD-derived normalized macro-service inventory against the current live ManuFactor architecture substrate without entering DDD-driven implementation design.

This pass answers only:

- what the live architecture currently calls a Shared Service or capability;
- which of those are genuine macro-service responsibilities under the whole-DDD test;
- which are sub-capabilities or ambient platform concerns rather than macro services;
- which major DDD-derived macro-service responsibilities are absent or obscured in the live substrate;
- which live business-component relationships are stale or unsupported by the completed DDD and therefore cannot be used to justify macro-service boundaries.

## Live architecture governance alignment

The live Architect Kernel already contains two rules that strongly align with the current DDD macro-service discovery method:

1. **Prospective shared-service test:** a shared-service/Port decision must be justified the same way for a different hypothetical Component; otherwise it remains Component-level content.
2. **No duplicate capability rule:** two Components, Shared Services, or Ports must not perform the same capability; decomposition against the full set is mandatory before accepting a new shared capability.

Therefore the DDD whole-model macro-service pass is not a competing method. It supplies a stronger semantic consumer set against which the existing architecture's shared-service inventory can be tested.

## Critical distinction: taxonomy root != macro service

The live taxonomy is intentionally broad. It contains roots/leaves for many technology and platform capabilities, including Identity, Governance, Observability, Compute, Storage, Data, Computation, Networking, Realtime, Integration, Security, Intelligence, Analytics, Geometry, Search, Notification, Documents, and other categories.

The live `architecture.dsl` currently mirrors many of those taxonomy roots directly as components inside `Shared-Services API Host`.

That does **not** make every taxonomy root a macro service.

The normalized DDD-derived macro-service inventory is a higher-level grouping based on repeated business/application responsibilities across the 10 Bounded Contexts. Taxonomy roots/leaves are capability vocabulary used to realize those responsibilities.

## Reclassification of current Shared-Services API components

| Live shared-service component / taxonomy family | Macro-service treatment | Discovery finding |
|---|---|---|
| `Identity` | **Macro service:** Identity, Access & Responsibility | Genuine cross-context macro responsibility. Authentication identity, ManuFactor Profile/app role, and BC business authority must remain distinct layers. |
| `Governance` | **Ambient/supporting capability**, not independent DDD macro service | Validation/configuration/policy mechanics are technical support. Domain validation/invariants remain BC-owned. |
| `Observability` | **Ambient platform concern** | Logging/metrics/tracing/technical audit are platform concerns. They do not replace Retained History. |
| `Compute` | **Platform capability**, not DDD macro service | Generic runtime/hosting/batch capability. Scheduling/background execution is the DDD-relevant macro responsibility where repeated. |
| `Storage / Documents` | **Macro service:** Documents, Artifacts & Evidence | Broadly correct family, but Storage/Compose/OCR/Printing are sub-capabilities. Evidence meaning/sufficiency remains BC-owned. |
| `Data` | **Macro service:** Data & State Persistence | Genuine universal supporting responsibility, but domain state ownership remains BC-local. `Data.Reference` also participates in Catalog/Reference/Hierarchy. |
| `Computation` | **Sub-capability**, not macro service | Math/stats/formula/query mechanics may support multiple macro services and BCs but do not establish an independent domain/application macro responsibility. |
| `Networking` | **Ambient/platform transport capability** | HTTP/socket/resilience/gateway mechanics support Integration and other services; not a DDD-derived macro service itself. |
| `Realtime` | **Platform transport capability** | SignalR/pub-sub/presence mechanics do not constitute a domain/application macro service under current DDD evidence. |
| `Integration.Workflows` | **Supporting workflow-runtime capability**, not the Integration macro service | Elsa-like orchestration is narrower than the actual DDD need. It does not cover source acquisition, translation/provenance, or external-work reference coordination by itself. |
| `Security` | **Ambient platform concern** | Security mechanisms apply broadly but are not a DDD-derived business/application macro-service responsibility. |
| `Intelligence` | **Outside current DDD-derived macro map** | AI is a valid platform capability but current DDD does not establish it as a repeated responsibility needed to support the 10 BCs. |
| `Analytics.ETL` | **Macro service:** Analytics, Projection & Data Lake, with overlap warning | Analytical transformation/projection belongs here. Source acquisition/provenance must not be hidden inside Analytics.ETL when the same integration mechanics serve operational reads and other non-analytical consumers. |
| `Geometry` | **Sub/platform capability** | No current whole-DDD macro-service need. |
| `Search` | **Sub-capability** | Search/indexing supports Catalog, operational read composition, and other consumers. No evidence for an independent macro-service boundary. |
| `Notification` | **Macro service:** Notification & Attention | Genuine repeated cross-context delivery/attention mechanics. Business escalation meaning remains BC-owned. |
| `DevOps` | **Ambient platform concern** | Deployment/operations mechanics, not a DDD-derived macro service. |
| `IoT` | **Potential future integration sub-capability** | Current DDD does not justify a standalone macro service. Future device/process-control evidence may change this. |
| `Migration` | **Platform/data lifecycle capability** | No independent macro-service evidence from current DDD. |
| `Testing` | **Ambient engineering concern** | Not a runtime macro service. |
| `Catalog` | **Macro service:** Catalog, Reference & Hierarchy | Genuine cross-context reference/hierarchy need. Must not become generic persistence owner for domain objects. |

## DDD-derived macro services missing or obscured in the live Shared-Services list

### 1. Forms & Structured Capture

The live shared-service list has no explicit macro-service home for reusable structured capture mechanics.

Current evidence across Operations Record, LTV Form Management, and Reliability Verification proves repeated capture mechanics. These mechanics are not equivalent to Documents, Data, or Catalog even though those capabilities support forms.

This is a **macro-service discovery gap in the live map**, not yet a design decision about a new deployable/service/Port.

### 2. Retained History

The live taxonomy exposes `Observability.Audit`, but technical audit is not sufficient for the DDD's retained business-history requirement.

Every current BC requires retained business history in some form: revision/correction, state transition, evidence/reason, supersession, historical basis, or retained judgment.

This is a **semantic capability gap/underclassification** in the live shared-service view. It may ultimately be realized through existing persistence mechanics, but the macro responsibility must be named separately from technical audit.

### 3. Operational Query & Read Composition

The live architecture has Data, Catalog, Integration/ETL, and Analytics pieces, but no clean responsibility for application-facing reads that compose current facts across independently authoritative contexts/sources.

This responsibility is repeatedly required by the canonical queries/context map and must remain distinct from Analytics/BI and from source ingestion.

This is a **major missing macro-service responsibility in the live map**.

### 4. Integration, Source Acquisition & Provenance

The live DSL represents `Integration.Workflows` rather than a general Integration shared-service component, while the taxonomy itself has a much broader Integration root (`Connectors`, `Events`, `Envelopes`, `Schemas`, `Bindings`, `Jobs`, etc.).

The DDD requires a general macro responsibility for source acquisition/publication mechanics, translation boundaries, checkpoints/retries, source identifiers, and provenance across MP2, ProTrack, learning systems, HR/timekeeping, MES/operations sources, and future systems.

`Integration.Workflows` is only one supporting capability inside this larger responsibility.

### 5. Work Routing & External Reference Coordination

CAPA, Reliability Verification, and Project Tracking establish a repeated need to route/associate work across ownership boundaries without claiming ownership of the destination work lifecycle.

The live map currently spreads this across direct Component coupling, Catalog/Thing links, and Integration.Workflows.

That repeated responsibility is coherent enough to remain a macro-service candidate, while a generic shared `Work`/`Task` domain model remains rejected.

### 6. Scheduling & Background Execution

The live architecture has `Cron-ETL Host`, `Cron-Jobs Host`, taxonomy `Integration.Jobs`, and historical Hangfire/Cron mechanics. The repeated responsibility exists, but it is represented as execution hosts/capabilities rather than a normalized platform macro responsibility.

The DDD-derived classification remains **platform macro service**, not a domain service.

## Live business-component map is not valid semantic authority after DDD completion

The current `architecture.dsl` still includes business components that do not correspond cleanly to current canonical Bounded Contexts, including:

- `Parameter/Process Change`
- `Inspections/Calibration`
- `Supplier/Incoming Quality`
- `Customer Complaints`
- `Risk`
- `Environmental`
- `Vehicle Management`

It also contains direct business-component arrows such as:

- Asset Management -> CAPA `triggers`
- Operational Reporting -> CAPA `triggers`
- LTV Forms -> CAPA `triggers`
- Inspections -> CAPA `triggers`
- Supplier Quality -> CAPA `triggers`
- Customer Complaints -> CAPA `triggers`
- CAPA -> Parameter/Process Change `corrective action`
- Kiln Drying -> Parameter/Process Change `optimizer changes`
- Inspections -> Kiln Drying
- Inspections -> Asset Management

The completed DDD does not support treating those arrows as generally authoritative relationships. Some may represent historical architecture assumptions, possible references, or future evidence; others overlap or conflict with the completed context map.

**Discovery rule:** these live direct-coupling arrows must not be used to justify macro-service boundaries unless independently supported by canonical DDD evidence.

## Live source/system evidence remains useful

The architecture drawing still supplies valuable implementation-environment evidence, including Windows/AD, Moodle, MES, MP2, Kronos, GP BI/SSAS, PostgreSQL, ManuFactor Data Lake, and existing host/container boundaries.

Those facts may inform later design, but during the discovery phase they are used only to verify that the macro-service map can accommodate the known source/system classes without inventing a source-specific macro service for each one.

## Reconciled macro-service set after live-substrate comparison

No new major macro-service category is required by the live textual substrate beyond the normalized inventory already established:

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

The live substrate **strengthens** several of these and exposes classification gaps, but it does not add another major category.

## Remaining major discovery work

The macro-service categories are stable, but the phase gate remains closed until the following are completed:

1. Reconcile the normalized macro-service map against the live workbook's actual `Components`, `Ports`, `Wrappers`, `Technologies`, `Endpoints`, and related tabs when that binary content is directly inspectable.
2. Reconcile each canonical BC's macro-service consumption against the live component/Port assignment without treating stale business components as DDD authority.
3. Audit the live taxonomy/architecture for any capability that appears to duplicate another normalized macro service or crosses more than one macro-service responsibility.
4. Produce one final whole-model macro-service consistency report with remaining mismatches classified as:
   - naming/taxonomy-only;
   - stale architecture evidence;
   - missing macro responsibility;
   - overlapping capability;
   - genuinely unresolved business/domain evidence.
5. Mark confirms that no major discovery work remains before the design gate can be considered for opening.

## No design conclusion

This artifact does not select deployment topology, physical service boundaries, Port interfaces, storage models, eventing patterns, workflow products, or implementation sequencing. Those remain outside the current phase gate.
