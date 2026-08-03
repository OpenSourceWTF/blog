# Vibengineer Compendium Outlines Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers-optimized:subagent-driven-development (recommended) or superpowers-optimized:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create five unpublished, connected article outlines for *The Vibengineer Compendium*.

**Architecture:** Each article lives in its own Markdown file under a non-publishing outline directory. The files share a fixed outline schema and explicit previous/next transitions, but each owns a distinct layer of the series argument. A final editorial pass checks terminology, progression, overlap, and unsupported claims across all five files.

**Tech Stack:** Markdown, YAML frontmatter, ripgrep, Git

**Assumptions:**

- Assumes only files under `posts/` enter the publishing pipeline — this plan will not work safely if the site begins ingesting `docs/outlines/`.
- Assumes these are drafting artifacts rather than finished posts — the plan does not add publication dates, polished prose, external citations, or illustrations.
- Assumes no commit or push is requested — the plan creates and verifies local files only.

---

## File Structure

- `docs/outlines/vibengineer-compendium/01-the-basics.md` — operating philosophy, workflow, and skill setup.
- `docs/outlines/vibengineer-compendium/02-architectural-patterns.md` — ownership, canonical authority, and dependency direction.
- `docs/outlines/vibengineer-compendium/03-keep-your-code-healthy.md` — Plan, Expand, Analyze, Reduce change loop.
- `docs/outlines/vibengineer-compendium/04-what-the-test.md` — evidence-oriented testing and suite discipline.
- `docs/outlines/vibengineer-compendium/05-on-actual-vibecoding.md` — boundary between exploratory vibecoding and accountable engineering.

Every file uses these sections in this order:

1. YAML frontmatter with `title`, `series`, `part`, and `status: outline`
2. `Thesis`
3. `Reader promise`
4. `Argument`
5. `Candidate examples and diagrams`
6. `Claims requiring evidence`
7. `Non-goals`
8. `Series connection`
9. `Open drafting questions`

### Task 1: Draft Part I — The Basics

**Files:**

- Create: `docs/outlines/vibengineer-compendium/01-the-basics.md`

**Security flag:** `none`

- [x] **Step 1: Create the outline**

Write a skeletal, question-driven outline that:

- Defines “vibengineer” without pretending it is an industry standard.
- Frames the model as an eager search engine rather than an accountable engineer.
- Covers premise checks, context control, reusable skills, plans, checkpoints, and evidence.
- Recommends the smallest useful skill setup and warns against ceremony for its own sake.
- Includes one candidate process-loop diagram.
- Ends by showing why good process cannot rescue a codebase with competing authorities.

- [x] **Step 2: Verify structure and scope**

Run:

```bash
rg -n '^---$|^## (Thesis|Reader promise|Argument|Candidate examples and diagrams|Claims requiring evidence|Non-goals|Series connection|Open drafting questions)$' docs/outlines/vibengineer-compendium/01-the-basics.md
```

Expected: frontmatter delimiters and all eight required section headings appear; no finished article prose is required.

### Task 2: Draft Part II — Architectural Patterns

**Files:**

- Create: `docs/outlines/vibengineer-compendium/02-architectural-patterns.md`

**Security flag:** `none`

- [x] **Step 1: Create the outline**

Write a skeletal, question-driven outline that:

- Explains why agents multiply plausible implementations.
- Defines ownership as one authoritative home for a concern.
- Defines dominance strictly as canonical authority between overlapping modules, contracts, or representations.
- Covers dependency direction, narrow interfaces, locality, controlled duplication, deletion paths, architectural tests, and repository maps.
- Includes candidate diagrams for competing authority and a dominant dependency direction.
- Never applies dominance to people or teams.
- Ends by creating the need for a safe method of changing an established architecture.

- [x] **Step 2: Verify terminology and structure**

Run:

```bash
rg -n '^## (Thesis|Reader promise|Argument|Candidate examples and diagrams|Claims requiring evidence|Non-goals|Series connection|Open drafting questions)$|canonical authority|people or teams' docs/outlines/vibengineer-compendium/02-architectural-patterns.md
```

Expected: all eight headings appear, canonical authority is explicit, and the people/team interpretation is rejected.

### Task 3: Draft Part III — Keep Your Code Healthy

**Files:**

- Create: `docs/outlines/vibengineer-compendium/03-keep-your-code-healthy.md`

**Security flag:** `none`

- [x] **Step 1: Create the outline**

Write a skeletal, question-driven outline that:

- Treats code generation as an entropy source.
- Gives Plan, Expand, Analyze, and Reduce distinct jobs.
- Makes expansion intentional exploration rather than permission to retain every generated option.
- Makes reduction cover scaffolding, duplicate paths, dead branches, false abstractions, and unnecessary tests.
- Applies the loop at change, module, and repository scale.
- Includes a candidate before/after worked example and a loop diagram.
- Ends by explaining that confident deletion depends on trustworthy tests.

- [x] **Step 2: Verify loop coverage and structure**

Run:

```bash
rg -n '^## (Thesis|Reader promise|Argument|Candidate examples and diagrams|Claims requiring evidence|Non-goals|Series connection|Open drafting questions)$|Plan|Expand|Analyze|Reduce' docs/outlines/vibengineer-compendium/03-keep-your-code-healthy.md
```

Expected: all eight headings and all four named loop stages appear.

### Task 4: Draft Part IV — What the Test

**Files:**

- Create: `docs/outlines/vibengineer-compendium/04-what-the-test.md`

**Security flag:** `none`

- [x] **Step 1: Create the outline**

Write a skeletal, question-driven outline that:

- Distinguishes test volume from evidence quality.
- Separates observable behavior, contracts, invariants, and architectural boundaries from implementation detail.
- Covers regression tests that first demonstrate the failure.
- Recommends targeted tests during iteration and broader suites at meaningful gates.
- Introduces mutation, deletion, and counterexample thinking.
- Includes a candidate diagram contrasting a large mirrored suite with a smaller evidence-oriented suite.
- Ends by separating disciplined verification from actual vibecoding.

- [x] **Step 2: Verify testing claims and structure**

Run:

```bash
rg -n '^## (Thesis|Reader promise|Argument|Candidate examples and diagrams|Claims requiring evidence|Non-goals|Series connection|Open drafting questions)$|observable behavior|targeted tests|counterexample' docs/outlines/vibengineer-compendium/04-what-the-test.md
```

Expected: all eight headings and the three evidence-oriented testing concepts appear.

### Task 5: Draft the Addendum — On Actual Vibecoding

**Files:**

- Create: `docs/outlines/vibengineer-compendium/05-on-actual-vibecoding.md`

**Security flag:** `none`

- [x] **Step 1: Create the outline**

Write a skeletal, question-driven outline that:

- Treats actual vibecoding as improvisational and intentionally under-controlled.
- Defends its usefulness for sketches, probes, throwaways, and personal tools.
- Identifies bad habitats involving shared authority, durable data, money, secrets, or production.
- Includes a concrete prototype graduation test.
- Allows that some weird little scripts should remain weird little scripts.
- Includes a candidate exploration-to-engineering boundary diagram.
- Closes the series with “vibe freely, engineer deliberately” as a working sentiment rather than finished copy.

- [x] **Step 2: Verify boundary coverage and structure**

Run:

```bash
rg -n '^## (Thesis|Reader promise|Argument|Candidate examples and diagrams|Claims requiring evidence|Non-goals|Series connection|Open drafting questions)$|prototype graduation|durable data|engineer deliberately' docs/outlines/vibengineer-compendium/05-on-actual-vibecoding.md
```

Expected: all eight headings and the exploration/production boundary concepts appear.

### Task 6: Integrate and Edit the Series

**Files:**

- Modify: `docs/outlines/vibengineer-compendium/01-the-basics.md`
- Modify: `docs/outlines/vibengineer-compendium/02-architectural-patterns.md`
- Modify: `docs/outlines/vibengineer-compendium/03-keep-your-code-healthy.md`
- Modify: `docs/outlines/vibengineer-compendium/04-what-the-test.md`
- Modify: `docs/outlines/vibengineer-compendium/05-on-actual-vibecoding.md`

**Security flag:** `none`

- [x] **Step 1: Perform the connected-argument edit**

Confirm and repair:

- Order is Basics → Architecture → Code Health → Tests → Actual Vibecoding.
- Each article owns one main claim and does not re-teach another article.
- Every `Series connection` names the logical inheritance and handoff.
- “Dominance” always means canonical code authority.
- Claims needing research remain questions or evidence notes, not asserted facts.
- Language stays informal, direct, and non-marketing.

- [x] **Step 2: Run the outline gate**

Run:

```bash
test "$(find docs/outlines/vibengineer-compendium -maxdepth 1 -name '*.md' | wc -l | tr -d ' ')" = "5"
for file in docs/outlines/vibengineer-compendium/*.md; do
  test "$(rg -c '^## (Thesis|Reader promise|Argument|Candidate examples and diagrams|Claims requiring evidence|Non-goals|Series connection|Open drafting questions)$' "$file")" = "8"
  rg -q '^status: outline$' "$file"
done
git diff --check -- docs/outlines/vibengineer-compendium docs/specs/2026-07-30-vibengineer-compendium-design.md docs/plans/2026-07-30-vibengineer-compendium-outlines.md
```

Expected: exactly five Markdown files exist, every file has eight required sections and outline status, and `git diff --check` reports no whitespace errors.
