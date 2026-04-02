---
title: "Hello, world"
description: "A short welcome post to verify your blog pipeline."
date: "2026-04-01"
tags:
  - meta
  - setup
draft: false
---

This is a sample post. If you can read this on your site, Markdown from GitHub is loading correctly.

## Quick checklist

1. **Repo** — `content/blog` and `content/projects` exist.
2. **Env** — `GITHUB_OWNER` / `GITHUB_REPO` match this repository.
3. **Cache** — After edits, revalidate or wait for KV TTL.

### Nested bullets

- Astro
  - Server-rendered routes
  - Cloudflare adapter
- Content
  - Markdown on GitHub
  - Frontmatter for metadata

### Tiny illustration

![Abstract gradient](https://images.unsplash.com/photo-1557682250-33bd709cbe85?w=800&h=400&fit=crop)

## What to try next

- Edit this file in your repo, push, then **revalidate** (or wait for cache TTL) to see updates.
- See **[Rich media showcase](/blog/rich-media-showcase)** for video, iframe, tables, tasks, and more code samples.

```ts
// Fenced code blocks use syntax highlighting
export function greet(name: string) {
  return `Hello, ${name}!`;
}
```

```bash
echo "Ship it 🚀"
```

Happy writing.
