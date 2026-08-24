# Live Workbook Macro-Service Reconciliation

**Status:** discovery evidence / no design authority  
**Date:** 2026-08-24  
**Source inspected:** uploaded `ManuFactor-Architecture-Workbook.xlsx` matching the live architecture workbook declared by `ManuFactor-arch`  
**Purpose:** reconcile the whole-DDD normalized macro-service map against the actual live workbook before any DDD-driven design gate can open.

## Guardrail

This is still **discovery**, not solution design. The workbook is architecture evidence; canonical DDD remains semantic authority. A workbook Port, taxonomy root, Wrapper, technology, endpoint, build phase, or historical Component does not become a DDD-derived macro service merely because it exists in the workbook.

## Workbook coverage inspected

The uploaded workbook contains 24 sheets. The reconciliation inspected the sheets that can materially affect macro-service discovery:

- Components
- Ports
- Wrappers
- Endpoints
- Layers
- Status Catalog Mappings
- Scheduled & Triggered Actions
- Notifications
- Rules
- Catalog Entities
- Decisions (ADRs)
- Build Order
- Solution Structure
- Taxonomy
- Constraints & Assumptions

No workbook-only evidence exposed a new major macro-service category beyond the current normalized set.

## Normalized macro-service set survives workbook inspection

The workbook supports keeping the following as the current DDD-derived macro-service families:

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

No 13th major category is required by the workbook.

## Live Port classification

| Workbook Port | Discovery classification | Macro-service relationship | Reconciliation finding |
|---|---|---|---|
| Storage | sub-capability | Documents, Artifacts & Evidence | Storage is mechanics, not a peer macro service. |
| Documents.Compose | sub-capability | Documents, Artifacts & Evidence | Composition is a document capability; it does not own form/domain semantics. |
| Printing | sub-capability | Documents, Artifacts & Evidence | Physical output capability only. |
| Data | macro-service-facing Port | Data & State Persistence | Strong direct fit. |
| Catalog | macro-service-facing Port with boundary risk | Catalog, Reference & Hierarchy | Must remain semantic/reference/hierarchy mechanics; must not become generic domain persistence. |
| Rules | supporting capability | consumed across macro services/BCs | Runtime evaluation mechanics; not owner of business rules/invariants. |
| Integration | under-scoped Port | Integration, Source Acquisition & Provenance | Live driver is still MP2 SQL Server, so the Port name is broader than its actual current implementation. General source integration responsibility must be separated conceptually from one MP2 adapter. |
| Integration.Workflows | supporting capability | Work Routing / orchestration consumers | Elsa is a runtime capability, not the macro-service boundary. |
| Identity.Permissions | macro-service-facing Port, internally inconsistent | Identity, Access & Responsibility | Workbook Ports sheet still says AD/DirectoryServicesWrapper, but accepted ADR 117 says role/permission assignment is ManuFactor-owned and explicitly rejects AD groups. Live workbook contains a contradiction that must be reconciled. |
| Analytics | macro-service-facing Port | Analytics, Projection & Data Lake | Strong fit, but Analytics.ETL must not absorb source integration authority. |
| Intelligence | optional platform capability | outside current DDD-derived macro map | No canonical DDD need promotes AI to a domain/application macro service. |
| Notification | macro-service-facing Port | Notification & Attention | Strong delivery-mechanics fit; business escalation remains local. |
| Identity.Login | ambient/platform sub-capability | Identity, Access & Responsibility | Windows Negotiate supplies authentication identity. |
| Realtime | ambient/platform transport | none as independent macro service | Transport only. |
| Observability | ambient platform concern | none as domain macro service | Technical telemetry/audit must not replace Retained History. |
| Security | ambient platform concern | none as domain macro service | Cross-cutting platform concern. |
| DevOps | ambient platform concern | none as domain macro service | Build/deploy operations only. |
| Testing | ambient platform concern | none as domain macro service | Architecture/development enforcement. |
| Governance | ambient/supporting capability | several macro services | Config/validation/policy mechanics; not domain authority. |

### Port-level conclusion

The workbook's Port table mixes four different levels in one inventory:

- macro-service-facing Ports;
- sub-capabilities;
- supporting runtimes;
- ambient platform concerns.

That is acceptable as a technical Port inventory, but it cannot be used directly as the macro-service map.

## Wrapper classification

All Wrapper rows remain implementation/technology adapters underneath capabilities. None creates a macro-service boundary by itself.

Notable workbook-only/strongly-live evidence includes:

- `SchedulingWrapper` using Hangfire + PostgreSQL: supports the **Scheduling & Background Execution** macro service.
- `PowerAutomateWrapper`: an optional external-work/integration transport candidate; does not create a new macro service.
- `AdomdWrapper`: source adapter for SSAS/BI data; belongs under source integration and/or analytical acquisition according to the actual use, not a separate service.
- `DirectoryServicesWrapper`: directory lookup/enrichment capability; accepted ADR 117 prevents interpreting it as permission authority.

## Taxonomy reconciliation

The workbook taxonomy is a broad capability vocabulary, not the macro-service hierarchy. Several taxonomy roots/leaves map under one macro service:

### Documents

`Storage.Blob`, `Documents.Compose`, `Documents.OCR`, `Documents.Printing`, transfer/raster capabilities are all lower-level mechanics contributing to **Documents, Artifacts & Evidence**. They do not justify multiple peer macro services.

### Integration

`Integration.Events`, `Envelopes`, `Schemas`, `Bindings`, `Jobs`, `Workflows`, connectors, queues/brokers are technical integration capabilities. They support at least two distinct macro responsibilities:

- **Integration, Source Acquisition & Provenance**;
- **Scheduling & Background Execution** and selected **Work Routing** mechanics.

`Integration.Workflows` must not be treated as the semantic owner of CAPA, Workforce, Project, LTV, or other domain lifecycles.

### Analytics

`Analytics.ETL`, `Lakes`, `Historian`, `BI`, streaming/charts are lower-level capabilities of **Analytics, Projection & Data Lake**. ETL is a transformation/load mechanism; its presence does not transfer source authority into Analytics.

### Catalog and Data.Reference

The taxonomy explicitly separates `Catalog.Entities/Actions/Forms` from `Data.Reference`. This is useful, but the boundary remains constrained:

- Catalog may describe/register/reference semantic objects and actions as architecture/application metadata;
- Data.Reference may serve shared lookup/reference datasets such as mill hierarchy or shift/crew reference;
- neither becomes the persistence owner or domain model for every referenced DDD concept.

`Catalog.Forms` is evidence that reusable form-definition mechanics exist, but that capability belongs under the **Forms & Structured Capture** macro-service family when reasoning at macro-service level. It does not make Catalog the owner of LTV, Operations Record, or Reliability form semantics.

### Observability.Audit vs Retained History

The workbook contains `Observability.Audit`, but this remains technical audit. Canonical DDD requires retained business history across all 10 BCs. These responsibilities are not interchangeable.

## Catalog Entities reconciliation

The `Catalog Entities` sheet mixes several categories:

- technical identity/linking (`manufactor.thing`);
- shared reference data (mill hierarchy, Lockout Items, Shift Calendar & Crew);
- DDD/business records (LTV Template/Instance, CAPA item, AFAL record, Project record, Asset records, etc.);
- pre-DDD/deferred concepts (Vehicle, Risk, Environmental).

The sheet is useful as a catalog/registry inventory, but it must not be read as evidence that Catalog owns the lifecycle or persistence semantics of all listed records.

`manufactor.thing` may remain a thin technical identity/link anchor. It cannot define a universal enterprise domain entity or universal lifecycle.

## Thing Status Catalog is a major semantic mismatch

The `Status Catalog Mappings` sheet currently assumes a base catalog of:

- Open
- InProgress
- Paused
- Closed
- Cancelled

and attempts to map component-specific states onto it.

This does **not** survive the canonical DDD semantic test as a universal status model. Examples already visible in the workbook itself show the mismatch (`Scrapped` is noted as not really `Cancelled`; Asset/Vehicle states do not naturally map to the base five).

Discovery conclusion:

- a technical status-display/category mechanism may exist;
- a universal domain-status semantic model is rejected;
- every BC retains its own status/state vocabulary and transition meaning.

This finding does not require a new macro service; it constrains Catalog/Forms/UI mechanics.

## Rules sheet reconciliation

The Rules sheet confirms that the current architecture contains a shared Rules runtime, but many rows are pre-DDD or stale:

- `InspectionsCalibration` rules;
- cross-cutting QMS fault-tolerance rule;
- generic Thing Status transition guard;
- older CAPA-vs-Project framing.

The existence of these rows does not promote Rules into a macro service or make it owner of those domain decisions. Canonical BC invariants/authority remain primary.

## Scheduled & Triggered Actions reconciliation

The workbook adds strong evidence for **Scheduling & Background Execution** as a real platform macro service:

- calendar/frequency jobs;
- MP2 ingestion;
- technical threshold polling/evaluation;
- cross-process signaling candidates.

However, several listed actions are tied to pre-DDD `InspectionsCalibration`/QMS concepts. They are not valid evidence for restoring those as macro/domain boundaries.

`Integration.Jobs`, `Cron-Jobs`, `Cron-ETL`, Hangfire, and worker services are one family of technical execution mechanics. They must not become duplicate scheduling macro services merely because different hosts/jobs exist.

## Notification reconciliation

The Notification sheet confirms real shared delivery mechanics. It also exposes stale AD-group assumptions for LTV notification recipients. Under accepted ADR 117, recipient business responsibility cannot be inferred from AD group membership as permission authority without separate evidence.

Notification remains a macro service for delivery/attention mechanics only.

## Component sheet reconciliation

The workbook currently lists 16 Component rows, while Solution Structure says 13 active Components and explicitly excludes/de-emphasizes others.

### Canonical DDD-aligned or reasonably mappable current components

- LtvForms -> LTV Form Management
- Afal -> Reliability Verification
- OperationalReporting -> Operations Record
- Capa -> Corrective Action
- Training -> Operational Training and Qualification
- Workforce -> Operational Workforce Availability
- AssetManagement -> Asset Lifecycle
- KilnDrying -> Kiln Operations
- ProjectTracking -> Project Tracking

### No clean canonical current equivalent

- **Quality Verification** has no clean workbook Component equivalent.

### Pre-DDD / non-authoritative / deferred component rows

- ParameterProcessChange
- VehicleManagement
- InspectionsCalibration
- Risk
- Environmental
- CustomerComplaints
- ReleaseOfProduct (already struck)

These rows are architecture history/implementation evidence, not authority to add BCs or macro services.

## Component-to-Port assignment audit

The workbook's Component -> Port assignments are useful positive evidence, but they materially underexpress the canonical macro-service consumer map.

Examples:

- most components do not list retained-history capability even though canonical DDD requires retained history;
- no component has an explicit Operational Read Composition Port because that responsibility is absent from the current Port table;
- Forms mechanics are implicit through Catalog/Data/Documents rather than represented coherently;
- authorization is listed only for LTV even though many BCs have explicit actor-authority rules;
- Integration assignments are shaped by historical source-specific work rather than the full DDD consumer map.

Therefore workbook Port assignments cannot be treated as exhaustive macro-service consumption evidence.

## Endpoints reconciliation

The Endpoints sheet adds concrete systems but no new macro-service category:

- MES Aurora PostgreSQL;
- MP2 SQL Server;
- AD/LDAP;
- printers;
- ClickHouse;
- SSAS sources;
- Moodle API/DB alternatives;
- Elsa state;
- AI stack;
- ntfy;
- SharePoint/PowerApps candidate;
- SAP integration alternatives;
- mechanic-shop app.

These are endpoints/drivers under existing Integration, Analytics, Identity, Documents, Notification, or optional Intelligence capabilities. Endpoint multiplicity does not imply service multiplicity.

## Accepted ADR reconciliation

Two accepted ADRs are especially important to macro-service discovery:

### ADR 117 — Identity.Permissions

Accepted workbook decision:

- ManuFactor owns role/tier assignment internally;
- no AD groups are consulted as permission source.

This supersedes the stale Ports row `Identity.Permissions -> DirectoryServicesWrapper -> AD` as an authority description. Directory services may still support identity/profile enrichment, but not app permission ownership.

### Data schema layout — single normalized schema

The workbook accepts one normalized PostgreSQL schema and one EF Core DbContext. This is physical implementation evidence only. It does not collapse canonical domain ownership or justify Catalog/`manufactor.thing` as universal semantic owners.

## Workbook-only capabilities not requiring new macro services

The live workbook contains capabilities not fully represented in the prior text snapshots, but all fit existing classifications:

- SchedulingWrapper/Hangfire -> Scheduling & Background Execution
- PowerAutomateWrapper -> Integration / Work Routing transport candidate
- AdomdWrapper/SSAS -> Integration/Analytics source adapter
- Data.Reference -> Catalog/Reference/Hierarchy supporting mechanism
- Catalog.Forms -> Forms & Structured Capture supporting mechanism at macro-service level
- Integration.Envelopes/Schemas/Bindings -> Integration sub-capabilities
- Observability.Audit -> ambient technical audit, not Retained History

No workbook-only row forces a new macro-service family.

## Final workbook reconciliation result

### Satisfied

- All live Port rows classified by level.
- All live Wrapper rows fit below existing macro services or ambient capabilities.
- All endpoint rows fit existing macro services.
- Workbook-only capabilities identified and classified.
- Component-to-Port assignments checked as positive but non-exhaustive evidence.
- No new major macro-service category discovered.
- Major overlaps/contradictions are explicit.

### Remaining macro-service discovery defects to resolve before gate exit

These are reconciliation defects, not invitations to begin design:

1. Resolve the live workbook contradiction between `Ports.Identity.Permissions = AD` and accepted ADR 117 = ManuFactor-owned roles/permissions.
2. Explicitly constrain `Thing Status Catalog` so it cannot be read as universal domain status semantics.
3. Reconcile Catalog/Catalog.Entities/Catalog.Forms wording so registration/reference metadata cannot be mistaken for ownership of domain records/lifecycles.
4. Mark pre-DDD/deferred Component rows and Rules/Scheduled Action rows as non-authoritative relative to canonical DDD.
5. Represent or explicitly acknowledge the macro responsibilities absent from the live Port table:
   - Forms & Structured Capture;
   - Retained History;
   - Operational Query & Read Composition;
   - Work Routing & External Reference Coordination as distinct from Elsa workflow runtime.
6. Add/acknowledge canonical Quality Verification as missing from the live Component inventory without prematurely designing its implementation.

These are the last material reconciliation items discovered by the live workbook. They do not change the 12-family normalized macro-service map.

## Gate implication

The live workbook verification is now complete enough to conclude that **no major macro-service category remains undiscovered**. The discovery gate should nevertheless remain closed until the listed live-architecture contradictions/misclassifications are reconciled and a final whole-model consistency audit confirms that no major discovery work remains. Opening the design phase still requires Mark's explicit confirmation under the discovery-completion gate.
