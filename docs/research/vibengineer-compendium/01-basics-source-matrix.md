# Part I Source Matrix

Status values:

- `queued` — identified but not inspected closely
- `reviewing` — primary material has been opened
- `captured` — relevant claims and limitations have been recorded
- `tested` — hands-on behavior has been reproduced

## Workflow systems

| System | Primary source | Claims to verify | Evidence needed | Status |
|---|---|---|---|---|
| Superpowers | [obra/superpowers](https://github.com/obra/superpowers) | Skills framework plus development methodology; brainstorming, plans, TDD, review, and branch completion form a connected workflow | Inspect current skill sources, routing, hooks, supported harnesses, and release changes | reviewing |
| Superpowers Optimized | Local installed skill sources | Adds routing, proportional fast paths, state management, verification, and other local policy changes | Diff installed behavior against upstream; label local conclusions as direct observation | reviewing |
| BMAD Method | [BMAD documentation](https://docs.bmad-method.org/) and repository | PRD, architecture, stories, developer, and QA artifacts support role-based handoffs and resumption | Trace a representative task and record artifact count, approval boundaries, and small-task behavior | queued |
| GitHub Spec Kit | [GitHub Spec Kit documentation](https://github.github.com/spec-kit/) and [repository](https://github.com/github/spec-kit) | Spec → Plan → Tasks → Implement produces structured artifacts and supports resumable workflows | Inspect current templates, analysis/checklist stages, brownfield behavior, and workflow automation | reviewing |
| OpenSpec | [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) | Lighter, iterative, brownfield-oriented spec workflow | Inspect current artifact-guided workflow and compare repository footprint and update flow | reviewing |

## Recommendation profiles

These are hypotheses to validate, not final endorsements.

| User priority | Candidate | Must prove | Disqualifiers to investigate | Status |
|---|---|---|---|---|
| Disciplined design, TDD, and review | Superpowers | Gates are coherent, current, and produce better evidence than lighter setups | Excess ceremony, weak small-task path, harness-specific behavior | queued |
| Proportional routing and resumable local policy | Superpowers Optimized | Task sizing and state improve continuity without silently skipping needed rigor | Local-only assumptions, policy complexity, divergence from upstream | queued |
| Role-based PRD-to-QA handoffs | BMAD | Role artifacts improve organizational clarity and resumption | Artifact bulk, role theater, poor fit for individuals or small changes | queued |
| Portable spec-to-task artifacts | GitHub Spec Kit | Artifacts remain usable across tools and implementation sessions | Greenfield bias, spec drift, update friction in brownfield work | queued |
| Lightweight brownfield specification | OpenSpec | Proposal workflow adds useful intent with lower maintenance cost | Insufficient gates for high-risk work, weak verification integration | queued |
| Small low-risk work | No full framework | Instructions, focused checks, Git, and state produce sufficient control | Poor resumption, inconsistent execution, missing approval boundaries | queued |

For every final recommendation, capture:

- Choose this when
- The failure it prevents
- You pay for it with
- Do not choose this when
- Lighter alternative
- Heavier alternative

## Harness primitives

| Primitive | Primary source | Claim or question | Status |
|---|---|---|---|
| Codex project instructions | [Codex `AGENTS.md` documentation](https://github.com/openai/codex/blob/main/docs/agents_md.md) | How are instructions discovered, scoped, and prioritized? | reviewing |
| Codex skills and plugins | [Plugins in Codex](https://help.openai.com/en/articles/20001256-plugins-in-codex/) and Codex skill sources | What is loaded on demand, what can execute, and how are dependencies trusted? | reviewing |
| Claude Code hooks | [Claude Code hooks guide](https://code.claude.com/docs/en/hooks-guide) | Which lifecycle events can invoke deterministic commands, and when can hooks block an action? | reviewing |
| Claude Code steering mechanisms | [Anthropic steering overview](https://claude.com/blog/steering-claude-code-skills-hooks-rules-subagents-and-more) | When should instructions, skills, hooks, and subagents be used? | reviewing |
| Tool permissions | Official Codex and Claude Code permission documentation | Which constraints are enforced rather than suggested? | queued |
| CI and repository rules | GitHub Actions and branch-protection documentation | Which completion claims can be independently gated outside the agent session? | queued |

## Process-pattern research

| Topic | Research question | Preferred evidence | Status |
|---|---|---|---|
| Spec-driven agent development | Do specs improve task correctness and reviewability, and how often do they drift from implementation? | Controlled studies, repository studies, matched-task experiment | queued |
| Agentic TDD | Does test-first agent work improve defect detection relative to test-after or no-test workflows? | Controlled studies plus mutation or hidden-test evaluation | queued |
| Workflow decomposition | Which benefits come from decomposition versus additional compute or human review? | Ablations and matched runs | queued |
| Human checkpoints | Where does human approval catch errors that automated gates miss? | Empirical studies and recorded experiments | queued |
| Resumability | What artifacts allow a fresh agent session to continue correctly? | Fresh-session recovery experiment | queued |
| Proportionality | When does workflow ceremony cost more than the risk it controls? | Task-size comparison and artifact/time measurements | queued |

## Project memory

| Approach | Source or implementation class | Question | Status |
|---|---|---|---|
| Linked human-readable authority | Repository instructions, project map, decisions, state, known issues, examples | How much project state can people and agents recover from one linked set of readable files and Git? | queued |
| Lexical retrieval | `rg`, BM25, SQLite FTS | When does ordinary exact or lexical search stop being sufficient? | queued |
| Parallel human and machine docs | Independently edited Markdown plus structured summaries/indexes | How quickly do the two authorities drift, and which version does the agent trust? | queued |
| Derived vector retrieval | Repository RAG systems and retrieval literature | Does semantic retrieval over the readable source improve recovery enough to justify indexing and staleness cost? | queued |
| Hierarchical agent-curated memory | [ByteRover](https://arxiv.org/abs/2604.01599) | Can curated retrieval remain derived, attributable, and rebuildable from readable sources? | reviewing |
| Derived memory graphs or DAGs | [EMG-RAG](https://aclanthology.org/2024.emnlp-main.281/) | When does relationship traversal help more than linked files, and can every node retain source provenance? | queued |
| Agent-memory landscape | [Memory in the Age of AI Agents](https://openreview.net/pdf/180d26775b5edf368b1aea4bcf724855acc29c14.pdf) | What memory types, update policies, and evaluation methods are relevant to coding work? | reviewing |
| Memory security | Prompt-injection and memory-poisoning research | How can persistent memory preserve hostile or stale instructions across sessions? | queued |

## Planned hands-on evidence

| Experiment | Comparison | Outcome measures | Status |
|---|---|---|---|
| Feature task | Unstructured vs instructions vs spec/plan vs TDD vs full workflow | Acceptance, scope, corrections, artifacts, time | queued |
| Bug task | Same five process levels | Root-cause accuracy, regression evidence, unnecessary edits | queued |
| Refactor task | Same five process levels | Behavior preservation, duplication, architecture drift, review time | queued |
| Interrupted task | No state vs linked readable state vs retrieval-backed state | Correct resumption, stale facts, reconstruction effort | queued |
| Documentation drift | One readable authority vs separately edited human and machine representations | Contradictions, time-to-drift, wrong-source retrieval, review cost | queued |
| Derived-index rebuild | Readable authority plus disposable RAG/graph/DAG index | Provenance, rebuildability, knowledge loss after index deletion | queued |
| Enforcement audit | Skill instruction vs hook vs CI gate | Whether skipped or failed checks actually block completion | queued |

## Immediate evidence gaps

1. A fair, current description of Superpowers Optimized relative to upstream Superpowers.
2. Controlled evidence for spec-driven agent development rather than author claims.
3. Agentic TDD evaluations that measure fault detection, not just test generation.
4. A reproducible definition and measurement method for resumption quality.
5. Repository-scale thresholds where lexical, vector, and graph retrieval separate.
6. Security evidence covering executable skills, hooks, and persistent agent memory.
7. One representative repository and task set for the matched experiment.
8. Empirical evidence that dual human/machine documentation drifts or confuses coding agents.
9. A representative linked-file cluster for testing direct agent search against derived retrieval.
