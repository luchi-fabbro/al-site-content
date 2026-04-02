---
title: "Rich media showcase"
description: "Reference post: images, video, embeds, lists, tables, tasks, and many code fences."
date: "2026-04-03"
tags:
  - demo
  - markdown
draft: false
---

Use this file to confirm **images**, **video**, **embedded players**, **lists**, and **syntax highlighting** all render the way you expect.

## Images (Markdown)

A landscape via Markdown image syntax (external URL):

![Foggy mountains — Unsplash](https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=900&h=500&fit=crop)

Smaller inline-friendly shot with title text:

![Ocean](https://images.unsplash.com/photo-1439405326854-014607f694d7?w=400&h=260&fit=crop "Waves at dusk")

## HTML5 video

Native `<video>` with **MP4** source (MDN-hosted sample, CC0). Your sanitizer allows `video`, `source`, and `poster`.

<video controls width="100%" poster="https://images.unsplash.com/photo-1492619375914-88005aa9e8fb?w=960&h=540&fit=crop">
  <source src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4" type="video/mp4" />
  Your browser does not support the video tag.
</video>

## Embedded iframe (YouTube)

Use sparingly for performance; good for demos and talks.

<iframe
  width="560"
  height="315"
  src="https://www.youtube.com/embed/aqz-KE-bpKQ"
  title="Big Buck Bunny — Blender Foundation (embed demo)"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
  allowfullscreen
></iframe>

## Lists

### Unordered (nested)

- First top-level item
- Second item with children:
  - Nested A
  - Nested B
    - Deeper level
- Third item

### Ordered

1. Clone the content repo
2. Push Markdown under `content/blog`
3. Trigger revalidate or wait for cache TTL

### Task list (GFM)

- [x] Wire GitHub as content source
- [x] Add KV caching
- [ ] Write your first real post
- [ ] Point `GITHUB_REPO` at this repository

## Blockquote

> “Good documentation is the difference between *confusion* and **clarity**.”  
> — handy reminder for project READMEs

## Table

| Asset type | Markdown | HTML in MD |
| ---------- | -------- | ---------- |
| Image      | `![]()`  | Optional   |
| Video      | —        | `<video>`  |
| Embed      | —        | `<iframe>` |

## Code snippets

`inline code` inside a sentence.

### TypeScript

```ts
type Post = {
  slug: string;
  title: string;
  draft?: boolean;
};

export function isPublished(post: Post, dev: boolean): boolean {
  if (dev) return true;
  return post.draft !== true;
}
```

### Shell

```bash
# Warm cache after a content push
curl -sS -X POST "https://your-domain.pages.dev/api/revalidate" \
  -H "X-Revalidate-Token: $WEBHOOK_SECRET" \
  -H "Content-Type: application/json"
```

### JSON (Wrangler-style)

```json
{
  "name": "al-sitenblog",
  "compatibility_date": "2024-12-01",
  "vars": {
    "GITHUB_OWNER": "you",
    "GITHUB_REPO": "site-content"
  }
}
```

### CSS

```css
.prose img {
  border-radius: 0.75rem;
}
```

### HTML fragment

```html
<section aria-label="Example">
  <h2>Hello</h2>
  <p>This is escaped inside the fence.</p>
</section>
```

---

That covers the usual building blocks. Delete or trim this post when you no longer need it.
