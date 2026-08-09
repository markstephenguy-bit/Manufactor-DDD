# ManuFactor Domain-Driven Design Modeling Specification

**Document type:** Normative project specification  
**Applies to:** ManuFactor domain-modeling work performed by Claude Code or other agents  
**Primary purpose:** Produce and maintain a complete, internally consistent Domain-Driven Design model of ManuFactor  
**Status:** Project modeling standard  
**Notation authority:** This specification  
**ArchiMate:** Explicitly excluded from the canonical DDD model

---

# 1. Purpose

This specification defines exactly how the ManuFactor Domain-Driven Design model is to be discovered, represented, validated, stored, and maintained.

The objective is not merely to describe ManuFactor using terminology associated with Domain-Driven Design.

The objective is to create a model that can legitimately be treated as the authoritative **DDD model of ManuFactor**.

The resulting model must:

1. Define the business domain.
2. Divide the domain into subdomains.
3. classify those subdomains.
4. Establish bounded contexts.
5. Establish the language and semantic boundary of every bounded context.
6. Define relationships between bounded contexts.
7. Define the domain concepts belonging to each bounded context.
8. Model aggregates and their consistency boundaries.
9. Model entities and value objects.
10. Model invariants and business rules.
11. Model commands, events, policies, services, repositories, factories, and use cases where applicable.
12. Preserve provenance for modeling decisions.
13. Explicitly represent unresolved questions instead of silently guessing.
14. Be machine-readable.
15. Be human-readable.
16. Be mechanically validatable.
17. Remain independent of implementation technologies.
18. Be capable of producing implementation, documentation, architectural, ontology, or visualization projections without those projections becoming authoritative.

This specification acts as the **local normative metamodel for ManuFactor DDD**.

DDD itself does not provide an ISO-style normative schema. ManuFactor therefore establishes this specification as the constraint system within which its DDD model must remain.

---

# 2. Normative terminology

The words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, and **MAY** have deliberate meanings throughout this specification.

- **MUST / REQUIRED** — mandatory for model validity.
- **MUST NOT** — prohibited.
- **SHOULD** — expected unless a documented reason exists to deviate.
- **SHOULD NOT** — generally prohibited unless a documented exception exists.
- **MAY** — optional.

Claude must interpret these terms literally.

Claude must not weaken a MUST into a recommendation.

Claude must not satisfy a MUST by creating placeholder prose that does not contain the required semantic information.

---

# 3. Critical rule: ArchiMate is not part of the ManuFactor DDD model

Previous design discussions considered using ArchiMate to constrain or represent the ManuFactor DDD model.

That approach has been intentionally abandoned.

## 3.1 Prohibition

Claude MUST NOT use ArchiMate as:

- the ManuFactor DDD metamodel;
- the canonical notation;
- the source of DDD element definitions;
- the source of allowed relationships;
- the source of containment rules;
- the source of bounded-context semantics;
- a substitute for DDD concepts;
- a mechanism for deciding whether a DDD object exists;
- a mechanism for determining aggregate boundaries;
- a mechanism for determining entity or value-object semantics;
- a mechanism for defining context relationships.

Claude MUST NOT map DDD concepts to ArchiMate elements while constructing the canonical DDD model.

Examples of prohibited reasoning include:

```text
Bounded Context = ArchiMate Grouping
Entity = ArchiMate Business Object
Aggregate = composition of ArchiMate Data Objects
Customer/Supplier = ArchiMate Serving
```

Even where such mappings might be useful for visualization, they are not semantic equivalences.

## 3.2 Future ArchiMate use

ArchiMate MAY later be used as a **projection** of information already contained in the canonical ManuFactor DDD model.

The direction of authority is strictly:

```text
ManuFactor DDD Model
        |
        +--> ArchiMate projection
        +--> C4 projection
        +--> UML projection
        +--> documentation
        +--> source-code guidance
        +--> database guidance
        +--> API guidance
        +--> ontology / knowledge graph
```

It is never:

```text
ArchiMate
    |
    +--> defines ManuFactor DDD
```

No information discovered only because an ArchiMate element requires it becomes part of the DDD model unless that information is independently valid under this specification.

---

# 4. Other notation systems

The same authority rule applies to other modeling notations.

C4, UML, BPMN, ER diagrams, database schemas, class diagrams, API specifications, source code, and physical deployment diagrams MAY be useful projections or evidence sources.

They MUST NOT define the semantic structure of the canonical DDD model.

The canonical model must remain understandable without any of them.

---

# 5. Fundamental model structure

The required high-level structure is:

```text
ManuFactor Domain
|
+-- Domain Vision
|
+-- Ubiquitous Domain Vocabulary
|
+-- Subdomains
|   |
|   +-- Core
|   +-- Supporting
|   +-- Generic
|
+-- Bounded Contexts
|   |
|   +-- Purpose
|   +-- Ownership
|   +-- Boundary
|   +-- Ubiquitous Language
|   +-- Domain Concepts
|   +-- Aggregates
|   |   |
|   |   +-- Aggregate Root
|   |   +-- Entities
|   |   +-- Value Objects
|   |   +-- Invariants
|   |   +-- Domain Events
|   |
|   +-- Domain Services
|   +-- Policies
|   +-- Commands
|   +-- Queries
|   +-- Application Use Cases
|   +-- Repositories
|   +-- Factories
|   +-- Integration Contracts
|
+-- Context Map
    |
    +-- Context Relationships
```

Not every bounded context is required to contain every tactical DDD construct.

Every construct that does exist must conform to this specification.

---

# 6. Strategic DDD and tactical DDD

ManuFactor requires both.

Claude MUST distinguish them.

## 6.1 Strategic DDD

Strategic modeling establishes:

```text
Domain
Subdomains
Subdomain classification
Bounded Contexts
Context ownership
Context boundaries
Ubiquitous Language
Context relationships
Upstream/downstream relationships
Integration semantics
Published contracts
```

Strategic modeling answers:

> Where is a particular model true?

and:

> Which part of ManuFactor owns a particular meaning?

Strategic modeling MUST be sufficiently stable before detailed tactical modeling is treated as authoritative.

## 6.2 Tactical DDD

Tactical modeling occurs inside a bounded context and establishes:

```text
Domain Concepts
Aggregates
Aggregate Roots
Entities
Value Objects
Invariants
Commands
Domain Events
Policies
Domain Services
Repositories
Factories
Queries
Application Use Cases
```

Tactical DDD MUST NOT erase bounded-context boundaries.

An Entity in one bounded context is not automatically the same model object as a similarly named Entity in another bounded context.

---

# 7. Source of truth

The canonical DDD model is the structured DDD model repository created under this specification.

Source code is evidence.

Database schemas are evidence.

Existing documentation is evidence.

Existing API contracts are evidence.

User statements are evidence.

Business procedures are evidence.

Production behavior is evidence.

Existing terminology is evidence.

None of those sources automatically override the canonical model merely because they already exist.

The model must distinguish:

```text
observed-current-state
confirmed-domain-rule
proposed-model
unresolved
deprecated
rejected
```

---

# 8. Evidence and provenance

Every significant model object MUST have provenance.

At minimum:

```yaml
provenance:
  sources:
    - type:
      reference:
      description:
  confidence:
  status:
```

Allowed `type` values SHOULD include:

```text
domain-expert
existing-code
database
api
procedure
business-document
existing-model
observation
external-system
inference
decision
```

Confidence MUST use:

```text
confirmed
high
medium
low
unknown
```

An inference MUST be marked as an inference.

Claude MUST NOT silently convert inference into fact.

---

# 9. Stable identifiers

Every canonical model object MUST receive a stable ID.

IDs MUST NOT depend on display names.

Example:

```text
domain.manufactor
subdomain.production
bc.production-tracking
concept.production-tracking.board
aggregate.production-tracking.production-run
entity.production-tracking.production-run.board
event.production-tracking.board-created
```

Renaming an object SHOULD NOT change its stable ID unless the modeler's intent is that it actually become a different semantic object.

---

# 10. Required common metadata

Unless otherwise specified, canonical objects MUST support:

```yaml
id:
name:
description:
status:
owner:
provenance:
tags: []
notes: []
```

`status` MUST use a controlled vocabulary:

```text
candidate
proposed
confirmed
deprecated
rejected
unresolved
```

Do not invent additional status values casually.

---

# 11. Domain

There MUST be exactly one top-level ManuFactor domain record.

Required fields:

```yaml
id:
name:
vision:
business_purpose:
scope:
out_of_scope:
primary_stakeholders:
domain_experts:
success_characteristics:
status:
provenance:
```

The Domain Vision MUST describe the problem space and purpose.

It MUST NOT become an application feature list.

It MUST NOT become a technology description.

---

# 12. Subdomains

Every significant area of domain responsibility MUST be assigned to a subdomain.

A subdomain record MUST contain:

```yaml
id:
name:
description:
classification:
business_capabilities:
business_value:
differentiation:
scope:
out_of_scope:
domain_experts:
candidate_bounded_contexts:
status:
provenance:
```

Classification MUST be one of:

```text
core
supporting
generic
```

## 12.1 Core

Use `core` when the capability materially differentiates ManuFactor or represents strategically important domain knowledge.

## 12.2 Supporting

Use `supporting` when the capability is specific or important to the domain but is not itself the principal competitive/domain differentiator.

## 12.3 Generic

Use `generic` when the capability is largely commodity functionality and does not need proprietary domain modeling.

Claude MUST NOT classify based solely on technical complexity.

---

# 13. Bounded Context

A Bounded Context is a semantic and model boundary.

It is not:

- merely a namespace;
- merely a module;
- merely a service;
- merely a database schema;
- merely a UI area;
- merely an organizational department.

A bounded-context record MUST contain:

```yaml
id:
name:
purpose:
subdomain:
model_statement:
responsibilities:
scope:
out_of_scope:
owned_concepts:
external_concepts:
ubiquitous_language:
business_rules:
owner:
domain_experts:
integration_surface:
upstream_dependencies:
downstream_consumers:
status:
provenance:
```

## 13.1 Model statement

Every bounded context MUST have a concise statement of what model is valid inside it.

Example structure:

```text
Within <context>, <term> means <specific contextual meaning>, and this
context is authoritative for <responsibility>.
```

## 13.2 Boundary test

Claude MUST be able to answer:

> What changes meaning when I cross this boundary?

If the answer is effectively “nothing,” the proposed bounded context boundary must be challenged.

## 13.3 Ownership test

For every major concept, Claude MUST identify which bounded context is authoritative for that concept's meaning or lifecycle.

Multiple contexts MAY use similarly named concepts.

They MUST NOT implicitly share one semantic model merely because their names are identical.

---

# 14. Ubiquitous Language

Ubiquitous Language is contextual.

It is not a global dictionary where every term must have one universal definition.

There MUST be:

1. domain-level vocabulary where truly universal terms exist; and
2. bounded-context vocabulary where contextual meanings exist.

A language term MUST contain:

```yaml
id:
term:
context:
definition:
business_meaning:
synonyms:
discouraged_synonyms:
examples:
counterexamples:
related_concepts:
ambiguities:
status:
provenance:
```

Claude MUST actively detect overloaded terms.

If one word has materially different meanings in two contexts, the model MUST preserve both contextual meanings.

Do not “clean up” legitimate domain language merely to make terminology globally uniform.

---

# 15. Domain Concept

`DomainConcept` is a ManuFactor modeling construct used to explicitly capture the conceptual vocabulary from which tactical modeling is derived.

A Domain Concept MAY later become an Entity, Value Object, Aggregate Root, Domain Service concern, event payload concept, or remain conceptual.

Required fields:

```yaml
id:
name:
bounded_context:
definition:
identity_semantics:
lifecycle:
state:
behaviors:
rules:
relationships:
candidate_model_role:
status:
provenance:
```

Allowed `candidate_model_role` examples:

```text
aggregate-root
entity
value-object
domain-service
policy
event
reference-data
concept-only
unresolved
```

Claude MUST NOT immediately turn every noun into an Entity.

---

# 16. Aggregate

An Aggregate is a consistency and invariant boundary.

It is NOT primarily a data-container hierarchy.

It is NOT created simply because objects have parent-child relationships.

Every aggregate MUST contain:

```yaml
id:
name:
bounded_context:
purpose:
aggregate_root:
members:
invariants:
commands:
domain_events:
transaction_boundary:
external_references:
repository:
creation_rules:
deletion_rules:
status:
provenance:
```

## 16.1 Aggregate test

Before confirming an aggregate, Claude MUST answer:

1. What invariant requires these objects to change consistently?
2. What is the aggregate root?
3. Which operations must be atomic from the domain's perspective?
4. Which objects may only be changed through the root?
5. What prevents this aggregate from being smaller?

If these questions cannot be answered, the aggregate remains `candidate` or `unresolved`.

## 16.2 Aggregate size

Prefer the smallest aggregate capable of enforcing its invariants.

Do not aggregate objects merely because they are convenient to load together.

Do not aggregate objects merely because the database contains a foreign key.

## 16.3 Cross-aggregate references

One aggregate SHOULD reference another aggregate by identity rather than directly owning its object graph.

Cross-context object references are prohibited in the canonical domain model.

Cross-context interaction MUST occur through an explicit context relationship or integration contract.

---

# 17. Aggregate Root

Every Aggregate MUST have exactly one Aggregate Root.

The root:

- provides the authoritative mutation boundary;
- protects aggregate invariants;
- exposes domain behavior;
- controls modification of internal members.

Other aggregates MUST NOT mutate internal members directly.

---

# 18. Entity

An Entity is defined principally by identity continuity.

Required fields:

```yaml
id:
name:
bounded_context:
aggregate:
domain_identity:
identity_rules:
attributes:
behaviors:
lifecycle:
invariants:
status:
provenance:
```

Claude MUST explicitly state the domain identity semantics.

Do not infer Entity solely because something has a database primary key.

Database identity and domain identity are not automatically equivalent.

---

# 19. Value Object

A Value Object is defined by its value rather than persistent identity.

Required fields:

```yaml
id:
name:
bounded_context:
aggregate:
components:
validation_rules:
equality_semantics:
immutability_expectation:
behaviors:
status:
provenance:
```

A Value Object SHOULD be treated as immutable conceptually.

Replacement with an equivalent value is normally more appropriate than independent lifecycle management.

Claude MUST challenge a proposed Value Object if independent identity or lifecycle is required.

---

# 20. Invariant

Invariants are first-class model objects.

They MUST NOT be hidden only inside Entity or Aggregate prose.

Required fields:

```yaml
id:
name:
bounded_context:
aggregate:
rule:
reason:
triggering_operations:
enforcement_boundary:
failure_semantics:
status:
provenance:
```

An invariant MUST be stated as a rule that can be evaluated conceptually.

Weak:

```text
Orders should generally be valid.
```

Strong:

```text
A released order cannot contain a line whose required quantity is zero.
```

The exact business rule must come from ManuFactor evidence. Claude must not invent rules from examples.

---

# 21. Command

A Command represents intent to change domain state.

Required fields:

```yaml
id:
name:
bounded_context:
target_aggregate:
intent:
initiators:
input:
preconditions:
invariants_checked:
possible_events:
failure_conditions:
idempotency_expectation:
status:
provenance:
```

Command names SHOULD use imperative form:

```text
CreateProductionRun
AssignBoardToPackage
ReleaseWorkOrder
RecordInspection
```

A command MUST NOT describe something that already happened.

---

# 22. Domain Event

A Domain Event represents a domain-significant fact that has occurred.

Required fields:

```yaml
id:
name:
bounded_context:
owning_aggregate:
meaning:
trigger:
payload_concepts:
consumers:
business_significance:
ordering_requirements:
status:
provenance:
```

Names SHOULD be past tense:

```text
ProductionRunStarted
BoardGraded
PackageCompleted
InspectionRejected
```

Events MUST describe facts.

Events MUST NOT contain imperative behavior.

A technical message is not automatically a Domain Event.

A database change is not automatically a Domain Event.

---

# 23. Policy

A Policy represents a business rule or decision mechanism that reacts to facts or determines an action.

Required fields:

```yaml
id:
name:
bounded_context:
purpose:
inputs:
conditions:
decision:
resulting_commands:
related_events:
exceptions:
status:
provenance:
```

A policy SHOULD be modeled when business behavior spans events, decisions, or multiple aggregates without belonging naturally to one Entity.

---

# 24. Domain Service

A Domain Service represents domain behavior that is genuinely part of the domain but does not naturally belong to one Entity or Value Object.

Required fields:

```yaml
id:
name:
bounded_context:
purpose:
inputs:
outputs:
domain_rules:
participating_aggregates:
side_effects:
status:
provenance:
```

Claude MUST NOT create Domain Services merely to hold miscellaneous logic.

Behavior belonging naturally to an Entity or Aggregate Root SHOULD remain there.

---

# 25. Repository

A Repository represents collection-like access to Aggregate Roots.

Required fields:

```yaml
id:
name:
bounded_context:
aggregate:
purpose:
retrieval_semantics:
persistence_semantics:
status:
provenance:
```

Repositories SHOULD normally exist per Aggregate Root, not per Entity.

Repository definitions MUST remain technology-independent in the DDD model.

The DDD Repository model MUST NOT specify SQL, Entity Framework, PostgreSQL, filesystem layout, or another persistence technology unless placed in a separate implementation projection.

---

# 26. Factory

A Factory is used when domain object or aggregate creation is sufficiently complex to deserve explicit domain semantics.

Required fields:

```yaml
id:
name:
bounded_context:
creates:
creation_rules:
required_inputs:
invariants_established:
failure_conditions:
status:
provenance:
```

Do not create factories mechanically for every Aggregate.

---

# 27. Query

A Query requests information without expressing intent to change domain state.

Required fields:

```yaml
id:
name:
bounded_context:
purpose:
inputs:
result:
authoritative_source:
consistency_expectation:
status:
provenance:
```

Queries do not need to mirror Aggregates.

Read models MAY cross internal persistence representations provided that semantic context boundaries remain explicit.

Cross-context information composition must be represented as composition, not disguised as a single shared domain model.

---

# 28. Application Use Case

Use cases describe orchestration around the domain model.

Required fields:

```yaml
id:
name:
bounded_context:
goal:
actors:
trigger:
preconditions:
commands:
queries:
domain_services:
events_observed:
result:
failure_paths:
status:
provenance:
```

Application orchestration MUST NOT redefine domain invariants.

Domain rules belong in the domain model.

---

# 29. Integration Contract

Every significant cross-context interaction MUST have an explicit Integration Contract.

Required fields:

```yaml
id:
name:
provider_context:
consumer_context:
purpose:
interaction_style:
published_model:
operations:
events:
compatibility_policy:
translation_required:
failure_semantics:
ownership:
status:
provenance:
```

Interaction styles MAY include:

```text
request-response
command
event
stream
batch
file
shared-reference-data
manual
```

The integration contract MUST explicitly state whether the consumer is allowed to use the provider's terminology directly or requires translation.

---

# 30. Context Map

There MUST be exactly one canonical Context Map for the current authoritative ManuFactor DDD model.

The Context Map consists of explicit relationships between Bounded Contexts.

A Context Relationship MUST contain:

```yaml
id:
upstream:
downstream:
relationship_type:
description:
model_influence:
integration_contracts:
translation_boundary:
organizational_notes:
status:
provenance:
```

Allowed relationship types are:

```text
partnership
shared-kernel
customer-supplier
conformist
anti-corruption-layer
open-host-service
published-language
separate-ways
unresolved
```

Multiple applicable patterns MAY be represented where semantically valid.

Claude MUST NOT create relationship types outside the controlled vocabulary without first recording a modeling decision explaining why the existing DDD relationship vocabulary is insufficient.

---

# 31. Upstream and downstream semantics

Direction matters.

The model MUST explicitly represent:

```text
upstream
downstream
```

where those semantics apply.

`upstream` means the context whose model or published capability influences the downstream consumer.

`downstream` means the consuming context that must decide whether to conform, translate, protect itself with an anti-corruption layer, or otherwise interact.

Claude MUST NOT infer direction merely from runtime network direction.

DDD model influence and technical call direction are different concerns.

---

# 32. Shared Kernel restriction

Shared Kernel MUST be treated as a high-coupling governance decision.

Claude MUST NOT use Shared Kernel merely because two contexts share:

- a database table;
- DTOs;
- source-code classes;
- a package;
- constants;
- identifiers;
- utility code.

A valid Shared Kernel requires deliberate shared ownership of part of the domain model.

If explicit shared governance cannot be established, Shared Kernel MUST NOT be confirmed.

---

# 33. Anti-Corruption Layer

An Anti-Corruption Layer protects one bounded context's model from another model.

It MUST be represented semantically.

It is not simply:

```text
adapter class exists
```

The model MUST identify:

```text
protected_context
external/upstream_context
concepts requiring translation
translation rules
lossy translations
unsupported semantics
ownership
```

Implementation components MAY later realize this relationship.

---

# 34. Published Language

Published Language means an intentionally stabilized integration language.

It MUST NOT be inferred merely because JSON, XML, protobuf, database tables, or API DTOs exist.

The model must record the published semantics and ownership.

---

# 35. Open Host Service

Open Host Service represents an intentionally defined general integration capability exposed to multiple consumers.

It MUST NOT be inferred merely because a web API exists.

The domain-level service contract and its intended consumers must be established.

---

# 36. Partnership

Partnership requires coordinated evolution between contexts.

Claude MUST capture the collaboration dependency.

Do not classify two contexts as Partnership simply because their developers communicate.

---

# 37. Customer/Supplier

Customer/Supplier relationships MUST identify which downstream needs influence upstream priorities or contracts.

The relationship is organizational and semantic, not merely a technical dependency.

---

# 38. Conformist

Conformist means the downstream accepts the upstream model rather than maintaining meaningful translation or negotiating its own model.

Claude MUST document that this is deliberate.

---

# 39. Separate Ways

Use Separate Ways when contexts intentionally avoid integration.

Absence of integration due to unfinished work does not automatically mean Separate Ways.

---

# 40. Allowed containment model

The following containment rules are normative:

```text
Domain
  contains Subdomains

Subdomain
  references Bounded Contexts

Bounded Context
  contains/contextually owns:
    Ubiquitous Language Terms
    Domain Concepts
    Aggregates
    Domain Services
    Policies
    Commands
    Queries
    Use Cases
    Integration Contracts

Aggregate
  contains:
    exactly one Aggregate Root
    zero or more Entities
    zero or more Value Objects
    one or more meaningful Invariants when behavior warrants an aggregate
    zero or more Domain Events

Context Map
  contains:
    Context Relationships
```

A Bounded Context MUST NOT be nested inside an Aggregate.

An Aggregate MUST NOT span Bounded Contexts.

An Entity MUST NOT belong to multiple Aggregates in the same semantic role.

A canonical Domain Event MUST have one owning semantic context.

---

# 41. Cross-context identity

The same real-world thing MAY be represented by different model objects in different contexts.

Claude MUST distinguish:

```text
same real-world referent
```

from:

```text
same domain model object
```

These are not equivalent.

Cross-context identity correlation MUST be modeled explicitly where needed.

Do not merge objects merely because they share an identifier.

---

# 42. External systems

External systems MUST NOT automatically become Bounded Contexts.

Determine first whether the external system represents:

```text
an external actor
an upstream model
a downstream consumer
an integration mechanism
a generic capability
a separate domain
```

Only model a Bounded Context when a meaningful model boundary exists.

---

# 43. People and organizational structures

Departments, teams, and job titles MUST NOT automatically become bounded contexts.

Organizational ownership is evidence.

Semantic model boundaries remain the determining factor.

---

# 44. Database boundaries

Database schemas and tables MUST NOT determine bounded-context boundaries.

Existing data structures are evidence of implementation history.

Claude MUST evaluate them against domain semantics.

---

# 45. Application boundaries

Existing applications MUST NOT automatically become Bounded Contexts.

A single application MAY implement several bounded contexts.

One bounded context MAY be implemented across several technical components.

DDD boundaries are semantic first.

---

# 46. Services and microservices

Claude MUST NOT assume:

```text
bounded context = microservice
```

Deployment architecture is separate.

A modular monolith can contain multiple bounded contexts.

A bounded context may later have one or more deployment units.

That is an implementation decision outside the canonical DDD model.

---

# 47. Discovery procedure

Claude MUST follow the discovery sequence below.

It MUST NOT jump immediately to Aggregate design.

## Phase 1 — Evidence inventory

Inspect available material relevant to ManuFactor.

Candidate evidence includes:

```text
existing source code
documentation
requirements
business procedures
API specifications
database definitions
configuration
existing terminology
forms
reports
screens
integration definitions
tests
issue trackers
previous architecture documents
user-provided direction
```

Create:

```text
ddd/discovery/evidence-index.yaml
```

Every item receives:

```yaml
id:
type:
location:
description:
relevance:
authority:
notes:
```

Do not reinterpret evidence yet.

---

# 48. Phase 2 — Vocabulary extraction

Extract business terms.

Create:

```text
ddd/discovery/term-candidates.yaml
```

For each term record:

```yaml
term:
observed_meanings:
sources:
possible_contexts:
ambiguities:
questions:
```

Do not force one definition when multiple meanings are observed.

Overloaded language is important boundary evidence.

---

# 49. Phase 3 — Capability discovery

Identify business capabilities independent of software organization.

Create:

```text
ddd/discovery/capability-candidates.yaml
```

Record:

```yaml
name:
purpose:
inputs:
outputs:
actors:
rules:
terminology:
existing_systems:
sources:
```

Do not classify software modules as capabilities without semantic justification.

---

# 50. Phase 4 — Subdomain discovery

Group related capabilities and domain knowledge into candidate subdomains.

For each candidate:

1. state why the group belongs together;
2. state what differentiates it from adjacent groups;
3. classify it as core/supporting/generic;
4. record evidence;
5. record uncertainty.

Do not confirm boundaries solely because organizational departments already exist.

---

# 51. Phase 5 — Bounded Context discovery

For each candidate context evaluate:

```text
language cohesion
model cohesion
business-rule cohesion
ownership
lifecycle ownership
rate of change
integration boundaries
conflicting meanings
source-of-truth responsibility
```

Create candidate records before confirming them.

Claude MUST actively search for:

- the same term with different meanings;
- different rules for seemingly similar objects;
- separate lifecycle ownership;
- translation already occurring;
- repeated integration friction;
- data being copied because semantics differ;
- one area being forced to understand another area's internal concepts.

These are potential context-boundary indicators.

---

# 52. Phase 6 — Boundary challenge

Every proposed bounded context MUST undergo a challenge.

For each context ask:

```text
Could this be merged with an adjacent context without creating semantic ambiguity?

Could this be split because two distinct models are being forced together?

Does this boundary exist because of the domain, or only because of current software?

Who owns the model?

Which words change meaning at the boundary?

Which invariants belong entirely inside the boundary?

What outside concepts must be translated?
```

Record the result.

---

# 53. Phase 7 — Context Map

Do not proceed deeply into tactical modeling until candidate bounded contexts and their major relationships have been mapped.

For every relationship identify:

```text
upstream
downstream
semantic dependency
DDD relationship pattern
integration mechanism if known
translation requirement
ownership
contract
```

Unknown relationships MUST be marked `unresolved`.

Claude MUST NOT fill unknowns with the most plausible DDD pattern.

---

# 54. Phase 8 — Ubiquitous Language stabilization

For each context:

1. collect terms;
2. define terms in that context;
3. identify synonyms;
4. identify ambiguous terms;
5. identify forbidden or misleading terminology;
6. connect terms to Domain Concepts;
7. identify terminology imported from another context.

Do not proceed as if the language is globally uniform.

---

# 55. Phase 9 — Domain Concept model

Within each bounded context identify:

```text
things with identity
descriptive/value concepts
business actions
business facts
rules
decisions
lifecycles
states
relationships
```

Do not assign tactical patterns until evidence supports them.

Start as Domain Concepts.

Then classify.

---

# 56. Phase 10 — Invariant discovery

Before aggregate design, identify business invariants.

This order is mandatory:

```text
business rules
    ↓
invariants
    ↓
consistency requirements
    ↓
aggregate boundaries
```

Do NOT use:

```text
database tables
    ↓
object graph
    ↓
aggregate
```

as the primary aggregate-discovery method.

---

# 57. Phase 11 — Aggregate design

For each set of invariants determine the smallest valid consistency boundary.

Identify:

```text
aggregate root
internal entities
value objects
commands
events
invariants
external aggregate references
```

Run the Aggregate test defined earlier.

---

# 58. Phase 12 — Behavior modeling

Identify Commands, Events, Policies, and Domain Services.

A recommended reasoning flow is:

```text
Who wants something to happen?
        ↓
Command
        ↓
Which Aggregate receives the intent?
        ↓
Which invariants must hold?
        ↓
What state changes?
        ↓
What domain-significant fact occurred?
        ↓
Domain Event
        ↓
Does another domain decision follow?
        ↓
Policy / subsequent Command
```

This is a discovery aid.

It does not override domain evidence.

---

# 59. Phase 13 — Repositories and factories

Only after aggregate boundaries are sufficiently stable:

- identify Repository abstractions;
- identify complex construction requiring Factories.

Do not design persistence infrastructure.

---

# 60. Phase 14 — Use cases and queries

Document application-level orchestration around the domain model.

Do not push domain behavior into Use Cases merely to simplify the Aggregate model.

---

# 61. Phase 15 — Integration validation

Review every cross-context interaction.

No direct cross-context model-object sharing is allowed unless an explicitly confirmed Shared Kernel justifies it.

Every interaction must be explainable through:

```text
context relationship
+
integration contract
```

---

# 62. Repository layout

Claude SHOULD create the following structure unless the repository already has an explicit equivalent location:

```text
/docs/ddd/
    README.md
    SPECIFICATION.md

    /domain/
        domain.yaml
        ubiquitous-language.yaml

    /subdomains/
        index.yaml
        <subdomain-id>.yaml

    /contexts/
        index.yaml

        /<context-id>/
            context.yaml
            language.yaml
            concepts.yaml
            aggregates.yaml
            entities.yaml
            value-objects.yaml
            invariants.yaml
            commands.yaml
            events.yaml
            policies.yaml
            domain-services.yaml
            repositories.yaml
            factories.yaml
            queries.yaml
            use-cases.yaml
            integrations.yaml

    /context-map/
        context-map.yaml
        relationships.yaml

    /discovery/
        evidence-index.yaml
        term-candidates.yaml
        capability-candidates.yaml
        context-candidates.yaml

    /decisions/
        DDD-0001-*.md
        DDD-0002-*.md

    /issues/
        unresolved.yaml

    /reports/
        validation-report.md
        model-summary.md
```

Do not create empty files solely to satisfy the directory structure.

Files should be created when corresponding content exists.

---

# 63. Human-readable README

`/docs/ddd/README.md` MUST explain:

```text
what the ManuFactor DDD model is
where its canonical files live
how the model is organized
how statuses work
how provenance works
how unresolved questions work
how validation is performed
how changes are proposed
```

It MUST explicitly state:

> ArchiMate is not the canonical DDD notation or metamodel for ManuFactor and must not be used to determine DDD semantics.

---

# 64. Modeling decisions

Significant model decisions MUST have decision records.

Examples:

```text
creating a bounded context
splitting a bounded context
merging contexts
declaring a Shared Kernel
changing aggregate boundaries
changing ownership of a concept
changing upstream/downstream direction
introducing a new model construct
deprecating major terminology
```

Decision record:

```markdown
# DDD-XXXX — Decision title

## Status

## Context

## Evidence

## Decision

## Alternatives Considered

## Consequences

## Affected Model Objects

## Unresolved Questions
```

---

# 65. Unresolved issues

Claude MUST maintain:

```text
/docs/ddd/issues/unresolved.yaml
```

Each issue:

```yaml
id:
title:
description:
affected_objects:
evidence:
possible_answers:
blocking:
needed_input:
status:
```

The presence of uncertainty is acceptable.

Inventing an answer to eliminate uncertainty is not acceptable.

---

# 66. Agent behavior rule

When Claude lacks sufficient evidence:

1. search existing project evidence;
2. search relevant existing DDD artifacts;
3. identify whether the answer can be logically derived;
4. if derived, label it as inference;
5. if not supported, create an unresolved issue.

Claude MUST NOT silently choose whichever answer makes the model cleaner.

---

# 67. Reasoning discipline for Claude Code

This section is deliberately explicit because the agent is expected to perform substantial autonomous work.

Claude MUST work from constrained steps rather than broad architectural intuition.

For each model decision use:

```text
1. Evidence
2. Observed domain fact
3. DDD concept that may represent it
4. Constraint from this specification
5. Candidate modeling decision
6. Counterexample/challenge
7. Final status:
      confirmed
      proposed
      unresolved
      rejected
```

Do not skip directly from source code to a confirmed DDD classification.

---

# 68. No architecture invention

Claude is modeling the domain.

Unless separately instructed, Claude MUST NOT:

- redesign application architecture;
- choose microservices;
- introduce message brokers;
- redesign databases;
- select frameworks;
- split repositories;
- add APIs;
- change source code;
- create deployment topology;
- introduce event sourcing;
- introduce CQRS;
- introduce a service mesh;
- introduce an ontology product;
- introduce ArchiMate;
- replace existing technologies.

DDD findings MAY later influence those decisions.

They are not part of this task.

---

# 69. Existing implementation must not bias the model improperly

Claude should inspect existing implementation because it contains domain knowledge.

However:

```text
class != Entity
table != Aggregate
project != Bounded Context
API != Open Host Service
event message != Domain Event
foreign key != domain relationship
service class != Domain Service
module != Subdomain
microservice != Bounded Context
```

Each mapping must be justified semantically.

---

# 70. Traceability to implementation

The model MAY record implementation references:

```yaml
implementation_refs:
  - type: class
    location:
  - type: table
    location:
  - type: api
    location:
```

These references are traceability data only.

They MUST NOT redefine the domain object.

---

# 71. Canonical relationships

Relationships SHOULD use controlled semantic types wherever possible.

Examples:

```text
belongs-to
contains
references
identifies
produces-event
handles-command
enforces-invariant
uses-value-object
invokes-domain-service
governed-by-policy
exposed-through
translated-to
correlates-with
upstream-of
downstream-of
```

Claude MUST NOT generate hundreds of arbitrary relationship names.

If a relationship cannot be represented by the controlled vocabulary, document why before extending the vocabulary.

---

# 72. Cardinality

Relationships SHOULD record cardinality when domain-significant:

```text
one
zero-or-one
one-or-more
zero-or-more
```

Do not infer cardinality from current database schema without confirming that it represents a domain rule.

---

# 73. Lifecycle

Entities and Aggregates SHOULD explicitly define lifecycle where lifecycle affects domain behavior:

```text
creation
active states
transitions
terminal states
correction
cancellation
replacement
archival if domain-significant
```

Lifecycle state is domain state only when the business cares about the distinction.

---

# 74. State transitions

Where state transitions are significant, model them explicitly.

Example structure:

```yaml
states:
  - candidate
  - active
  - completed
transitions:
  - from:
    to:
    command:
    preconditions:
    resulting_events:
```

Do not create state machines for objects with no meaningful lifecycle.

---

# 75. Temporal semantics

Claude MUST distinguish:

```text
current state
historical fact
effective state
recorded state
correction
supersession
```

when the domain makes these distinctions.

Do not overwrite historical semantics merely because implementation currently overwrites rows.

---

# 76. Corrections

When the domain allows correction of previously recorded information, determine whether correction means:

```text
mutation of current state
new domain fact
supersession
reversal
replacement
administrative fix
```

This must be modeled semantically.

Do not decide correction behavior based solely on persistence convenience.

---

# 77. Domain events versus audit history

Audit records and Domain Events are different concepts.

A Domain Event exists because the domain considers the occurrence meaningful.

Audit data exists because change history is being recorded.

One may produce the other.

They are not automatically equivalent.

---

# 78. Validation gates

The DDD model MUST pass the following gates.

---

# 79. Gate A — Domain completeness

Verify:

```text
Domain Vision exists.
Scope exists.
Out-of-scope exists.
Major subdomains are represented.
Each subdomain has classification.
Major domain terminology is represented.
```

Failure means strategic modeling is incomplete.

---

# 80. Gate B — Bounded Context validity

For every Bounded Context verify:

```text
Purpose is explicit.
Scope is explicit.
Out-of-scope is explicit.
Owner is known or explicitly unresolved.
Model statement exists.
Language exists.
Major concepts are owned.
Boundary can be distinguished from adjacent contexts.
```

A context that merely corresponds to an existing software module fails this gate unless semantic justification exists.

---

# 81. Gate C — Context Map validity

Verify every meaningful context interaction has:

```text
relationship record
direction where applicable
relationship type
integration contract or unresolved marker
translation decision
provenance
```

No implicit cross-context dependencies should remain hidden.

---

# 82. Gate D — Language validity

Check:

```text
overloaded terms
undefined important terms
same term with conflicting definitions
different terms for same concept
foreign-context terminology leaking into local model
```

Conflicts do not have to be eliminated.

They must be explicit.

---

# 83. Gate E — Aggregate validity

For every confirmed Aggregate verify:

```text
Aggregate Root exists.
Invariant rationale exists.
Transaction/consistency boundary is stated.
Members are explicit.
External references are explicit.
Aggregate is not merely a data hierarchy.
Aggregate does not span contexts.
```

If no meaningful invariant justifies the aggregate, challenge or remove it.

---

# 84. Gate F — Entity/Value Object validity

For every Entity:

```text
identity semantics must be explicit
```

For every Value Object:

```text
value/equality semantics must be explicit
```

Objects lacking either justification remain Domain Concepts until resolved.

---

# 85. Gate G — Behavior validity

Check:

```text
Commands represent intent.
Events represent facts.
Policies represent decisions.
Domain Services contain domain behavior.
Use Cases contain orchestration.
```

Reject procedural “service soup.”

---

# 86. Gate H — Boundary leakage

Search for:

```text
cross-context entity sharing
cross-context aggregate sharing
foreign domain objects imported directly
database tables treated as shared model
shared DTOs treated as domain model
one context mutating another context's Aggregate
```

Every occurrence must be corrected or explicitly justified through an allowed DDD relationship.

---

# 87. Gate I — Provenance

Every major confirmed object must have evidence.

Low-confidence confirmed objects must be challenged.

Inference must remain marked as inference.

---

# 88. Gate J — Unresolved questions

All known uncertainty must appear in the issue registry.

Claude MUST NOT declare the model complete while known blocking issues remain undocumented.

---

# 89. Model validation report

Claude MUST generate:

```text
/docs/ddd/reports/validation-report.md
```

The report must contain:

```text
Model version
Date
Files evaluated
Objects evaluated

Gate A result
Gate B result
Gate C result
Gate D result
Gate E result
Gate F result
Gate G result
Gate H result
Gate I result
Gate J result

Errors
Warnings
Unresolved questions
Recommended next modeling work
```

Use:

```text
PASS
PASS WITH WARNINGS
FAIL
```

Do not use vague statements such as “looks good.”

---

# 90. Completion states

The ManuFactor model progresses through:

```text
Discovery
Strategic Candidate
Strategic Stable
Tactical Candidate
Tactical Stable
Validated
Maintained
```

These states are model maturity states, not object statuses.

---

# 91. Strategic Stable definition

Strategic DDD is considered stable when:

```text
major subdomains are established
major bounded contexts are established
each context has purpose and scope
major terminology is contextualized
context ownership is established or explicitly unresolved
context relationships are mapped
major integrations are understood
blocking boundary ambiguity has been resolved
```

Tactical modeling MAY begin before this point experimentally.

Large-scale tactical confirmation SHOULD wait until this state.

---

# 92. Tactical Stable definition

A bounded context is tactically stable when:

```text
major concepts are represented
important invariants are represented
aggregate boundaries are justified
aggregate roots are defined
major entities/value objects are classified
major commands are represented
major domain events are represented
important policies/services are represented
repositories are aligned to aggregate roots
major use cases are traceable
```

This does not mean every implementation detail has been modeled.

---

# 93. Validated definition

The model reaches `Validated` when:

1. all validation gates have run;
2. no structural FAIL exists;
3. known uncertainty is recorded;
4. no ArchiMate dependency exists;
5. model files are internally consistent;
6. cross-references resolve;
7. stable IDs are unique;
8. terminology references resolve;
9. ownership conflicts are either resolved or documented;
10. a validation report exists.

---

# 94. Machine validation

Claude SHOULD build a lightweight validation mechanism if doing so does not require changing application architecture.

The validator SHOULD check mechanically detectable rules including:

```text
duplicate IDs
missing required fields
invalid status values
unknown context references
unknown aggregate references
aggregate without root
multiple roots
entity assigned to multiple aggregates
aggregate spanning contexts
context relationship referencing missing contexts
integration contract referencing missing contexts
invalid relationship type
event referencing missing aggregate
repository referencing non-root entity
unresolved references
```

Semantic rules still require model review.

A validator cannot determine whether an aggregate invariant is truly correct.

---

# 95. Model change procedure

When modifying an established model:

1. identify affected canonical objects;
2. identify evidence causing the change;
3. identify downstream model dependencies;
4. record a decision if the change is significant;
5. update canonical files;
6. update affected cross-references;
7. rerun validation;
8. update validation report.

Do not patch one file and knowingly leave conflicting model artifacts elsewhere.

---

# 96. Deletion

Do not silently delete confirmed modeling concepts.

If a concept is no longer valid:

```text
mark deprecated or rejected
record rationale
record replacement if any
preserve decision history
```

Physical removal MAY happen later according to repository maintenance policy.

---

# 97. Modeling anti-patterns

Claude MUST actively avoid the following.

## 97.1 Noun-to-Entity mapping

Wrong:

```text
I found a noun, therefore it is an Entity.
```

## 97.2 Table-to-Aggregate mapping

Wrong:

```text
These tables have foreign keys, therefore they are one Aggregate.
```

## 97.3 Project-to-Context mapping

Wrong:

```text
This is a .NET project, therefore it is a Bounded Context.
```

## 97.4 CRUD-driven modeling

Wrong:

```text
Create/Read/Update/Delete define the domain behavior.
```

DDD behavior should describe business intent.

## 97.5 God Aggregate

Do not put a large connected object graph into one Aggregate merely to guarantee consistency.

## 97.6 Anemic model by default

Do not place all business behavior into services while Entities become property bags.

## 97.7 Service explosion

Do not create Domain Services for every operation.

## 97.8 Event explosion

Not every state change is a Domain Event.

## 97.9 Global canonical object model

Do not force every bounded context to consume one universal object representation.

Shared identity does not require shared semantics.

## 97.10 Architecture-first modeling

Do not decide deployment boundaries and then retrofit DDD terminology onto them.

## 97.11 ArchiMate leakage

Do not introduce ArchiMate concepts to solve gaps in the DDD model.

If this specification lacks a needed DDD construct, document the gap and make an explicit ManuFactor DDD metamodel decision.

---

# 98. Handling gaps in this specification

If Claude encounters a domain concept that cannot be represented cleanly:

1. do not use ArchiMate to fill the gap;
2. do not silently overload an existing DDD construct;
3. create an unresolved modeling issue;
4. describe the missing semantic requirement;
5. determine whether an existing DDD concept already covers it;
6. if not, propose a ManuFactor extension;
7. record the extension as a DDD decision;
8. add validation rules for it.

Extensions must remain domain-model constructs, not architecture-notation constructs.

---

# 99. Output discipline

Claude must distinguish three categories of output.

## Canonical

Authoritative model data:

```text
domain
subdomains
bounded contexts
language
concepts
aggregates
entities
value objects
invariants
commands
events
policies
services
repositories
factories
use cases
integration contracts
context map
```

## Supporting

```text
evidence
decisions
issues
validation
discovery notes
```

## Projection

Non-authoritative generated views:

```text
Markdown summaries
Mermaid diagrams
Graphviz
C4
UML
ArchiMate
database mapping
source-code mapping
API mapping
knowledge graph
```

Projection data MUST be regenerable from canonical data where practical.

---

# 100. Diagrams

DDD diagrams MAY be generated for comprehension.

A diagram is never canonical merely because it is easier to read.

Prefer diagrams generated from canonical structured data.

A diagram MUST NOT contain semantic information absent from the canonical model.

Mermaid or Graphviz MAY be used without making either one the model authority.

---

# 101. Context Map diagram

A generated Context Map SHOULD show:

```text
Bounded Context name
upstream/downstream direction
DDD relationship type
major integration contract
ACL or Published Language where applicable
```

Do not display tactical internal objects on the strategic Context Map unless creating a separately labeled detailed view.

---

# 102. Aggregate diagram

Aggregate views MAY show:

```text
Aggregate Root
Entities
Value Objects
Invariants
Commands
Events
references to other Aggregates by identity
```

They MUST show the Aggregate boundary clearly.

---

# 103. No false precision

Claude must not produce detailed tactical structures solely because the specification has fields for them.

An unresolved field is better than a fabricated one.

For example:

```yaml
aggregate_root: unresolved
```

with an issue reference is preferable to inventing an Aggregate Root.

---

# 104. Working procedure for Claude Code

When instructed to "build the ManuFactor DDD model," execute the following sequence.

```text
STEP 1
Read this specification completely.

STEP 2
Locate any existing DDD artifacts.

STEP 3
Inventory relevant source material.

STEP 4
Build or update the evidence index.

STEP 5
Extract domain vocabulary.

STEP 6
Extract business capabilities.

STEP 7
Propose subdomains.

STEP 8
Challenge subdomain boundaries.

STEP 9
Propose bounded contexts.

STEP 10
Challenge bounded-context boundaries.

STEP 11
Build the candidate Context Map.

STEP 12
Identify unresolved strategic questions.

STEP 13
Stabilize contextual Ubiquitous Language.

STEP 14
Identify Domain Concepts within each context.

STEP 15
Identify business rules and invariants.

STEP 16
Derive candidate Aggregates from invariants.

STEP 17
Identify Aggregate Roots.

STEP 18
Classify Entities and Value Objects.

STEP 19
Identify Commands.

STEP 20
Identify Domain Events.

STEP 21
Identify Policies and Domain Services.

STEP 22
Identify Repositories and Factories.

STEP 23
Identify Queries and Use Cases.

STEP 24
Model Integration Contracts.

STEP 25
Validate all context boundaries.

STEP 26
Validate all Aggregate boundaries.

STEP 27
Validate provenance.

STEP 28
Record unresolved questions.

STEP 29
Run mechanical validation.

STEP 30
Produce validation report.

STEP 31
Produce human-readable model summary.
```

Claude MUST NOT skip from Step 3 directly to Step 16.

---

# 105. Work incrementally

For a large domain, Claude SHOULD work context-by-context after strategic stabilization.

Preferred sequence:

```text
establish whole-domain strategic map

then:

Context A
    discover
    model
    validate

Context B
    discover
    model
    validate

Context C
    discover
    model
    validate

then:

cross-context validation
```

Do not attempt to perfectly model the entire ManuFactor tactical domain in one undifferentiated pass.

---

# 106. Agent checkpoints

At meaningful checkpoints Claude SHOULD summarize:

```text
What was discovered
What was confirmed
What changed
What remains candidate
What is unresolved
What evidence was used
What validation failures exist
```

Do not merely report files changed.

---

# 107. When to stop and request domain input

Claude should continue autonomously when evidence can resolve a question.

Claude SHOULD request domain input when a decision materially affects:

```text
bounded-context boundaries
business terminology
ownership
business invariants
identity semantics
lifecycle semantics
upstream/downstream authority
meaning of a business event
core/supporting/generic classification
```

and available evidence cannot determine the answer.

Before asking, Claude must state exactly:

```text
the unresolved question
why it matters to the DDD model
the competing interpretations
which model objects would be affected
what evidence has already been checked
```

Do not ask broad questions such as:

```text
How should this work?
```

Ask one precise domain question.

---

# 108. Candidate versus confirmed modeling

Claude MUST be conservative about `confirmed`.

Use:

```text
candidate
```

when discovered but not sufficiently evaluated.

Use:

```text
proposed
```

when the modeler has a supported recommendation.

Use:

```text
confirmed
```

only when evidence and model constraints justify treating it as authoritative.

---

# 109. Model summaries

`model-summary.md` SHOULD provide a human-readable view containing:

```text
Domain Vision
Subdomains
Core Domain
Bounded Contexts
Context Map
Key language
Major Aggregates per context
Major Commands
Major Events
Major integration relationships
Important unresolved questions
Validation status
```

The summary is derived from the canonical model.

It is not itself canonical.

---

# 110. Completion criterion

Claude must not claim:

> The ManuFactor DDD is complete.

merely because files have been generated.

Claude may state that a model iteration is complete only when it can report:

```text
Strategic model state:
Tactical model state by context:
Validation status:
Known unresolved strategic issues:
Known unresolved tactical issues:
Evidence coverage:
Boundary validation:
Aggregate validation:
```

A DDD model can remain valid while containing explicitly unresolved questions.

It cannot be considered valid when uncertainty has been hidden.

---

# 111. Final quality test

Before treating a ManuFactor DDD model as authoritative, Claude must be able to answer all of the following from the canonical model without consulting source code:

### Domain

What business domain is being modeled?

### Strategic structure

What are the subdomains?

Which subdomains are core, supporting, and generic?

### Context boundaries

What are the bounded contexts?

Why does each boundary exist?

What model is authoritative within each context?

### Language

What does each important term mean within its context?

Where does terminology change meaning?

### Context relationships

Which contexts depend on which others?

Who is upstream?

Who is downstream?

Which DDD relationship pattern applies?

Where does translation occur?

### Tactical structure

What are the principal Aggregates?

Why is each an Aggregate?

What are their roots?

What invariants define their consistency boundaries?

### Identity

Which objects are Entities and why?

### Values

Which objects are Value Objects and why?

### Behavior

What Commands express important business intent?

What Domain Events represent meaningful facts?

What Policies encode business decisions?

What Domain Services contain domain behavior that cannot naturally belong elsewhere?

### Persistence abstraction

Which Aggregates have Repositories?

### Integration

What semantic contract exists at every significant cross-context boundary?

### Uncertainty

What remains unresolved?

### Provenance

Why does the model believe each major assertion is true?

If these questions cannot be answered, the DDD model is incomplete.

---

# 112. Governing principle

The governing principle for ManuFactor DDD is:

> The canonical ManuFactor DDD model describes domain meaning, ownership, boundaries, behavior, rules, identity, consistency, and relationships using DDD semantics directly.

It does not require ArchiMate, UML, C4, a database schema, source-code structure, or another external modeling notation to define what its elements mean.

Those technologies may represent, implement, visualize, or project the DDD model.

They do not govern it.

---

# 113. Instruction to Claude Code

When this document is supplied as governing instructions, Claude Code must treat it as the modeling contract.

Do not reinterpret it as a request for general DDD recommendations.

Do not redesign the specification during ordinary modeling work.

Do not substitute another framework.

Do not introduce ArchiMate.

Do not optimize the model for the current source-code structure.

Do not fabricate missing domain knowledge.

Discover evidence, construct the model under these constraints, validate it, expose uncertainty, and maintain traceability.

The expected outcome is not a diagram.

The expected outcome is a **bounded, explicit, machine-readable, validated ManuFactor Domain-Driven Design model** from which diagrams, implementation guidance, architecture views, and other projections can subsequently be derived.