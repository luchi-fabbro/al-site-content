---
title: "This is a draft"
description: "You should only see this in local dev, not in production."
date: "2026-12-31"
tags:
  - draft
draft: true
---

If `draft: true`, this post is hidden on the **production** build but still visible when you run `npm run dev`.

Use it to preview work in progress.

## Draft-only checklist

- [ ] Final title and hero image
- [ ] Proofread intro
- [ ] Set `draft: false` when ready

```bash
npm run dev
# open /blog/draft-example
```
