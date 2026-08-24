# Dry-End Operational Report Form Evidence

**Status:** Direct domain evidence for Operations Record and architecture contract refinement  
**Date received:** 2026-08-24  
**Source:** Mark-provided screenshots of current Diboll Lumber Operation dry-end reports  
**Authority:** Evidence of current report types/field labels only. This artifact does not infer field source systems, calculations, validation rules, or ownership beyond what the forms themselves demonstrate.

## Confirmed report types

The screenshots establish at least two distinct operational report types in the Diboll dry-end workflow:

1. **Dry End - First Hour Production Summary**
2. **Dry End - End of Shift Report**

They share many field labels but are not to be collapsed into a generic report type merely because of structural overlap. They are separate business report types within the canonical `bc.operations-record` Bounded Context.

## Shared header/context fields visible on both forms

- Date
- Crew
- Shift
- Safety Issues
- Comments

## First Hour Production Summary fields visible

- Run Time (min)
- BF Ran
- Pieces
- Cars Un-Stacked
- MBF/Hour
- Product Running
- Gap
- Trimmer Uptime
- Moisture Content
- Standard Deviation
- Too Wet
- Too Dry
- Cars Stacked

## End of Shift Report fields visible

- Scheduled Hours
- BF Ran
- Pieces
- Cars Un-Stacked
- MBF/Hour
- Gap
- Re Run
- Trimmer Uptime
- Moisture Content
- Standard Deviation
- Too Wet
- Too Dry
- Cars Stacked

## Structural observations supported by the forms

- The two record types have a common operational-report shell: date, crew, shift, safety issues, comments, and a production metric body.
- The metric bodies overlap heavily but are not identical.
- `First Hour Production Summary` includes `Run Time (min)` and `Product Running`.
- `End of Shift Report` includes `Scheduled Hours` and `Re Run`.
- The current forms present production, throughput, moisture, uptime, and narrative/safety information together as one business report.
- The forms show enough concrete structure to define an End-of-Shift publication payload shape without inventing a generic `OperationalRecordPayload` contract.

## Provenance intentionally unresolved

The screenshots do **not** establish whether each field is:

- manually entered;
- pulled directly from MES;
- pulled from ProTrack or another process system;
- calculated by the current report process;
- copied from a separate report/data source;
- defaulted from reference data such as shift calendar/crew;
- or some combination of these.

Therefore, every field's `source_kind`, `source_system`, `calculation`, `entry_authority`, and `correction_authority` remain unresolved until the current report-generation workflow is inspected or Mark supplies that information.

This is especially important because a future ManuFactor form may prepopulate externally sourced values while still owning the submitted End-of-Shift record. Prepopulation does not transfer source authority for the originating fact, and submission does not imply ManuFactor generated every value itself.

## Architecture consequence

The End-of-Shift publication contract should separate:

1. **record identity/provenance** — ManuFactor Operations Record identity, revision, business date, crew, shift, site;
2. **business payload** — the concrete End-of-Shift fields listed above;
3. **field provenance where material** — source/derivation metadata once known;
4. **publication/delivery metadata** — idempotency, schema version, attempts, destination result.

The transport contract may be reusable, but the End-of-Shift business payload remains a named concrete schema.

## DDD consequence

This evidence strengthens the existing `Operations Record` boundary. It does not create a new Bounded Context and does not justify a generic Form/Report domain abstraction. Both confirmed report types are examples of the canonical rule that each operational record type corresponds to a distinct business need with its own field set and rules.
