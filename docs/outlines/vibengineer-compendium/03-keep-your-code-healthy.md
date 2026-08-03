---
title: "The Vibengineer Compendium, Part III: Keep Your Code Healthy — Plan, Expand, Analyze, Reduce"
series: "The Vibengineer Compendium"
part: 3
status: outline
draft: true
---

**Working title:** The Vibengineer Compendium, Part III: Keep Your Code Healthy — Plan, Expand, Analyze, Reduce

## Thesis

- Working claim to validate: generated code can become an entropy source, creating plausible paths faster than a team can understand or maintain them.
- The healthy loop: Plan, Expand, Analyze, Reduce. Keep turning it instead of treating the first working output as architecture.

## Reader promise

- Make room for useful AI-assisted exploration without keeping every weird little idea forever.
- Give the reader a repeatable change cycle for when code arrives faster than judgment.
- Scope boundary: Part I controls one AI-assisted task. This part governs what to retain, simplify, or delete across change, module, and repository scales.

## Argument

- Plan: What claim are we making, what boundary matters, and what evidence would tell us it worked?
- Expand: What options should we try to learn from? Expansion is exploration, not a retention policy.
- Analyze: Which option has the behavior, shape, cost, and surprise level we can actually live with?
- Reduce: What can go now that we know more—scaffolding, duplicate paths, dead branches, false abstractions, and unnecessary tests?
- At the change scale, what gets removed before this diff lands?
- At the module scale, what has become two ways to do one job?
- At the repository scale, what old experiments are still quietly defining the system?
- Planned before/after walkthrough:
  - Before: generate a small webhook handler that arrives with a compatibility wrapper, two parsing paths, a generic adapter factory, retry scaffolding, and tests coupled to every helper.
  - Analyze: which path handles the real payloads, which failures matter, and which tests describe behavior rather than the generated shape?
  - After Reduce: one parsing path, one explicit boundary, no unused retry branch or adapter factory, and a smaller set of contract tests that makes the deletion safe.

## Candidate examples and diagrams

- Candidate loop diagram: `Plan -> Expand -> Analyze -> Reduce -> Plan`, with change, module, and repository as three zoom levels around it.
- Could the diagram show that Expand temporarily increases surface area, while Reduce earns the right to make it smaller again?

## Claims requiring evidence

- What evidence supports the claim that code generation increases complexity faster than review capacity in real teams?
- Are there useful measures for accidental complexity: files touched, alternate paths, dependency count, test runtime, or review time?
- When do “unnecessary tests” become a risky phrase? Evidence note: separate tests that duplicate implementation trivia from tests protecting real behavior.

## Non-goals

- Not “delete aggressively because minimalism feels clean.”
- Not a claim that every generated alternative is bad; sometimes the ugly branch teaches us the thing worth keeping.
- Not a substitute for Part IV’s deep dive on what makes tests trustworthy.

## Series connection

- Part I set up the philoso-process. Part II mapped which module or contract is authoritative and how dependencies respect that boundary.
- This part asks: once you own it, how do you stop it from slowly turning into a haunted pile?
- The handoff to Part IV: deletion is only confident when tests tell behavior apart from implementation.

## Open drafting questions

- What is the smallest concrete before/after example that makes false abstractions and duplicate paths obvious?
- Should the worked example come from a UI change, an API integration, or a build/tooling task?
- How rough should the reduction checklist get before it starts reading like process cosplay?
- What does a trustworthy test suite need to prove so that deletion feels routine rather than heroic?
