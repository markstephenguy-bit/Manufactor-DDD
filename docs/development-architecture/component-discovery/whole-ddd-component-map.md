# ManuFactor Whole-DDD Reusable Component Map

**Status:** Candidate development-architecture baseline  
**Date:** 2026-08-23  
**Authority:** Derived from the canonical ManuFactor DDD; this document does not redefine DDD semantics.  
**Scope:** First whole-model reusable component discovery pass across all 10 current Bounded Contexts.

## Governing rule

Reusable components may own technical mechanics, but they must not absorb domain meaning, invariant ownership, lifecycle rules, or source-system authority.

A shared history mechanism may retain transitions, but each Bounded Context owns which transitions are valid and what they mean. A shared evidence mechanism may retain files and references, but each Bounded Context owns what evidence is required and how it affects business decisions. Shared integration mechanics must preserve source provenance and must never fabricate lineage absent from the source data.

## Current Bounded Contexts

1. Asset Lifecycle
2. Corrective Action
3. Kiln Operations
4. Operations Record
5. Project Tracking
6. Quality Verification
7. Reliability Verification
8. LTV Form Management
9. Operational Training and Qualification
10. Operational Workforce Availability

## Candidate component map

| Candidate component | Classification | Consuming Bounded Contexts | Technical ownership | Domain guardrail | Integration implication | Primary abstraction risk |
|---|---|---|---|---|---|---|
| Identity & Reference Resolution | Platform infrastructure | All 10; strongest need in Asset Lifecycle, Quality Verification, Reliability Verification, LTV Form Management, Corrective Action, Training, Workforce, Project Tracking | Stable technical IDs, external-reference namespaces, source identifier mappings, unresolved-reference state, mapping provenance | Does not own the meaning or lifecycle of Asset, Person, Project, Qualification, Quality Concern, Nonconformity, Job, or other referenced concepts | Must preserve source identifiers and provenance while translating references between systems and contexts | Becoming a universal enterprise entity model or shared domain object graph |
| Retained History / Audit Journal | Platform infrastructure | All 10 | Append/history mechanics, actor/time/reason metadata, correction/supersession mechanics, historical revision linkage | Each BC owns which transitions are valid, what a state means, and whether a change is permitted | History entries may reference external evidence or source facts but cannot rewrite their authority | Turning technical history into a generic lifecycle model |
| Evidence & Document Handling | Application/platform component | Quality Verification, Corrective Action, Reliability Verification, LTV Form Management, Training; potentially Operations Record and Project Tracking | File/blob retention, metadata, checksums, versions, scan/upload mechanics, evidence references | Each BC owns what evidence is required, sufficient, admissible, or transition-enabling | External evidence must retain source provenance and exact version/identity where relevant | Treating existence of evidence as proof that a business rule is satisfied |
| Contextual / Effective-Dated Parameter Mechanics | Application component | Quality Verification confirmed; Kiln, Reliability, LTV, Operations Record only if later evidence demonstrates equivalent mechanics | Effective dating, contextual applicability, version history, historical resolution, ambiguity detection | Quality Target remains Quality-owned; other parameter meanings remain local to their BCs | Parameter values may originate from external guidance while ManuFactor retains only the authority explicitly established by the DDD | Premature universal parameter model based on superficial value/version similarity |
| External Source Adapter Framework | Integration component | Asset Lifecycle, Kiln Operations, Training, Workforce; extensible to future source readers | Connectivity, pull/push execution, retries, cursors/checkpoints, health, raw source envelope, ingestion provenance | Does not interpret source facts into domain meaning by itself | Current concrete adapters include MP2, ProTrack, learning systems, and HR/timekeeping | One generic adapter silently normalizing away source semantics or authority |
| Reference Translation / Anti-Corruption Mechanics | Integration component | Asset Lifecycle, Kiln Operations, Training, Workforce, and explicit BC-to-BC contracts | Mapping framework, source/target identifiers, translation failure handling, mapping version/provenance | Translation semantics belong to the consuming context/integration contract | Required wherever source terminology or identity differs from downstream meaning | Central translation model becoming authoritative for both sides |
| Projection / Query Infrastructure | Read/projection component | All 10 | Projection building, indexing, filtering, denormalized read stores, refresh/freshness metadata, query plumbing | Projections are not authoritative domain state; authoritative meaning remains with source BC/system | Enables Superset and application reads without transferring ownership | Treating denormalized projection data as a new master record |
| Cross-Context Read Composer | Read/projection component | Quality Verification + Corrective Action; Asset Lifecycle + MP2; Kiln + ProTrack; Workforce + Training + HR/timekeeping; Reliability + Asset | Query orchestration, provenance per contribution, freshness/staleness representation | Must preserve each source's authority and distinguish unavailable/stale state from locally owned state | Explicitly composes independently authoritative facts without inventing shared ownership | Composite screen/query accidentally becoming a shared domain model |
| Authorization / Responsibility Enforcement | Platform/application component | Quality, Corrective Action, Reliability, LTV, Training, Workforce, Asset; likely Project and Operations Record | Authentication claims, scoped permission checks, delegated responsibility mechanics | Each BC defines which actor may perform which business action and under what business rule | May consume enterprise identity/role data while preserving ManuFactor-specific responsibility semantics | Generic RBAC replacing domain-specific responsibility rules |
| Work Routing & External Work Reference | Application/integration component | Corrective Action, Project Tracking, Reliability Verification; future consumers only when evidenced | Routing envelope, destination reference, acknowledgement, correlation, routing status mechanics | CAPA, Project, MP2 work, and other work classes retain separate ownership and lifecycle semantics | Enables coordination with externally owned execution while retaining reference/provenance | Creating a generic shared Work aggregate |
| Workflow State-Machine Mechanics | Domain-supporting application infrastructure | LTV, Corrective Action, Asset, Project, Training, Quality Concern, Kiln | Generic transition execution, concurrency control, transition logging, optional timers/hooks | All allowed transitions, invariants, and meanings remain BC-owned | May invoke integration or notification mechanics after domain-approved transitions | Generic BusinessWorkflow with shared status/approve/reject/close semantics |
| Notification / Attention Service | Platform infrastructure | Potentially Corrective Action, Reliability, LTV, Workforce, Project, Quality | Channel delivery, retry, subscriptions, acknowledgement, notification templates/mechanics | BC owns whether notification is required, its business significance, and intended responsible parties | May consume domain events or application signals without becoming owner of those events | Notification workflow being mistaken for business escalation lifecycle |
| Business Subject Reference Picker / Resolver | Application component | Operations Record, Quality, Reliability, LTV, Corrective Action, Project, Workforce/Training | Search/reference UI mechanics and canonical reference payloads | Referencing BC owns applicability and meaning of the selected subject | Uses Identity & Reference Resolution and read projections | UI-level reuse turning into semantic sharing |

## High-confidence reusable components

### Identity & Reference Resolution

The DDD repeatedly requires one model to reference another context or external system without taking ownership of that referenced concept. Asset Lifecycle additionally requires stable ManuFactor Asset identity while MP2 hierarchy mappings may change. The reusable component may own technical identity and mapping mechanics only.

### Retained History / Audit Journal

Retained history appears across all current contexts. The common mechanism should preserve who/when/reason/provenance and correction/supersession mechanics. It must not own lifecycle rules or state meaning.

### External Source Integration + Provenance

The current DDD contains materially different integrations: MP2 to Asset Lifecycle, ProTrack to Kiln Operations, learning systems to Operational Training and Qualification, and HR/timekeeping to Operational Workforce Availability. Reusable connector mechanics are justified; domain-specific adapters/translators remain separate.

### Projection + Cross-Context Query Infrastructure

ManuFactor is explicitly a source-system reader, aggregator, correlator, and gap filler. Current queries already require composition across independently authoritative sources. A shared read substrate is therefore central, but it must never create new authority simply because facts are joined in one view.

### Evidence & Document Handling

Quality Verification, Reliability Verification, LTV Form Management, Operational Training and Qualification, and Corrective Action all require retained evidence with materially different business meanings. Storage, integrity, metadata, versioning, and reference mechanics can be shared; sufficiency and effect remain local.

### Authorization / Responsibility Mechanics

The DDD contains multiple distinct business authority rules: salaried management controls Quality targets; the Quality office drives CAPA; the maintenance supervisor promotes reliability findings; the Safety Office records returned LTV forms; supervisors/designated trainers establish qualifications; supervisors manage withdrawal and staffing judgment. Shared enforcement mechanics are justified, but business permission definitions remain context-owned.

## Candidate components requiring tighter limits

### Workflow engine

A reusable workflow runtime is acceptable only as infrastructure. It may execute BC-approved transitions and preserve history, but it must not define universal statuses or lifecycle rules. `Recorded`, `Closed`, `Qualified`, `Operating`, `Retired`, and similar terms remain context-specific.

### Effective-dated parameter service

Quality Verification strongly justifies reusable effective-dated/contextual parameter mechanics. The component should not yet become a universal parameter domain model. Promotion to a broader platform abstraction requires equivalent technical needs in additional materially different contexts.

### Work/task abstraction

A generic shared Work domain component is rejected at this stage. Corrective Tasks, Project Tasks/Actions, MP2 maintenance work, and other work references have different owners, triggers, and lifecycle semantics. Reuse is limited to routing/reference mechanics.

## Rejected abstractions

| Rejected abstraction | Reason |
|---|---|
| GenericRecord domain model | Operations Record explicitly requires each operational record type to correspond to a distinct business need rather than a catch-all model |
| GenericWorkflowItem | Would collapse LTV, CAPA, Qualification, Project, Quality Concern, Asset, and Kiln lifecycle meanings |
| GenericTask shared domain aggregate | Corrective Tasks, Project tasks, and MP2 work have different ownership and lifecycle semantics |
| GenericVerification domain model | Quality Verification and Reliability Verification share technical shape but not business meaning, authority, criteria, or downstream consequences |
| Universal enterprise domain entity model | Cross-context references must not erase contextual ownership |
| UniversalStatus | State labels and meanings are context-specific |
| UniversalParameter domain model | Only Quality currently establishes a strongly evidenced parameter aggregate |
| Analytics Bounded Context created from tooling | Superset, dashboards, grains, and projections are implementation/read concerns, not automatically domain authority |

## Three-context abstraction tests

### Retained History

- Asset Lifecycle: condition/lifecycle changes and stable identity history.
- Quality Verification: concern status history and retained historical target/evaluation basis.
- Operational Training and Qualification: qualification grant/withdrawal history and evidence.

Result: reusable mechanics justified; state meaning remains local.

### Evidence Handling

- Quality Verification: measurement and evaluation basis.
- Reliability Verification: checkpoint/result evidence and findings.
- LTV Form Management: exact template version, returned scan, and origin match.
- Operational Training and Qualification: shadowing/observed-capability evidence.
- Corrective Action: corrective-task and effectiveness-verification evidence.

Result: shared storage/integrity/reference mechanics justified; sufficiency remains local.

### Integration/Translation

- Asset Lifecycle consumes MP2 hierarchy/WR-WO facts without accepting MP2 as the rich Asset model.
- Kiln Operations consumes ProTrack moisture facts by lumber dimension without inventing kiln-run lineage.
- Training consumes learning completion as evidence without equating it to Operational Qualification.
- Workforce consumes HR/timekeeping absence facts without transferring operational coverage ownership.

Result: common integration substrate justified; domain-specific translation is mandatory.

### Authorization/Responsibility

- Quality Target changes require salaried-management authority.
- Reliability finding promotion belongs to the maintenance supervisor.
- LTV `Recorded` transition requires Safety Office receipt/scan/match workflow.
- Qualification lifecycle is managed by supervisor/designated training authority.

Result: common enforcement mechanics justified; permission meaning is domain-local.

## Candidate architectural shape

```text
MANUFACTOR PLATFORM
|
+-- Identity / Reference Resolution
+-- Authorization / Responsibility Mechanics
+-- Retained History Infrastructure
+-- Evidence / Document Infrastructure
+-- Notification Infrastructure
|
+-- INTEGRATION SUBSTRATE
|   |
|   +-- Connector/runtime mechanics
|   +-- Provenance
|   +-- Mapping/reference infrastructure
|   |
|   +-- MP2 adapter + Asset translation
|   +-- ProTrack adapter + Kiln translation
|   +-- Learning adapter + Qualification translation
|   +-- HR/Timekeeping adapter + Workforce translation
|
+-- READ SUBSTRATE
|   |
|   +-- Projection infrastructure
|   +-- Query infrastructure
|   +-- Cross-context read composition
|
+-- APPLICATION MECHANICS
    |
    +-- Evidence association
    +-- Work routing/reference tracking
    +-- Workflow execution mechanics
    +-- Effective-dated parameter mechanics
```

The Bounded Contexts remain semantic owners above/alongside these mechanics. This is neither one deployable component per Bounded Context nor one generic workflow/data platform replacing the domain model.

## Integration implications

1. Every external-source adapter must preserve source provenance and source authority.
2. Missing source identifiers must remain missing; the architecture must not create lineage by inference unless the DDD explicitly permits an analytical inference and labels it as such.
3. Cross-context reads must represent ownership per field or contribution where material.
4. Read-model convenience must not create write authority.
5. Integration failures and unresolved mappings must remain explicit states rather than silently guessed mappings.
6. Domain transitions caused by external facts must be performed through the owning BC and its rules, not by the connector or projection layer.

## Architectural questions before implementation

These are development-architecture decisions, not open domain-discovery questions:

1. Deployment granularity: modular monolith first versus independently deployable components.
2. Persistence isolation: one physical database with strict logical ownership versus separate stores.
3. History implementation: application journal, temporal database support, event-store-like mechanism, or combination.
4. Integration execution: scheduled pull, event/API integration, CDC, or adapter-specific mixture.
5. Projection consistency: synchronous versus asynchronous/eventually consistent projections.
6. Cross-context query composition: request-time composition versus prebuilt integration projections.
7. Reference resolution topology: centralized service versus shared protocol/library with distributed mappings.
8. Evidence storage: relational/blob storage versus object/document storage with metadata references.
9. Authorization source: enterprise identity/role integration and representation of ManuFactor-specific responsibilities.
10. Workflow mechanics: dedicated reusable state-machine/runtime versus conventional application services invoking BC-owned domain behavior.

## Current conclusion

The DDD supports substantial reusable technical infrastructure, especially around identity/reference mapping, retained history, evidence, integration/provenance, projections/read composition, and authorization mechanics. It does not support collapsing domain semantics into generic Record, Workflow, Verification, Work, Task, Status, or Parameter domain models.

No new business-domain contradiction was exposed by this first component-discovery pass. No Bounded Context merge or split is indicated.
