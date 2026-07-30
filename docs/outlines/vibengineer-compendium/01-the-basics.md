---
title: "The Vibengineer Compendium, Part I: The Basics — A Philoso-Process for LLM Coding"
series: "The Vibengineer Compendium"
part: 1
status: outline
draft: true
---

**Working title:** The Vibengineer Compendium, Part I: The Basics — A Philoso-Process for LLM Coding

## Thesis

- What if a vibengineer is simply someone who uses generative tools to move fast while still owning the consequences?
- This is a working label, not an industry-standard job title. Do we need a cleaner definition before publication?
- Working claim to validate: treat the model as an eager search engine for plausible code and ideas, not as the accountable engineer in the room.

## Reader promise

- Give the reader a small operating loop for the next AI-assisted task without making them adopt somebody else's ritual.
- Make “go faster” mean faster learning and verification, not faster accumulation of code.
- Scope boundary: this part governs how to frame and control one task before and during execution. Part III deals with what to retain, simplify, or delete after generated code starts accumulating.

## Argument

- Premise check: before asking for an implementation, what problem are we actually solving, for whom, and should it exist at all?
- Context control: what is the minimum trustworthy context the model needs, and what noise or stale instructions should stay out?
- Reusable skills: which repeated tasks deserve a compact instruction or tool wrapper, and which are too rare to justify one?
- Smallest useful setup: perhaps one repository map, a short test command, and a few task-specific skills. What earns its place?
- Ceremony warning: does every extra checklist, role, or framework make a decision clearer, or is it just process cosplay?
- Plans: can the work be split into observable steps with an explicit stopping point?
- Checkpoints: where do we pause to inspect the diff, re-check the premise, and choose whether to continue, redirect, or delete?
- Evidence: what result would count as proof of the intended behavior, rather than a confident-looking model response?

## Candidate examples and diagrams

- Candidate process-loop diagram:

  ```text
  premise check -> bounded context -> plan -> generate/search -> checkpoint
        ^                                                    |
        |---- evidence <- test/inspect <- revise/delete -----|
  ```

- Candidate tiny-change example: turn a vague “add this feature” prompt into a one-paragraph premise, a narrow file map, one testable claim, and a checkpoint.
- Candidate contrast: a skill that captures a recurring release check versus a giant “engineering agent” setup nobody can explain or maintain.

## Claims requiring evidence

- Evidence note: do small, named skills measurably improve repeatability, or do they mainly help teams communicate intent? Find studies or label this as experience.
- Evidence note: what research supports treating language models as search/selection systems rather than accountable decision-makers?
- Question: are there useful examples of teams cutting agent ceremony and getting better reviewability as a result?

## Non-goals

- Not a claim that every coding task needs an agent, a skill, or a formal plan.
- Not a permission slip to delegate responsibility to a model.
- Not a universal workflow; the point is a minimum useful control surface, not a new bureaucracy.

## Series connection

- This part sets up process, but process only helps when the codebase has somewhere unambiguous for a change to land.
- Next: architectural patterns — one canonical authority per overlapping concern, or the model can generate two convincing answers and the process cannot tell which is real.

## Open drafting questions

- Is “vibengineer” worth keeping as a deliberately loose label, or should the opening spend less time defending it?
- Which concrete personal example best shows a premise check preventing needless work?
- How rough should the model-as-search-engine analogy be before it starts obscuring important differences?
- Can the final transition say, plainly: good process cannot rescue competing code authorities?
