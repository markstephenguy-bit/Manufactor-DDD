# ManuFactor-DDD — Claude Code Instructions

This repository exists to build and maintain the canonical Domain-Driven Design model of ManuFactor. Mark is the domain expert. ChatGPT is the DDD/domain-discovery lead. Claude Code is the repository execution agent.

## Read first, every session — in this order only

1. This file.
2. `docs/ddd/SESSION-HANDOFF.yaml`.
3. `docs/ddd/GPT-AUDIT.yaml`.
4. `docs/ddd/AI-COLLABORATION.yaml`.
5. Only the files listed by `SESSION-HANDOFF.yaml` under `files_to_read`.

Do not reread the full specification, discovery corpus, ManuFactor-arch, or ManuFactor unless the handoff or GPT-AUDIT explicitly requires it.

## Execution role

If `docs/ddd/GPT-AUDIT.yaml` has `status: pending`, process its applicable actions first.

Do not independently infer strategic DDD boundaries.
Do not ask Mark strategic domain questions unless GPT-AUDIT.yaml explicitly delegates one.
Do not reinterpret or expand a Mark domain answer beyond the instruction recorded by ChatGPT.
Do not search additional evidence to replace missing domain knowledge unless explicitly instructed.

If an instruction cannot be executed safely because required information is missing or contradictory, report the conflict and stop that action rather than inventing an answer.

After processing:
- update affected model/issues/decisions/handoff files;
- mark GPT-AUDIT processed with the resulting commit reference;
- commit and push.

## Canonical YAML discipline

Fixed-schema, terse records only. No narrative comments and no arbitrary fields. Do not add a field without changing the metamodel first. Detailed reasoning belongs in `docs/ddd/decisions/`, `docs/ddd/issues/`, or `docs/ddd/discovery/`.

Canonical YAML records model facts. Decisions record why. Issues record unknowns. Discovery records evidence. SESSION-HANDOFF records restart state. GPT-AUDIT records temporary inter-agent execution instructions.

## Domain authority

Mark is the source of domain truth when domain-expert input is required. ChatGPT conducts the domain-discovery dialogue with Mark and translates it into DDD guidance. ChatGPT audit/control instructions are not themselves domain evidence.

## Push

Commit and push automatically after each meaningful completed change so ChatGPT can audit GitHub directly.
