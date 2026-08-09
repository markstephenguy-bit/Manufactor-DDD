# ManuFactor-DDD — Claude Code Instructions

This repository exists to build and maintain the canonical Domain-Driven Design model of ManuFactor, under a normative specification authored by Mark. This is not a general architecture-discovery repo — it has one governing document, and that document is binding.

## Read first, every session — in this order only

1. This file.
2. **`docs/ddd/SESSION-HANDOFF.yaml`** — the actual restart point. States current phase, what's blocking, the next single action, and exactly which files matter right now.
3. Only the files `SESSION-HANDOFF.yaml`'s `files_to_read` lists.

Do not reread the full `docs/ddd/SPECIFICATION.md`, the discovery files, or the evidence repos (`ManuFactor-arch`, `ManuFactor`) unless the handoff explicitly says it's necessary. `SESSION-HANDOFF.yaml` is kept current after every meaningful change specifically so a new session doesn't have to re-derive state from scratch — trust it over re-deriving.

## Canonical YAML discipline

Fixed-schema, terse records only. No narrative comments, no arbitrary fields — do not add a field without changing the metamodel first. Detailed reasoning belongs in `docs/ddd/decisions/`, `docs/ddd/issues/`, or `docs/ddd/discovery/` — never accumulated inside a canonical record. Excluded/dead material lives in `docs/ddd/discovery/excluded.yaml`, out of active candidate files.

## Domain authority

Mark is the primary domain expert. Repository evidence helps recover and test the model; it does not substitute for his knowledge. When a strategic boundary can't be determined confidently from evidence, ask one precise question at a time — never invent the answer, never present a questionnaire, never search for more evidence first as a substitute for asking.

## Push

Commit and push to GitHub automatically after each meaningful change — Mark audits this repo via GitHub, not the local working directory.
