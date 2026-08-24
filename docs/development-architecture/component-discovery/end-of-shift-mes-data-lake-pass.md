# End-of-Shift Reporting → MES Data Lake Architecture Pass

**Status:** Candidate development-architecture forcing case  
**Date:** 2026-08-24  
**Semantic authority:** canonical Operations Record DDD  
**New evidence:** Mark direct, 2026-08-24 — End-of-Shift Reporting needs to get into the MES data lake; MP2 → Asset Lifecycle is not ready to serve as the first integration/reference forcing case and requires substantially more discovery.

## Purpose

Use End-of-Shift Reporting as the next concrete architecture proof because it exercises a real ManuFactor-owned transactional record flowing into an analytical/data-lake destination without transferring domain ownership.

This pass replaces the earlier proposal to use MP2 → Asset Lifecycle as the first external-reference mapping forcing case.

MP2 → Asset Lifecycle remains important, but it is explicitly **not implementation-ready**. The work required there includes deeper source-structure, identity, hierarchy, mapping, restructuring, WR/WO correlation, and authority discovery before a trustworthy integration contract can be designed.

---

# Ownership model

```text
Operations Record / End-of-Shift Reporting
    owns:
      - the submitted shift report
      - correction/history semantics
      - report-type business fields and rules
      - record identity

MES data lake / analytical destination
    owns:
      - analytical storage/projection mechanics after ingestion
      - analytical indexes/grains/materializations built from the report

ManuFactor Analytics/ETL mechanics
    owns:
      - extraction/publication mechanics
      - transformation into the agreed analytical contract
      - delivery/checkpoint/retry/provenance mechanics

Neither analytics nor the MES data lake becomes authoritative for the original End-of-Shift Report lifecycle.
```

The authoritative record remains the Operations Record aggregate even after a copy/projection is successfully delivered downstream.

---

# Why this is a better forcing case

End-of-Shift Reporting exercises several reusable architecture responsibilities with less semantic ambiguity than MP2/Asset identity:

1. ManuFactor is unquestionably the source of the shift-report record.
2. The downstream direction is one-way publication/ETL, consistent with the established no-write-back posture.
3. The record already has retained correction/history semantics.
4. The downstream need is analytical/data-lake consumption rather than another domain lifecycle.
5. Failures can be modeled without guessing another source system's identity semantics.
6. The case tests whether the existing Data + Analytics/ETL + scheduling/integration substrate actually generalizes to a second materially different component.

---

# Target flow

```text
User completes End-of-Shift Report
        |
        v
Operations Record command/application handler
        |
        v
Operations Record-owned PostgreSQL tables
        |
        +--> retained record/history query
        |
        +--> publishable analytical record/change
                  |
                  v
          Analytics/ETL extraction boundary
                  |
                  v
       transform to downstream contract
                  |
                  v
            MES data lake
```

The ETL path must not write directly from a UI handler into the data lake as part of the domain transaction. Persisting the Operations Record and delivering an analytical copy are separate responsibilities.

---

# Minimum integration contract to discover

The next implementation-facing discovery should determine these facts before code is committed to a specific ingestion mechanism.

## Source record identity

Required:

- stable ManuFactor `OperationalRecordId`;
- concrete record type = End-of-Shift Report;
- mill/site identity;
- shift identity;
- report occurrence/business date;
- current revision/version identity or equivalent correction marker;
- created/submitted/corrected timestamps;
- provenance identifying ManuFactor Operations Record as source.

The data-lake copy must carry enough identity to distinguish a new report from a correction/revision of an existing report.

## Payload shape

Discover and document:

- exact End-of-Shift business fields;
- which fields are dimensions versus measures versus narrative/text;
- nullable/optional semantics;
- reference fields such as mill, crew, shift, area, equipment, or person where actually present;
- whether historical revisions are all published or only the latest current representation plus correction metadata;
- downstream schema/data types.

Do not infer the final payload from a generic Form abstraction. The End-of-Shift Report is a distinct Operations Record type with its own fields and rules.

## Destination

Must be made concrete before implementation:

- exact meaning of “MES data lake” in the current environment;
- destination technology/store/schema;
- target database/table/topic/object path;
- owning team/system;
- connection protocol and credentials;
- accepted ingestion method;
- whether ManuFactor writes directly to the lake or publishes to an intermediate ingestion endpoint.

The current architecture substrate already knows `mes_lum` as a read-only MES PostgreSQL source and separately models a ManuFactor Data Lake. Those facts must **not** be conflated with the newly stated MES-data-lake destination without verification.

## Delivery semantics

Define:

- trigger: on submit, on close/finalize, scheduled batch, or another confirmed point;
- latency expectation;
- idempotency key;
- retry behavior;
- checkpoint/watermark strategy;
- poison/dead-letter handling if needed;
- acknowledgement/success definition;
- correction/update semantics;
- backfill/replay behavior.

Until these are known, do not lock the implementation to CDC, outbox, direct DB insert, API push, file drop, queue, or scheduled polling.

---

# Recommended architectural seam

The existing `Analytics` capability is the correct first place to test this flow, not a new Bounded Context and not a new network service.

Conceptual contract:

```text
Operations Record
  -> exposes authoritative application/read data or publication records

Analytics.ETL
  -> obtains eligible End-of-Shift records
  -> transforms to the agreed MES-data-lake analytical schema
  -> delivers using the destination-specific adapter
  -> records checkpoint, source record/revision, delivery time, outcome, and destination identity
```

If the existing first-party Analytics/ETL implementation cannot express the delivery contract without special-case coupling, that is evidence for a narrower reusable ingestion/export mechanic. Do not create it in advance.

---

# Transaction and failure boundaries

## Domain transaction

The End-of-Shift Report submission/correction succeeds or fails based on Operations Record rules and persistence only.

A data-lake outage must not invalidate an otherwise valid Operations Record.

## Analytical delivery

Delivery is independently retryable.

Required observable states are technical, for example:

- pending publication;
- delivered;
- retryable failure;
- terminal/manual-attention failure;
- superseded by later revision.

These states belong to integration/publication mechanics, not the Operations Record's business lifecycle unless the domain later establishes a business rule requiring downstream delivery.

---

# Correction/history behavior

Operations Record explicitly preserves correction history. Therefore the lake path must not silently overwrite history in a way that destroys source provenance.

At minimum, each delivered representation needs:

- source OperationalRecordId;
- source revision/correction identity;
- effective/current indicator if the destination requires a latest-state view;
- source recorded timestamp;
- source correction timestamp where applicable;
- ingestion timestamp;
- ManuFactor source marker.

The destination may materialize a latest-current row for analytics, but that is a projection. Historical source revisions must remain recoverable from ManuFactor and should be retainable downstream when the analytical use case requires them.

---

# Architecture fitness rules exercised by this case

1. **One authoritative writer:** Operations Record owns the shift-report source record.
2. **No cross-context/domain write transfer:** Analytics may copy/project but does not become the operational writer.
3. **Separate transactions:** report persistence is not coupled to data-lake availability.
4. **Explicit provenance:** every analytical row/event derived from the report identifies ManuFactor and the source record/revision.
5. **Idempotent replay:** retries/backfills cannot create indistinguishable duplicate business records.
6. **No generic-record leakage:** downstream contract may be generic at the transport envelope level, but the payload retains End-of-Shift semantics.
7. **No invented lineage:** references are exported only when the source report actually carries them or a validated resolver supplies them.
8. **Correction-safe:** source corrections remain distinguishable from original submissions.
9. **Destination adapter isolation:** destination technology/protocol does not leak into Operations Record domain/application logic.
10. **Data-lake read model is not domain authority.**

---

# MP2 → Asset Lifecycle status correction

The earlier component-discovery pass overstated readiness by naming MP2 → Asset Lifecycle as the first external-reference forcing case.

Correct state:

```text
MP2 → Asset Lifecycle
status: discovery-required-before-contract-design
not_ready_for:
  - canonical external-reference contract forcing case
  - implementation mapping design
  - automatic hierarchy/asset reconciliation

requires_future_work:
  - inspect real MP2 asset/equipment hierarchy and identifiers
  - determine stable versus mutable identifiers
  - determine how hierarchy restructuring appears in source data
  - inspect WR/WO references and equipment relationships
  - establish current matching/correlation evidence
  - define unmatched/ambiguous cases
  - define bootstrap of ManuFactor Asset identity
  - validate source authority field-by-field
  - only then define mapping history/provenance contract
```

No generic shared mapper should be designed from assumptions about MP2 before that work is complete.

---

# Immediate next implementation-architecture work

Using End-of-Shift Reporting as the forcing case, the next pass should produce:

1. a .NET component-boundary fitness-rule set;
2. an End-of-Shift analytical publication envelope with provenance/revision/idempotency fields;
3. a destination-discovery checklist for the MES data lake;
4. a delivery-state/checkpoint model owned by Analytics/ETL mechanics;
5. a comparison of the existing Analytics/ETL Port contract against this concrete flow;
6. a list of exactly which unknown destination facts block implementation and which do not.

The work should stop short of choosing an ingestion technology until the actual MES-data-lake endpoint and accepted ingestion method are verified.
