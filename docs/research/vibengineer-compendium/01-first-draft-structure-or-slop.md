---
title: "The Vibengineer Compendium, Part I: The Basics — Structure or Slop"
series: "The Vibengineer Compendium"
part: 1
status: first-draft
draft: true
---

# The Vibengineer Compendium, Part I: Structure or Slop

Coding agents are extremely good at forward motion.

Ask for a feature and you get files. Ask for a fix and you get a patch. Ask whether everything works and, quite often, you get a confident paragraph explaining that it does.

This is useful. It is also how you end up with slop.

I do not just mean ugly code. Ugly code can be honest. I mean plausible work with no stable intention behind it: two implementations of the same rule, tests that only agree with the code that was just generated, a plan that disappeared three context windows ago, and a completion claim nobody can reproduce.

The basic problem is that agents can produce code faster than we can preserve intent, inspect consequences, and keep a coherent path forward.

My thesis for this series is simple:

> A coding agent needs a proportionate system process. That process preserves intent, constrains execution, records state, and demands evidence. Without it, you are mostly asking a probability machine to keep its own story straight.

The short version: give your agent some structure or it is going to produce slop.

## The process is part of the product

A useful agent process should give you at least seven things:

- **Consistency:** similar work goes through similar checks.
- **Predictability:** you know what artifact or decision comes next.
- **Resumability:** a new session can recover what is happening and why.
- **Auditability:** important decisions leave something readable behind.
- **Correctability:** there are places to stop, inspect, and redirect.
- **Proportionality:** a typo does not need the same ritual as an authentication rewrite.
- **Evidence:** “done” means something observable happened.

None of these require a huge framework. A short spec, one failing test, a Git branch, and a saved state note may be enough.

The point is not maximum process. The point is enough process to control the failure you are worried about.

That gives us a better way to look at the current agent workflow frameworks. Do not ask which one has the most commands. Ask what kind of failure it is designed to prevent, and whether you are willing to pay its operating cost.

## Specs, tests, plans, and roles are different controls

Most of the current systems mix a few recurring patterns:

- **Spec-driven development** records what and why before the code.
- **Test-driven development** asks for executable evidence before production code.
- **Plan-driven execution** turns a design into small steps and checks.
- **Role-driven workflows** pass artifacts between product, architecture, development, and QA perspectives.
- **Review-driven workflows** force a pause before work becomes permanent.
- **Investigation loops** require a root-cause theory before a bug fix.

These are not competing religions. They constrain different failure modes.

A spec can stop the agent from solving the wrong problem. It cannot prove the implementation works.

A test can prove a behavior. It cannot prove the behavior was worth building.

A plan can make work resumable. It cannot rescue a bad premise.

A review can catch a bad diff. It becomes expensive if every tiny change needs three agents and a constitutional convention.

The useful systems combine these controls. The annoying systems apply all of them to everything.

## Instructions are not enforcement

This is the most important mechanical distinction in the whole article.

| Mechanism | What it does | What it actually guarantees |
|---|---|---|
| Project instructions | Keeps repository conventions in context | Guidance |
| Skills | Reuses a reasoning or workflow recipe | Guidance unless backed by tools |
| Specs and plans | Preserves intent and task state | A reviewable artifact |
| Hooks | Runs something at a lifecycle event | Automatic invocation |
| Tool permissions | Limits what actions are available | An enforced boundary |
| Tests, linters, and CI | Checks observable properties | A deterministic gate |
| Human checkpoints | Decides whether work should continue | Accountable judgment |

Telling an agent “always run the formatter” is not the same as a hook running it after every edit. Telling an agent “never read secrets” is not the same as a permission system making the file unavailable.

Anthropic makes this distinction directly in its current [Claude Code steering guidance](https://claude.com/blog/steering-claude-code-skills-hooks-rules-subagents-and-more): instructions and skills influence model behavior, while hooks and permissions are the right tools when behavior must happen reliably. Its [hooks documentation](https://code.claude.com/docs/en/hooks-guide) also distinguishes deterministic command, HTTP, and tool hooks from hooks that ask another model to make a judgment.

Codex has a similar separation. Repository instructions live in scoped `AGENTS.md` files, reusable workflow guidance can live in skills, and plugins can package skills together with approved apps and actions. See the current [Codex `AGENTS.md` documentation](https://developers.openai.com/codex/guides/agents-md) and [plugin overview](https://help.openai.com/en/articles/20001256-plugins-in-codex/).

The practical rule is:

> Put preferences in instructions, procedures in skills, durable intent in readable artifacts, and non-negotiable constraints in deterministic gates.

## Which process should you use?

The research for this article looked at the current source and documentation for Superpowers, Superpowers Optimized, BMAD, GitHub Spec Kit, and OpenSpec.

This is not a benchmark result. We have not yet run the same task through every system enough times to make productivity claims. What we can do now is compare their actual workflow mechanics and make conditional recommendations.

### You want disciplined engineering by default: Superpowers

[Superpowers](https://github.com/obra/superpowers) is the cleanest fit when your priority is a connected design, planning, TDD, review, and completion process across several coding-agent harnesses.

Choose it when the failure you most want to prevent is an agent jumping straight from an idea into an implementation and declaring victory without evidence.

You pay for it with checkpoints and ceremony. That is reasonable for behavior changes and risky work. It is silly for a typo.

Do not choose the full workflow if your team will routinely bypass it. A small repository-native process that people actually follow is better than an impressive skills folder everybody learns to ignore.

### You want the same discipline with local routing and memory: Superpowers Optimized

Superpowers Optimized is our local adaptation of that model. It adds premise checks, task sizing, explicit fast paths, project maps, task state, known-issue memory, stronger verification rules, and Codex-specific hooks.

Choose it when work spans sessions, the repository is expensive to rediscover, and you want the workflow to distinguish a trivial change from a full architecture task.

You pay for it with more moving parts, more local policy, and the maintenance cost of a fork. It is also less portable than upstream Superpowers.

This is not a neutral recommendation. It reflects the way I prefer to work. The comparison still needs matched-task experiments before I claim it produces better outcomes.

### You want explicit product-to-QA handoffs: BMAD

The current [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) is built around artifact-mediated work: PRDs, architecture, epics, stories, implementation state, and review.

Choose it when several sessions, agents, or people need to carry a product intention through architecture and implementation without each one inventing its own version.

It is especially interesting for ambiguous or cross-system work. Its current workflow also has lighter routes for bounded changes, which matters because running the full product ceremony for everything would be unbearable.

You pay for it with artifact stewardship. If nobody owns the freshness of the PRD, architecture, stories, and state, the system becomes a highly organized source of stale instructions.

Do not mistake BMAD's roles or review prompts for deterministic governance. Its workflow is useful context discipline; CI, permissions, and human accountability still have to exist outside it.

### You want governed, portable specification workflows: GitHub Spec Kit

[GitHub Spec Kit](https://github.github.com/spec-kit/) currently provides the most explicit general artifact chain: Spec → Plan → Tasks → Implement, with optional clarification, analysis, convergence, workflow gates, and persisted workflow state.

Choose it when your priority is durable intent, portability across coding agents, organizational templates, and workflows that can pause and resume at explicit gates.

You pay for it with more artifacts and governance machinery. That can be a strength for a team and a nuisance for a small patch.

Do not choose it merely because “spec-driven” sounds responsible. Choose it when someone will review the artifacts and when the cost of process drift is greater than the cost of maintaining them.

### You want lighter brownfield change specs: OpenSpec

[OpenSpec](https://github.com/Fission-AI/OpenSpec) is the better starting hypothesis when you want a smaller, iterative change proposal inside an existing repository.

Its core model is closer to explore/propose → apply → archive, with delta specifications accumulating around actual changes rather than requiring the whole existing system to be documented first.

Choose it when the failure you want to prevent is chat-only development losing the intent of a contained brownfield change.

You pay for it with more reliance on human review and external gates. Its verification is useful, but it is not a non-bypassable compliance system.

Do not choose it alone for high-risk work that requires mandatory approvals, privileged-command controls, or release gates. Pair it with the repository's real tests, CI, code ownership, and security process.

### You want to fix a small thing: maybe use none of them

For small, reversible work, the correct framework may be:

- One concise `AGENTS.md` or equivalent
- One short issue or task statement
- One targeted verification command
- Git
- A pull request

That is still a system process. It is simply small enough to fit the risk.

## Project memory: keep one readable truth

There is a temptation to treat agent memory as a database problem immediately.

Index the repository. Embed the documents. Extract a knowledge graph. Build a DAG. Generate summaries for the humans and separate summaries for the machines.

Now you have two documentation systems.

One of them will drift.

My recommendation is:

> Keep one linked, human-readable set of project documents. Let humans and agents read the same files. Any RAG index, graph, DAG, embedding store, or generated summary should be a disposable view derived from that source.

Start with:

1. Repository instructions
2. A project map linking to the real files
3. A decision log with small examples
4. Current task state
5. Known issues and their verified fixes
6. Ordinary lexical search

Only escalate when you can name a decision the agent consistently fails to recover from those files.

This is not anti-graph. Recent work such as [ByteRover](https://arxiv.org/abs/2604.01599) is interesting partly because it uses hierarchical, human-readable Markdown with explicit provenance and progressive retrieval rather than requiring an external vector or graph database. Its reported benchmark results are about long-term memory benchmarks, not proof that every coding repository needs its architecture.

Readable files have boring advantages:

- People can review them in the same pull request as the code.
- Examples explain conventions better than compressed metadata.
- Links express relationships without creating another authority.
- Git shows who changed the memory and why.
- `rg` works.
- If a derived index breaks, the knowledge still exists.

The danger is not only drift. Persistent memory is also a security boundary. Recent research on [prompt injection through agent memory](https://arxiv.org/abs/2607.14611) found that malicious instructions already planted in memory files can affect current and later sessions. Memory deserves provenance, review, and the same suspicion as executable configuration.

Before adding a memory service, ask:

> What can the agent not recover from Git, the current task file, a linked project map, and normal search?

If you do not have a concrete answer, you probably do not need RAG. You need better docs.

## The minimum viable setup

If you want a starting point without adopting a framework:

- Keep one short repository instruction file.
- Record the task before changing code.
- For non-trivial work, write down the intended behavior and the evidence that will prove it.
- Run targeted checks while iterating.
- Save a short state file before stopping unfinished work.
- Keep durable decisions in linked, human-readable documents.
- Use Git as the history.
- Add hooks only for checks that really must run.
- Require fresh evidence before accepting “done.”

That is enough to turn an agent from an extremely enthusiastic typist into something you can engineer with.

It does not solve architecture. A beautiful process can still place the same business rule in three different modules.

That is Part II: establish ownership and dominance before the code goes out of control.

---

## Draft evidence note

This first draft is grounded in current official documentation and direct inspection of the installed Superpowers systems. The conditional recommendations are provisional. The matched feature, bug, refactor, interruption, and documentation-drift experiments in the research proposal have not yet been completed, so this draft does not claim measured productivity or correctness improvements.
