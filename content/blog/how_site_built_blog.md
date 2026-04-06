---
title: "How This Site Is Built"
description: "A walkthrough of the stack behind aluchi.com — Astro on Cloudflare, Markdown from GitHub, KV caching, and no CMS in sight."
date: "2026-04-02"
tags:
  - Astro
  - Cloudflare
  - Development
  - Infrastructure
draft: false
---

I've built a lot of hosting infrastructure over the years. For my own site I wanted something that reflected how I actually think about systems: content separated from code, edge-first delivery, minimal moving parts, and nothing paying for itself in complexity. Here's how it came together.

---

## The core idea

There is no CMS. Almost all copy — blog posts, project pages, the about page — lives as Markdown files in a public GitHub repository. The site code lives in a separate repo. The two never merge; content is fetched at request time from GitHub, parsed, and rendered.

This approach means I can write and publish from anywhere with a text editor and a git push. It also means the site itself has no admin panel, no database, no auth layer to maintain. The content is just files.

---

## The stack

| Layer | Choice |
| ----- | ------ |
| Framework | Astro 4, `output: "server"` |
| Hosting | Cloudflare Pages + Workers via `@astrojs/cloudflare` |
| Styling | Tailwind CSS + `@tailwindcss/typography` |
| Markdown | unified, remark (GFM), rehype (sanitize, syntax highlighting, raw HTML) |
| Frontmatter | gray-matter |
| Search | MiniSearch in the browser over a JSON index |
| Forms | Cloudflare Turnstile + Mandrill for email delivery |
| Config | `wrangler.toml` with KV bindings and `nodejs_compat` |

Astro's server output mode was the right call here. Pages are rendered at the edge on each request, which gives full control over caching headers and means content changes show up without a rebuild.

---

## Where content lives

Content lives in one GitHub repo, configured via environment variables that point to the owner, repo name, and branch. The default paths are straightforward:

- `content/blog/*.md` — blog posts
- `content/projects/*.md` — project pages
- `content/pages/about.md` — the about page

Each file has YAML frontmatter (title, date, tags, `featured`, `draft`, `repo`, and so on) and a Markdown body. Blog posts and projects are sorted newest-first by the `date` field in frontmatter.

---

## How content is read

When a page is requested, the Worker fetches content from GitHub in two steps. First, the GitHub Contents API lists `.md` files under the relevant directory to discover which slugs exist. Second, raw Markdown is fetched from `raw.githubusercontent.com` for the specific file.

An optional GitHub token improves rate limits significantly — without it, anonymous API calls are aggressively throttled and can cause intermittent failures on busy days.

Once the raw Markdown arrives, `gray-matter` splits frontmatter from body, and a remark/rehype pipeline converts the body to sanitized HTML. The pipeline supports GitHub Flavored Markdown, fenced code blocks with syntax highlighting, and a set of allowed raw HTML tags — `<video>`, `<iframe>`, and `<img>` with inline style for sizing — so rich embeds work without a custom shortcode system.

---

## Caching

Hitting GitHub on every request would be slow and would exhaust rate limits quickly. The solution is a write-through cache using Cloudflare KV, with a dedicated KV namespace binding configured in `wrangler.toml`.

Directory listings (the list of slugs under `content/blog`) are cached with a short TTL of around ten minutes. Raw Markdown files are cached longer, around 24 hours, since posts don't change often. 404 responses are cached briefly to avoid hammering GitHub for slugs that don't exist.

Cache versioning handles invalidation cleanly. A version key in KV acts as a namespace prefix. A revalidation endpoint — protected by a shared secret — bumps the version number, which causes all subsequent reads to use new keys, effectively invalidating the cache without waiting for every TTL to expire. A single curl after a content push is all it takes.

Beyond KV, middleware sets `Cache-Control` headers on rendered HTML pages with `max-age`, `s-maxage`, and `stale-while-revalidate` values. Repeat visitors often hit Cloudflare's edge cache without touching the Worker, KV, or GitHub at all.

Final HTML is intentionally not cached in KV — Markdown is cached, but the parse and sanitize step runs fresh each time. This keeps rendering consistent and avoids stale HTML if the pipeline changes.

---

## Search

Search runs entirely in the browser. A `/api/search-index` endpoint returns a JSON index built from all post and project frontmatter. MiniSearch loads it on the client side and handles queries locally with no server round-trip after the initial load. For a site this size it is fast and simple, and there is nothing to keep in sync.

---

## Images

Images use Astro's noop image service — no built-in resize pipeline. Sizing comes from Markdown or inline HTML. A small preprocessor handles `{width=…}` syntax on image tags, which lets you write:

```md
![My image](https://cdn.example.com/image.png){width=600}
```

and get a properly sized image without touching the HTML directly.

---

## Contact and forms

The contact form posts JSON to `/api/contact`. The server verifies the Cloudflare Turnstile token, then sends the message via Mandrill (Mailchimp Transactional). No email infrastructure to manage, no inbox to scrape for form submissions.

---

## Deployment

The build outputs to `dist`. Deployment is a single command:

```bash
npm run deploy
```

Which runs `wrangler pages deploy` against the project defined in `wrangler.toml`. CI can run the same command on push. There is no staging environment to babysit.

---

## Why this way

The alternative was a headless CMS, a database, a build hook pipeline, a preview environment, and a handful of third-party integrations. Instead this is a GitHub repo, a KV namespace, and a Worker. The content is readable and portable. The infrastructure is auditable. The bill is low.

It is not the most beginner-friendly setup, but for someone who has been managing infrastructure for two decades, it is exactly the kind of system I want to own.
