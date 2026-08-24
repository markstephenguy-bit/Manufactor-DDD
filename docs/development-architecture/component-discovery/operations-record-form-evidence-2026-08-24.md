# Dry-End Operations Form Evidence

**Status:** Direct domain evidence for Operations Record and architecture contract refinement  
**Date received:** 2026-08-24  
**Source:** Mark-provided screenshots of current Diboll Lumber Operation dry-end forms  
**Authority:** Evidence of current operations forms and visible field labels only. This artifact does not infer field source systems, calculations, validation rules, or ownership beyond what the forms themselves demonstrate.

## Confirmed operations forms

The screenshots establish two current **operations forms** in the Diboll dry-end workflow:

1. **Dry End - First Hour Production Summary**
2. **Dry End - End of Shift Report**

These are not distinct Operations Record types. They are operations forms within the canonical `bc.operations-record` Bounded Context. Their overlapping structure is therefore expected and may be reused technically without inventing separate domain types for each form.

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

- Both are operations forms using a common operational-report shell: date, crew, shift, safety issues, comments, and a production metric body.
- The metric bodies overlap heavily but are not identical.
- `First Hour Production Summary` includes `Run Time (min)` and `Product Running`.
- `End of Shift Report` includes `Scheduled Hours` and `Re Run`.
- The current forms present production, throughput, moisture, uptime, and narrative/safety information together.
- Their common shape is a valid candidate for shared form mechanics; differences in fields remain form-specific configuration/behavior.
- The forms show enough concrete structure to define an End-of-Shift publication payload without claiming End-of-Shift is a separate domain record type.

## Provenance intentionally unresolved

The screenshots do **not** establish whether each field is:

- manually entered;
- pulled directly from MES;
- pulled from ProTrack or another process system;
- calculated by the current report process;
- copied from a separate report/data source;
- defaulted from reference data such as shift calendar/crew;
- or some combination of these.

Mark's current direction is **pull-first, manual-by-exception**: populate as many fields as practical from reliable authoritative sources or deterministic calculations, and use manual entry only where a trustworthy source is unavailable.

Therefore, every field's `source_kind`, `source_system`, `calculation`, `entry_authority`, and `correction_authority` remain unresolved until the current report-generation workflow/source data is inspected.

## Architecture consequence

The End-of-Shift publication contract should separate:

1. **record/form identity and provenance** — ManuFactor Operations Record identity/revision plus form identity, business date, crew, shift, site;
2. **business payload** — the End-of-Shift form fields listed above;
3. **field provenance where material** — source/derivation metadata once known;
4. **publication/delivery metadata** — idempotency, schema version, attempts, destination result.

The transport contract may be reusable, and the form mechanism may be reusable, without creating separate domain record types for First Hour and End of Shift.

## DDD consequence

This evidence strengthens the existing `Operations Record` boundary. It does not create a new Bounded Context and does not establish First Hour Production Summary or End of Shift Report as distinct domain types. They are confirmed **operations forms** handled by Operations Record.
