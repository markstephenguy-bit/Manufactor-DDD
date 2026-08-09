# ManuFactor DDD Model

## What this is

This directory holds the canonical Domain-Driven Design model of ManuFactor — the authoritative record of the domain's subdomains, bounded contexts, ubiquitous language, and (once strategic modeling is stable) the tactical model within each context: aggregates, entities, value objects, invariants, commands, events, policies, services, repositories, factories, queries, and use cases.

It is not architecture. It is not a technology choice. It is a model of the business domain itself, built and governed under `SPECIFICATION.md`.

## Where the canonical files live

- **`SPECIFICATION.md`** — the governing modeling contract. Every file in this directory exists because that specification requires or permits it. When in doubt about how something should be modeled, the specification is authoritative, not this README.
- **`domain/`** — the single top-level Domain record and the domain-level Ubiquitous Language.
- **`subdomains/`** — candidate and confirmed subdomains, each classified core / supporting / generic.
- **`contexts/<context-id>/`** — one directory per Bounded Context, holding that context's language, concepts, aggregates, entities, value objects, invariants, commands, events, policies, services, repositories, factories, queries, use cases, and integration contracts.
- **`context-map/`** — the single canonical Context Map: every meaningful relationship between bounded contexts, with direction and DDD relationship type.
- **`discovery/`** — working material from the discovery phases: the evidence index, term candidates, capability candidates, context candidates. Not itself canonical domain content — it's what canonical content gets derived from.
- **`decisions/`** — one record per significant modeling decision (creating/splitting/merging a context, declaring a Shared Kernel, changing an aggregate boundary, etc.).
- **`issues/unresolved.yaml`** — every open question the model currently depends on. A model with open, honestly-recorded issues is valid. A model with hidden uncertainty is not.
- **`reports/`** — the validation report and the human-readable model summary, both generated from canonical content, neither itself canonical.

None of these exist yet except this file and `SPECIFICATION.md`. Per the specification's own §62: files are created when the corresponding content exists, not in advance as empty scaffolding.

## How statuses work

Every canonical object carries a `status` from a controlled vocabulary: `candidate`, `proposed`, `confirmed`, `deprecated`, `rejected`, `unresolved`. `confirmed` is used conservatively — only when evidence and the specification's own constraints justify treating something as authoritative (§108).

## How provenance works

Every significant model object records where it came from: a `type` (domain-expert, existing-code, database, api, procedure, business-document, existing-model, observation, external-system, inference, decision) and a `confidence` (confirmed, high, medium, low, unknown). An inference is always labeled as an inference — it never silently becomes a stated fact (§8).

## How unresolved questions work

Open questions live in `issues/unresolved.yaml`, not buried in prose or silently resolved by picking the most plausible answer. The model is allowed to stay uncertain about something. It is not allowed to hide that uncertainty (§65, §110).

## How validation is performed

The model must pass ten gates (Gate A through Gate J, §78-§88) covering domain completeness, bounded-context validity, context-map validity, language validity, aggregate validity, entity/value-object validity, behavior validity, boundary leakage, provenance, and unresolved-question tracking. Results are recorded in `reports/validation-report.md` using `PASS`, `PASS WITH WARNINGS`, or `FAIL` — never a vague summary (§89).

## How changes are proposed

Significant changes to an established model follow §95: identify affected objects, identify the evidence causing the change, identify downstream dependencies, record a decision if the change is significant, update canonical files and cross-references, rerun validation, update the validation report. Confirmed concepts that stop being valid are marked `deprecated` or `rejected` with rationale — never silently deleted (§96).

## ArchiMate

ArchiMate is not the canonical DDD notation or metamodel for ManuFactor and must not be used to determine DDD semantics.
