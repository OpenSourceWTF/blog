# The Vibengineer Compendium — Series Design

## Purpose

Create a connected five-part blog series about using LLM coding agents without surrendering engineering judgment. The series should feel like one argument unfolding, while each article remains useful when linked on its own.

The series is aimed at experienced developers who already use coding agents and have discovered that generating more code is much easier than keeping the resulting system understandable.

## Voice

- Informal, opinionated, and technically specific.
- Slightly rough rather than polished into marketing copy.
- Joke-friendly titles, followed by concrete engineering arguments.
- Short enough to read without treating the subject like a textbook.
- Prefer real failure shapes, commands, diffs, and diagrams over abstract advice.

## Connected Argument

The series progresses in this order:

1. Establish a disciplined operating philosophy.
2. Give the codebase clear authority and ownership boundaries.
3. Evolve the code through a repeated expansion and reduction loop.
4. Build tests that preserve behavior without fossilizing generated code.
5. Separate disciplined vibengineering from actual vibecoding.

Each article should briefly inherit the conclusion of the previous article, add one new layer, and end by creating the problem addressed in the next.

## Article 1: The Basics

### Working title

**The Vibengineer Compendium, Part I: The Basics — A Philoso-Process for LLM Coding**

### Thesis

The model is not the engineering process. Reliable LLM coding comes from the constraints, context, checkpoints, and judgment surrounding the model.

### Reader promise

The reader leaves with a small operating model for working with coding agents and a practical way to set up reusable skills without building a ceremony machine.

### Outline direction

- What a vibengineer is
- The model as an eager search engine, not an accountable engineer
- Premise before plan
- Context as working memory, not a landfill
- Skills as reusable process constraints
- Plans, checkpoints, and evidence
- The smallest useful setup
- Handoff: a good process still cannot save a codebase with competing authorities

## Article 2: Architectural Patterns

### Working title

**The Vibengineer Compendium, Part II: Establish Ownership and Dominance Before Your Code Goes Out of Control**

### Thesis

LLM-generated systems stay coherent only when every important concern has one obvious owner and dependency direction makes that authority difficult to bypass.

### Reader promise

The reader learns how to make a repository legible to both humans and agents: canonical modules, narrow interfaces, dependency direction, controlled duplication, and explicit deletion paths.

### Outline direction

- Why agents multiply plausible implementations
- Ownership: one concern, one authoritative home
- Dominance: which abstraction wins when two overlap
- Dependency direction and narrow interfaces
- Locality: keep related truth close together
- Duplicate code as competing authority
- Architectural tests and repository maps
- Handoff: once the code has a shape, it needs a safe way to change

### Terminology note

“Dominance” refers only to authority inside the codebase: which module, contract, or representation is canonical. The article should define this immediately so it cannot be mistaken for team hierarchy or performative machismo.

## Article 3: Keep Your Code Healthy

### Working title

**The Vibengineer Compendium, Part III: Keep Your Code Healthy — Plan, Expand, Analyze, Reduce**

### Thesis

Healthy AI-assisted development is a loop: decide what should exist, explore enough alternatives, inspect what was produced, and then remove the accidental complexity.

### Reader promise

The reader gets a repeatable change cycle that permits useful exploration without allowing every generated idea to become permanent architecture.

### Outline direction

- Code generation creates entropy faster than understanding
- Plan: define the claim, boundary, and evidence
- Expand: generate options and learn from implementation
- Analyze: compare behavior, architecture, cost, and surprise
- Reduce: delete scaffolding, duplicates, dead branches, and false abstractions
- Repeat at feature, module, and repository scale
- A worked before-and-after example
- Handoff: reduction is unsafe without tests that distinguish behavior from implementation

## Article 4: What the Test

### Working title

**The Vibengineer Compendium, Part IV: What the Test — On Big-Ass Vibe Test Suites**

### Thesis

Large generated test suites are not automatically strong test suites. Tests should protect observable behavior and architectural boundaries while remaining cheaper to understand than the code they constrain.

### Reader promise

The reader learns what to test, what not to test, how to stage targeted and full-suite verification, and how to detect tests that merely mirror generated implementation details.

### Outline direction

- How agents turn uncertainty into test volume
- Behavior, contracts, invariants, and boundaries
- Regression tests that prove the bug existed
- Targeted tests during iteration
- Full suites as gates, not nervous tics
- Mutation, deletion, and counterexample thinking
- When the test suite is larger than its evidence
- Handoff: disciplined verification is vibengineering; sometimes you actually want to vibe

## Article 5: Addendum

### Working title

**The Vibengineer Compendium, Addendum: On Actual Vibecoding**

### Thesis

Actual vibecoding is improvisational and intentionally under-controlled. It is useful for discovery, sketches, and disposable tools, but it should not be mislabeled as a production engineering method.

### Reader promise

The reader gets a practical boundary between exploration and accountable engineering, including ways to turn a successful vibe prototype into owned, tested code.

### Outline direction

- Vibecoding is real, and it is fun
- Why attempts to redefine it as ordinary engineering miss the point
- Good habitats: sketches, probes, throwaways, and one-person tools
- Bad habitats: shared authority, durable data, money, secrets, and production
- The prototype graduation test
- When to keep the weird little script
- Closing argument: vibe freely, engineer deliberately

## Stub Format

Create one Markdown outline per article under:

`docs/outlines/vibengineer-compendium/`

Each stub will contain:

- Draft frontmatter clearly marked as unpublished
- Working title
- Thesis
- Reader promise
- Section-by-section outline
- Candidate examples or diagrams
- Claims requiring evidence
- Non-goals
- Previous/next article connection
- Open drafting questions

The stubs will not live under `posts/`, so they cannot be mistaken for publishable articles.

## Scope

### Included

- Five connected outline stubs
- Consistent series terminology and progression
- Drafting prompts and evidence notes
- Cross-links between articles at the outline level

### Not included

- Finished article prose
- Publication dates
- Illustrations or image generation
- External research or citations
- Changes to the blog publishing system
- Publishing, committing, or pushing the outlines

## Failure-Mode Check

### Generic AI advice

**Severity:** Critical.

Every article must make a specific claim and include at least one concrete failure shape or worked example. Generic prompt tips are outside the series.

### Repetition across articles

**Severity:** Critical.

Each article owns one layer of the argument. Part I owns process foundations, Part II owns structural authority, Part III owns the change loop, Part IV owns evidence, and the addendum owns the exploration/production boundary.

### “Dominance” is misunderstood

**Severity:** Critical.

Define it at the start of Part II as canonical authority among code representations and modules. Do not apply it to people or teams.

### Outlines become prematurely polished drafts

**Severity:** Minor.

The first pass stays skeletal and question-driven. Voice work and final examples happen article by article.

## Acceptance Criteria

- Five outline files exist outside the publishing directory.
- Their order is Basics → Architecture → Code Health → Tests → Actual Vibecoding.
- Every outline has a distinct thesis and reader promise.
- Transitions make the five pieces read as one connected argument.
- Part II explicitly defines ownership and dominance as codebase concepts.
- No stub presents unverified examples or claims as established fact.
