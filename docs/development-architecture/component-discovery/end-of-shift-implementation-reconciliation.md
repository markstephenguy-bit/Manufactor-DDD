# End-of-Shift Implementation Reconciliation

**Status:** Candidate implementation architecture
**Date:** 2026-08-24
**Forcing case:** Operations Form -> End of Shift Report -> MES data lake

## Current implementation reality

The current `ManuFactor.Components.OperationalReporting` implementation is only a module/navigation shell. Its Domain, Application, and Infrastructure folders contain no implemented business model yet.

The current `ManuFactor.CronEtl` deployable is also only a host shell. It has the normal mediator and adapter registration but its worker currently performs no ETL work.

The existing `IAnalyticsPort` is a marker interface describing ClickHouse/TimescaleDB/Superset intent; it does not yet define a concrete publication or ETL contract.

Therefore End-of-Shift Reporting is a suitable first concrete case for proving the Operations Form -> analytical publication seam without adapting around legacy implementation behavior.

## Semantic boundary

```text
OperationalReporting component / Operations Record context
    owns:
      - Operations Form submission
      - retained submitted values
      - corrections/history
      - form-specific business validation
      - field provenance attached to the submitted form

CronEtl / Analytics mechanics
    owns:
      - detecting publication work
      - transforming a submitted form into a destination contract
      - retry/checkpoint behavior
      - delivery outcome
      - destination-specific serialization/transport

MES data lake
    owns:
      - accepted destination schema/storage and its own analytical representation
```

Neither CronEtl nor the destination may change the source Operations Form lifecycle.

## Operations Form shape

First Hour Production Summary and End of Shift Report remain Operations Forms, not separate domain types merely because their layouts/fields/timing differ.

Implementation may use a common form engine plus form-specific definitions. The implementation must not create separate domain ownership simply because each form has a separate class, screen, schema, or layout.

## Pull-first population

Before submission, an Operations Form may compose values from multiple sources:

```text
source/reference facts
    -> field-source adapters/read queries
    -> deterministic calculations where applicable
    -> editable form presentation
    -> user supplies remaining manual facts
    -> submit Operations Form
```

The submitted Operations Form must preserve enough per-field metadata to tell, where material, whether a value was:

- pulled;
- calculated;
- manually entered;
- manually overridden from a pulled/calculated value.

The source value must not be silently overwritten in provenance merely because the submitted form contains an override.

## Target application seams

### 1. Form population seam

A reusable application mechanic may gather candidate field values from source-specific readers.

It must return field contributions with provenance rather than mutate the Operations Form domain object directly from external adapters.

Conceptual shape:

```text
FieldContribution
  field_key
  value
  source_kind
  source_system
  observed_at
  source_reference
  calculation_version (if calculated)
```

This is application/read composition mechanics, not a domain Aggregate.

### 2. Operations Form submission seam

The Operations Record application layer accepts the completed form and applies the owning context's business rules.

Submission is one authoritative source transaction. Destination publication is not part of this transaction.

### 3. Publication-read seam

CronEtl must obtain publication-ready source data through an explicit read/export contract from Operations Record.

It must **not**:

- query OperationalReporting internal tables opportunistically;
- instantiate OperationalReporting domain objects and mutate them;
- reference internal component namespaces;
- decide whether a business form is valid/submitted/finalized.

The export contract should expose the exact source revision and provenance needed for downstream publication.

### 4. Destination adapter seam

The MES data-lake writer is destination-specific and belongs behind integration/analytics infrastructure.

The adapter is not chosen until the real destination and ingestion mechanism are known.

## CronEtl role

Use the existing `CronEtl` deployable if the real destination is appropriately served by asynchronous/scheduled ETL.

Do not assume scheduling is the final trigger merely because the host is named CronEtl. If the destination requires immediate/event-driven publication, the same source/export and destination-adapter contracts should survive while the trigger changes.

The worker should eventually orchestrate something conceptually like:

```text
find/read pending publication work
    -> obtain exact submitted source revision
    -> map to destination schema
    -> send through destination adapter
    -> record checkpoint/outcome
    -> retry technical failures independently
```

The worker does not own form status transitions.

## Existing capability reuse

| Need | Current mechanism | Treatment |
|---|---|---|
| Source persistence | Data / PostgreSQL | Reuse for Operations Form records |
| Form/component host | `OperationalReporting` | Implement Operations Form application/domain behavior here |
| Background ETL host | `CronEtl` | Reuse as candidate execution host |
| In-process dispatch | mediator/contracts | Reuse for source export/query orchestration |
| Analytics taxonomy | `IAnalyticsPort` / Analytics | Extend only after concrete capability is known |
| Destination access | not yet known | Add destination-specific adapter once MES data lake is identified |
| Retry/checkpoint | not implemented | Genuine missing technical behavior |
| Field provenance | not implemented for this use case | Genuine missing application/data behavior |
| Publication contract | DDD architecture artifact only | Implement after destination/schema discovery |

## What is genuinely missing

1. Operations Form persistence/application model in `OperationalReporting`.
2. Concrete First Hour and End-of-Shift form definitions/configuration.
3. Pull-first field population composition.
4. Per-field provenance/override representation.
5. A read/export seam for exact submitted form revisions.
6. Publication checkpoint persistence.
7. Actual CronEtl job orchestration.
8. MES data-lake destination adapter.
9. Real destination schema/protocol/credentials/network facts.

## What should not be built yet

- an MP2/Asset mapping framework based on incomplete MP2 discovery;
- a generic enterprise `Form` domain Aggregate;
- separate domain components for First Hour and End of Shift;
- a new service solely for End-of-Shift publication;
- a generic event bus because one ETL case exists;
- direct destination writes from `OperationalReporting` domain/application code;
- a generic ETL abstraction that assumes all future integrations have the same source/destination semantics.

## Boundary fitness additions when implementation begins

Extend architecture tests so:

- CronEtl cannot reference `ManuFactor.Components.OperationalReporting.Domain` or `.Infrastructure`;
- destination client libraries may appear only inside designated adapter/wrapper namespaces;
- OperationalReporting cannot reference destination-specific client types;
- Analytics/ETL code cannot reference OperationalReporting persistence entity types directly;
- form-specific presentation/configuration classes do not become separate component namespaces subject to independent semantic ownership.

## Next discovery needed

The next integration-specific investigation should focus on two tracks:

### Track A — field sourcing

For each End-of-Shift field, identify whether a reliable source exists and where. Candidate source categories remain hypotheses until verified:

- MES/process production data;
- moisture/ProTrack data;
- shift/crew reference data;
- deterministic calculation;
- manual entry.

### Track B — MES data-lake destination

Determine the actual platform and accepted ingestion path. Do not equate the read-only `mes_lum` Aurora source with the destination unless evidence confirms it.

## Result

The existing architecture provides the right high-level containers and dependency seams, but the actual Operations Form and ETL behavior is largely unimplemented. End-of-Shift Reporting should be used to implement and validate those seams first, while keeping source form semantics in Operations Record and analytical publication mechanics outside it.
