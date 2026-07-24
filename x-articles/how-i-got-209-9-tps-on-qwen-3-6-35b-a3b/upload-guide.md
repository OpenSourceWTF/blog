# X upload guide: How I got 209.9 tps on Qwen-3.6-35b-a3b.

This is a manual publication runbook. It does not authorize or perform a live
X publication.

## Source and title

- Article source:
  `x-articles/how-i-got-209-9-tps-on-qwen-3-6-35b-a3b/article.md`
- Exact title: `How I got 209.9 tps on Qwen-3.6-35b-a3b.`
- Author line: `David Tai, OpenSource.WTF`
- Header image: `a3b-dispatch-investigation-thumbnail.png`

The source is the current 209.9 tok/s article. Do not paste from an older
candidate draft.

## Before opening the composer

1. Use a desktop browser. X limits Article publishing to Premium, Premium+,
   Premium Business, and Premium Organization accounts.
2. Keep this guide, the article source, and `posts/images/` open in Finder or
   the editor.
3. Do not upload any `.svg` file. The SVGs are editable sources; every upload
   below uses the matching `.png`.
4. Do not remove the arithmetic-lane or prompt-dependence caveats from the
   article.

Official references:

- [About Articles](https://help.x.com/en/using-x/articles)
- [Photo formats, size limits, and aspect ratios](https://help.x.com/en/using-x/posting-gifs-and-pictures)
- [Writing image descriptions](https://help.x.com/en/using-x/picture-descriptions)

## Compose the Article

1. Sign in at [x.com](https://x.com).
2. Select **Articles** in the left navigation.
3. Select **Write**.
4. Enter the exact title above.
5. Add `a3b-dispatch-investigation-thumbnail.png` as the header image. Treat
   the first image marker in the source as this header placement; do not insert
   a second inline copy if X already displays the header.
6. Paste the body from `article.md`, omitting the leading `#` title because
   the composer has a separate title field.
7. For every `[INSERT IMAGE: ...]` marker:
   - remove the marker, `ALT:`, and `CAPTION:` control lines from the visible
     article;
   - insert the named PNG at that point;
   - copy the `ALT:` text into X's image-description field;
   - add the `CAPTION:` text as an italic paragraph immediately below the
     image.
8. Apply the source hierarchy in the composer:
   - `##` becomes a heading;
   - `###` and `####` become subheadings;
   - preserve bold, italics, numbered lists, bullets, block quotes, and tables
     where the composer supports them;
   - recreate each Markdown link with the same visible text and URL.
9. For the profiler record-flow code block, use an indented or monospace block
   if available. It is literal technical vocabulary, not a missing image.

## Image placement order

The article has 17 placements using 17 distinct PNGs. Every experiment and
shipped upgrade has its own flow-specific image with the same number and title
as its article heading.

### 1. Header image

- File: `a3b-dispatch-investigation-thumbnail.png`
- Placement: Article header, before the introduction
- Alt: An investigator studies a profiler-like queue with many small coral
  commands on the top lane and fewer long blue GPU operations on the bottom
  lane
- Caption: The investigation began with faster GPU kernels and ended with the
  command stream feeding them.

### 2. K1 speculative decode

- File: `a3b-k1-cycle.png`
- Placement: Section 2, after the first paragraph explaining K1 speculative
  decode
- Alt: K1 speculative decode route from a primary token through one-token
  drafting, two-row verification, acceptance, or one-row repair
- Caption: The target verifies the primary token and one trained draft together.
  Accepted drafts commit directly; rejected drafts continue through a one-row
  repair from the verified primary-token state.

### 3. Experiment 1: Fixed-M2 GDN post-conv

- File: `a3b-experiment-1-fixed-m2-gdn.png`
- Placement: Experiment 1
- Alt: Historical fixed-M2 GDN post-convolution flow before and after two
  serial positions are held inside one fused owner
- Caption: The fusion keeps normalization, gates, recurrence, capture, and
  state output inside one owner without changing their order.

### 4. Experiment 2: Row-owned router construction

- File: `a3b-experiment-2-row-owned-router.png`
- Placement: Experiment 2
- Alt: Historical router construction before and after one threadgroup owns
  one complete 256-expert row
- Caption: The owner is the complete semantic row, including exact selection
  and normalization—not merely a convenient launch shape.

### 5. Experiment 3: Combine-tail construction

- File: `a3b-experiment-3-combine-tail.png`
- Placement: Experiment 3
- Alt: Historical combine-tail construction before and after one output
  element owns the ordered eight-expert accumulation
- Caption: Output ownership removes the broadcast temporary only while one
  owner can preserve the required eight-value accumulation order.

### 6. Experiment 4: Captured-primary rejection repair

- File: `a3b-experiment-4-captured-primary-repair.png`
- Placement: Experiment 4
- Alt: Rejected K1 draft flow before and after repair continues from the
  captured primary-token state in verifier row 0
- Caption: A rejection continues from the exact prefix already owned by row 0
  instead of reconstructing that state through the host.

### 7. Experiment 5: Early beneficial bundle

- File: `a3b-experiment-5-early-bundle.png`
- Placement: Experiment 5
- Alt: Five installed changes flowing into one measured early-bundle result
  without assigning the gain to an individual component
- Caption: The experiment measured all five changes as one treatment. It
  proved that the combined path won, not how much any one member contributed.

### 8. Profiler result and performance ladder

- File: `a3b-profiler-performance-ladder.png`
- Placement: Section 3, after the C1/C2/C3 comparison
- Alt: Cycle-aligned profiler comparison showing fewer dispatches, less CPU
  encode work, and slightly more GPU work after PR 174
- Caption: The successful path shortens the CPU command stream. Its measured
  GPU work is slightly longer, but the active range still finishes sooner.

### 9. Profiler before trace

- File: `mlx-profiler-before-v230-k1-cycle-aligned.png`
- Placement: “Before: pristine MTPLX 2.3.0”
- Alt: MLX Profiler timeline for the cycle-aligned pristine MTPLX 2.3.0 A3B K1
  run
- Caption: One active decode burst: 8.54 ms selected wall span, 1,976
  dispatches, and 635.91 µs of exposed host encoding.

### 10. Profiler after trace

- File: `mlx-profiler-after-pr174-k1-cycle-aligned.png`
- Placement: “After: the post-#174 exact-arithmetic stack”
- Alt: MLX Profiler timeline for the cycle-aligned post-PR-174 A3B K1 run
- Caption: The equivalent active decode burst: 8.21 ms selected wall span,
  1,459 dispatches, and 205.58 µs of exposed host encoding. The yellow markers
  are asynchronous task-cap waits that overlap GPU execution, not additional
  time to add to the wall span. Compared with the before trace, the CPU encodes
  517 fewer GPU operations and spends 1.275 ms less time building the command
  stream.

### 11. 1. Compile the K1 route, not just the model

- File: `a3b-compiled-k1-route.png`
- Placement: “Compile the K1 route, not just the model”
- Alt: The old K1 loop rebuilds and revalidates known work every cycle, while
  the installed route proves invariants once and enters fixed verifier and
  repair graphs directly
- Caption: Compilation pays here because shapes, arithmetic, cache ownership,
  and the repair prefix are proved once rather than rediscovered every cycle.

### 12. 2. Collapse the whole MoE block

- File: `a3b-whole-moe-collapse.png`
- Placement: “Collapse the whole MoE block”
- Alt: Whole-MoE execution before and after twelve source-level device
  boundaries collapse into three ownership stages
- Caption: The fused lane removes nine launches per block and carries only the
  compact activation between stages; it keeps its own arithmetic baseline
  because the final combine changes accumulation order.

### 13. 2. Collapse the whole MoE block — Stage 1 detail

- File: `a3b-two-row-router-tiling.png`
- Placement: Shipped upgrade 2, after the rejected first two-row Stage 1
- Alt: Rejected two-row grouping that rereads all router weights for each row
  versus expert-axis tiles that reuse one fixed weight region across both rows
- Caption: The accepted geometry earns its name by sharing each packed
  router-weight region across both rows, then finalizing the route exactly once.

### 14. 3. GDN post-conv fusion

- File: `a3b-gdn-postconv-fusion.png`
- Placement: “GDN post-conv fusion”
- Alt: GDN post-convolution path before and after a state-quarter owner fuses
  the serial recurrent tail
- Caption: The shipped C1 owner removes intermediate traffic without
  reassociating the serial recurrence.

### 15. 4. Pack gate and up projections once

- File: `a3b-packed-gate-up.png`
- Placement: “Pack gate and up projections once”
- Alt: Separate gate and up quantized projections versus load-time output-axis
  packing into one dispatch
- Caption: The output-axis pack is installed once and leaves every input-axis
  quantization group intact.

### 16. 5. Give each router row one owner

- File: `a3b-row-owned-router.png`
- Placement: “Give each router row one owner”
- Alt: Separate post-softmax router stages versus one owner completing exact
  top-8 selection and normalization for a row
- Caption: The historical construction A/B measured the mechanism, but the
  final stack does not assign it a clean isolated contribution.

### 17. 6. Fuse the weighted combine tail

- File: `a3b-output-owned-combine.png`
- Placement: “Fuse the weighted combine tail”
- Alt: Broadcast temporary and expert reduction versus one output-column owner
  performing the ordered weighted combine
- Caption: This supporting kernel removes a temporary and a launch; its
  isolated windows were too mixed for a clean current TPS claim.

## Compatibility manifest

Every listed file is a 1600×900, opaque, sRGB PNG and is below X's documented
5 MB photo limit.

| PNG | Bytes | Workspace location |
|---|---:|---|
| `a3b-dispatch-investigation-thumbnail.png` | 1,596,848 | `posts/images/a3b-dispatch-investigation-thumbnail.png` |
| `a3b-compiled-k1-route.png` | 384,429 | `posts/images/a3b-compiled-k1-route.png` |
| `a3b-experiment-1-fixed-m2-gdn.png` | 379,081 | `posts/images/a3b-experiment-1-fixed-m2-gdn.png` |
| `a3b-experiment-2-row-owned-router.png` | 305,491 | `posts/images/a3b-experiment-2-row-owned-router.png` |
| `a3b-experiment-3-combine-tail.png` | 335,241 | `posts/images/a3b-experiment-3-combine-tail.png` |
| `a3b-experiment-4-captured-primary-repair.png` | 378,805 | `posts/images/a3b-experiment-4-captured-primary-repair.png` |
| `a3b-experiment-5-early-bundle.png` | 311,221 | `posts/images/a3b-experiment-5-early-bundle.png` |
| `a3b-gdn-postconv-fusion.png` | 285,619 | `posts/images/a3b-gdn-postconv-fusion.png` |
| `a3b-k1-cycle.png` | 274,548 | `posts/images/a3b-k1-cycle.png` |
| `a3b-output-owned-combine.png` | 325,814 | `posts/images/a3b-output-owned-combine.png` |
| `a3b-packed-gate-up.png` | 383,190 | `posts/images/a3b-packed-gate-up.png` |
| `a3b-profiler-performance-ladder.png` | 268,026 | `posts/images/a3b-profiler-performance-ladder.png` |
| `a3b-row-owned-router.png` | 283,862 | `posts/images/a3b-row-owned-router.png` |
| `a3b-two-row-router-tiling.png` | 400,119 | `posts/images/a3b-two-row-router-tiling.png` |
| `a3b-whole-moe-collapse.png` | 393,674 | `posts/images/a3b-whole-moe-collapse.png` |
| `mlx-profiler-before-v230-k1-cycle-aligned.png` | 308,478 | `posts/images/mlx-profiler-before-v230-k1-cycle-aligned.png` |
| `mlx-profiler-after-pr174-k1-cycle-aligned.png` | 304,468 | `posts/images/mlx-profiler-after-pr174-k1-cycle-aligned.png` |

## Pre-publish check

1. Preview at desktop width and in a narrow/mobile window.
2. Confirm every diagram title and caption remains legible when opened.
3. Confirm both profiler screenshots show one aligned active burst:
   - before: 8.54 ms, 1,976 dispatches;
   - after: 8.21 ms, 1,459 dispatches.
4. Confirm the after caption explains that yellow task-cap waits overlap GPU
   execution.
5. Confirm the six-upgrade table contains exactly six upgrades.
6. Confirm gains use positive TPS percentages and losses use negative TPS
   percentages.
7. Confirm the result section keeps stock and whole-MoE arithmetic lanes
   separate.
8. Confirm 206.8 and 209.9 are described as random-prompt observations, not a
   universal throughput rate.
9. Test the profiler links:
   - `https://github.com/OpenSourceWTF/mlx-profiler`
   - `https://mlx-profiler.opensource.wtf`
10. Select the intended audience. X permits public and subscriber-only Article
    audiences; use public unless there is a separate publication decision.
11. Select **Publish**, review the final audience confirmation, then publish.

Editing a published Article requires selecting **Edit Article** and confirming
that X will unpublish it. Republish after the edit; do not assume the old
published version stays live during editing.

## Optional promotional post

This is 269 characters including the `[LINK]` placeholder:

> Qwen3.6-35B-A3B reached 209.9 tok/s on a random coding prompt on my M5
> Max—but the breakthrough wasn't a faster GPU kernel. Faster kernels made the
> model slower. I built an MLX profiler, found the CPU dispatch bottleneck, and
> removed 517 launches from one burst. [LINK]

Attach `a3b-dispatch-investigation-thumbnail.png` to the promotional post and
replace `[LINK]` with the published Article URL.
