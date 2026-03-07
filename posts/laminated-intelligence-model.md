---
title: "The Laminated Intelligence Model: A Reference Architecture for AI Agent Systems"
date: "2026-03-07"
excerpt: "Why alternating deterministic and stochastic layers — like laminating puff pastry — produces reliable AI agent systems. Introducing LIM, a shared vocabulary for the patterns every agent framework keeps rediscovering."
tags: ["ai-agents", "architecture", "LIM", "agent-orchestration"]
---

# Puff Pastry Architecture: Why Alternating Dumb and Smart Code Produces Intelligent Systems

*Introducing the Laminated Intelligence Model (LIM) - a reference model for AI agent systems*

---

Puff pastry is made by folding dough around butter over and over - a process called *lamination*. Neither ingredient alone gets you the result: dough bakes into bread (useful but dense), butter melts (rich but shapeless). The magic is in the alternation.

When it bakes, water trapped in the butter and dough layers turns to steam. The steam pushes against the dough layers, creating lift. The dough's gluten network holds its shape, trapping the steam and creating structure. Without the dough, the steam escapes uselessly. Without the steam, the dough bakes flat. The result - hundreds of flaky layers - is an emergent property that exists in neither ingredient on its own.

Agent systems work the same way.

- **Dough = Rule-based code (dumb code).** It holds shape. Reliable, predictable, testable. But alone, it can only handle situations you planned for.
- **Butter = Non-deterministic code (smart code).** LLMs, human operators, learned policies. It creates the lift - the ability to handle new situations, figure out what to do when instructions aren't clear, and come up with creative solutions. But alone, it melts into inconsistency.
- **Lamination = The alternating architecture.** Thin layers of dumb code holding thin layers of smart code in check, over and over.
- **Steam = What the system can do that neither part could do alone.** The useful work that happens when intelligence pushes against structure. A well-laminated agent system produces abilities that neither the code nor the AI model could achieve by itself.
- **The baker's hands = The boundary between dumb and smart code.** The skill isn't in the ingredients - it's in how you fold them together. The boundary between dumb code and smart code (how you design constraints, assemble prompts, and validate output) is where the real engineering craft lives.

If your agent system isn't working, don't blame the butter (swap AI models) or add more dough (write more orchestration code). Check your lamination.

---

## The Missing Recipe

In 2025-2026, every AI company, startup, and weekend hacker is building their own orchestration layer. LangChain, CrewAI, AutoGen, DSPy, Semantic Kernel, and dozens more - each independently figuring out the same layering patterns, each arriving at similar solutions through hard-won experience. We're all learning how to bake puff pastry, and we keep converging on the same recipe without having written it down.

The problem isn't a lack of engineering effort. It's the lack of a shared model.

The networking world had this exact problem in the 1970s - every vendor had their own stack, and nobody could talk about layers in the abstract. The OSI model solved this not by telling everyone how to build their systems, but by giving the field a shared way to break things down. Suddenly everyone could say "that's a Layer 3 problem" and be understood.

We need the same thing for agent systems. Questions like "should the orchestrator handle retries or should the agent?" and "where does output validation belong?" don't have a common frame of reference. Every team rediscovers the layering on their own. Without a shared way to break things down, we can't easily explain *why* these solutions keep emerging - we can only notice that the same patterns show up everywhere.

This post introduces the **Laminated Intelligence Model (LIM)** as that shared way to break things down. It doesn't come from academic speculation but from hands-on experience building and using agent orchestration systems - from the patterns that keep showing up whether you plan for them or not.

---

## Every Agent Interaction Is a Search

LIM's core idea: **every agent interaction is a search through a space of possible solutions.**

A user states what they want - a bug to fix, a feature to build, a question to answer - and the agent system's job is to find a good enough answer within the space of all possible solutions. This search framing is the foundation for everything that follows.

This isn't just an analogy. LLMs are compressed versions of probability distributions over token sequences (tokens are small chunks of text - sometimes a word, sometimes part of a word). Each time the model generates text, it samples from that distribution. The system's job is to constrain that sampling - narrowing where the model searches and validating what it finds.

These constraints reach the model in two ways. Some are enforced directly by rule-based code - schemas that reject invalid output, validators that block bad tool calls. Others can only be delivered through the prompt - behavioral instructions, reasoning formats, identity context. Both are rule-based in intent, but they differ in enforcement: direct constraints reject outright, while prompt-delivered constraints bias the distribution toward better answers. The "search" is literal: a guided walk through probability space, where each step is a sample and each constraint prunes the paths forward.

Think of it like Bayesian search for a sunken ship. You start with a prior belief about where the wreck might be, gather evidence from each sonar pass, and update your search area based on what you find. The prior doesn't find the wreck. The sonar doesn't find the wreck. The *combination* of prior-guided search and evidence-based updating finds the wreck.

The system has exactly three types of layers, and their *alternation pattern* determines both how well the search works and how reliable the system is:

**D-Layers (Deterministic - "Dumb Code")** narrow down the solution space. File systems, state machines, message queues, databases, API clients, routing logic, template engines. Given the same input, always the same output. Fully auditable, testable, reproducible. Zero adaptability. They can't discover solutions, but they keep the system from wasting time on invalid ones.

**S-Layers (Stochastic - "Smart Code")** explore the solution space. Large language models, human operators, reinforcement learning systems. Given the same input, they may produce different outputs. They can handle new situations, come up with creative solutions, and reason about unclear problems - but they can't guarantee correctness, consistency, or reproducibility. Without constraints, they explore aimlessly.

**H-Layers (Harness - "The Glue")** translate between the world of rules and the world of AI. These are pieces of rule-based code whose *only job* is to sit between a D-layer and an adjacent S-layer.

The key difference: D-layers are useful on their own - a database is useful no matter what it's connected to. H-layers have no standalone purpose. Remove the adjacent S-layer and the H-layer has no reason to exist, the same way a power adapter serves no purpose without the device it adapts. An H-layer with no adjacent S-layer is just a D-layer. This is where system designers have the most leverage over agent reliability.

Concrete H-layers you've probably already built:
- **Input harness (D→S):** System prompt assembly, identity injection, prompt templates. The CLI wrapper, the API preprocessor, the server that puts prompt content together before the LLM sees it. Some things delivered *through* the H-layer are themselves D-layer constraints: structured reasoning formats, context window caps, and behavioral instructions are all rule-based in intent. They just happen to be expressed through the H-layer.
- **Output harness (S→D):** Response extraction, format cleanup, structured output enforcement. The H-layer's job here is thin - translating S-layer output into a form that D-layer code can work with. The actual validation logic (JSON parsing, parameter checking, retry decisions) is D-layer code with its own purpose.

The alternation IS the search algorithm: expand (S) → narrow down (D) → expand (S) → narrow down (D). This matches a general pattern in problem-solving: generate possibilities, then prune the bad ones. Generate, prune. Generate, prune.

### The Alternation Pattern

```
... - D - H - S - H - D - H - S - H - D - ...
```

In practice, D-S transitions almost always pass through some kind of H-layer - though the thickness of the boundary varies a lot. A raw API call to an LLM with a hardcoded string has a near-zero boundary. A full orchestration system with system prompts, tool permissions, and appended instructions has a thick one. Every AI vendor ships their own boundary tools (system message fields, function calling specs, structured output schemas), and every orchestration framework wraps them with more.

The point isn't that every system *must* follow a rigid pattern, but that they *do* - and the thickness of the constraint boundary between dumb code and smart code affects reliability:
- Thin or absent constraints → the S-layer gets less guidance, more freedom, more variance
- Thick constraints → the S-layer is more boxed in, more predictable, more reliable
- The sweet spot depends on the task - creative writing needs thin boundaries; production code generation needs thick ones

### Why This Framing Matters

The search idea explains things that a purely architectural view can't:

**Context preservation.** Each S-layer's reasoning depends on its context - the accumulated state from prior search steps. If context gets polluted (irrelevant info leaks in) or lost (relevant info gets dropped), the S-layer searches in the wrong part of the solution space. Context pollution isn't just an efficiency problem - it's a *search corruption* problem. In the pastry analogy: if the dough layers have holes, the steam escapes instead of creating lift.

**Chat layer separation.** Modern orchestration systems put a coordinator between the user and the actual worker agents. The user interacts with a chat layer while work happens in isolated sub-agents. This exists because the coordinator's context and each sub-agent's context represent *different search branches*. Mixing them would corrupt both searches.

**Clean sub-agent contexts.** When a coordinator hands off work to a child agent, the child should only get the context relevant to its subtask. This is search branch isolation: each child explores its own subspace without contamination from sibling branches.

**Diminishing returns on prompting.** The prompt is an H-layer that often carries D-layer constraints - behavioral rules that are rule-based in intent but can only be expressed in natural language. Improving these constraints improves the *initial search direction*. But up-front constraints alone can't prevent drift. If the D-layers don't also constrain the space as the search goes on - with validation, tool restrictions, and structural enforcement - the S-layer will wander. Reliability needs ongoing constraint at every boundary, not just a good starting point.

---

## Knowledge vs. Evidence: Two Ways to Constrain

The input/output split in D→H→S and S→H→D layers maps to a deeper divide: **before-the-fact** versus **after-the-fact** constraint.

**Before-the-fact constraints (input D-layer + H-layer)** inject knowledge *before* the smart code acts. The D-layer provides raw material - files on disk, database records, configuration, tool definitions. The H-layer assembles it into something the S-layer can use - system prompts, identity files.

Some of this assembled content is itself D-layer in nature: structured reasoning formats and example templates are rule-based, just delivered through the H-layer. Together, they front-load the solution space with prior knowledge. In Bayesian terms, the input stack sets the **prior distribution**: "you are a senior developer," "always create feature branches," "use this scoring rubric." The S-layer's search is then conditioned on this prior.

**After-the-fact constraints (output H-layer + D-layer feedback)** filter results *after* the smart code acts. Structured output schemas reject badly formatted responses. Tool call validators reject invalid parameters. Type checkers reject incorrect code. Test suites reject wrong behavior. These are **posterior updates** (corrections based on what actually happened) - the system observes what the S-layer actually produced and constrains the accepted solution space based on evidence, not belief.

This distinction explains several things that practitioners discover the hard way:

1. **Prompts have diminishing returns.** Adding more instructions to a system prompt follows a pattern of diminishing value. Each instruction competes for the model's attention, the S-layer may fail to follow all constraints at once, and contradictory priors can paralyze the search. You can't prompt your way to reliability.

2. **Tools and automated checks beat prompts.** A `pnpm test` failure is an objective signal; "always write tests" in the system prompt is a hope. After-the-fact constraints are grounded in what actually happened, not what you hoped would happen.

3. **Some rule-based constraints can only be expressed before the fact.** The constraint "always consider edge cases before writing code" can't be enforced after the fact - there's no gate after the model runs that checks "did the model actually think about edge cases?" The only channel is the system prompt. The H-layer becomes the sole carrier of what is really a D-layer constraint - rule-based in *intent*, delivered to the S-layer through the prompt. This is why boundary engineering matters so much: for many behavioral constraints, the prompt is the only enforcement tool you have.

4. **The most reliable systems use both.** Before-the-fact constraints narrow the search space before exploration begins. After-the-fact constraints prune invalid results after exploration produces them. Neither alone is enough.

### Tool Use Is Iterative Search

Agents often use tools in a **trial-and-error** pattern: run a command, observe the output, adjust, retry. This isn't a failure mode - it's the S-layer running an **iterative search** using D-layer tools as test queries.

```
S: form hypothesis → H: format tool call → D: run tool → H: format result → S: update beliefs → ...
```

The S-layer is **testing one guess at a time** - forming a hypothesis, querying the D-layer to test it, using the dumb code's answer to update its beliefs and form the next guess. Each tool call is chosen to get the most information. The "search tree" is implicit in the context window's accumulated observations.

What this means in practice:

- **Prior knowledge reduces trial and error.** An agent with a detailed system prompt about the codebase doesn't need to `grep` for every file. The prior narrows the search space.
- **Real evidence is often more reliable than prior knowledge.** The agent that *reads* actual file contents has ground truth; the agent relying on a system prompt description has potentially stale beliefs. When an agent runs `ls` before acting, it's choosing to gather real evidence - a sensible search strategy.
- **Too much trial and error *may* point to weak input constraints.** An agent that "just tries stuff" might lack prior knowledge - or it might be rationally choosing to gather evidence because the prior is outdated. The distinction matters: if the codebase changes often, up-front descriptions go stale. Sometimes the right fix isn't a thicker input harness but better D-layer tools that make evidence-gathering cheap.

**Before-the-fact constraints inject knowledge. After-the-fact constraints inject evidence.** Knowledge tells the agent where to look. Evidence tells the agent what it found. Intent emerges in between.

---

## The Reference Stack

Here's what LIM looks like in a typical agent orchestration system:

```
          ... USER .............. (external) Starts the search, provides intent, judges results
Layer 5:  PRESENTATION ......... (D) Chat UI, session management, routing, state aggregation
         --- Coord Harness --- (H) Synthesis templates, delegation prompt wrapping
Layer 4:  COORDINATOR .......... (S) Task breakdown, delegation, strategy
         --- Orch Harness ---- (H) Prompt assembly, output parsing
Layer 3:  ORCHESTRATION ........ (D) Queues, state machines, lifecycle management, routing
         --- Agent Harness --- (H) System prompt assembly, identity injection
Layer 2:  AGENT ................ (S) LLM reasoning, code generation, analysis
         --- Tool Harness ---- (H) Tool call formatting, result extraction
Layer 1:  TOOLS ................ (D) File system, shell, APIs, MCP servers, databases
```

Layer numbers are just labels, not a strict hierarchy. Unlike OSI, LIM layers don't strictly contain each other - a Layer 4 coordinator may use Layer 1 tools directly, with the right harness layers in between.

The user is deliberately **outside** the layer model. While humans are unpredictable, the user is the *environment* the system serves - the source of intent and the final judge - not a component to be engineered. LIM models the system boundary; the user sits outside it. The Presentation layer is the system's outer edge, translating between human intent and internal contracts.

In human-in-the-loop systems, the user doesn't act as an S-layer *within* the architecture - they're the outside force that triggers *repeated runs* of it. Each cycle of "say what you want → see the result → adjust and try again" is a separate pass through the full D-H-S-H-D stack. The user judges each pass and decides whether to accept or iterate. This is how Vibe Kanban works: drag a card to In Progress (invoke the stack), review the agent's output (evaluate), move to Done or rework (iterate). The user drives the outer search loop; the agent system drives the inner one.

### Communication Channels

Each transition between layers is a **communication event** that travels through some transport medium. The choice of transport at each boundary matters architecturally:

| Boundary | Typical Transports | Constraint Properties |
|---|---|---|
| User ↔ Presentation | WebSocket, HTTP | Two-way, session-scoped |
| Presentation ↔ Coordinator | Internal or HTTP | Low-latency, typed events |
| Coordinator ↔ Orchestration | Message queue (BullMQ), HTTP | Durable, async, survives restart |
| Orchestration ↔ Agent | CLI stdio, HTTP API, process spawn | Serialized, one-shot or streaming |
| Agent ↔ Tools | MCP protocol, CLI, HTTP API | Schema-typed, request-response |

This mirrors OSI's insight: the *choice of transport* between layers affects system behavior even when the layers themselves are correct. An agent system using durable message queues between orchestration and agent layers behaves differently from one using short-lived process spawns - even with identical D, S, and H layers.

The communication channel determines what can go wrong, how fast things are, and how well-separated the layers are from each other. Running an agent in a separate process means it can't corrupt the orchestrator's memory - that's a stronger constraint than any D-layer validation logic you could write.

### Recursive Lamination

The model is fractal - it repeats at every level of zoom. Any S-layer, looked at closely, reveals its own internal laminated stack:

```
User
 └─ Coordinator (S)              ← explores how to break down the task
     ├─ [D: orchestration routes to child A]
     │   └─ Agent A (S)          ← explores subtask A's solutions
     │       ├─ [D: tool execution]
     │       └─ [D: result validation]
     ├─ [D: orchestration routes to child B]
     │   └─ Agent B (S)          ← explores subtask B's solutions
     └─ [D: synthesis merges results]  ← constrains the combined space
         └─ Synthesizer (S)      ← explores how to merge
```

The graph structure is a D-layer. The delegation structure - which agents exist, how they connect - is dumb code that constrains which S-layers can interact and prevents search branches from contaminating each other.

Each node is a full LIM stack. Context isolation is search branch isolation. Communication channels recur at each level. This self-similarity (the same pattern repeating at every scale) is what makes agent systems pluggable - a "tool" at one level is itself a laminated agent system at a lower level, and you can snap them together like building blocks.

---

## Nature Got There First

The alternation of dumb and smart processing isn't a software invention - biological evolution arrived at it independently.

The human visual system is the clearest example. Before your brain does any "smart" visual processing - recognizing faces, reading words, judging distances - every piece of visual information passes through a **fixed, dumb preprocessing pipeline**: the retina, a relay station (LGN), and the primary visual cortex (V1).

V1 contains cells that work like a bank of tuned filters. Each cell responds to a specific orientation (horizontal lines, vertical lines, diagonals) at a specific frequency and size scale - not just detecting edges, but performing a local frequency analysis that achieves the mathematically optimal decomposition of visual information (Daugman, 1985; Jones & Palmer, 1987). This is a fixed mathematical operation - the same in every person, locked in after early childhood.

The critical point is that this preprocessing is:
- **Locked in** after early childhood - it doesn't change based on what you're looking at
- **The same** across all normally-sighted people
- **Unavoidable** - there is no pathway that delivers raw visual input to the thinking parts of the brain. Everything passes through these fixed filters first.
- **Mathematically optimal** - evolution converged on the best possible set of filters

Only after this dumb preprocessing do the higher brain areas kick in - doing flexible, context-dependent, probabilistic reasoning. This smart processing is highly variable, shaped by attention and what you're trying to do, and takes much longer (100-500ms vs. the 50ms dumb stage).

| Visual System | LIM |
|---|---|
| Retina/LGN/V1 (tuned filters) | D-layer: fixed transform constraining input |
| Unavoidable architecture | The boundary between dumb and smart is mandatory, not optional |
| Higher brain areas | S-layer: adaptive, probabilistic reasoning |
| Feedforward sweep (~50ms, stereotyped) | D-layer: fast, dumb, one-direction signal flow |
| Recurrent processing (~100-500ms, variable) | S-layer: slow, smart, feedback loops |

Even the most powerful pattern recognition system in nature imposes a **non-negotiable dumb transform** before any smart processing happens. The brain doesn't re-learn its visual filters for each task. It constrains the search space with an optimal fixed basis, then lets higher areas explore flexibly within that constrained version of reality.

---

## Six Design Principles

### 1. Search Constraint
*D-layers don't just "support" S-layers - they prune the solution space. Without constraints, S-layers search an unbounded space and produce garbage.*

Hallucination is searching outside the valid solution space. Inconsistency is searching different regions each time. Infinite loops are searching the same region over and over. Every common agent failure mode is a *search problem* caused by not enough constraint.

When an agent system produces bad results, the fix is almost always to add or strengthen a D-layer constraint, not to improve the S-layer. The S-layer is already exploring - the problem is that it's exploring the wrong space.

### 2. Lamination
*Useful capability comes from the alternation of dumb and smart layers, not from either alone.*

A system with only D-layers is classical automation - powerful but brittle. A system with only S-layers is raw LLM output - capable but unreliable. The lamination produces systems that are both, because each layer type makes up for the other's weakness.

Merging two D-layers into one, or two S-layers into one, doesn't reduce capability. But removing a layer that breaks the alternation pattern hurts reliability.

### 3. Boundary Primacy
*The boundary between dumb and smart code is the most important engineering surface. Most agent failures start at this boundary, not in the S-layer itself.*

This is the baker's hands principle. A beginner baker with great butter and flour produces a dense mess. A skilled baker with average ingredients produces something flaky and light. The difference isn't the ingredients - it's the folding technique, the temperature control, the number of turns. In agent systems, the difference between a reliable system and an unreliable one is rarely the model - it's how constraints reach the model and how its output gets checked.

When an LLM produces inconsistent results, the instinct is to blame the model. But inconsistency is its nature - that's what makes it smart code. The engineering question is whether the constraints reaching the S-layer - both D-layer structure and H-layer mediation - sufficiently limit the output space.

Recent work on semi-formal reasoning showed 78% → 93% accuracy improvement just by adding a structured reasoning template. The template is a D-layer constraint (a fixed format) delivered through the H-layer (the prompt). Same model, same weights. Only the constraint boundary changed.

When debugging agent unreliability, start at the boundaries between dumb and smart code. The model is probably fine. The constraints probably aren't reaching it well enough.

### 4. Thickness
*Thin S-layers with thick D-layer constraints produce more reliable systems than thick S-layers with thin constraints.*

A "thick" S-layer is one where the LLM is asked to do many things in a single pass: plan, execute, validate, and format. A "thin" S-layer does one thing. Thick S-layers compound errors from the smart code. Thin S-layers, interleaved with D-layers, contain errors to single steps.

This is why ReAct works: it thins the S-layer to one reasoning step at a time, with D-layers (tool execution, observation formatting) between each step.

### 5. Constraint Surface
*The boundary between dumb and smart code defines a set of rules that limits what the adjacent S-layer can do. System reliability is proportional to the total rule coverage.*

Constraint surfaces include D-layer constructs - structured output schemas, reasoning templates, tool whitelists, validation logic - some enforced directly by dumb code, others expressed as D-layer constraints delivered through the H-layer (like system prompt instructions). More rule coverage generally improves reliability - but rules can also create dead ends. Like walls in a maze, each constraint channels the search toward better solutions, but poorly placed walls can box the S-layer into regions where no good answer is reachable from its starting point. The constraints aren't contradictory - they're individually reasonable - but collectively they eliminate all viable paths. The engineering art is choosing constraints that funnel toward solutions rather than wall them off.

### 6. Interface Contract
*Every boundary between dumb and smart code defines a typed contract between them. The quality of this contract determines how easily you can plug layers together.*

Well-typed contracts enable layer substitution: swap Claude for Gemini (S-layer) without changing the orchestration (D-layer) or presentation (D-layer), because the H-layers translate between them. Poorly-typed contracts (raw text in, raw text out) make layers tightly coupled.

---

## When Lamination Fails

Every common agent failure maps to a specific layer violation:

| Failure Mode | LIM Diagnosis | Layer Violation |
|---|---|---|
| **Hallucination** | S-layer generating content without D-layer grounding | Missing D-layer (no retrieval system providing factual data) |
| **Prompt injection** | External input bypassing the boundary to manipulate the S-layer | Missing D-layer sanitization before input reaches H-layer |
| **Inconsistent results** | S-layer variance not limited by output D-layer constraints | Missing output D-layer (no structured schema enforcement) |
| **Brittle automation** | D-layer handling cases it should hand off to an S-layer | Missing S-layer where the system needs one |
| **Tool misuse** | S-layer calling D-layer tools incorrectly | Not enough D-layer validation on tool parameters |
| **Infinite loops** | S-layer repeatedly calling the same D-layer action | Orchestration D-layer lacks cycle detection |
| **Context overflow** | S-layer receiving more than it can process | D-layer not enforcing context window caps |
| **Delegation failure** | Coordinator S-layer breaking down tasks poorly | Missing D-layer constraints on delegation format |
| **Context pollution** | Sibling context leaking across S-layers | Missing D-layer isolation boundary |
| **Search drift** | S-layer gradually leaving the valid region | Not enough D-layer checkpoints |

Each failure has a *layer-appropriate* fix. Hallucination needs grounding data from a D-layer, not "better prompting." Inconsistent results need tighter D-layer constraints (structured schemas, validation), not a model swap. The model points you to the correct layer for the fix.

In the pastry analogy, every failure mode is steam escaping through the wrong place - holes in the dough (missing D-layer constraints), layers that weren't folded tightly enough (weak boundaries), or butter that was too warm when it went in (an S-layer given too much freedom before the structure was ready).

---

## Real Systems, Real Lamination

These three systems weren't designed with LIM in mind - each was built as a step forward in the problem of creating a good harness for AI agents. The theory came after, when we noticed the same patterns kept showing up. Looking back through the LIM lens, the progression tells a story about how lamination emerges from practical need.

*A note on scope: these case studies come from a single team's work. We saw the same layering patterns showing up independently in LangChain, CrewAI, DSPy, and other frameworks (as noted in the introduction), which motivated the search for a shared model. Systematic LIM analysis of independently developed systems is left as future work.*

### Vibe Kanban: Where the Layers Start

Vibe Kanban started as a simple idea: a Kanban board where AI agents execute the tasks. Drag a card into "In Progress," pick an executor (Claude, Codex, Gemini, Amp - nine options and counting), and the agent works the task.

The lamination is thin but real. The UI, SQLite database, task state machine, and file I/O are all D-layer - dumb code managing state. The agent executor is the S-layer. Between them, CLI wrappers format the prompt differently for each model (H). Below the agent, git worktrees (D) isolate each task's filesystem so agents can't step on each other's work.

The interesting LIM observation is the multi-executor support. Because the H-layer defines a consistent contract - here's your task, here are your tools, here's your workspace - the S-layer becomes swappable. Swap Claude for Gemini without touching the task management code. That's the Interface Contract principle in action: when the harness is well-designed, the smart code is a plug-in.

The limitation is also visible through the LIM lens. There's no coordinator S-layer - task breakdown is entirely manual. The user creates the tasks on the board. The D-layer constraints are thin - no structured reasoning formats, no scoring criteria, no output validation. Vibe Kanban works great for well-defined, single-step tasks, but struggles with unclear work that needs breaking down. More lamination layers would help, but the system wasn't designed to need them.

### OpenClaw: Engineering the Constraint Stack

OpenClaw is a full agent orchestration platform - an always-on personal AI framework with multi-channel presence (WhatsApp, Telegram, Discord, Slack, iMessage, and dozens more), persistent memory, proactive scheduling, and multi-agent support. But the LIM-relevant insight isn't its breadth of features - it's how it engineers the constraint stack feeding into the S-layer.

At its core, OpenClaw builds agent system prompts from a stack of markdown files - personality, tools, identity, user preferences, memory - plus runtime data. The D-layer reads these files from disk and applies size limits. The H-layer assembles them into a single system prompt, filtering how much each agent gets to see. Then the whole thing ships to the model.

What makes OpenClaw interesting through the LIM lens is that its personality instructions - things like "never skip IN_REVIEW" or "always create feature branches" - work as D-layer constraints expressed through an H-layer medium. They're rule-based instructions disguised as prompt text. The agent registry is a lookup table (D-layer) that defines which agents can delegate to which. A settings toggle controls how much D-layer constraint material reaches each S-layer through the H-layer.

**But here's the gap: OpenClaw's D-layer checking is heavily weighted toward the input side.** It has real D-layer constraints - size limits, file filtering, prompt mode settings - but they're all before-the-fact. They shape the search space *before* the S-layer acts. On the output side, the D-layer checking is insufficient. The system prompt might say "never run destructive commands," but there's no dumb-code gate that blocks `rm -rf ~/` before it actually runs.

This isn't just theory. In 2025-2026, at least ten documented incidents across major AI coding tools followed the same pattern - an S-layer decided to run a destructive action, and no D-layer stopped it:

- **Claude Code** (December 2025): Ran `rm -rf tests/ patches/ plan/ ~/`, deleting a user's entire home directory. The trailing `~/` expanded via shell to the home directory. The system prompt said "be careful." The dumb code didn't care what the system prompt said. ([Willison, Dec 2025](https://simonwillison.net/2025/Dec/9/claude/); [GitHub #10077](https://github.com/anthropics/claude-code/issues/10077))
- **Replit AI Agent** (July 2025): Deleted a live production database during a code freeze, wiping records for 1,200+ executives. The agent admitted it "panicked" and "ran database commands without permission," then generated over 4,000 fake users to cover up the deletion. Feeling sorry about it afterward is not a D-layer. ([Fortune, July 2025](https://fortune.com/2025/07/23/ai-coding-tool-replit-wiped-database-called-it-a-catastrophic-failure/); [AI Incident Database #1152](https://incidentdatabase.ai/cite/1152/))
- **Amazon Kiro** (December 2025): Deleted and recreated an entire AWS production environment, causing a 13-hour outage of AWS Cost Explorer. The deploying engineer had broader permissions than typical, and Kiro inherited those elevated privileges - a D-layer misconfiguration that no H-layer could make up for. ([Financial Times, Feb 2026](https://www.ft.com/content/kiro-aws-outage); [Engadget](https://www.engadget.com/ai/13-hour-aws-outage-reportedly-caused-by-amazons-own-ai-tools-170930190.html))
- **Claude Cowork** (February 2026): Asked to organize a desktop, the agent deleted a folder containing 15 years of family photos - kids growing up, weddings, travel. The files were removed via terminal (bypassing Trash) and the deletion had already synced to iCloud before the user noticed. Recovery required an Apple support call to restore from a backup. ([Futurism, Feb 2026](https://futurism.com/artificial-intelligence/claude-wife-photos); [Davidov, X/Twitter](https://x.com/Nick_Davidov/status/2019982510478995782))

Every one of these is the same failure: **strong before-the-fact constraints with insufficient after-the-fact enforcement.** The system prompt says "don't delete production data." The agent agrees. Then it deletes production data anyway, because the dumb code between the decision and the irreversible action isn't thick enough. The dough was carefully laminated on the input side, but on the output side there was a gap - and the steam blew straight through it.

The fix is not better prompts. The fix is adding S→H→D validation: command whitelists, destructive action blockers, confirmation gates, sandboxed execution. Claude Code's permission system (`--dangerously-skip-permissions` is the flag's actual name - the naming is honest) is a D-layer gate between tool calls and execution. When users disable it for speed, they're removing the only after-the-fact constraint the system has. We intuitively know this - it's where most agent orchestration platforms add their restriction logic: the agent can't do X, Y, or Z.

### Huddle: When Lamination Goes Recursive

Huddle is where all of this comes together. It's a multi-agent orchestration platform where a coordinator agent breaks down tasks by delegating to specialist agents - each running in its own isolated context, communicating through prompt files on disk, jobs flowing through queues.

Huddle has six distinct lamination layers: a React UI (D) connects to a coordinator (S) through synthesis templates (H). The coordinator delegates through an orchestration layer (D) - queues, state machines, job lifecycle - which spins up individual agents (S) through their own identity stacks (H, using layered prompt assembly like OpenClaw's). Those agents run tools (D) against the filesystem and shell infrastructure (D), with D-layer validation and H-layer mediation at every boundary.

The key insight is **recursive lamination**. When a coordinator calls `send_prompt`, it spawns a new D-H-S-H-D stack for every child agent - each with its own identity stack, context window, and search branch. The delegation graph is a directed acyclic graph (a structure where work flows forward and never loops back, but results can reconverge - unlike a tree, a node can receive input from multiple parents). Each child can itself delegate, creating deeper recursion.

The dumb code does the heavy lifting: a state machine validates prompt transitions so agents can't skip steps, backpressure limits cap how wide and deep delegations can go, deduplication prevents message amplification on restart, and crash recovery markers let the system pick up where it left off when things go wrong. D-layer constraints control what each agent can see and do - a reviewer can't run `bash`, a coder can't delegate to other agents, and session mode (`fresh` vs `resume`) determines whether each run starts clean or carries history forward.

### The Evolutionary Pattern

None of this was planned. Each system added more lamination because practical needs forced it:

Vibe Kanban added worktree isolation because tasks were stepping on each other's files. OpenClaw added prompt modes and truncation caps because leaf agents didn't need the full identity stack, and multi-agent routing because a single agent couldn't cover all use cases. Huddle added context isolation because sibling agents were polluting each other's reasoning, and session mode emerged because some agents needed clean context while coordinators needed persistent state.

The progression traces a path from thin lamination to thick, from manual breakdown to recursive. The theory didn't drive the architecture. The architecture revealed the theory.

---

## Practical Implications

**Start from the boundary between dumb and smart code.** The baker plans the folding technique before buying flour. When designing a new agent capability, don't start with the model (S) or the infrastructure (D). Start with the constraint design: what D-layer constraints will limit the S-layer's behavior? What structured output must the S-layer produce, and what D-layer validation will enforce it? A concrete process: (1) define the output schema first - a D-layer contract for what a valid result looks like. (2) Work backward to the input - what D-layer data and constraints does the S-layer need? (3) Design the H-layer that delivers those constraints to the model. (4) Only then choose the model and write the prompt.

**Keep S-layers thin.** If an LLM call does more than one job ("analyze this code AND decide whether to approve it AND format the response"), split it into multiple thin S-layers with D-layer routing between them. Each fold in puff pastry is thin - you don't try to do all the lamination in one pass.

**Make D-layer constraints work regardless of which AI model you use.** A well-designed reasoning template is a D-layer constraint - a fixed format. It should improve both Claude and Gemini when delivered through the H-layer. If your constraints only work with one model, they're compensating for the model rather than constraining it. The baker's technique works regardless of the butter brand.

**Debug layer by layer.** When an agent system fails, identify which layer the failure starts in and fix it at that layer. Don't patch S-layer failures with D-layer workarounds (brittle) or D-layer failures with S-layer cleverness (unreliable). Use the failure mode table above to map symptoms to layers.

**Engineer constraint surfaces, not prompts.** "Prompt engineering" is a misnomer. What you're actually doing is rule-based constraint design - defining the rules, schemas, and formats that shape smart-code behavior. The prompt is just the H-layer channel that delivers those constraints. You're not writing a prompt; you're designing D-layer contracts that happen to be expressed through an H-layer medium.

**Balance before-the-fact and after-the-fact constraints.** Don't rely solely on the system prompt (input harness) - add dumb-code gates on the output side too. The incidents in the case studies all share one root cause: careful input lamination with no output lamination. Test suites, command whitelists, structured output validation, and confirmation gates are all after-the-fact D-layers. If your system has thick input harnesses but thin output harnesses, your lamination is lopsided.

---

## Open Questions

**Formal verification of constraint surfaces.** Can we prove that a given set of D-layer constraints - whether enforced directly or delivered through H-layers - is *enough* to limit S-layer behavior for a given type of task?

**Optimal lamination depth.** Is there a best number of alternating layers? Too few = unreliable. Too many = slow and expensive.

**Adaptive constraint tightness.** Can D-layer constraints automatically adjust based on how well the S-layer is performing? For example, loosening output validation when the model consistently produces valid results, or tightening it when error rates spike.

**Cross-system lamination standards.** If LIM is to serve the role OSI serves for networking, it needs standardized interface contracts - the "protocols" of agent system lamination.

**Testing the theory.** Can the model's predictions - thinner S-layers improve reliability, thicker D-layer constraints reduce variance, more alternation layers increase capability - be measured and benchmarked across independently developed systems? LIM is currently a descriptive model based on observation. Rigorous benchmarking would determine whether it has genuine predictive power or is just a useful vocabulary.

---

The Laminated Intelligence Model gives us a vocabulary for what practitioners already know by feel: that agent systems are built from dumb and smart layers, that the coupling between them is where engineering leverage lives, and that the alternation pattern is not a compromise - it's the mechanism that produces capability.

Like OSI, its value is not in telling you how to build things but in providing a shared way to break them down. When a team can say "that's an H-layer problem" and everyone knows what it means, the field has taken a step forward.

The case studies show that these layers emerge whether you have the theory or not. Every orchestration framework eventually discovers the need for structured output schemas (D-layer constraints), dumb validation gates (D-layer), and thin agent calls (S-layer thickness), with H-layers bridging at every boundary. The theory just tells you why, and saves you the months of pain it takes to rediscover each principle through failure.

The puff pastry isn't the dough. It isn't the butter. It's the lamination - and it's the baker's hands that make it right. The steam will do the rest.

---

## Appendix: Glossary

- **D-layer (Deterministic):** Code with no randomness. Tools, infrastructure, state machines, routing.
- **S-layer (Stochastic):** Code with built-in non-determinism - given the same input, it may produce different outputs. LLMs, human judgment, learned policies.
- **H-layer (Harness):** Rule-based code that sits between D and S layers - the glue. Prompt assembly, output extraction. No standalone purpose - remove the adjacent S-layer and the H-layer has no reason to exist.
- **Lamination:** The alternating D-H-S-H-D pattern.
- **Constraint surface:** The set of D-layer rules limiting what an adjacent S-layer can do - some enforced directly by dumb code, others delivered through H-layers.
- **Recursive lamination:** The fractal property where any S-layer reveals its own internal laminated stack.
- **Layer thickness:** How much responsibility a single layer has. Thin = one job. Thick = many.

## Appendix: Further Reading

### Foundational Theory

1. **OSI Reference Model** - ISO 7498-1:1994. The classic layered abstraction for networked systems.

2. **Wiener, N.** *Cybernetics* (1948). Foundational theory of feedback systems mixing intelligent control with mechanical actuation.

3. **Ashby, W.R.** *An Introduction to Cybernetics* (1956). Law of Requisite Variety: a control system must match the complexity of what it controls.

4. **Minsky, M.** *The Society of Mind* (1986). Agents as compositions of simpler agents.

5. **Brooks, R.A.** "A Robust Layered Control System for a Mobile Robot." *IEEE J. Robotics and Automation*, 1986. Subsumption architecture: layered control alternating reactive and deliberative layers.

### Biological Precedent

6. **Hubel & Wiesel.** "Receptive fields in cat visual cortex." *J. Physiology*, 1962 (Nobel Prize 1981). V1 simple cells as fixed edge/bar detectors.

7. **Daugman, J.G.** "Uncertainty relation for resolution optimized by 2D visual cortical filters." *JOSA A*, 1985. V1 cells converge on the mathematically optimal Gabor basis.

8. **Jones & Palmer.** "Evaluation of the 2D Gabor filter model." *J. Neurophysiology*, 1987. Empirical validation that V1 cells *are* Gabor filters (r > 0.9).

### Modern Agent Frameworks

9. **Yao et al.** "ReAct: Synergizing Reasoning and Acting in Language Models." *ICLR 2023.*

10. **Khattab et al.** "DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines." *ICLR 2024.* https://dspy.ai/

11. **Wei et al.** "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models." *NeurIPS 2022.*

### Agent Architecture (2024-2025)

12. **Verdecchia et al.** "SALLMA: Software Architecture for LLM-Based Multi-Agent Systems." 2025.

13. **Comprehensive Survey on LLM Agent Architectures.** arXiv:2510.25445, 2025.

14. **Architecting Resilient LLM Agents.** arXiv:2509.08646, 2025.

15. **Fundamentals of Building Autonomous LLM Agents.** arXiv:2510.09244, 2025.

16. **From Prompt-Response to Goal-Directed Systems.** arXiv:2602.10479, 2025.

### Deterministic/Stochastic Hybrid Architectures

17. **Praetorian.** "Deterministic AI Orchestration." 2025. Closest existing work to LIM - LLM as "nondeterministic kernel wrapped in a deterministic runtime."

18. **Multi-Agent LLM Orchestration for Incident Response.** arXiv:2511.15755, 2025. 100% actionable rate via deterministic orchestration vs 1.7% for single-agent.

19. **Blueprint First, Model Second.** arXiv:2508.02721, 2025.

### Semi-Formal Reasoning

20. **Agentic Code Reasoning via Semi-Formal Verification.** arXiv:2603.01896, 2025. Structured reasoning templates improve accuracy from 78% to 93%.

### Neuro-Symbolic AI

21. **Neuro-Symbolic AI in 2024: A Systematic Review.** arXiv:2501.05435, 2025.

22. **ATA: Neuro-Symbolic Approach to Autonomous Agents.** arXiv:2510.16381, 2025.

### Cognitive Architectures

23. **Anderson & Lebiere.** *The Atomic Components of Thought* (ACT-R, 1998).

24. **Laird, J.E.** *The Soar Cognitive Architecture* (2012).

### Industry Commentary

25. **Lipton, J.** "Deterministic LLM Development with an SSOT Workflow." Substack, 2025.

26. **Greyling, C.** "AI Agent Architectures." Substack, 2025.

27. **Greyling, C.** "Three AI Agent Architectures Have Emerged." Substack, 2025.

28. **Stack Overflow.** "Reliability for Unreliable LLMs." 2025.

### Supplementary

29. **Sutton, R.** "The Bitter Lesson." 2019. LIM suggests the interleaving of general methods and hand-crafted structure is what actually works.

30. **SE-ML.** "Engineering LLM-Based Agentic Systems." 2025.
