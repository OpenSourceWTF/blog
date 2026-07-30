---
title: "The Vibengineer Compendium, Part II: Establish Ownership and Dominance Before Your Code Goes Out of Control"
series: "The Vibengineer Compendium"
part: 2
status: outline
draft: true
---

**Working title:** The Vibengineer Compendium, Part II: Establish Ownership and Dominance Before Your Code Goes Out of Control

**Scope:** “Dominance” here means code dominance—canonical authority among modules, contracts, and representations. Never people or teams.

## Thesis

- Working claim to validate: agent-assisted changes can multiply plausible implementations because they produce locally reasonable answers quickly without necessarily preserving one global shape.
- The point is not fewer implementations. It is one obvious place that owns each important decision.
- Define **dominance** narrowly: canonical authority between modules, contracts, and representations. It never applies to people or teams.

## Reader promise

- By the end: a practical way to spot competing authority, choose the authoritative home, and keep a repository navigable while agents are changing it.
- Keep this at architecture level: direct, a little rough, and usable in a real codebase.

## Argument

- Start with the failure mode: two agents add two plausible sources of truth, adapters, schemas, or UI states; both work until they diverge.
- Ownership: every policy, contract, and representation needs one authoritative home. What are the signals that a thing has no owner, or several?
- Dominance: how should a canonical module or contract win over derived copies and competing representations?
- Dependency direction: make dependencies point toward stable authority. Where should callers depend on an interface rather than reach sideways into implementation details?
- Narrow interfaces: what is the smallest contract that lets a module be reused without exposing its guts?
- Locality: put behavior near the data and decisions it changes. When does centralization make locality worse?
- Controlled duplication: duplicate a small, stable thing on purpose when coupling costs more; record the boundary and review trigger.
- Deletion paths: every new abstraction needs a clear way to remove, replace, or collapse it. What would make this safe?
- Architectural tests: encode a few structural rules so drift is caught before a plausible patch becomes permanent.
- Repository maps: maintain lightweight maps for authoritative homes, dependency direction, and intentionally duplicated seams.
- Bridge to Part III: once the shape is clear, use its `Plan, Expand, Analyze, Reduce` code-health loop to propose, test, land, and simplify safe architecture changes.

## Candidate examples and diagrams

- A feature flag or validation rule implemented in a UI component, API handler, and shared package: which layer is canonical and which copies are derived?
- An API schema versus an internal type versus a generated client: show the authoritative contract and allowed dependency direction.
- Diagram: competing authority — two modules both write/read the same policy, with divergent arrows and an ambiguous source of truth.
- Diagram: dominant dependency direction — consumers depend inward on one canonical contract; derived representations flow outward.
- Before/after repository-map snippet: owner, consumers, interfaces, deliberate duplicates, and deletion path.

## Claims requiring evidence

- What research or incident evidence supports the claim that agent-assisted work increases parallel, plausible implementations rather than simply speeding delivery?
- What evidence shows that one canonical contract reduces integration defects or time-to-change? Note sources, scope, and counterexamples.
- Which architectural-test techniques have credible evidence for catching forbidden dependencies or authority drift in practice?
- What evidence distinguishes healthy controlled duplication from copy-paste entropy?

## Non-goals

- Not a hierarchy for people, teams, or who gets to decide; dominance is only about modules, contracts, and representations.
- Not a demand for a single giant shared package or zero duplication.
- Not a claim that a repository map replaces code review, tests, or product judgment.
- Not a full change-management process; hand off to Part III's `Plan, Expand, Analyze, Reduce` code-health loop.

## Series connection

- Part 1 set up the vibengineer problem: fast changes, unclear boundaries, and confidence outrunning understanding.
- Part 2 gives the structural guardrails: ownership, canonical authority, and dependency shape.
- Part III's `Plan, Expand, Analyze, Reduce` code-health loop picks up here: map the current authority, make the smallest reversible change, test the contracts, and leave the map better than found.

## Open drafting questions

- Which running example is small enough to stay concrete but rich enough to show competing authority?
- What is the earliest concrete example that makes the approved code-only meaning of “dominance” impossible to miss?
- What minimum repository-map format will readers actually keep current?
- Which architectural tests are language-neutral enough for this compendium, and which need sidebars?
- How explicit should the handoff checklist be before it starts duplicating Part III's `Plan, Expand, Analyze, Reduce` code-health loop?
