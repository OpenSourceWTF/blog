# A3B Visuals and X Article Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use
> superpowers-optimized:subagent-driven-development (recommended) or
> superpowers-optimized:executing-plans to implement this plan task-by-task.
> Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the A3B draft's explanatory ASCII with compatible technical
art, add a shareable editorial thumbnail, and deliver a complete native X
Article plus upload instructions.

**Architecture:** Technical diagrams are deterministic 1600-by-900 SVG sources
rendered into opaque sRGB PNG publication assets. The generated cartoon
thumbnail is normalized into the same PNG contract. The website draft and X
Article share one asset directory and one set of captions/alt text so every
relevant diagram is back-ported automatically.

**Tech Stack:** Markdown, SVG 1.1, headless Chromium, macOS `sips`, `xmllint`,
the built-in image-generation tool, and the existing prose linter.

**Assumptions:**

- Assumes X's documented photo contract remains PNG/JPEG/GIF up to 5 MB and
  standard aspect ratios from 2:1 through 3:4 — this plan will not upload SVG,
  TIFF, BMP, or files larger than 5 MB.
- Assumes the current `PAPER.md` is the canonical publication snapshot — the
  stale 160 tok/s X draft will not be used as source.
- Assumes publication remains manual — this plan will not authenticate to X or
  publish a live Article.

---

## File structure

### Create

- `candidates/a3b-200-tok-s/images/a3b-dispatch-investigation-thumbnail.png`
  — shareable editorial thumbnail.
- `candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-compiled-k1-route.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-compiled-k1-route.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-whole-moe-collapse.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-whole-moe-collapse.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-two-row-router-tiling.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-two-row-router-tiling.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-gdn-postconv-fusion.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-gdn-postconv-fusion.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-packed-gate-up.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-packed-gate-up.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-row-owned-router.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-row-owned-router.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-output-owned-combine.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-output-owned-combine.png`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-profiler-performance-ladder.svg`
- `candidates/a3b-200-tok-s/images/diagrams/a3b-profiler-performance-ladder.png`
- `candidates/x-hy3-moe-series/02-a3b-209.md` — native X Article source.
- `candidates/x-hy3-moe-series/02-a3b-209-upload.md` — manual upload guide.

### Modify

- `candidates/a3b-200-tok-s/PAPER.md` — add thumbnail and replace explanatory
  ASCII with PNG figures.
- `candidates/x-hy3-moe-series/02-a3b-160.md` — add a superseded notice only.
- `candidates/x-hy3-moe-series/README.md` — register the current X Article.

## Task 1: Lock one representative diagram and the render contract

**Files:**

- Create:
  `candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.svg`
- Create:
  `candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.png`

**Security flag:** none

- [ ] **Step 1: Author the K1 cycle SVG**

Create a 1600-by-900 SVG with internal styles only. It must show primary token,
draft M1, verify M2, accept/commit, reject/repair M1, and a separate compact
supported-depth-versus-measured-default strip. Use the palette and ownership
rules in the approved spec.

- [ ] **Step 2: Validate SVG structure**

Run:

```bash
xmllint --noout \
  candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.svg
```

Expected: exit 0 with no output.

- [ ] **Step 3: Render the PNG**

Run from the blog repository:

```bash
CHROME="/Users/davidtai/Library/Caches/ms-playwright/chromium-1226/chrome-mac-arm64/Google Chrome for Testing.app/Contents/MacOS/Google Chrome for Testing"
SVG_PATH="$(pwd)/candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.svg"
PNG_PATH="$(pwd)/candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.png"
"$CHROME" --headless=new --hide-scrollbars --force-device-scale-factor=1 \
  --window-size=1600,900 --screenshot="$PNG_PATH" "file://$SVG_PATH"
```

Expected: a 1600-by-900 PNG.

- [ ] **Step 4: Check publication compatibility**

Run:

```bash
sips -g pixelWidth -g pixelHeight -g format -g hasAlpha \
  candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.png
stat -f '%z' \
  candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.png
```

Expected: 1600, 900, PNG, no alpha, and fewer than 5,242,880 bytes.

- [ ] **Step 5: Inspect at full and mobile width**

Inspect the 1600-by-900 PNG, then create a temporary mobile copy:

```bash
cp candidates/a3b-200-tok-s/images/diagrams/a3b-k1-cycle.png \
  /tmp/a3b-k1-cycle-mobile.png
sips --resampleWidth 375 /tmp/a3b-k1-cycle-mobile.png
```

Expected: arrows and labels remain distinguishable at full size; the 375-pixel
copy preserves the main before/after reading order.

## Task 2: Produce the remaining technical diagrams

**Files:**

- Create the eight remaining SVG/PNG pairs listed under File structure.

**Security flag:** none

- [ ] **Step 1: Author the eight SVG sources**

Implement the exact mechanism contracts from the design spec. Each SVG must be
1600 by 900, self-contained, and share the approved palette, typography,
arrowheads, padding, and before/after grammar.

- [ ] **Step 2: Validate all SVGs**

Run:

```bash
for file in candidates/a3b-200-tok-s/images/diagrams/*.svg; do
  xmllint --noout "$file" || exit 1
done
```

Expected: exit 0.

- [ ] **Step 3: Render all PNGs**

Run:

```bash
CHROME="/Users/davidtai/Library/Caches/ms-playwright/chromium-1226/chrome-mac-arm64/Google Chrome for Testing.app/Contents/MacOS/Google Chrome for Testing"
for svg in candidates/a3b-200-tok-s/images/diagrams/*.svg; do
  png="${svg%.svg}.png"
  absolute_svg="$(cd "$(dirname "$svg")" && pwd)/$(basename "$svg")"
  absolute_png="$(cd "$(dirname "$png")" && pwd)/$(basename "$png")"
  "$CHROME" --headless=new --hide-scrollbars --force-device-scale-factor=1 \
    --window-size=1600,900 --screenshot="$absolute_png" \
    "file://$absolute_svg" || exit 1
done
```

Expected: one PNG beside every SVG.

- [ ] **Step 4: Check all publication assets**

Run:

```bash
for png in candidates/a3b-200-tok-s/images/diagrams/*.png; do
  bytes="$(stat -f '%z' "$png")"
  test "$bytes" -lt 5242880 || exit 1
  sips -g pixelWidth -g pixelHeight -g format -g hasAlpha "$png" \
    | grep -q 'pixelWidth: 1600' || exit 1
  sips -g pixelWidth -g pixelHeight -g format -g hasAlpha "$png" \
    | grep -q 'pixelHeight: 900' || exit 1
done
```

Expected: exit 0.

- [ ] **Step 5: Visually inspect every diagram**

Inspect every PNG for clipping, arrow direction, label spelling, ownership
semantics, row/expert counts, tensor shapes, and mobile readability. Correct the
SVG source and rerender rather than editing a PNG.

## Task 3: Generate and normalize the shareable thumbnail

**Files:**

- Create:
  `candidates/a3b-200-tok-s/images/a3b-dispatch-investigation-thumbnail.png`

**Security flag:** none

- [ ] **Step 1: Generate the editorial cartoon**

Use the built-in image-generation tool with the approved thumbnail prompt:
wide investigative editorial cartoon; CPU dispatch-card traffic jam; generic
investigator with magnifying glass; profiler timeline revealing the bottleneck;
small cyan fused stream flowing into a GPU-like compute engine; dark navy,
cyan, coral, amber, and warm off-white palette; no text, numbers, logos,
watermarks, recognizable people, or branded hardware.

- [ ] **Step 2: Copy the selected generated image into the candidate workspace**

Preserve the original generation outside the publication path and copy the
selected result to:

```text
candidates/a3b-200-tok-s/images/a3b-dispatch-investigation-thumbnail.png
```

- [ ] **Step 3: Normalize the publication copy**

Crop centrally to 16:9, resize to exactly 1600 by 900, convert to sRGB, and
flatten any alpha against `#08171d` using macOS image tooling available on the
machine. Do not stretch the composition.

- [ ] **Step 4: Verify shareability**

Run:

```bash
sips -g pixelWidth -g pixelHeight -g format -g hasAlpha -g profile \
  candidates/a3b-200-tok-s/images/a3b-dispatch-investigation-thumbnail.png
test "$(stat -f '%z' \
  candidates/a3b-200-tok-s/images/a3b-dispatch-investigation-thumbnail.png)" \
  -lt 5242880
```

Expected: 1600 by 900, PNG, opaque, sRGB, under 5 MB.

- [ ] **Step 5: Inspect the thumbnail**

Confirm no text-like artifacts, logos, watermarks, accidental brand marks,
distorted hands/faces, or misleading benchmark graphics. If one appears, make
one targeted regeneration and repeat the compatibility gate.

## Task 4: Back-port the visuals into the website draft

**Files:**

- Modify: `candidates/a3b-200-tok-s/PAPER.md`

**Security flag:** none

- [ ] **Step 1: Add the thumbnail**

Add:

```yaml
image: "images/a3b-dispatch-investigation-thumbnail.png"
```

to the existing front matter.

- [ ] **Step 2: Replace explanatory ASCII**

Replace the K1 lifecycle, operating depth, GDN fusion, row-owned router,
combine-tail, captured repair, compiled route, on-device draft, whole-MoE,
two-row tiling, packed gate/up, and profiler/performance-ladder ASCII blocks
with the relevant PNG. Reuse figures for repeated mechanisms.

- [ ] **Step 3: Preserve literal technical code**

Keep code blocks whose subject is literal profiler schema, environment
variables, or exact record vocabulary. Do not convert those into artwork.

- [ ] **Step 4: Add captions and alt text**

Every image gets mechanism-specific alt text and a caption stating the
transferable idea and any performance caveat.

- [ ] **Step 5: Verify references**

Run:

```bash
rg -n 'images/(a3b-dispatch-investigation-thumbnail|diagrams/a3b-.*)\\.png' \
  candidates/a3b-200-tok-s/PAPER.md
for png in candidates/a3b-200-tok-s/images/diagrams/*.png \
  candidates/a3b-200-tok-s/images/a3b-dispatch-investigation-thumbnail.png; do
  test -s "$png" || exit 1
done
```

Expected: all ten PNG assets are referenced or intentionally documented, and
all referenced files exist.

## Task 5: Create the current native X Article

**Files:**

- Create: `candidates/x-hy3-moe-series/02-a3b-209.md`
- Modify: `candidates/x-hy3-moe-series/02-a3b-160.md`
- Modify: `candidates/x-hy3-moe-series/README.md`

**Security flag:** none

- [ ] **Step 1: Adapt the current article**

Create a full X Article from `PAPER.md`. Preserve the title, chronological
investigation, profiler analysis, six shipped upgrades, official prompt results,
conclusion, Hall of Failures, citations that add provenance, and arithmetic-lane
caveats.

- [ ] **Step 2: Add upload markers**

At each visual location, add:

```text
[INSERT IMAGE: filename.png]
ALT: ...
CAPTION: ...
```

Use only the compatible PNG filenames.

- [ ] **Step 3: Mark the old draft superseded**

Add an editor note to `02-a3b-160.md` pointing to `02-a3b-209.md`. Do not
rewrite or delete the old draft.

- [ ] **Step 4: Update the series README**

Register `02-a3b-209.md` as the current A3B X Article and
`02-a3b-160.md` as superseded.

- [ ] **Step 5: Run editorial checks**

Run:

```bash
python3 /Users/davidtai/.codex/skills/write-articles/scripts/prose_lint.py \
  candidates/x-hy3-moe-series/02-a3b-209.md
if rg -n '160\\+ tok/s|at 160 tok/s|pending|SOURCE NEEDED' \
  candidates/x-hy3-moe-series/02-a3b-209.md; then
  exit 1
fi
```

Expected: prose-lint findings are reviewed; no stale headline or unresolved
publication markers remain.

## Task 6: Write the X upload and sharing guide

**Files:**

- Create: `candidates/x-hy3-moe-series/02-a3b-209-upload.md`

**Security flag:** none

- [ ] **Step 1: Write exact upload order**

Include account eligibility, x.com Articles navigation, Write, exact title,
paste source, heading/list/link formatting, image order, captions, alt text,
desktop/mobile preview, audience selection, Publish, and the edit-unpublishes
warning.

- [ ] **Step 2: Add compatibility table**

List all ten PNGs with dimensions, file size, insertion section, caption, and
alt text. State explicitly that SVG sources are not upload assets.

- [ ] **Step 3: Add a promotional post**

Write one optional post under 280 characters that uses the thumbnail and links
to the X Article after publication. Do not claim 209.9 is universal across
prompts.

- [ ] **Step 4: Link official X instructions**

Link:

```text
https://help.x.com/en/using-x/articles
https://help.x.com/en/using-x/posting-gifs-and-pictures
https://help.x.com/en/using-x/picture-descriptions
```

- [ ] **Step 5: Verify marker coverage**

Run:

```bash
for png in \
  a3b-dispatch-investigation-thumbnail.png \
  a3b-k1-cycle.png \
  a3b-compiled-k1-route.png \
  a3b-whole-moe-collapse.png \
  a3b-two-row-router-tiling.png \
  a3b-gdn-postconv-fusion.png \
  a3b-packed-gate-up.png \
  a3b-row-owned-router.png \
  a3b-output-owned-combine.png \
  a3b-profiler-performance-ladder.png; do
  rg -q "$png" candidates/x-hy3-moe-series/02-a3b-209-upload.md || exit 1
done
```

Expected: all ten assets are covered.

## Task 7: Final compatibility and publication verification

**Files:**

- Verify all files created or modified by Tasks 1 through 6.

**Security flag:** none

- [ ] **Step 1: Validate all vector and raster assets**

Run:

```bash
for svg in candidates/a3b-200-tok-s/images/diagrams/*.svg; do
  xmllint --noout "$svg" || exit 1
done
for png in candidates/a3b-200-tok-s/images/diagrams/*.png \
  candidates/a3b-200-tok-s/images/a3b-dispatch-investigation-thumbnail.png; do
  test "$(stat -f '%z' "$png")" -lt 5242880 || exit 1
  details="$(sips -g pixelWidth -g pixelHeight -g format -g hasAlpha "$png")"
  printf '%s\n' "$details"
  printf '%s\n' "$details" | grep -q 'pixelWidth: 1600' || exit 1
  printf '%s\n' "$details" | grep -q 'pixelHeight: 900' || exit 1
  printf '%s\n' "$details" | grep -q 'format: png' || exit 1
done
```

Expected: exit 0.

- [ ] **Step 2: Verify article image paths**

Run:

```bash
python3 - <<'PY'
import re
from pathlib import Path

paper = Path("candidates/a3b-200-tok-s/PAPER.md")
missing = []
for target in re.findall(r"\\]\\((images/[^)]+\\.png)\\)", paper.read_text()):
    if not (paper.parent / target).is_file():
        missing.append(target)
if missing:
    raise SystemExit("missing PAPER images: " + ", ".join(missing))
print("PAPER image paths valid")
PY
```

Expected: `PAPER image paths valid`.

- [ ] **Step 3: Verify claim discipline**

Run:

```bash
rg -n '206\\.8|209\\.9|arithmetic lane|profiler-enabled|diagnostic' \
  candidates/a3b-200-tok-s/PAPER.md \
  candidates/x-hy3-moe-series/02-a3b-209.md
```

Expected: both articles retain prompt and diagnostic caveats near the numbers.

- [ ] **Step 4: Run prose and whitespace checks**

Run:

```bash
python3 /Users/davidtai/.codex/skills/write-articles/scripts/prose_lint.py \
  candidates/a3b-200-tok-s/PAPER.md
python3 /Users/davidtai/.codex/skills/write-articles/scripts/prose_lint.py \
  candidates/x-hy3-moe-series/02-a3b-209.md
git diff --check
```

Expected: no whitespace errors; heuristic findings are reviewed rather than
mechanically suppressed.

- [ ] **Step 5: Inspect all final images**

Visually inspect the thumbnail, nine diagram PNGs, and two profiler screenshots.
Confirm that the website and X Article use the same compatible assets and that
no superseded screenshot or 160 tok/s artwork is referenced.
