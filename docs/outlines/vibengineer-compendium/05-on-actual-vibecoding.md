---
title: "The Vibengineer Compendium, Addendum: On Actual Vibecoding"
series: "The Vibengineer Compendium"
part: addendum
status: outline
draft: true
---

**Working title:** The Vibengineer Compendium, Addendum: On Actual Vibecoding

## Thesis

- Actual vibecoding is improvisational and intentionally under-controlled: follow the hunch, move fast, and accept that the result may be messy or disposable.
- The point is not to pretend this is production engineering. It is to use a different mode honestly.

## Reader promise

- Help readers recognize when a loose, fast build is useful and when it has crossed into something that needs deliberate engineering.
- Give them permission to make weird little scripts without turning every experiment into a tiny enterprise.

## Argument

- Why attempts to redefine vibecoding as ordinary disciplined engineering miss the useful distinction: the under-controlled improvisation is the point.
- Good habitats: sketches, probes, throwaways, one-person personal tools, and experiments where learning is the deliverable.
- Bad habitats: anything with shared authority, durable data, money, secrets, or production consequences.
- A concrete graduation test: if another person must rely on it, or if failure can permanently affect people, data, access, or cash, pause the vibe and deliberately re-scope, document, test, secure, and operate it.
- Weird little scripts can stay weird when their blast radius stays personal, reversible, and understood by the person running them.
- Closing argument to develop: vibe freely while exploring; engineer deliberately when the result starts to matter to anyone else.

## Candidate examples and diagrams

- A one-file script that sorts a personal downloads folder versus the same script pointed at a shared customer bucket.
- A quick interface probe with fake data versus a tool writing to a durable database.
- Exploration-to-engineering boundary diagram:

  ```text
  hunch -> sketch -> probe -> useful personal script
                                |
                                | shared reliance / durable consequences
                                v
                     engineer deliberately: ownership, boundaries,
                     tests, review, security, operations
  ```

## Claims requiring evidence

- What examples or postmortems best show a small internal convenience becoming an unowned production dependency? [research]
- Is there evidence for a practical threshold beyond shared reliance and irreversible consequences? [evidence note]
- Which security failures most often begin with casually retained credentials in personal scripts? [research]

## Non-goals

- Not an argument that improvisation is irresponsible or that every useful tool needs a rewrite.
- Not a license to call unsupported, shared, or high-impact software “just a prototype.”
- Not a finished policy template for production readiness.

## Series connection

- Place this after all four numbered parts, directly following Part IV's handoff, as the honest boundary around the series: vibe freely during exploration; engineer on purpose when the thing matters to others.
- Cross-link to the parts on architecture, testing, and maintenance once their final titles and claims are settled. [drafting note]

## Open drafting questions

- What is the cleanest way to distinguish a personal tool from a tool with shared authority?
- Which prototype graduation story will feel concrete without becoming a horror-story lecture?
- How much of the boundary diagram belongs in the final piece versus a companion visual?
- Working closing sentiment, not finished copy: “vibe freely, engineer deliberately.”
