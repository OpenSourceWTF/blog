# A3B article visuals and X Article design

Date: 2026-07-24

Status: approved direction; written-spec review pending

## Purpose

Replace the explanatory ASCII diagrams in
`candidates/a3b-200-tok-s/PAPER.md` with accurate, readable graphics; create an
editorial thumbnail for the investigation; and produce a complete native X
Article package with upload instructions.

The visual system must communicate the article's central finding: several GPU
optimizations were locally faster but slower end to end, and the large win came
after profiling exposed the CPU command stream and dispatch overhead.

## Scope

### Deliverables

1. One abstract editorial-cartoon thumbnail.
2. Nine reusable technical diagram families:
   - K1 speculative accept/reject cycle and depth operating point.
   - Compiled K1 route and on-device draft construction.
   - Whole-MoE twelve-boundary path collapsed into three stages.
   - Two-row tiled router-weight ownership.
   - GDN post-convolution fusion.
   - Packed gate/up projections.
   - Row-owned top-8 router finalization.
   - Output-owned weighted combine tail.
   - Profiler CPU/GPU overlap and the performance ladder.
3. PNG exports of every publishable image for both the website and X.
4. Updated `PAPER.md` with the PNG figures replacing explanatory ASCII.
5. A current X Article at
   `candidates/x-hy3-moe-series/02-a3b-209.md`.
6. An X upload guide at
   `candidates/x-hy3-moe-series/02-a3b-209-upload.md`.
7. Captions and accessible alt text for every embedded image.

### Non-goals

- Do not generate technical diagrams with an image model.
- Do not put benchmark numbers, labels, logos, or readable prose inside the
  generated cartoon thumbnail.
- Do not publish to X or modify any live post.
- Do not delete the stale `02-a3b-160.md`; mark it superseded and preserve it
  as history.
- Do not turn the X Article into a short thread. The requested deliverable is a
  native long-form X Article, plus one optional promotional post.

## Chosen approach

Use a hybrid visual system:

- The thumbnail is a generated raster editorial illustration.
- Technical figures are deterministic SVG source files rendered to compatible
  PNGs.
- The website article and X Article share the same PNG exports and alt text.
- Repeated mechanisms reuse one figure rather than producing visually different
  explanations of the same operation.

This keeps the cartoon expressive while keeping technical geometry, arithmetic,
ownership, and labels exact.

## Visual language

### Thumbnail

Wide editorial cartoon showing:

- a CPU-side traffic jam made of many small coral dispatch cards;
- an investigator following the jam with a magnifying glass;
- a profiler-like timeline revealing the blockage;
- the cards being consolidated into a small number of clean cyan blocks flowing
  into a GPU-like compute engine.

The image should feel investigative rather than triumphant. Use a dark navy
background, cyan GPU work, coral CPU/dispatch work, amber waits, and warm
off-white highlights. Do not use Apple, MLX, X, Qwen, or OpenSource.WTF logos.
Do not include generated text or numbers.

### Technical diagrams

- Dark navy background compatible with the profiler screenshots.
- Cyan: GPU execution and retained device-side work.
- Coral: CPU graph construction, dispatch, and host round trips.
- Amber: waits, rejected paths, or removed boundaries.
- Green: committed output or successful fused path.
- Warm off-white: primary labels and arrows.
- Dashed outlines: eliminated temporary tensors or boundaries.
- Solid ownership frames: one threadgroup, row, tile, or output owner.
- Before/after diagrams use the same geometry so changes are visually
  comparable.

Labels must describe operations, not advertise conclusions. Captions carry the
performance result and caveat.

## Image compatibility contract

The publishable contract is deliberately narrower than either platform's
maximum feature set:

- Format: PNG.
- Dimensions: 1600 by 900 pixels.
- Aspect ratio: 16:9, within X's documented full-display range of 2:1 through
  3:4.
- Color: sRGB.
- Alpha: none; every publishable image is fully opaque.
- File size: less than 5 MB.
- Metadata: strip unnecessary profiles and private EXIF/location metadata while
  retaining sRGB interpretation.
- Text: minimum effective label size of 28 px in the 1600 px export.
- Mobile check: labels remain readable when the image is displayed at 375 CSS
  pixels wide or the caption supplies the detail that cannot remain legible.
- Filenames: lowercase ASCII kebab-case ending in `.png`.
- No external fonts, linked images, scripts, or remote resources.

Editable SVG sources use the same 1600 by 900 view box and remain next to the
exports, but neither article embeds or asks X to upload SVG. SVG is source only.

Generated thumbnail output will be cropped and converted into the same
1600-by-900 opaque sRGB PNG contract.

## Diagram contracts

### 1. K1 cycle and operating depth

Show primary token, one-token draft, two-row verification, accept/commit, and
reject/one-row repair. A compact secondary strip distinguishes supported maximum
depth from measured default depth without implying that depth three is invalid.

### 2. Compiled K1 route

Before: repeated Python graph construction, invariant checks, cache mutation,
host draft materialization, verification, and rollback repair.

After: invariants proved once at installation; fixed verify-M2 and repair-M1
entrypoints; request-owned state slots; draft sampling and verify-input
construction remain on device.

### 3. Whole-MoE collapse

Before: the twelve source-level boundaries listed in the article.

After: Stage 1 route/scores/shared gate; Stage 2 routed and shared gate/up plus
SwiGLU; Stage 3 routed and shared down plus weighted combine. Show the compact
`[M,9,512]` activation crossing the only inter-stage boundary.

### 4. Two-row tiled ownership

Contrast one router-weight read per row with eight expert-axis tiles, each
serving both rows, followed by one exact finalizer. Do not suggest that merely
grouping rows shares weights.

### 5. GDN post-convolution fusion

Preserve the serial order of q/k normalization, gates, recurrence, state update,
capture, and output. Show intermediate device traffic removed inside one C1
owner without implying reassociated arithmetic.

### 6. Packed gate/up

Show two projections consuming the same input before, then one load-time packed
output-axis projection followed by a split. Indicate that input-axis
quantization groups are not crossed and weights are not requantized.

### 7. Row-owned router

Show stock softmax followed by partition/gather/sum/divide versus one owner that
performs exact top-8 selection, gather, and normalization for a complete row.
Preserve the 256-expert and top-8 facts.

### 8. Output-owned combine

Show broadcast multiply and `[rows,8,2048]` temporary materialization before;
one output owner performing eight ordered weighted accumulations after.

### 9. Profiler and performance ladder

The profiler half explains hidden versus exposed host encode, GPU work, waits,
and dispatch density on one timeline. The ladder half presents paired
checkpoints without adding unrelated percentages or mixing arithmetic lanes.
The verified before/after profiler screenshots remain separate evidence figures.

## Article integration

### Website article

- Replace explanatory ASCII blocks with the matching PNG.
- Retain short code-like text only where literal field names, environment
  variables, or record schemas are the subject.
- Keep the original explanation below each diagram; the image supplements the
  reasoning and does not replace it.
- Add the thumbnail to front matter using the blog's existing `image` field.
- Preserve the two verified profiler screenshots unchanged.

### X Article

- Start from the current `PAPER.md`, not the stale 160 tok/s X draft.
- Preserve the title, author voice, chronological investigation, six shipped
  upgrades, profiler explanation, official 206.8 and 209.9 observations,
  arithmetic-lane caveats, conclusion, and Hall of Failures.
- Remove website front matter and local-only editorial notes.
- Use X-supported headings, subheadings, bold, italics, lists, links, and PNG
  images.
- Put explicit insertion markers in the source:
  `[INSERT IMAGE: filename.png]`.
- Put the exact alt text and caption immediately below each marker.
- Include one short promotional post under 280 characters in the upload guide.

## X upload guide

The guide will:

1. State the eligible Premium-account requirement.
2. Direct the author to x.com, the Articles navigation item, and Write.
3. Provide the exact title and source file to paste.
4. List images in upload order with filenames, insertion points, captions, and
   alt text.
5. Explain how to apply heading, bold, italics, list, and link formatting.
6. Require a desktop and mobile preview check before publishing.
7. Explain audience selection and publishing.
8. Warn that editing a published Article unpublishes it until it is republished.
9. Provide the optional promotional post and thumbnail upload.

The guide will link to X's official Articles and image-format documentation.

## Validation

### Technical accuracy

- Compare every diagram against the ASCII source and adjacent prose.
- Check row counts, expert counts, route width, tensor shapes, stage count,
  arithmetic order, and ownership labels.
- Ensure no diagram credits a clean isolated TPS contribution where the article
  says none exists.

### Asset compatibility

- Validate SVG XML with `xmllint`.
- Render each SVG at exactly 1600 by 900.
- Confirm PNG signature, dimensions, sRGB color space, full opacity, and file
  size below 5 MB.
- Strip private EXIF and location metadata.
- Produce and inspect a 375-pixel-wide mobile contact sheet.
- Inspect every final image visually for clipping, illegible labels, broken
  arrows, incorrect layering, and color ambiguity.

### Article checks

- Confirm every image path exists.
- Confirm every X insertion marker maps to one compatible PNG.
- Run Markdown and prose lint checks.
- Check for stale 160 tok/s headline language in the new X Article.
- Check that 206.8 and 209.9 remain prompt-dependent observations rather than a
  universal model claim.
- Verify the website article still distinguishes the stock and whole-MoE
  arithmetic lanes.

## Failure-mode review

### Technical simplification changes the mechanism

Severity: critical.

Mitigation: deterministic vector diagrams, one contract per mechanism, and
line-by-line comparison against the existing ASCII and prose before replacing
anything.

### X accepts the file but makes labels unreadable

Severity: critical.

Mitigation: PNG-only publication assets, 1600-by-900 exports, minimum 28 px
labels, mobile-width inspection, and captions that repeat any detail that
cannot survive downscaling.

### Generated thumbnail invents text, brands, or misleading hardware

Severity: minor if caught before publication; critical if published.

Mitigation: prompt for no text, numbers, or logos; inspect the result; make one
targeted regeneration if needed; crop and normalize only an approved image.

### Website and X copies drift

Severity: minor for this delivery.

Mitigation: generate the X Article from the current `PAPER.md`, record the
source date and title in an editor note, and treat the X file as a publication
snapshot rather than pretending there is an automatic sync system.

## Rollout

1. Build and validate one representative technical diagram to lock the visual
   grammar.
2. Generate and approve the thumbnail.
3. Produce the remaining diagram families and PNG exports.
4. Replace website ASCII and add thumbnail metadata.
5. Create the X Article and upload guide.
6. Run compatibility, accuracy, mobile-readability, and article checks.

No live publishing is part of this rollout.
