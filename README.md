# OpenSourceWTF Blog

Blog content for [opensource.wtf](https://opensource.wtf). Markdown posts that auto-deploy to the site.

## Writing a Post

Create a new `.md` file in `posts/` with YAML frontmatter:

```markdown
---
title: "Your Post Title"
date: "2026-03-15"
excerpt: "A short description of the post."
tags: ["tag1", "tag2"]
---

# Your Post Title

Your content here in markdown...
```

Push to `main` and the site rebuilds automatically.

## How It Works

1. Push markdown to this repo
2. GitHub Action dispatches `blog-content-updated` to the site repo
3. Site repo checks out this content, generates RSS/sitemap/index, and deploys
4. Posts are available as rendered HTML and raw `.md` for AI agents

## Setup

Requires a `SITE_REPO_PAT` secret — a GitHub PAT with `repo` scope to trigger the site repo rebuild.
See the [site repo setup docs](https://github.com/OpenSourceWTF/opensource.wtf#setup) for full instructions.
