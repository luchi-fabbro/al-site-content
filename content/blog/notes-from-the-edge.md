---
title: "Notes from the edge"
description: "Why serverless at the edge pairs nicely with content in Git."
date: "2026-04-02"
tags:
  - cloudflare
  - architecture
hero: "https://images.unsplash.com/photo-1451187580459-43490279c0fa?w=1200&h=600&fit=crop"
draft: false
---

Content lives in Git; the site renders it close to your readers. That keeps editing familiar and deployments simple.

### Bulleted ideas

- **Cache** markdown listings and files so you rarely hit GitHub API limits.
- **Search** can run in the browser over a cached index—no query per keystroke.
- **Drafts** stay out of production when `draft: true` in frontmatter.

### Numbered flow

1. Author commits Markdown.
2. Webhook bumps cache version _or_ TTL expires.
3. Next request rebuilds directory listings from GitHub once, then KV serves the hot path.

> A blockquote for typography checks. It should read clearly in both light and dark system themes.

## Inline media

Side-by-side style is just two images in a row in Markdown:

![Code on screen](https://images.unsplash.com/photo-1517694712202-14dd9538aa97?w=480&h=320&fit=crop)

![Server lights](https://images.unsplash.com/photo-1558494949-ef010cbdcc31?w=480&h=320&fit=crop)

## Short clip (optional)

<video controls width="100%" poster="https://images.unsplash.com/photo-1544197150-b99a580bb7a8?w=960&h=540&fit=crop">
  <source src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4" type="video/mp4" />
</video>

## Comparison table

| Approach        | Pros              | Cons           |
| --------------- | ----------------- | -------------- |
| Git + MD        | Versioned, simple | No WYSIWYG     |
| Headless CMS    | Editorial UI      | Extra service  |
| DB-only content | Dynamic           | Heavier to run |

### Task list

- [x] Ship first version
- [ ] Add RSS feed (future)
- [ ] Tune cache TTLs per environment

```json
{
  "edge": true,
  "content": "github"
}
```

---

End of sample post.
