# Current Architecture Macro-Service Overlap Audit

**Status:** Discovery-only audit  
**Date:** 2026-08-24  
**Design gate:** CLOSED — this document identifies classification/ownership problems only; it does not prescribe implementation changes.

## Purpose

Compare the revised macro-service discovery map against current/historical `ManuFactor-arch` mechanisms and identify:

- responsibilities assigned to the wrong macro service;
- low-level Ports presented as peer macro services;
- overlapping ownership;
- generic mechanisms that risk swallowing domain semantics;
- macro-service needs that are currently absent or only implicit.

The current Port inventory remains useful implementation evidence. It is not treated as semantic authority.

---

# 1. Catalog currently extends beyond the confirmed macro-service boundary

Historical/current architecture evidence describes a generic `catalog_entities` structure containing `entity_type`, hierarchy `path`, and generic `definition`, with examples including `Template`, `PrintedInstance`, and `HierarchyNode`.

The macro-service boundary test established that **Catalog, Reference & Hierarchy** may own:

- technical registration/reference identity;
- hierarchy/navigation;
- lookup;
- typed cross-links/reference metadata.

It must not become the generic persistence owner for domain concepts merely because they need catalog registration.

## Classification problem

A `HierarchyNode` can naturally be Catalog-owned technical/reference data.

An LTV Template or LTV Form Instance is different: canonical LTV DDD makes those domain concepts owned by LTV Form Management. Catalog may hold a reference/index/navigation registration for them, but a generic catalog record must not become their authoritative semantic representation.

The same rule applies to future Assets, Projects, Quality Targets, Qualifications, Nonconformities, etc.

## Discovery finding

**Current Catalog evidence conflates registration/reference with generic entity persistence.**

This is a major macro-service ownership issue that must be reconciled before discovery closes.

Status: **MAJOR OVERLAP / OWNERSHIP RISK**.

---

# 2. `manufactor.thing` and `thing_links` need narrower classification

The current architecture treats these as cross-cutting mechanisms.

## Valid shared roles

- stable technical envelope/reference ID;
- typed link between independently owned records;
- reason/provenance for the link;
- navigation/correlation support.

## Invalid role

They must not imply that every linked record is one common domain `Thing`, with common lifecycle/status/ownership.

## Macro-service mapping

- stable reference envelope -> **Catalog, Reference & Hierarchy**;
- external-system correlation -> **Integration, Source Acquisition & Provenance**;
- routed-work destination correlation -> **Work Routing & External Reference Coordination**;
- composite current-state views -> **Operational Query & Read Composition**.

A single `thing_links` storage mechanic may technically support several of these use cases, but technical reuse does not collapse the macro-service responsibilities.

Status: **SHARED MECHANIC; MUST NOT BECOME MACRO-SERVICE SEMANTIC OWNER**.

---

# 3. Historical Form Engine concept spans too many responsibilities

Earlier architecture material bundled a Form baseline from:

- Catalog registration;
- Data persistence;
- Documents.Compose;
- Storage;
- Save/Edit;
- sometimes Printing;
- sometimes lifecycle/status assumptions.

The boundary tests show that this bundle crosses at least three macro-service families:

1. **Forms & Structured Capture** — capture/input shell and field interaction mechanics;
2. **Documents, Artifacts & Evidence** — rendering, storage, scanning, printing/artifacts;
3. **Data & State Persistence** — durable owning-record persistence.

Catalog/reference may be consumed, but it is not inherently part of every form's semantic definition.

## Domain-ownership risk

LTV Template/version is domain-significant in LTV. Operations forms do not prove equivalent domain template semantics. AFAL structured capture does not prove them either.

Therefore a universal `Catalog.Forms` model is not currently justified by the completed DDD.

Status: **MAJOR OVERLAP; FORMS BOUNDARY STILL OPEN**.

---

# 4. `Data` is correctly ubiquitous but too low-level to represent all record/state concerns

The current architecture correctly finds `Data` in nearly every component.

That demonstrates a universal persistence requirement, but it does not make `Data` the semantic home of:

- retained history;
- forms;
- evidence;
- catalog/reference;
- operational read composition.

Generic `IDataPort<T>`-style access can be a technical mechanism while the macro-service families above remain distinct.

Status: **VALID SUBSTRATE; DO NOT OVERLOAD SEMANTICALLY**.

---

# 5. Integration is currently described as an MP2 adapter rather than the macro service

The current `Integration` Port is described with `SqlClientWrapper` / MP2 SQL Server. That is one concrete source adapter.

The confirmed macro-service family must cover heterogeneous source/destination interaction such as:

- MP2;
- ProTrack;
- learning systems;
- HR/timekeeping;
- MES/process sources;
- future source readers;
- outbound publication where independently evidenced.

## Discovery classification

`Integration` at macro-service level = source/destination interaction, execution, provenance, health/checkpoint mechanics.

MP2 SQL access = one adapter/termination under that family.

Status: **CURRENT PORT DESCRIPTION IS TOO SOURCE-SPECIFIC FOR MACRO-SERVICE ROLE**.

---

# 6. Integration and Analytics overlap at ETL

Current architecture material places ETL under Analytics and also gives Cron-ETL/source-reading responsibilities to integration infrastructure.

The macro-service boundary tests produce a clearer conceptual distinction:

## Integration

- acquire/publish data across a system boundary;
- know source/destination connection/protocol;
- preserve source/destination provenance;
- checkpoint extraction/delivery;
- report transport/source failures.

## Analytics

- transform facts into analytical structures;
- aggregate/time-series/OLAP projection;
- analytical retention/query;
- BI/dashboard exposure.

## Overlap point

An ETL process can invoke both. The existence of a single ETL job does not mean source acquisition and analytical modeling are one macro-service responsibility.

Status: **MAJOR RESPONSIBILITY OVERLAP REQUIRING TAXONOMY RECONCILIATION**.

---

# 7. Operational Read Composition is missing from the current Port taxonomy

The DDD strongly requires current operational composition across independent authorities.

Existing mechanisms each cover only part:

- `Data` -> local persistence/read;
- `Catalog` -> reference/lookup;
- `Integration` -> external acquisition;
- `Analytics` -> analytical projection.

None owns the application-level responsibility of combining current independently authoritative results while retaining provenance/freshness and no write authority.

Status: **MAJOR MACRO-SERVICE GAP**.

---

# 8. Retained History/Audit is missing as a coherent macro-service family

History is pervasive across all 10 Bounded Contexts, but the current Port inventory does not represent it cleanly.

It appears implicitly in:

- domain/component tables;
- status fields;
- audit/logging concepts;
- correction/supersession needs.

The DDD requires more than logging and more than generic `status` storage.

Status: **MAJOR MACRO-SERVICE GAP / CROSS-CUTTING RESPONSIBILITY CURRENTLY SCATTERED**.

---

# 9. `Thing Status Catalog` conflicts with the macro-service/domain boundary if treated universally

A technical registry of status codes per owning context may be useful.

A universal status model is not supported.

Canonical examples intentionally differ:

- Asset Condition vs Asset Lifecycle State;
- LTV `Recorded`;
- Qualification current/withdrawn/superseded semantics;
- Quality Concern handling;
- CAPA closure;
- Project lifecycle;
- Kiln schedule/run lifecycle.

Status: **TECHNICAL CATALOGING MAY REMAIN; UNIVERSAL STATUS SEMANTICS REJECTED**.

---

# 10. `Integration.Workflows` / Elsa is over-promoted if treated as the Workflow macro service

Pass 2 separated:

- **Work Routing & External Reference Coordination** — confirmed macro-service need;
- long-running workflow runtime — supporting capability not yet proven as a macro service.

Elsa is evidence of a runtime option/mechanism, not evidence that CAPA/LTV/Reliability/Project share one workflow domain or even one required workflow-runtime model.

Status: **SUPPORTING CAPABILITY; CURRENT TAXONOMY PLACEMENT RISKS OVER-PROMOTION**.

---

# 11. `Rules` is over-promoted if interpreted as owner of business rules

Canonical invariants are context/aggregate owned.

The reusable portion of `Rules` is technical expression/predicate/policy evaluation only.

The current macro-service discovery does not justify a Rules macro service.

Status: **SUPPORTING CAPABILITY, NOT MACRO SERVICE**.

---

# 12. Identity taxonomy is incomplete/internally inconsistent

Current architecture separates:

- `Identity.Login` -> Windows/Negotiate;
- `Identity.Permissions` -> described as AD/DirectoryServices.

Newer implementation evidence indicates:

- Windows identity establishes login identity;
- ManuFactor Profile data owns app role/tier/grants.

The canonical DDD additionally requires context-specific responsibility/authority rules.

The macro service must distinguish:

1. authenticated identity;
2. application profile/permission/responsibility facts;
3. domain-specific business authority.

Status: **MAJOR AUTHORITY-MODEL RECONCILIATION REQUIRED**.

---

# 13. Storage / Compose / Printing should not appear as peer macro services

These are real reusable technical Ports, but macro-service classification places them under **Documents, Artifacts & Evidence**.

This distinction matters because otherwise architecture diagrams and inventories make low-level mechanics appear equal in semantic scope to Integration, Analytics, Identity, or Catalog.

Status: **VALID PORTS; MACRO-SERVICE TAXONOMY LEVEL INCORRECT IF SHOWN AS PEERS**.

---

# 14. Cron-Jobs / Cron-ETL belong to platform background execution

Scheduled/background execution is a platform macro service.

Cron hosts/jobs are mechanisms under it. They must not own:

- Kiln scheduling;
- Project scheduling;
- workforce/vacation scheduling;
- CAPA lifecycle deadlines;
- qualification validity;
- domain transitions.

Status: **VALID PLATFORM MECHANISM; KEEP OUT OF DOMAIN MACRO-SERVICE SEMANTICS**.

---

# 15. Intelligence / AI is not part of the current DDD-derived macro-service map

The existing architecture contains an Intelligence capability. The canonical DDD does not currently prove repeated AI-owned business responsibility across the current 10 contexts.

This does not reject Intelligence as a platform capability. It means it should not influence DDD-derived macro-service boundaries until concrete use establishes a real repeated need.

Status: **EXISTING OPTIONAL PLATFORM CAPABILITY; OUTSIDE CURRENT DISCOVERY SCOPE**.

---

# Overlap summary

## Major ownership/taxonomy problems

1. Catalog generic entity persistence exceeds Catalog/Reference responsibility.
2. Form Engine concept spans Forms + Documents + Data + Catalog and risks universal form semantics.
3. Integration Port is too MP2-specific to represent the confirmed Integration macro service.
4. Analytics and Integration overlap ambiguously at ETL/source acquisition.
5. Operational Read Composition is missing as a macro-service family.
6. Retained History/Audit is missing as a coherent macro-service family.
7. Identity permission authority model is inconsistent.
8. Workflow runtime is over-promoted relative to confirmed shared routing need.
9. Rules is over-promoted if treated as shared business-rule ownership.
10. Port-level mechanics and macro services are currently mixed in one taxonomy level.

## Areas currently well aligned

- `Data` as ubiquitous persistence substrate, provided semantic ownership remains local.
- `Notification` as shared delivery mechanics.
- `Analytics` as analytical projection/BI capability, once separated clearly from source acquisition and operational reads.
- `Storage`/`Documents.Compose`/`Printing` as reusable technical capabilities under the larger artifact/document family.

---

# Discovery consequence

The macro-service discovery gate remains **CLOSED**.

Before closure, the project must reconcile the ten major taxonomy/ownership problems above and then run canonical command/query/use-case coverage against the resulting macro-service map.

This audit intentionally does not choose code structure, database structure, deployment boundaries, technologies, contracts, or migration steps.