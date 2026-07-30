---
title: "The Vibengineer Compendium, Part I: The Basics — Structure or Slop"
series: "The Vibengineer Compendium"
part: 1
status: outline
draft: true
---

**Working title:** The Vibengineer Compendium, Part I: The Basics — Structure or Slop

## Thesis

**Problem statement:** Coding agents can produce working code faster than developers can preserve intent, inspect consequences, and maintain a coherent path forward. The result is not merely bad code. It is plausible work with weak ownership, missing evidence, and no reliable way to resume or correct it.

**Thesis to test:** A proportionate system process reduces that slop by preserving intent, constraining execution, recording state, and demanding evidence. The right process is not universal: it depends on which failures a developer most needs to prevent.

The short version: give the agent some structure or it is going to produce slop.

For this article, **slop** means work that may function locally but has unclear intent, duplicated authority, unsupported completion claims, or no safe continuation path.

## Reader promise

- Give the reader a practical vocabulary for the system surrounding a coding agent.
- Explain what specifications, tests, skills, hooks, state, and deterministic gates each control.
- Show how to choose enough process for the task without building a ceremony machine.
- End with a minimum setup that can be adopted one piece at a time.

## Argument

### 1. Structure or slop

- Agents are good at producing locally plausible work.
- Local plausibility does not preserve repository-wide intent over a long task.
- More prompting is not automatically more control.
- The engineer remains responsible for the process, its stopping conditions, and its evidence.

### 2. What a system process must provide

- **Consistency:** similar work follows similar gates.
- **Predictability:** the developer knows what artifact or decision comes next.
- **Resumability:** another session can recover the current state and the reason behind it.
- **Auditability:** important decisions leave evidence.
- **Correctability:** there are deliberate points where the agent can be stopped or redirected.
- **Proportionality:** a typo and an authentication change do not need the same machinery.
- **Evidence:** completion means an observable result, not an agent saying “done.”

### 3. The common process patterns

- Spec-driven development: intent becomes an artifact before implementation.
- Test-driven development: behavior is expressed as executable evidence before production code.
- Plan-driven execution: a design becomes small steps with explicit checks.
- Story and role systems such as BMAD: artifacts pass through specialized planning, development, and review roles.
- Review and checkpoint systems: work pauses at defined decision boundaries.
- Investigation loops: bugs require evidence and a root-cause hypothesis before a fix.
- Small-task fast paths: process cost must remain proportional to risk.

Frame these as related patterns with different costs and guarantees—not competing religions.

### 4. The control stack

Separate mechanisms by what invokes them and what they can guarantee:

| Mechanism | Primary job | Strength of guarantee |
|---|---|---|
| Project instructions | Persistent repository conventions | Soft guidance |
| Skills | Reusable reasoning and workflow recipes | Soft unless backed by tools |
| Specs and plans | Preserve intent and task state | Inspectable artifact |
| Hooks | Run behavior around agent lifecycle events | Automatic invocation |
| Tool permissions | Limit what actions are possible | Enforced boundary |
| Tests, linters, and CI | Check observable properties | Deterministic gate |
| Human checkpoints | Decide whether work should continue | Accountable judgment |

The important distinction: asking an agent to run a check is not equivalent to a system that runs or enforces it.

### 5. A comparative review of workflow systems

Use Superpowers, Superpowers Optimized, BMAD, GitHub Spec Kit, and OpenSpec as case studies.

Compare:

- Entry and routing model
- Artifact chain
- TDD and verification policy
- Human approval points
- Small-task escape hatch
- Recovery after interruption
- Parallel-agent model
- Enforcement versus prompting
- Repository pollution and maintenance cost
- Portability across coding harnesses
- Trust implications of executable skills and hooks

Separate documented behavior from our hands-on observations and the projects' own marketing claims.

The comparison is evidence collection, not the article's conclusion. Convert it into recommendations:

- If you prioritize strict engineering discipline and repeated verification, which system fits?
- If you prioritize a portable spec-to-implementation artifact chain, which system fits?
- If you prioritize role-based planning and organizational handoffs, which system fits?
- If you prioritize lightweight brownfield iteration, which system fits?
- If you prioritize a customized process with explicit fast paths and state management, which system fits?
- When is no framework—just a few instructions, checks, and state files—the better answer?

Each recommendation must state:

- The user's priority
- The failure they are trying to prevent
- The mechanism that addresses it
- The workflow cost they accept
- The point at which the recommendation stops fitting

### 6. A process-selection decision guide

Build a small decision tree rather than a winner's podium:

1. What is the cost of a wrong change?
2. Does the work need to survive multiple sessions or people?
3. Is the repository greenfield or brownfield?
4. Does the user want defined roles or one flexible agent?
5. Must testing and review be mandatory or merely encouraged?
6. How much process will the user actually maintain?

Working recommendation hypotheses to test:

- **Superpowers:** likely fit for users prioritizing disciplined design, TDD, review gates, and explicit branch completion.
- **Superpowers Optimized:** likely fit when those controls need proportional routing, resumable state, and local policy customization.
- **BMAD:** likely fit for users prioritizing PRD, architecture, story, developer, and QA role handoffs.
- **GitHub Spec Kit:** likely fit for users prioritizing a portable, explicit Spec → Plan → Tasks → Implement artifact chain.
- **OpenSpec:** likely fit for users prioritizing lighter, iterative, brownfield specification work.
- **No full framework:** likely fit for small, low-risk work where a project instruction file, focused test command, and short state note provide enough control.

These are research hypotheses, not conclusions. The final recommendations must follow the evidence and hands-on evaluation.

### 7. Project memory: start boring

Use a memory escalation ladder:

1. Repository instructions
2. Project map
3. Decision log
4. Current task state
5. Known-issues file
6. Lexical search across those artifacts
7. Vector retrieval after ordinary search demonstrably fails
8. Graph-assisted retrieval when relationship traversal is an actual requirement

Ask one hard question before adding RAG or a graph:

> What decision could the agent not recover from Git, the current task file, a project map, and normal search?

If we cannot answer it, the memory system is probably architecture cosplay.

### 8. The minimum viable vibengineering setup

- One concise project instruction file
- One targeted verification command
- A written task or spec for non-trivial work
- A small state artifact for interrupted work
- A checkpoint before implementation
- A checkpoint before completion
- Git as durable history
- Two or three reusable skills, not thirty
- Hooks only for checks that genuinely must run automatically

### 9. Handoff to architecture

Process gives the agent a way to work. Architecture gives its work somewhere unambiguous to belong.

Part II asks what happens when the process is sound but two modules both believe they own the same truth.

## Candidate examples and diagrams

- **Control-stack diagram:** soft instructions at the top, inspectable artifacts in the middle, deterministic enforcement and accountable human decisions at the bottom.
- **Process-property table:** show how specs, tests, state, and hooks contribute differently to consistency, predictability, resumability, and evidence.
- **Same task, two runs:** an unstructured agent produces a plausible diff; a bounded workflow produces a spec, focused change, verification output, and resumable state.
- **Priority-to-process decision tree:** start from user needs and risk, then route toward a framework or the no-framework setup.
- **Recommendation cards:** “choose this when,” “you pay for it with,” and “do not choose this when.”
- **Memory escalation ladder:** flat versioned files first, search second, RAG or graphs only after a measured retrieval failure.
- **Minimum setup tree:** the smallest useful repository layout, with optional additions clearly marked.

## Claims requiring evidence

- Do structured agent workflows improve correctness, consistency, or reviewability compared with unstructured prompting?
- Which measurable workflow characteristics actually predict suitability for different user priorities?
- Which benefits come from the methodology itself, and which come from simply forcing more human review?
- How reliably do specification artifacts remain aligned with implementation in brownfield repositories?
- Does agentic TDD improve defect detection, or can it also inflate implementation-mirroring test suites?
- What evidence supports persistent project state improving fresh-session resumption?
- At what repository scale do lexical search, vector retrieval, or graph traversal produce measurably different outcomes?
- What new prompt-injection or supply-chain risks arrive with third-party skills, hooks, and persistent memory?

## Non-goals

- Not a universal workflow or a ranking based only on feature count.
- Not a strict feature comparison or an attempt to name one overall winner.
- Not a claim that every task needs a formal spec, TDD, subagents, or approval ceremony.
- Not a full treatment of testing; Part IV owns test-suite quality.
- Not a full treatment of architectural authority; Part II owns that.
- Not a recommendation to install every workflow system discussed.
- Not a claim that GraphRAG is useless—only that its cost needs a demonstrated retrieval problem.

## Series connection

- Part I defines the operating system around the coding agent: how intent, constraints, state, and evidence survive the work.
- Part II establishes canonical authority inside the codebase so the process has an unambiguous destination.
- Part III governs what to retain, simplify, and delete after generated code begins accumulating.

## Open drafting questions

- Is “Structure or Slop” the final public subtitle, or the opening section and repeated refrain?
- Which one task should be used for the matched workflow comparison?
- Should Superpowers Optimized be described as a separate system or explicitly as a local adaptation of upstream Superpowers?
- How much implementation detail about hooks belongs here before it distracts from the control-stack distinction?
- Can the memory section stay useful without turning into a survey article of its own?
