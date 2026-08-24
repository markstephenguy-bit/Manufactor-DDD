# Live Component → Macro-Service Consumption Audit

**Status:** Discovery audit only  
**Date:** 2026-08-24  
**Semantic authority:** canonical ManuFactor DDD  
**Architecture evidence:** live `ManuFactor-arch/drawings/architecture.dsl`

## Evidence rule

The live drawing is a current architecture Drawing, but it is not a complete dependency matrix for every canonical DDD responsibility.

Therefore:

- an explicit live arrow from a Component to a Shared Service is **positive evidence** that the dependency exists in the current architecture substrate;
- absence of an arrow is **not negative evidence** that the macro-service responsibility is unnecessary;
- DDD-derived consumption is evaluated from the complete Bounded Context/use-case model, not from drawing sparsity;
- historical/noncanonical business Components in the drawing are not semantic authority for current DDD boundaries.

This prevents the architecture drawing from silently under-scoping the macro-service map.

## Current live explicit shared-service calls

The live drawing explicitly shows:

- LTV Forms -> Storage / Documents
- LTV Forms -> Data
- LTV Forms -> Notification
- LTV Forms -> Intelligence
- Operational Reporting -> Data
- Operational Reporting -> Notification
- Operational Reporting -> Governance
- CAPA -> Data
- CAPA -> Integration.Workflows
- CAPA -> Notification
- Workforce -> Data
- Workforce -> Integration.Workflows
- Workforce -> Notification
- Workforce -> Analytics.ETL
- Training -> Data

The drawing also states that the Web Application "calls every Shared Service" at the container level, which means the component-level links are selective/highlighted relationships rather than an exhaustive dependency matrix.

## Canonical Bounded Context reconciliation

### Asset Lifecycle

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Identity, Access & Responsibility
- Integration, Source Acquisition & Provenance
- Operational Query & Read Composition
- Retained History
- Documents/Artifacts support where history/evidence/export requires it
- Work Routing/External Reference support where maintenance/CAPA/project references are coordinated

**Live drawing:** no explicit Shared-Service links from Asset Management.

**Finding:** drawing is insufficient for consumption validation. This is not evidence that Asset Lifecycle needs no shared services.

### Corrective Action

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Documents, Artifacts & Evidence
- Identity, Access & Responsibility
- Operational Query & Read Composition
- Retained History
- Work Routing & External Reference Coordination
- Notification & Attention
- Integration support for destination/external work where applicable

**Live explicit links:** Data, Integration.Workflows, Notification.

**Finding:** live drawing captures only a subset. `Integration.Workflows` does not cover the whole routing/reference responsibility and should not be treated as proof that workflow runtime is the macro service.

### Kiln Operations

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Identity, Access & Responsibility
- Integration, Source Acquisition & Provenance
- Operational Query & Read Composition
- Analytics, Projection & Data Lake
- Retained History
- Scheduling/Background support for technical source/analytical jobs where required

**Live drawing:** no explicit Shared-Service link from Kiln Drying.

**Finding:** current drawing is not an adequate consumption map for Kiln Operations.

### Operations Record

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Forms & Structured Capture
- Identity, Access & Responsibility
- Integration, Source Acquisition & Provenance where fields are sourced/published
- Operational Query & Read Composition
- Analytics, Projection & Data Lake for downstream analytical use
- Retained History
- Notification & Attention where a concrete form requires it
- Documents/Artifacts support where forms generate/retain artifacts

**Live explicit links:** Data, Notification, Governance.

**Finding:** live drawing is materially underexpressive for the canonical Operations Record responsibility. Governance does not substitute for Forms, History, Integration, Read Composition, or Analytics.

### Project Tracking

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Identity, Access & Responsibility
- Operational Query & Read Composition
- Retained History
- Work Routing & External Reference Coordination
- Notification & Attention

**Live drawing:** no explicit Shared-Service links from Project Tracking.

**Finding:** drawing is insufficient for consumption validation.

### Quality Verification

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Documents, Artifacts & Evidence
- Identity, Access & Responsibility
- Operational Query & Read Composition
- Retained History
- Notification & Attention
- Integration support for external context/standards/facts where applicable
- effective-dated/contextual parameter mechanics as a supporting capability

**Live drawing:** no canonical Quality Verification component exists. `Inspections/Calibration` is not a semantic substitute.

**Finding:** this is a real architecture-to-DDD coverage mismatch, not merely a missing arrow.

### Reliability Verification

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Forms & Structured Capture
- Documents, Artifacts & Evidence
- Identity, Access & Responsibility
- Integration, Source Acquisition & Provenance
- Operational Query & Read Composition
- Retained History
- Work Routing & External Reference Coordination
- Notification & Attention

**Live drawing:** AFAL exists, but no explicit Shared-Service arrows are shown from AFAL in the current drawing.

**Finding:** live drawing cannot validate its macro-service consumption; older text evidence had a much richer AFAL Port set, confirming the drawing is selective rather than exhaustive.

### LTV Form Management

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Forms & Structured Capture
- Documents, Artifacts & Evidence
- Identity, Access & Responsibility
- Retained History
- Notification & Attention
- Work-routing/reference support only where later consumers require it

**Live explicit links:** Storage/Documents, Data, Notification, Intelligence.

**Finding:** positive alignment for Data, Documents, Notification. Catalog/Forms/Identity/History are not explicitly represented at component level. Intelligence is a platform capability but is not required by the current DDD macro-service map.

### Operational Training & Qualification

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Documents, Artifacts & Evidence for qualification evidence
- Identity, Access & Responsibility
- Integration, Source Acquisition & Provenance for learning-system evidence
- Operational Query & Read Composition
- Retained History

**Live explicit links:** Data only.

**Finding:** live drawing substantially underrepresents canonical Training macro-service needs.

### Operational Workforce Availability

**Canonical repeated macro-service needs:**

- Data & State Persistence
- Catalog, Reference & Hierarchy
- Identity, Access & Responsibility
- Integration, Source Acquisition & Provenance
- Operational Query & Read Composition
- Analytics, Projection & Data Lake where trend/BI use exists
- Retained History
- Notification & Attention

**Live explicit links:** Data, Integration.Workflows, Notification, Analytics.ETL.

**Finding:** Data/Notification/Analytics alignment exists. `Integration.Workflows` is not a substitute for HR/timekeeping/source integration or cross-context read composition. Catalog/Identity/History/Operational Read Composition remain absent from the explicit component-level drawing.

## Whole-map findings

### Finding 1 — the live component-level arrows are selective, not exhaustive

The live WebApp container is drawn as calling every Shared Service, while only a handful of business Components have explicit service arrows. The component-level view therefore cannot be used as a complete consumer matrix.

### Finding 2 — canonical Quality Verification has no clean live component home

This is the strongest direct business-component mismatch exposed by the consumption audit. It must remain visible through the discovery phase; `Inspections/Calibration` must not be treated as equivalent merely because both contain verification-like behavior.

### Finding 3 — direct use of `Integration.Workflows` is semantically overread if treated as general Integration

CAPA and Workforce have direct `Integration.Workflows` links, but their canonical needs are different:

- CAPA needs routing/reference coordination across work ownership boundaries.
- Workforce needs HR/timekeeping source facts and Training qualification composition.

A workflow engine may support either, but it cannot define the shared macro-service boundary for both.

### Finding 4 — Retained History and Operational Read Composition remain invisible in the live drawing

Both are strongly evidenced across the canonical model and neither is cleanly represented by a live Shared-Service component.

### Finding 5 — Forms & Structured Capture remains invisible as a shared macro responsibility

LTV, Operations Record, and Reliability Verification prove reusable structured-capture mechanics. Those mechanics are currently distributed among Data, Storage/Documents, Catalog, and component-local behavior rather than named coherently.

### Finding 6 — current architecture includes capabilities not demanded by the DDD macro map

Examples include Intelligence, Geometry, IoT, Migration and other broad taxonomy roots. Their existence is not a problem, but they must not be confused with DDD-derived macro services. They are platform/general capability inventory unless repeated BC evidence promotes a responsibility into the macro map.

## Coverage conclusion

The canonical 10-context consumer map remains the stronger source for macro-service consumption.

The live drawing supplies useful **positive architecture evidence**, but its component-level dependency coverage is too selective to close the macro-service discovery gate by itself.

No new macro-service category was exposed by this audit.

## Remaining discovery work after this audit

1. Inspect/reconcile the live architecture workbook when directly readable.
2. Compare workbook Component->Port assignments to this canonical BC->macro-service consumer map.
3. Classify workbook-only capabilities under the normalized macro-service/sub-capability/ambient-platform hierarchy.
4. Run the final whole-model duplication/overlap audit.
5. Produce the gate-readiness report and obtain Mark confirmation before any DDD-driven design begins.
