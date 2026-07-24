# X upload guide: Make your MLX kernels faster with the MLX Profiler

This is a manual publication runbook. It does not authorize or perform a live
X publication.

## Source and title

- Article source:
  `x-articles/make-your-mlx-kernels-faster-with-the-mlx-profiler/article.md`
- Exact title: `Make your MLX kernels faster with the MLX Profiler`
- Author line: `David Tai, OpenSource.WTF`
- Header image: `a3b-profiler-performance-ladder-no-section-number.png`

## Compose the Article

1. Sign in at [x.com](https://x.com).
2. Select **Articles** in the left navigation, then **Write**.
3. Enter the exact title above.
4. Add `a3b-profiler-performance-ladder-no-section-number.png` as the header
   image. Do not insert a second inline copy if X already displays the header.
5. Paste the body from `article.md`, omitting its leading `#` title.
6. At each `[INSERT IMAGE: ...]` marker:
   - remove the marker, `ALT:`, and `CAPTION:` control lines;
   - insert the named PNG from `posts/images/`;
   - paste the `ALT:` value into X's image-description field;
   - add the `CAPTION:` value as an italic paragraph below the image.
7. Preserve headings, bold text, bullets, block quote, code blocks, table, and
   links where the composer supports them.

## Image placement order

### 1. Header image

- File: `a3b-profiler-performance-ladder-no-section-number.png`
- Placement: Article header
- Alt: A profiler comparison showing fewer CPU dispatches after the A3B
  optimization
- Caption: The faster A3B path did slightly more GPU work. It won because the
  CPU had less work to send.

### 2. Before trace

- File: `mlx-profiler-before-v230-k1-cycle-aligned.png`
- Placement: Section 3, before the profiler-reading tips
- Alt: MLX Profiler timeline before the A3B optimization, with a dense stream
  of CPU dispatches
- Caption: Before: 1,976 dispatches in one active A3B decode burst.

### 3. After trace

- File: `mlx-profiler-after-pr174-k1-cycle-aligned.png`
- Placement: Section 4, before the result table
- Alt: MLX Profiler timeline after the A3B optimization, with fewer CPU
  dispatches and less exposed host time
- Caption: After: 1,459 dispatches and much less exposed CPU encoding.

## Final checks

- Confirm the title and author line.
- Confirm all three images render clearly.
- Confirm all three images have their supplied alt text.
- Confirm command lines remain readable and are not converted into prose.
- Confirm the profiler, visualizer, quickstart, and public-workbench links open.
- Preview on desktop and mobile before publishing.
