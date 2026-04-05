---
title: "Personal site (this one)"
description: "Astro on Cloudflare, Markdown from GitHub, KV cache, Turnstile contact form."
image: "https://images.unsplash.com/photo-1460925895917-afdab827c52f?w=1200&h=675&fit=crop"
repo: "https://github.com/yourusername/your-content-repo"
demo: "https://angeloluchi.com"
order: 1
featured: true
tech:
  - Astro
  - Cloudflare Workers
  - TypeScript
  - Tailwind CSS
draft: true
---

This project powers your personal homepage: **projects** and **blog** posts are Markdown files in a single public repository.

## Highlights

- **Git-sourced content** — review changes in PRs like any other code.
- **Edge caching** — KV + CDN so GitHub sees few repeated fetches.
- **Rich posts** — images, `<video>`, fenced code, tables, and task lists (see the blog demos).

### Screenshot-style image in the body

![Desk setup](https://images.unsplash.com/photo-1498050108023-c5249f4df085?w=900&h=500&fit=crop)

## Features

1. **GitHub-sourced MD** — edit content where you already work.
2. **Edge caching** — KV stores listings and raw files to protect API rate limits.
3. **Search** — client-side index built from the same cached content.

```ts
// How slugs map to files
const path = `${blogPath}/${slug}.md`;
```

```json
{ "adapter": "@astrojs/cloudflare", "output": "server" }
```

Replace `repo` and `demo` in the frontmatter with your real URLs after you publish.
