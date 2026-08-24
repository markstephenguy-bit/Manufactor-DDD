# ManuFactor Whole-Model Macro-Service Discovery Map

**Status:** Discovery candidate — macro-service pass only  
**Date:** 2026-08-24  
**Authority:** Canonical DDD supplies business semantics; this artifact classifies reusable macro-service needs only.  
**Design gate:** `docs/development-architecture/component-discovery/discovery-completion-gate.md` remains closed. Nothing in this document authorizes implementation design.

## Purpose

Identify the large reusable technical/application capabilities ("macro services") that are genuinely repeated across the 10 canonical Bounded Contexts, then reconcile those capabilities with the current `ManuFactor-arch` Port inventory.

A **macro service** in this discovery pass is a reusable capability family large enough to serve multiple Bounded Contexts and contain multiple technical operations/sub-capabilities, while remaining below domain-semantic ownership.

This is deliberately different from:

- a Bounded Context;
- a deployable service;
- a .NET project;
- a single Port/driver;
- an ambient platform concern;
- a UI screen/form;
- a domain aggregate.

## Canonical Bounded Context abbreviations

| Abbrev. | Bounded Context |
|---|---|
| AL | Asset Lifecycle |
| CA | Corrective Action |
| KO | Kiln Operations |
| OR | Operations Record |
| PT | Project Tracking |
| QV | Quality Verification |
| RV | Reliability Verification |
| LTV | LTV Form Management |
| TQ | Operational Training and Qualification |
| WA | Operational Workforce Availability |

## Discovery classification

- **Confirmed macro service** — repeated responsibility is materially different across at least 3 consumers and remains coherent without absorbing domain meaning.
- **Strong candidate** — repeated responsibility is clear, but boundary/completeness still requires discovery.
- **Constrained candidate** — useful shared mechanics exist, but evidence is not strong enough for a broad macro-service boundary.
- **Sub-capability** — belongs inside a larger macro service rather than standing as a peer macro service.
- **Ambient platform concern** — real platform need, but not part of domain-driven macro-service discovery.

---

# 1. Macro-service inventory

| Macro service | Discovery status | Current architecture material | Directly evidenced BC consumers | Core reusable responsibility | Boundary that must remain outside |
|---|---|---|---|---|---|
| **Data & State Persistence Services** | Confirmed macro service | `Data` Port, PostgreSQL, EF/Npgsql | AL, CA, KO, OR, PT, QV, RV, LTV, TQ, WA | Durable storage/query mechanics, optimistic/concurrency mechanics, persistence transactions, context-owned table access | Aggregate invariants, lifecycle meaning, ownership of domain state |
| **Catalog, Reference & Hierarchy Services** | Confirmed macro service | `Catalog`, `manufactor.thing`, `thing_links`, Data.Reference | AL, OR, PT, QV, RV, LTV; reference needs also appear in CA/TQ/WA | Stable technical references, hierarchy/navigation, lookup, cross-links, subject resolution | Universal enterprise entity model; ownership of Asset/Person/Project/Qualification/etc. |
| **Forms & Structured Capture Services** | Strong candidate; major discovery item | Existing Form Engine ideas are spread across Catalog/Data/Documents; no clean single macro-service entry in current Port list | OR, LTV, RV (AFAL); possibly additional consumers only when directly evidenced | Reusable form definition/capture mechanics, field configuration, common input behavior, technical version/reference handling, shared capture shell | Form-specific business meaning, lifecycle, invariants, approval/recording semantics, treating forms as domain types |
| **Documents, Artifacts & Evidence Services** | Confirmed macro service | `Storage`, `Documents.Compose`, `Printing` | QV, CA, RV, LTV, TQ; OR where artifacts are needed | Artifact storage, rendering/composition, retrieval, integrity metadata, scan/upload, printing, evidence references | Whether evidence is sufficient, what a document means, domain transition authority |
| **Identity, Access & Responsibility Services** | Confirmed macro service; source model needs reconciliation | `Identity.Login`, `Identity.Permissions`, Profile data | All 10 require actor identity; strongest explicit responsibility rules in AL, CA, QV, RV, LTV, TQ, WA | Authentication identity, application permissions, responsibility lookup/enforcement mechanics | Business authority rules such as who may change Quality targets or promote reliability findings |
| **Integration, Source Acquisition & Provenance Services** | Confirmed macro service; current Port is too MP2-shaped | `Integration`, wrappers/adapters, Cron/ETL mechanics | AL/MP2, KO/ProTrack, TQ/learning, WA/HR-timekeeping, OR/MES and other source pulls | Connection/runtime mechanics, extraction, retries/checkpoints, source envelopes, provenance, adapter health, source-specific translation seams | Source-system authority; domain interpretation; fabricated lineage; one universal external model |
| **Operational Query & Read-Composition Services** | Strong candidate; current macro-service gap | Data queries cover pieces; no exact current Port | QV+CA, RV+AL, KO+ProTrack, WA+TQ+HR, AL+MP2; OR may compose source values | Operational read models, composition of independently authoritative facts, freshness/provenance, reference resolution for current application views | Domain writes; new composite source of truth; analytics-only semantics |
| **Analytics, Projection & Data-Lake Services** | Confirmed macro service | `Analytics`, ClickHouse/TimescaleDB, Superset, ETL | OR, KO, AL, WA; QV/CA/PT/RV where analytical views are evidenced or natural downstream consumers | Analytical ETL, time-series/OLAP projection, historical aggregations, BI/dashboards, downstream analytical publication | Operational source records, command authority, current-state cross-context orchestration |
| **Retained History & Audit Services** | Confirmed macro-service need; current first-class boundary is missing | Persistence exists; audit/history mechanics are scattered/not represented as a coherent macro service | All 10 | Technical revision/history append mechanics, actor/time/reason/provenance, correction/supersession records, audit retrieval | Domain transition legality, lifecycle semantics, replacing context-owned history meaning with generic status history |
| **Workflow, Routing & Coordination Services** | Constrained candidate | `Integration.Workflows`/Elsa, `thing_links`, Notification, Integration | CA strongly; LTV has multi-step lifecycle; RV routes promoted findings; PT may reference routed work | Long-running orchestration, waiting/signals/timers, routing envelope, external destination reference/correlation | Domain status model, CAPA/Project/LTV/Reliability lifecycle authority, generic Work/Task domain |
| **Notification & Attention Services** | Confirmed macro service | `Notification` | CA, QV, RV, LTV, PT, WA; others as events require attention | Delivery channels, retry, templates, recipient resolution, acknowledgement mechanics | Whether notification is required, escalation meaning, business deadline ownership |
| **Scheduling & Background Execution Services** | Strong platform macro-service candidate | Cron-Jobs, Cron-ETL, Hangfire/background workers | Integration/ETL consumers, periodic checks/publications, potentially CA/QV/RV/LTV/PT operational timers when evidenced | Recurring/background execution, job checkpoints, retries, operational scheduling mechanics | Business due dates, qualification expiry rules, workflow transitions, source-system authority |

---

# 2. Whole-context consumption matrix

`D` = direct canonical/evidenced need.  
`S` = supporting use is strongly implied by canonical responsibilities but should not be used alone to justify expansion.  
Blank = no current evidence strong enough to claim consumption.

| Macro service | AL | CA | KO | OR | PT | QV | RV | LTV | TQ | WA |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Data & State Persistence | D | D | D | D | D | D | D | D | D | D |
| Catalog / Reference / Hierarchy | D | S | D | D | D | D | D | D | S | S |
| Forms & Structured Capture |  |  |  | D |  |  | D | D |  |  |
| Documents / Artifacts / Evidence | S | D |  | S | S | D | D | D | D |  |
| Identity / Access / Responsibility | D | D | D | D | D | D | D | D | D | D |
| Integration / Source Acquisition / Provenance | D | S | D | D | S | S | D | S | D | D |
| Operational Query / Read Composition | D | D | D | D | S | D | D | S | D | D |
| Analytics / Projection / Data Lake | S | S | D | D | S | S | S |  | S | D |
| Retained History / Audit | D | D | D | D | D | D | D | D | D | D |
| Workflow / Routing / Coordination | S | D | S |  | S | S | D | D | S | S |
| Notification / Attention | S | D | S | S | D | D | D | D | S | D |
| Scheduling / Background Execution | S | D | S | S | S | S | S | S | S | S |

The `S` marks are intentionally conservative. They are not permission to create behavior; they identify where a macro service may eventually be consumed if the existing canonical responsibility actually needs the technical mechanic.

---

# 3. Reconciliation of current Ports to macro services

The current `ManuFactor-arch` inventory lists Ports and ambient services at one level. That list is useful implementation evidence, but it is not yet a clean macro-service taxonomy.

| Current Port / concern | Macro-service treatment | Discovery finding |
|---|---|---|
| `Data` | Sub-capability / primary Port of **Data & State Persistence** | Correct reusable foundation, but not equivalent to every persistence-related macro concern such as retained history |
| `Catalog` | Primary Port inside **Catalog, Reference & Hierarchy** | Strong fit, but `manufactor.thing`/`thing_links` must remain technical reference mechanics rather than universal domain entities |
| `Storage` | Sub-capability of **Documents, Artifacts & Evidence** | Too low-level to stand as its own macro-service peer |
| `Documents.Compose` | Sub-capability of **Documents, Artifacts & Evidence** and used by Forms | Rendering/composition is not the same capability as Forms Runtime or Evidence semantics |
| `Printing` | Sub-capability of **Documents, Artifacts & Evidence** | Output transport/formatting, not a macro-service peer |
| `Identity.Login` | Sub-capability of **Identity, Access & Responsibility** | Authentication only |
| `Identity.Permissions` | Sub-capability of **Identity, Access & Responsibility** | Current AD-driver description conflicts with newer ManuFactor-owned Profile permission evidence; discovery reconciliation still needed |
| `Integration` | Primary Port inside **Integration, Source Acquisition & Provenance** | Current definition is too tied to MP2/SQL Server to represent the full source landscape cleanly |
| `Integration.Workflows` | Sub-capability of **Workflow, Routing & Coordination** | Elsa is a runtime mechanic, not the macro service itself |
| `Analytics` | Primary Port inside **Analytics, Projection & Data Lake** | Strong fit for analytical workloads; must not absorb operational read composition |
| `Notification` | Primary Port inside **Notification & Attention** | Strong fit |
| `Rules` | **Not yet proven as a macro service**; supporting evaluation capability | Must not become a central owner of aggregate invariants. Needs a separate discovery test before promotion |
| `Realtime` | Ambient/platform transport | Not a DDD-derived macro service |
| `Observability` | Ambient platform concern | Important but outside macro-service/domain-reuse map |
| `Security` | Ambient platform concern | Important but outside macro-service/domain-reuse map |
| `DevOps` | Ambient platform concern | Outside macro-service/domain-reuse map |
| `Testing` | Ambient platform concern | Outside macro-service/domain-reuse map |
| `Governance` | Ambient platform concern unless a concrete reusable application capability is established | Configuration/validation is currently platform mechanics, not domain macro-service evidence |
| `Intelligence` | Existing optional platform capability, **not currently justified by canonical DDD reuse** | Keep outside the current DDD-derived macro-service inventory unless a concrete cross-context business need is established |
| `Compute`, `Networking`, `Geometry`, `Migration`, `IoT` | Platform/technology taxonomy | No current DDD evidence to promote them into domain-driven macro services |
| `Search` | Likely sub-capability of Catalog/Reference and Operational Read services | Do not create a standalone macro service until materially different search needs prove it |
| `Computation` | Supporting technical capability | Do not create a generic Calculation domain/service merely because multiple contexts compute values |

---

# 4. Major macro-service gaps or misclassifications found

## 4.1 Forms & Structured Capture is missing as a clean macro-service boundary

Three materially different contexts already test this:

- **Operations Record** — operations forms such as First Hour and End of Shift capture operational information; form variation does not create new domain types.
- **LTV Form Management** — forms/templates/instances are domain-significant, but reusable capture/rendering mechanics remain technical.
- **Reliability Verification / AFAL** — structured verification/category-check capture uses form-like mechanics while preserving distinct reliability semantics.

This is sufficient to keep **Forms & Structured Capture** in macro-service discovery. It does **not** justify a `GenericForm` domain aggregate.

The current architecture distributes the mechanics among `Catalog`, `Data`, `Documents.Compose`, `Storage`, and component-specific code. Discovery must determine the correct macro-service boundary and which existing Ports are merely its supporting operations.

## 4.2 Retained History & Audit is pervasive but not first-class

All 10 contexts retain meaningful history, revisions, corrections, decisions, or state changes. The need survives radically different semantics:

- Asset condition/lifecycle history;
- CAPA lifecycle/effectiveness history;
- Kiln plan/run history;
- Operations form correction history;
- Project history;
- Quality Concern/Check/Target history;
- Reliability findings;
- LTV returned-form/status history;
- qualification history;
- workforce coverage decisions.

Therefore history/audit is not just a helper inside one Component. It is a genuine macro-service need. However, its technical boundary must not invent a universal lifecycle or status vocabulary.

## 4.3 Integration is broader than the current `Integration` Port definition

The canonical model contains materially different source families:

- MP2 maintenance;
- ProTrack moisture;
- learning/LMS evidence;
- HR/timekeeping;
- MES/operations source data;
- future company systems where ManuFactor reads rather than replaces.

A Port whose concrete description is `SqlClientWrapper -> MP2 SQL Server` is evidence of one adapter, not a complete macro-service boundary.

The macro service needs source acquisition/runtime/provenance behavior broad enough to host heterogeneous adapters without normalizing away source semantics.

## 4.4 Operational Read Composition must remain separate from Analytics

The DDD repeatedly needs current operational facts from multiple authorities. Examples include Workforce + Qualification + HR facts and Reliability + Asset facts. This is different from BI/data-lake projection.

Current architecture has no clean macro-service boundary for this responsibility. It remains a major discovery item.

## 4.5 Identity needs responsibility semantics, not only login/AD permissions

Canonical business authority is richer than authentication and generic group membership. Shared identity/access mechanics are justified, but the macro service must support context-owned responsibility decisions without becoming the owner of those decisions.

The existing architecture evidence is internally inconsistent about whether application permissions are AD-driven or ManuFactor Profile-owned. That must be resolved during discovery before this macro service can be called complete.

---

# 5. Candidates deliberately *not* promoted to macro services yet

## Rules

There is repeated need for validation/evaluation mechanics, but the canonical DDD already contains 37 invariants and explicit domain authority. A broad Rules macro service would be dangerous unless the remaining discovery proves a class of configurable rules that are genuinely technical/application-owned across materially different contexts.

Current status: **supporting capability; promotion not justified yet**.

## Effective-dated parameters

Quality Verification strongly needs contextual/effective-dated target mechanics. Other contexts do not yet provide enough equivalent evidence.

Current status: **domain-local/reusable helper candidate; not a macro service yet**.

## Generic Work / Task

Corrective work, Project work, MP2 work, and other work references have different ownership and lifecycle semantics.

Current status: **rejected as a macro domain service**. Routing/reference mechanics belong under Workflow/Integration.

## Search

Search/reference picking is useful broadly, but current evidence can be satisfied as sub-capabilities of Catalog/Reference and Operational Read services.

Current status: **not an independent macro service**.

## Intelligence / AI

The platform already has an Intelligence capability, but the canonical DDD does not currently demonstrate a repeated business responsibility requiring AI across three materially different Bounded Contexts.

Current status: **existing platform capability outside this DDD-derived macro-service pass**.

---

# 6. Macro-service boundary tests still required before discovery can close

The discovery-completion gate remains **closed**. Major work remains.

1. **Forms boundary test** — determine whether Forms & Structured Capture is one coherent macro service or should split into form-definition/capture versus document/rendering concerns. Test OR, LTV, and RV explicitly.
2. **History/Audit boundary test** — determine the common retained-history mechanics across at least AL, OR, QV, and TQ without producing a universal lifecycle model.
3. **Catalog/Reference boundary test** — distinguish hierarchy/catalog/reference lookup from external-system identity mapping and from operational query composition.
4. **Integration boundary test** — compare MP2, ProTrack, LMS, HR/timekeeping, and MES/source-data acquisition to discover the true common macro layer versus adapter-local behavior.
5. **Operational Read boundary test** — prove what is common across WA+TQ+HR, RV+AL, QV+CA, and KO+ProTrack without drifting into Analytics.
6. **Identity/Responsibility boundary test** — reconcile authentication, ManuFactor Profile/permissions, and BC-specific responsibility/authority.
7. **Workflow boundary test** — determine whether CAPA/LTV/RV provide enough materially different evidence for a macro-service boundary beyond Elsa runtime mechanics.
8. **Notification boundary test** — verify whether acknowledgement/escalation/subscription mechanics are common or context-local.
9. **Scheduling/background boundary test** — distinguish generic execution mechanics from business scheduling and lifecycle timers.
10. **Rules capability test** — decide whether `Rules` remains a technical helper or has enough repeated configurable semantics to be a macro service.
11. **Current architecture overlap audit** — locate where `Catalog`, `Data`, `Integration`, `Analytics`, Form Engine ideas, `thing_links`, and background jobs currently duplicate or cross responsibilities.
12. **Coverage audit** — verify that every canonical command/query/use case can be supported by the macro-service inventory without inventing a new generic domain abstraction.

Until these are complete, **do not begin "design using DDD."**

---

# Current discovery conclusion

The first macro-service normalization produces **nine high-confidence/confirmed macro-service families**, plus three strong/constrained candidates that need substantial whole-model testing:

### High-confidence / confirmed

1. Data & State Persistence
2. Catalog, Reference & Hierarchy
3. Documents, Artifacts & Evidence
4. Identity, Access & Responsibility
5. Integration, Source Acquisition & Provenance
6. Analytics, Projection & Data Lake
7. Retained History & Audit
8. Notification & Attention
9. Operational Query & Read Composition (need is strong; exact boundary remains to be proven)

### Major candidates requiring more discovery

10. Forms & Structured Capture
11. Workflow, Routing & Coordination
12. Scheduling & Background Execution

The major discovery work is **not complete**. The next pass should test these macro-service boundaries across the model, beginning with Forms, History/Audit, Catalog/Reference, and Integration because those currently have the greatest overlap and classification ambiguity.