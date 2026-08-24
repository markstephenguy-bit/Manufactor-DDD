# Whole-Model Macro-Service Duplication / Overlap Audit

**Status:** Discovery audit  
**Date:** 2026-08-24  
**Design gate:** closed

## Purpose

Apply the live Architect Kernel's no-duplicate-capability rule to the normalized DDD-derived macro-service map and the live architecture taxonomy.

The objective is not to choose implementation boundaries. It is to prevent multiple capability names from silently claiming the same responsibility before design begins.

## Results

### 1. Catalog vs Data.Reference

**Potential overlap:** reference data, lookup, hierarchy, registry.

**Reconciled distinction:**

- **Catalog, Reference & Hierarchy macro service** owns the shared responsibility: technical registration, identity/reference lookup, hierarchy/navigation, typed reference/link semantics.
- `Data.Reference` is a **storage/serving mechanism** for reference datasets where appropriate.
- `Data` does not become the semantic owner of reference entities merely because it persists them.
- Catalog does not become generic domain persistence.

**Status:** overlap resolved conceptually.

### 2. Catalog / `manufactor.thing` vs universal domain entity model

**Potential overlap:** the architecture has historically used generic Thing/entity registration for multiple subjects.

**Reconciled distinction:**

- technical reference/link identity may be shared;
- domain identity, lifecycle, invariants, and semantics remain in the owning BC;
- a generic Thing record cannot replace Asset, Project, Qualification, Quality Concern, Nonconformity, LTV record, etc.

**Status:** dangerous overgeneralization rejected.

### 3. Observability.Audit vs Retained History

**Potential overlap:** both record past actions/events.

**Reconciled distinction:**

- **technical audit** records system/operator activity for operations/security/support;
- **Retained History macro service** supports business revision/state/correction/evidence history required by the domain;
- business-history validity and meaning remain BC-owned.

A log entry that a request occurred does not satisfy a requirement to preserve the prior Quality Target, Qualification, Asset state, LTV lifecycle state, or corrected Operations Record.

**Status:** separate responsibilities; no merge.

### 4. Storage.Archive vs Retained History

**Potential overlap:** both involve retention.

**Reconciled distinction:** Archive is a storage tier/lifecycle mechanism. Retained History is the business/application responsibility for preserving historically meaningful state/revisions and retrievability.

**Status:** no duplicate.

### 5. Governance.Validation / Rules vs BC invariants

**Potential overlap:** shared validation/rule evaluation can appear to own business rules.

**Reconciled distinction:**

- validation/rules engines may execute reusable technical evaluations;
- each BC owns what is valid and which invariants must hold;
- Quality target authority, CAPA closure requirements, qualification authority, supervisor staffing judgment, Asset transitions, and LTV recording rules cannot be moved into generic Governance/Rules ownership.

**Status:** supporting capability only; not a macro service.

### 6. Governance.Parameters vs Quality Target Parameter

**Potential overlap:** both use the word parameter.

**Reconciled distinction:**

- `Governance.Parameters` is generic technical key/value configuration capability;
- Quality Target Parameter is a domain concept with contextual applicability, history, authorization, and evaluation meaning.

The generic parameter capability may support persistence/config mechanics but cannot become the domain owner.

**Status:** semantic collision resolved.

### 7. Data.Mapping vs Integration translation / anti-corruption

**Potential overlap:** both transform representations.

**Reconciled distinction:**

- `Data.Mapping` is technical DTO/object transformation;
- Integration translation/anti-corruption is semantic translation between source/context meanings while preserving authority/provenance.

Auto-mapping fields is not equivalent to deciding that LMS completion means evidence rather than Qualification, ProTrack measurements mean statistical drying evidence rather than kiln-run lineage, or MP2 identifiers map to a stable ManuFactor Asset.

**Status:** distinct; Integration owns the macro responsibility.

### 8. Integration.Connectors / Bindings vs source-specific adapters

**Potential overlap:** generic connectors vs MP2/ProTrack/HR/Moodle/MES adapters.

**Reconciled distinction:**

- **Integration, Source Acquisition & Provenance macro service** owns reusable connector/binding/execution/provenance mechanics;
- each source-specific translator owns source semantics at its boundary;
- no separate macro service per source.

**Status:** reuse confirmed; no source-specific macro services.

### 9. Integration.Workflows vs Work Routing & External Reference Coordination

**Potential overlap:** both coordinate multi-step/external work.

**Reconciled distinction:**

- workflow runtime is a supporting orchestration engine;
- **Work Routing & External Reference Coordination** is the repeated business/application-supporting responsibility for sending/associating work across ownership boundaries while retaining destination references and uncertainty;
- a routing need does not imply a long-running workflow engine;
- an orchestration engine must not create a generic Work domain.

**Status:** macro responsibility separated from runtime capability.

### 10. Integration.Jobs vs Scheduling & Background Execution

**Potential overlap:** both describe jobs.

**Reconciled distinction:**

- `Integration.Jobs` is a taxonomy capability for time/event-triggered single-action execution;
- **Scheduling & Background Execution** is the normalized platform macro responsibility spanning integration pulls, analytics refresh, notification retries, and other technical jobs;
- Cron-Jobs/Cron-ETL/Hangfire/background workers are current mechanisms/callers, not competing macro-service definitions.

**Status:** normalized under one platform macro responsibility.

### 11. Compute.Batch vs Scheduling & Background Execution

**Potential overlap:** both execute work outside interactive requests.

**Reconciled distinction:**

- Batch is an execution/compute model;
- Scheduling & Background Execution determines when/how recurring/deferred technical jobs execute and recover.

A scheduled job may use batch compute, but the responsibilities are not identical.

**Status:** no duplicate.

### 12. Analytics.ETL vs Integration, Source Acquisition & Provenance

**Potential overlap:** traditional ETL includes extract/load, which overlaps connector/source acquisition.

**Reconciled distinction:**

- **Integration** owns interaction with operational/external sources and destinations, acquisition/publication execution, source identifiers, checkpoints, raw provenance, and explicit unavailable/unresolved state;
- **Analytics** owns transformation into analytical grains/projections, analytical refresh/retention, historical aggregation, OLAP/time-series and BI consumption;
- an implementation may use one job/process for both phases, but that does not merge the responsibilities.

**Status:** major overlap resolved conceptually; live taxonomy naming remains potentially confusing.

### 13. Analytics.BI vs Observability.Dashboards

**Potential overlap:** both display dashboards.

**Reconciled distinction:**

- Analytics.BI = business/operational analytical views over domain/source facts;
- Observability.Dashboards = platform/runtime/health/telemetry views.

**Status:** distinct.

### 14. Analytics vs Operational Query & Read Composition

**Potential overlap:** both join/transform data for viewing.

**Reconciled distinction:**

- **Operational Query & Read Composition** serves current application questions by composing independently authoritative facts, preserving freshness/provenance and missing/unresolved contributions;
- **Analytics** serves historical/aggregate/dimensional/time-series/BI questions;
- current operational decisions must not depend on an analytical projection being mistaken for source truth unless the DDD explicitly establishes that model.

**Status:** separate macro services required.

### 15. Computation.Query vs Operational Query & Read Composition

**Potential overlap:** both contain “query.”

**Reconciled distinction:** LINQ/in-memory query is a technical computation primitive. Operational Read Composition is an application responsibility spanning sources/contexts and provenance.

**Status:** no duplicate.

### 16. Search vs Catalog vs Operational Read Composition

**Potential overlap:** all locate information.

**Reconciled distinction:**

- Search = indexing/search primitive;
- Catalog = technical subject/reference/hierarchy resolution;
- Operational Read Composition = application-level assembly of current authoritative contributions.

Search may support either macro service without owning their semantics.

**Status:** Search remains a sub-capability.

### 17. Notification vs Observability.Alerts

**Potential overlap:** both send attention signals.

**Reconciled distinction:**

- **Notification & Attention macro service** delivers domain/application-driven messages to people/channels;
- Observability.Alerts report technical/runtime health conditions.

The same channel technology may be reused, but the source meaning and responsibility are different.

**Status:** distinct responsibilities.

### 18. Realtime.PubSub vs Integration.Events/Brokers

**Potential overlap:** both publish/subscribe.

**Reconciled distinction:**

- Realtime is an interactive/session-oriented transport capability where low-latency client update is the concern;
- Integration events/brokers are system/application integration mechanics where message contract, delivery semantics and source provenance matter.

If a future implementation uses one technology for both, taxonomy responsibility remains distinct.

**Status:** live taxonomy overlap is manageable; not a macro-map defect.

### 19. Documents.Compose vs Forms & Structured Capture

**Potential overlap:** forms can render documents.

**Reconciled distinction:**

- **Forms & Structured Capture** manages structured presentation/input/capture mechanics;
- Documents.Compose renders a document artifact from structured data;
- an Operations form can exist without printable output;
- an LTV workflow can use both capabilities without merging them.

**Status:** separate responsibilities.

### 20. Documents.OCR vs Forms capture

**Potential overlap:** OCR extracts structured information from a returned form.

**Reconciled distinction:** OCR is document-to-data extraction. Forms & Structured Capture is the application capture shell/mechanics. OCR can supply candidate input values but does not own form semantics or validation.

**Status:** Documents sub-capability.

### 21. Printing vs Documents.Compose

**Potential overlap:** both participate in hard-copy output.

**Reconciled distinction:** Compose creates an artifact; Printing delivers an already-composed artifact to physical output. Both remain sub-capabilities of Documents, Artifacts & Evidence.

**Status:** no independent macro services.

### 22. Identity.Directory / Identity.Login / Identity.Permissions vs ManuFactor Profile

**Potential overlap:** several layers can appear to own roles/users.

**Reconciled distinction:**

1. Windows/AD supplies authentication identity and directory enrichment.
2. ManuFactor Profile supplies application-local profile/role facts.
3. The owning BC supplies business-action authority rules.

No single layer may claim all three.

**Status:** Identity macro service retained with explicit authority layers.

### 23. Technical Audit vs Identity responsibility history

**Potential overlap:** actor/permission events may be logged technically and retained historically.

**Reconciled distinction:** technical auth/audit logs support security/operations; domain records must retain the actor/authority basis when the business rule requires it. Retained History supports the latter.

**Status:** no merge.

### 24. Intelligence/AI vs Operational Read Composition / Search

**Potential overlap:** AI may retrieve/search/compose information.

**Reconciled distinction:** Intelligence is a platform capability/consumer of data, not a replacement for deterministic reference resolution, source integration, or canonical read composition under current DDD evidence.

**Status:** excluded from current DDD-derived macro map.

### 25. IoT vs Integration

**Potential overlap:** device/process data acquisition is integration.

**Reconciled distinction:** current DDD does not prove a standalone IoT macro responsibility. Device/process-control mechanisms, when evidenced, begin as source-specific Integration capabilities unless materially different repeated responsibilities justify promotion later.

**Status:** no IoT macro service yet.

## Whole-audit conclusion

The normalized macro-service inventory survives the no-duplicate-capability test.

No pair of the 12 normalized macro services currently performs the same responsibility once semantic boundaries are applied.

The main live-taxonomy collision zones are:

1. Catalog vs Data.Reference;
2. Audit vs Retained History;
3. Rules/Validation vs domain invariants;
4. Integration.Workflows vs Work Routing;
5. Integration.Jobs/Compute.Batch vs Scheduling & Background Execution;
6. Analytics.ETL vs Integration source acquisition;
7. Analytics vs Operational Read Composition;
8. Search vs Catalog/Read Composition;
9. Observability Alerts vs Notification;
10. Documents vs Forms.

All ten can be separated conceptually without adding another macro-service category.

## Remaining major discovery risk

The unresolved risk is no longer conceptual duplication in the DDD-derived map. It is **live workbook reconciliation**: the workbook may contain additional Components/Ports/Wrappers/Endpoints or more detailed caller assignments not visible in the current textual Drawings.

Until the workbook is directly inspected, the phase gate remains closed.
