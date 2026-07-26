# X upload guide: Why MoE Streaming Needs a Different Kind of Hugging Face Checkpoint

This is a manual publication runbook. It does not authorize or perform a live
X publication.

## Source and title

- Article source:
  `x-articles/why-moe-streaming-needs-a-different-hugging-face-checkpoint/article.md`
- Easy-copy page:
  `x-articles/why-moe-streaming-needs-a-different-hugging-face-checkpoint/preview-for-x.html`
- Exact title:
  `Why MoE Streaming Needs a Different Kind of Hugging Face Checkpoint`
- Author line: `David Tai, OpenSource.WTF`
- Header image: `moe-streaming-hero.png`

The source is the X-native copy of the published OpenSource.WTF article. Keep
48.04 tok/s as the headline result. Do not replace it with the 47.65 tok/s
short-prompt matrix cell or describe MTP results as current API-server
throughput.

## Before opening the composer

1. Use an X account with access to the long-form Articles composer.
2. Keep this guide, `article.md`, and `posts/images/` open.
3. Upload the PNG files listed below. The SVG files are the website sources,
   not the X upload assets.
4. Keep the q4 HumanEvalPlus comparison and GLM t158 quality caveat next to
   their corresponding performance claims.

Official references:

- [About Articles](https://help.x.com/en/using-x/articles)
- [Photo formats and size limits](https://help.x.com/en/using-x/posting-gifs-and-pictures)
- [Writing image descriptions](https://help.x.com/en/using-x/picture-descriptions)

## Easy-copy workflow

1. Open `preview-for-x.html` in a browser.
2. Click **Copy title** and paste it into X's title field.
3. Add `moe-streaming-hero.png` as the X Article header.
4. Click **Copy article body** and paste it into the composer.
5. Delete the first black image guide. It marks the header image and should not
   become a duplicate inline image.
6. Replace the other two black image guides with their named PNG files.
7. Paste each yellow ALT line into the corresponding image-description field.
8. Keep the italic caption beneath each inline image, then delete the remaining
   black and yellow guide bars.
9. Preview the article at desktop and mobile widths before publishing.

If browser clipboard permissions block the button, click inside the white
article panel and use `⌘A`, then `⌘C`. `article.md` remains the source of truth
and the manual fallback.

## Image placement order

### 1. Header

- File: `moe-streaming-hero.png`
- Placement: Article header, before the opening 48.04 tok/s claim
- Alt: An SSD expert bank sends only selected weight tiles through a
  unified-memory processor into a bounded resident pool
- Caption: The router chooses the experts. The checkpoint determines whether
  each one is already resident or has to come from SSD.

### 2. Expert-major checkpoint

- File: `checkpoint-to-expert-bank.png`
- Placement: “We rebuilt the checkpoint around the router”
- Alt: Ordinary component-major safetensors are extracted into resident
  shards, an authoritative manifest, and fixed expert-major records in
  experts.bin
- Caption: The published checkpoint contains the expert-major bank MTPLX
  consumes, so each user does not have to build a second local copy.

### 3. Islands and paging

- File: `islands-and-paging.png`
- Placement: “How we kept paging bounded”
- Alt: A resident router sends selected expert IDs either to a fully resident
  island or through hits and checked misses in a fixed paged slot bank
- Caption: Islands keep a whole routed layer resident. Paged layers use bounded
  slots and checked SSD reads, then restore the router's original assignment
  order.

## Compatibility manifest

All three upload assets are PNG files below 5 MB.

| PNG | Dimensions | Bytes | Workspace location |
|---|---:|---:|---|
| `moe-streaming-hero.png` | 1672×941 | 2,172,432 | `posts/images/moe-streaming-hero.png` |
| `checkpoint-to-expert-bank.png` | 1600×900 | 717,035 | `posts/images/checkpoint-to-expert-bank.png` |
| `islands-and-paging.png` | 1600×900 | 917,844 | `posts/images/islands-and-paging.png` |

## Pre-publish check

1. Confirm the title and author line match this guide.
2. Confirm the header is not duplicated inline.
3. Confirm all three image markers have been replaced in the listed order.
4. Confirm every image has its supplied description and caption.
5. Confirm the opening uses “I” for the investigation, explains that the first
   Hy3 runtime required a second expert bank, and mentions 48.04 tok/s once.
6. Confirm the 1,024-input, 256-output protocol and the 320-token q4 sweep stay
   in the q4 section rather than moving into the opening.
7. Confirm the q4 HumanEvalPlus result remains 142/164 versus 143/164 for q8,
   with McNemar p=1.0.
8. Confirm the GLM result still says 5.33 tok/s AR and 5.93 tok/s MTP D3 and
   still states that t158 has no end-to-end task-quality receipt.
9. Confirm the article does not describe the current streamed server as an MTP
   route.

Publishing remains a manual action in the authenticated X session.
