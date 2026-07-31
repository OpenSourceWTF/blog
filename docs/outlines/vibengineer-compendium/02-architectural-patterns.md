---
title: "The Vibengineer Compendium, Part II: Establish Ownership and Dominance Before Your Code Goes Out of Control"
series: "The Vibengineer Compendium"
part: 2
status: developed-outline
draft: true
---

# The Vibengineer Compendium, Part II: Establish Ownership and Dominance Before Your Code Goes Out of Control

## Problem

Coding agents are very good at making a local patch plausible.

They are much less reliable at preserving the global shape of a repository while
they do it. Ask two agents to add the same validation rule from two different
entry points and you can easily get two good-looking answers in two reasonable
places. Both work. Neither knows which one owns the decision. Six changes later,
they disagree.

The architectural failure is not simply duplication. It is **competing
authority**: the repository no longer has one legible answer to “where does this
decision live?”

## Thesis

> Before you let agents increase the rate of change, make authority in the code
> legible. Within a declared semantic boundary, every important decision needs
> one canonical home; dependencies should point toward that authority; derived
> representations must identify their source; intentional duplicates need a
> boundary and review trigger; and the most important rules should be
> mechanically enforced.

This is falsifiable. If explicit authority and enforceable dependency direction
do not reduce wrong-layer edits, competing implementations, review ambiguity,
or the cost of a later change, the prescription is ceremony rather than control.

**Dominance means dominance among modules, contracts, and representations. It
never means dominance over people or teams.**

## Reader promise

By the end, the reader should be able to:

- identify competing sources of authority;
- decide what kind of thing is actually being owned;
- choose a canonical home without building a giant shared-code landfill;
- label copies as generated, cached, translated, temporary, or independent;
- pick guardrails proportionate to the repository;
- leave enough of a map that the next human or agent knows where to change it.

## Running example: the validation rule that reproduced

A user-name rule begins in a web form. An agent adds it to the API because the
API cannot trust the browser. A generated client exposes its own type. A worker
needs the same check and copies the regular expression. All four choices are
locally defensible.

Use this example throughout:

1. The UI has presentation hints, not authority over validity.
2. The domain policy module owns the semantic rule.
3. The public contract expresses the externally observable constraint.
4. The API and worker independently enforce it at their trust boundaries by
   calling the owned policy or a validator generated from the contract.
5. Conformance tests prove those required enforcement points agree with the
   semantic rule; they are not automatically competing authorities just because
   validation runs in more than one place.
6. Clients derive types or validators from the contract where practical.
7. A service with a genuinely different meaning gets its own bounded model and
   an explicit translation at the boundary.
8. A temporary duplicate created during migration has an owner, parity check,
   and deletion condition.

This is richer than a feature flag but still fits on one whiteboard.

## Proposed argument

### 1. Your repository is not a democracy between implementations

- Open with two plausible patches that both pass their local tests.
- Show how ambiguity compounds: the next agent greps, finds both, and either
  chooses arbitrarily or creates a third adapter.
- Name the failure: competing authority.
- Define code dominance before the word can be mistaken for team hierarchy.

### 2. First decide what kind of authority you are assigning

Not every “source of truth” is the same. Separate:

- **Policy authority:** business rules and invariants.
- **Contract authority:** schemas, protocols, public interfaces, and events.
- **State authority:** the system allowed to accept writes for a datum.
- **Representation authority:** the canonical model from which generated,
  cached, serialized, or presentation forms are derived.
- **Workflow authority:** the component that owns a state transition.

The phrase “single source of truth” is useless until the reader names which of
these is meant.

### 3. Give each decision one canonical home, not one canonical universe

- One decision should have one home.
- A large system can and should have multiple canonical models separated by
  explicit bounded contexts.
- Keep behavior near the data and invariant it changes.
- Do not answer duplication by putting every type and helper into `shared/`.
- The test is whether a new person or agent can predict where a change belongs.

### 4. Make dependency direction reveal the authority

- Consumers depend on the stable contract, not sideways on another consumer.
- Public interfaces should be narrow enough to hide internal choices.
- Dependencies should point inward toward the owned rule; generated and
  presentation forms flow outward.
- Cycles are often a structural confession that ownership is unresolved.

### 5. Label every second representation

Every non-canonical representation should answer:

- What is it: generated output, cache, materialized view, translation,
  compatibility shim, fork, or accidental copy?
- What is its source?
- How does it stay in sync?
- Can it accept writes?
- What proves parity?
- What event causes review or deletion?

“DRY” is not enough. Some duplication is cheaper than coupling. Unlabelled
duplication is the dangerous kind.

### 6. Migrations temporarily break the rule, so time-box the exception

- Dual reads and dual writes can create temporary shared authority.
- Record the old owner, new owner, reconciliation mechanism, cutoff condition,
  and deletion path.
- Treat permanent “temporary” adapters as an architectural smell.
- A migration is complete when the old authority can be removed, not merely
  when the new path works.

### 7. Write the smallest useful authority map

Keep one human-readable map in the repository:

```md
## User-name validity

- Owns: domain/user-name-policy
- Public contract: api/schemas/user-name.json
- Consumers: web signup, account API, import worker
- Derived: generated/client/user-name.ts (do not edit)
- Deliberate duplicate: legacy/import-name-check (remove after importer v3)
- Allowed direction: consumers -> contract -> policy
- Guardrail: architecture/import-boundaries.test
```

This follows Part I’s memory recommendation: one readable source for humans and
agents, linked to code and checks. Dependency graphs and indexes may be generated
from it or from the repository, but are not a separately edited truth.

### 8. Turn the expensive rules into cheap failures

Choose the smallest enforcement that catches the likely drift:

| Situation | Minimum useful control | Add when the failure becomes expensive |
|---|---|---|
| Small application | module names, local README, ordinary tests | a focused forbidden-import test |
| Growing monorepo | public package APIs and dependency direction | tag or graph-based boundary checks in CI |
| Contract-driven clients | one readable schema and generated outputs | generation/parity check that fails on drift |
| Multiple services | explicit context and contract owners | compatibility tests and versioned contracts |
| Replicated data | label writer, cache, and materialized views | freshness, provenance, and reconciliation checks |
| Active migration | owner, deadline, parity check, deletion issue | block new consumers of the old path |

The table is a recommendation ladder, not a framework ranking.

### 9. Review the architecture by asking where the next change goes

The closing review should make the reader test their own repository:

- If the rule changes tomorrow, which file changes first?
- Can two modules accept conflicting writes?
- Which copies are derived and how can an agent tell?
- Where is translation intentional rather than accidental?
- Does dependency direction expose or obscure the owner?
- What duplicated code is buying useful independence?
- What “temporary” path has no deletion condition?
- Which rule is costly enough to enforce mechanically?
- Could a fresh agent find the canonical home with grep and linked docs?
- If the answer is “it depends,” is the boundary explicit or merely confused?

Restate the thesis: agents do not create the need for architecture, but they
increase the rate at which architectural ambiguity becomes code. Legible
authority lets speed compound into a system instead of a pile.

## Conditional recommendations

### If you have one application

Prefer obvious module boundaries, a short authority map, narrow public entry
points, and one or two structural tests. Do not introduce a platform team’s
architecture into a repository that fits in one person’s head.

### If you have a monorepo

Prefer public package APIs, explicit dependency direction, boundary tags where
they are maintained, and CI checks for forbidden edges and cycles. Generate the
dependency graph; do not maintain a second diagram by hand.

### If contracts generate code

Prefer one readable contract as authority and mark generated artifacts loudly.
Make regeneration reproducible and fail CI when committed outputs drift.

### If you have multiple services or domains

Prefer bounded canonical models and explicit translations over one global
“shared” model. Version the contracts crossing boundaries. Similar words do not
necessarily mean identical concepts.

### If data is replicated

Name the write authority and label every cache, projection, and materialized
view with provenance and freshness behavior. “Source of truth” without write
semantics is hand-waving.

### If you are migrating authority

Allow temporary duality only with reconciliation, observability, a cutoff
condition, and a deletion path. Prevent new consumers from attaching to the old
owner.

## Candidate illustrations

Do not generate until the argument and captions are approved.

1. **Hero — The Repository Has Three Mayors:** the Vibengineer and Agent face
   three modules, each wearing a sash reading “SOURCE OF TRUTH,” while the same
   validation rule differs on three Post-its.
2. **Authority and derivation:** one canonical policy card in the center;
   generated client, UI hint, API enforcement, and cache flow outward. The Agent
   uses its pencil to label the arrows.
3. **The shared-code junk drawer:** the Agent tries to solve duplication by
   stuffing unrelated types into an overflowing `shared/` drawer; the
   Vibengineer points instead to two bounded cabinets with a small translation
   bridge.
4. **Migration with an exit:** old and new owners connected by a temporary
   bridge whose Post-it names parity, cutoff, and deletion. The bridge visibly
   has a demolition date.

Use the established whiteboard/sketch style and existing character model sheets.

## Claims requiring evidence

- Agent-generated functional correctness does not imply maintainability under
  later requirement changes.
- Fast local generation can increase volume, coupling, or structural defects.
- Clear responsibility boundaries and lower coupling improve dynamic
  maintainability.
- Bounded contexts permit multiple canonical models without ambiguous authority.
- Architecture tests can detect forbidden dependencies, cycles, and access
  outside public interfaces.
- Generated contracts and parity checks reduce representation drift.
- Controlled duplication can be preferable to inappropriate coupling; the
  article must state the conditions rather than repeat DRY as dogma.

## Non-goals

- No people or team dominance.
- No universal folder structure.
- No demand for one model across an entire distributed system.
- No giant `shared`, `common`, or `utils` package as a substitute for ownership.
- No zero-duplication absolutism.
- No claim that a repository map replaces tests, review, or judgment.
- No catalogue of every architecture framework.
- No full refactoring workflow; Part III handles Plan, Expand, Analyze, Reduce.

## Series connection

- Part I established the minimum process required to preserve intent and demand
  evidence.
- Part II establishes the code shape that process is trying to preserve.
- Part III will use Plan, Expand, Analyze, Reduce to repair or extend that shape
  without letting exploration become permanent complexity.
