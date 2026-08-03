# Part II Research Proposal: Ownership and Dominance

## Research objective

Test whether making code authority explicit helps developers direct and review
coding agents as they place changes, avoid competing implementations, and adapt
the system later with less rework.

The end product is a conditional architecture guide. It should not prescribe one
folder structure or sell one tool. It should answer:

> Given the kind of repository and authority problem I have, what is the
> smallest structural rule that will make the correct place for a change
> obvious—and what should enforce it?

## Problem statement

Coding agents optimize for a plausible solution to the task in front of them.
A repository must also preserve decisions across tasks. When policy, contracts,
state, or representations have no obvious owner, multiple locally correct
patches create competing authority. Later agents inherit the ambiguity and
compound it.

## Working thesis

> Before increasing the rate of agent-assisted change, make authority in the
> code legible. Give each important decision one canonical home, direct
> dependencies toward it, identify every derived or duplicated representation,
> and mechanically enforce the few boundaries whose violation is expensive.

The thesis must be narrowed, qualified, or rejected if research shows that these
controls do not improve change placement, reduce drift, or lower later rework.

## Primary research question

In agent-assisted development, does explicit, inspectable, and selectively
enforced code authority reduce wrong-layer edits, competing sources of truth,
integration ambiguity, human correction, and later maintenance effort?

## Supporting questions

1. Which things can meaningfully have authority: policies, contracts, state,
   representations, and workflows?
2. When does one canonical model help, and when do bounded contexts require
   several canonical models?
3. What repository signals let a fresh human or agent predict the correct place
   for a change?
4. Does dependency direction reliably communicate ownership?
5. When should a consumer depend on a public interface rather than another
   consumer or an implementation detail?
6. When is duplication an intentional boundary rather than unresolved
   authority?
7. What metadata makes a generated file, cache, projection, translation, or
   compatibility shim safe?
8. How should authority temporarily move during migrations?
9. Which architecture constraints can be tested without locking a codebase into
   a brittle idealized diagram?
10. What is the smallest human-readable authority map that people will keep
    current?
11. Can the map link to executable checks and code so it remains one readable
    source instead of a second architecture?
12. Which recommendations change for a small app, monorepo, contract-generated
    client, distributed system, or replicated-data system?

## Research streams

### Stream A: Architecture literature review

Study primary and canonical sources for:

- information hiding and design decisions;
- coupling, cohesion, and changeability;
- dependency inversion and stable boundaries;
- domain-driven bounded contexts and context maps;
- canonical data models and the limits of global models;
- deliberate duplication versus inappropriate coupling;
- branch-by-abstraction and authority migration.

For every source, record:

- the claim actually made;
- the system scale and assumptions;
- whether the evidence is empirical, formal, experiential, or rhetorical;
- counterexamples or limits;
- how cautiously the claim can be applied to agent-assisted work.

Do not retroactively label classic architecture literature as evidence about
LLMs. Use it to define mechanisms; use agent research to test the new context.

### Stream B: Agent-generated maintainability evidence

Review current primary research on:

- maintainability of LLM- and agent-generated code;
- behavior under evolving requirements rather than one-shot tests;
- code volume, coupling, cohesion, duplication, and structural defects;
- repository-level localization and structural comprehension;
- the difference between functional correctness and dynamic maintainability.

Prioritize replicated or peer-reviewed work. Treat new preprints as provisional.
Record benchmark construction, model versions, repository realism, baselines,
metrics, and threats to validity.

Initial leads:

- MaintainCoder and MaintainBench for requirement-change evaluation;
- empirical audits of AI-generated code and architectural smells;
- repository-representation studies for agent localization;
- studies comparing agent and human maintainability.

### Stream C: Enforcement mechanism review

Study official documentation and representative implementations for:

- forbidden-import and public-interface lint rules;
- dependency graph, layer, and cycle tests;
- language-specific architecture tests such as ArchUnit;
- monorepo boundary rules such as Nx module constraints;
- schema generation and committed-output drift checks;
- API and event contract compatibility tests;
- migration checks preventing new dependencies on a legacy owner.

The article should teach the mechanism in language-neutral terms, then use tools
only as concrete examples.

For each mechanism, record:

- what structural fact it observes;
- which violations it can block;
- what it cannot infer;
- setup and maintenance cost;
- escape-hatch behavior;
- likelihood that stale configuration creates false confidence.

### Stream D: Repository case studies

Select three to five mature open repositories that visibly encode different
authority models:

- a modular application with enforced package boundaries;
- a monorepo with public package APIs and dependency constraints;
- a contract-first project with generated clients;
- a service system with versioned API or event contracts;
- a project performing a documented authority migration.

Trace one real decision through each:

- canonical owner;
- consumers;
- derived representations;
- allowed dependency direction;
- checks;
- exceptions;
- deletion or migration path.

Avoid cherry-picking only clean examples. Include one failure or postmortem where
ambiguous ownership materially contributed to drift.

### Stream E: Matched agent experiment

Use one small repository containing a web entry point, API, worker, shared
contract, and generated client. The same developer gives agents the same
cross-cutting validation change under four conditions:

1. Existing code only
2. A prose architecture description
3. A linked authority map
4. The authority map plus focused boundary and parity checks

After the initial change, introduce a requirement revision in a fresh session.

Record:

- first file inspected;
- files changed;
- wrong-layer or sideways dependencies;
- competing validations or representations introduced;
- whether generated code was edited directly;
- acceptance and architecture-test results;
- human corrections and review time;
- whether the developer can explain and verify the agent's placement from the
  available authority artifacts;
- ability to name the canonical owner;
- change size and later rework;
- token and elapsed-time cost;
- whether the process created stale documentation.

This is an exploratory mechanism test, not a universal productivity benchmark.
Publish prompts, repository fixture, expected authority, and scoring rubric.

### Stream F: Recommendation ladder

Develop recommendations by situation rather than framework:

| User situation | Initial recommendation hypothesis | Main cost to test |
|---|---|---|
| Small application | Obvious modules, short linked authority map, focused import test | Documentation may cost more than discovery |
| Growing monorepo | Public package APIs, directed dependencies, boundary and cycle checks | Tags and exceptions can become policy debris |
| Generated clients | One readable contract, reproducible generation, drift check | Generated representations may not cover domain policy |
| Multiple services | Bounded models, explicit translations, versioned contracts | Too much independence can duplicate volatile policy |
| Replicated data | Named write authority, provenance, freshness and reconciliation | Runtime ownership cannot be proven by imports alone |
| Active migration | Old/new owners, parity, cutoff, block new legacy consumers | Temporary duality can become permanent |

Each final recommendation must include:

- choose it when;
- the failure it prevents;
- the minimum artifact or rule;
- the enforcement option;
- what the reader pays for it;
- warning signs it is too light or too heavy;
- the next stronger and weaker option.

## Operational definitions

Use these consistently during research:

- **Authority:** the code or system allowed to define or accept changes to a
  particular decision.
- **Canonical home:** the inspectable location where that authority is
  represented.
- **Dominance:** the rule that, inside a declared boundary, one representation
  wins when representations disagree.
- **Derived representation:** a generated, cached, translated, projected, or
  presentation-specific form whose relationship to an authority is explicit.
- **Competing authority:** two or more locations that can independently define
  the same decision without an explicit boundary or reconciliation rule.
- **Controlled duplication:** an intentional independent copy with a stated
  reason, boundary, and review trigger.
- **Authority drift:** a derived or duplicated representation silently becoming
  an independent decision point.

## Evidence standard

Every material claim in the article should be labeled internally as:

- **Documented behavior**
- **Empirical evidence**
- **Case-study observation**
- **Hands-on experiment**
- **Inference**
- **Author recommendation**

The article may be opinionated, but it must not turn a new preprint, tool
documentation, or one repository example into a universal law.

## Planned outputs

1. Updated long-form outline
2. Source matrix with claim limits
3. Authority taxonomy and anti-pattern catalogue
4. Repository case-study notes
5. Reproducible matched-agent experiment
6. Situation-based recommendation table
7. First article draft in the established Part I voice
8. Proposed illustration list using the existing recurring characters
9. Final review checklist that forces readers to test the thesis against their
   own repository

## Drafting gate

Begin prose after:

- the authority taxonomy survives at least two case studies;
- the agent-maintainability evidence is sufficient to support or narrow the
  opening problem;
- the matched experiment fixture and scoring rubric are written;
- the recommendation ladder has explicit costs and counterexamples;
- “dominance” is defined clearly enough that no reasonable reader could mistake
  it for authority over people.
