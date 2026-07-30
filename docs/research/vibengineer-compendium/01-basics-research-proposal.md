# Part I Research Proposal: Structure or Slop

## Research objective

Determine which combinations of workflow artifacts, reusable skills, hooks, deterministic gates, and project memory make coding-agent work more consistent, predictable, resumable, and correct without imposing disproportionate ceremony.

The end product is a recommendation guide, not a strict comparison. It should answer:

> Given what this developer values and what can go wrong in their work, which level and style of process should they adopt?

The research must separate:

- Documented system behavior
- Claims made by system authors
- Independent research evidence
- Our own hands-on observations
- Opinions or working hypotheses that still need validation

## Primary research question

Coding agents can produce plausible code faster than developers can preserve intent, inspect consequences, and maintain a coherent path forward. Which process controls materially reduce that slop, and which combination best fits a user's priorities and risk?

## Thesis development rule

The article begins with a strong problem statement and a falsifiable thesis. Research is used to develop, qualify, or reject that thesis—not pasted into the article as a catalogue of framework features.

Working thesis:

> A proportionate system process reduces agent-produced slop by preserving intent, constraining execution, recording state, and demanding evidence. The correct process depends on which failures the developer most needs to prevent.

The final thesis must be updated if the comparative review or experiments do not support it.

## Supporting questions

1. What guarantees do project instructions, skills, hooks, permissions, tests, CI, and human checkpoints provide?
2. Where do Superpowers, Superpowers Optimized, BMAD, GitHub Spec Kit, and OpenSpec overlap?
3. Where do they make materially different trade-offs?
4. Does agentic TDD improve reliability, or can it also generate implementation-mirroring test bulk?
5. What state must persist for a different session or agent to resume correctly?
6. At what repository size or task duration do lexical search, vector retrieval, or graph traversal become justified?
7. What are the security implications of installing third-party skills, executable hooks, or persistent memory?
8. Which process elements remain useful across different coding-agent products?
9. Which user priorities meaningfully change the recommendation?
10. When is a small hand-built process better than adopting a full framework?

## Research streams

### Stream A: Workflow-system comparative review

Systems:

- Superpowers
- Superpowers Optimized
- BMAD Method
- GitHub Spec Kit
- OpenSpec

Comparison dimensions:

- Stated philosophy
- Entry and routing mechanism
- Artifact sequence
- Specification model
- Planning model
- TDD policy
- Verification and completion gate
- Human approval points
- Small-task fast path
- Debugging and recovery workflow
- State and resumption model
- Parallel-agent model
- Hook usage
- Enforcement versus natural-language guidance
- Portability across coding-agent harnesses
- Installation and repository footprint
- Trust and supply-chain boundary

Method:

1. Record claims from official documentation and repositories.
2. Inspect the actual installed workflow files where available.
3. Trace one representative feature task through each documented workflow.
4. Mark undocumented behavior as observation, not project fact.
5. Record meaningful omissions as well as advertised capabilities.

The comparison supplies evidence for recommendations. It is not itself the editorial structure.

### Stream B: User-priority decision model

Define recommendation profiles around actual needs:

- **Maximum engineering discipline:** design approval, TDD, review gates, and completion evidence matter more than speed.
- **Portable specification artifacts:** the user wants intent, plan, and tasks to survive across agents and tools.
- **Role-based organizational handoffs:** product, architecture, development, and QA responsibilities need explicit artifacts.
- **Lightweight brownfield iteration:** the user wants small proposals and change tracking without a large ceremony layer.
- **Customized resumable process:** the user wants task-size routing, local policies, persistent state, and controlled exceptions.
- **Low-risk minimalism:** the user needs a few durable instructions and checks, not a framework.

For each profile, determine:

- Primary priority
- Main failure to prevent
- Required mechanisms
- Acceptable workflow cost
- Repository and team assumptions
- Recommended system or minimum setup
- Reasons the recommendation fits
- Conditions that make it a bad fit
- A smaller and a heavier alternative

Initial recommendation hypotheses:

| User priority | Candidate recommendation | Hypothesis to test |
|---|---|---|
| Disciplined design, TDD, and review | Superpowers | Its connected skills and gates provide stronger default process discipline |
| Proportional routing and resumable local policy | Superpowers Optimized | Its local adaptations add task sizing and state controls at the cost of more machinery |
| Role-based PRD-to-QA handoffs | BMAD | Its explicit roles and story artifacts better support organizational handoffs |
| Portable spec-to-task artifacts | GitHub Spec Kit | Its explicit artifact chain travels cleanly across supported coding agents |
| Lightweight brownfield specification | OpenSpec | Its iterative proposal model imposes less ceremony on existing repositories |
| Small, low-risk tasks | No full framework | Instructions, focused verification, Git, and a state note may be sufficient |

These rows must not graduate into recommendations until verified against current sources and hands-on use.

### Stream C: Control-stack analysis

Build a taxonomy for:

- Repository instructions
- Skills
- Specs and plans
- Hooks
- Tool permissions
- Tests, linters, and CI
- Human checkpoints

For each mechanism, record:

- Who decides it runs
- Whether execution is automatic
- Whether failure can block progress
- What artifact it produces
- Whether another agent can inspect the result
- Whether it survives a fresh session
- How it can fail silently

The intended output is a guarantee ladder, not a product feature checklist.

### Stream D: Matched workflow experiment

Use one small, representative repository and three task types:

- A contained feature
- A reproducible bug
- A cross-module refactor

Run each task under:

1. Unstructured agent prompt
2. Project instructions only
3. Spec and plan workflow
4. Spec plus TDD
5. Full skill, hook, and checkpoint workflow

For selected runs, interrupt the task halfway and resume it in a fresh session.

Record:

- Acceptance-test result
- Scope expansion and unrequested files
- Duplicate implementations introduced
- Human corrections required
- Ability to resume without verbal reconstruction
- Completion claims backed by command evidence
- Token usage and elapsed time
- Number and size of process artifacts
- Stale or contradictory state
- Human review time
- Fit against the relevant user-priority profile
- Whether the workflow cost would plausibly be maintained after the experiment

The experiment is exploratory. It should not claim statistical significance from a small number of runs.

### Stream E: Project memory escalation test

Compare:

1. Raw conversation resumption
2. Versioned Markdown state
3. Markdown plus lexical search
4. Vector retrieval
5. Graph-assisted retrieval

Use the same recovery questions across every approach:

- What was being changed?
- Why was that approach chosen?
- What was rejected?
- What remains incomplete?
- Which commands establish the current state?
- Which recorded facts are now stale?

Measure:

- Correct fact recovery
- Provenance availability
- Stale-fact detection
- Contradiction handling
- Retrieval latency
- Setup and maintenance cost
- Whether the retrieved memory changes the final task outcome

Default hypothesis:

> Small, versioned, deliberately maintained files plus ordinary search will cover most project-memory needs. Retrieval or graphs should be added only after a measured failure.

This hypothesis must remain falsifiable.

### Stream F: Security and trust review

Threats to assess:

- Malicious or compromised third-party skills
- Executable hook abuse
- Prompt injection persisted into project memory
- Stale instructions overriding current repository reality
- Sensitive material captured in state or retrieval indexes
- Tool permissions broader than the workflow requires
- Supply-chain updates that silently change agent behavior

The article only needs a concise warning and a safe-installation checklist. A complete security treatment is outside Part I.

## Evidence hierarchy

Prefer sources in this order:

1. Actual workflow source code and versioned configuration
2. Official documentation
3. Peer-reviewed or clearly labeled preprint research
4. Reproducible hands-on experiments
5. Maintainer commentary
6. Community reports and anecdotes

Community sources can identify questions and failure modes but should not establish broad claims by themselves.

## Deliverables

- Completed source matrix with claim-level citations
- Workflow-system comparison table
- Priority-to-process recommendation matrix
- Decision tree routing user needs to a framework or minimum setup
- Recommendation cards stating “choose this when,” “you pay for it with,” and “do not choose this when”
- Control-stack guarantee table
- Matched-task experiment protocol and logs
- Memory escalation experiment and decision rule
- Small security and trust checklist
- Revised Part I outline
- Draft-ready evidence notes
- Short bibliography split into systems, research, and practitioner evidence

## Article scope guardrails

- Part I owns process foundations, resumability, and the control stack.
- Part II owns architectural authority.
- Part III owns expansion, analysis, and reduction of accumulated code.
- Part IV owns detailed test-suite quality.
- The memory section gets a decision rule, not a complete GraphRAG tutorial.
- The workflow review should explain trade-offs rather than crown a winner.
- Recommendations must be conditional on user priorities, failure costs, and willingness to maintain the process.

## Completion criteria

Research is sufficient to begin drafting when:

- Every major factual claim in the outline has at least one suitable source or is explicitly labeled as experience.
- All five workflow systems have been inspected across the same comparison dimensions.
- Every user-priority profile has a supported recommendation, a lighter alternative, a heavier alternative, and explicit disqualifiers.
- The control-stack taxonomy distinguishes soft guidance from enforced gates.
- The resumption experiment has a written protocol.
- The memory section can state when plain files are sufficient and what evidence justifies escalation.
- Security risks are represented without taking over the article.
