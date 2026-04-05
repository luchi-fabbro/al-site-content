---
title: "HAB Log"
description: "A private voice journal that transcribes everything you say and makes it searchable — so you can talk through your thoughts today and actually find them tomorrow."
image: "https://cdn.datanut.net/assets/images/hablogs_record_main.png"
demo: "https://www.hablogs.com"
order: 1
featured: true
date: "2026-04-01"
tech:
  - Astro
  - Cloudflare Workers
  - Cloudflare R2
  - Cloudflare D1
  - Cloudflare Vectorize
  - Cloudflare Workers AI
  - TypeScript
  - React
  - Hono
draft: false
---

Think out loud. Find it later.

HAB Log is a private voice journal that transcribes everything you say and makes it searchable, so you can talk through your thoughts today and actually find them tomorrow.

[Try it free](https://www.hablogs.com) · [Log in](https://www.hablogs.com/login)

---

## Why I built it

I've always thought out loud. Journaling helped me work through problems, hash out decisions, and process what was happening in my life, but I didn't want to type. I wanted to talk, and then come back later and actually find what I said.

There's something different about speaking your thoughts versus writing them. When you type, you edit as you go. You pause, reconsider, delete. The result is polished but often hollow: a cleaned-up version of what you actually thought, not the thought itself. Speaking in a continuous stream doesn't give you that escape hatch. You find out what you actually think by hearing yourself say it.

For over ten years, I searched for something that would let me capture that. A private video journal where I could speak freely, have it transcribed automatically, and search it like a conversation. It didn't exist. Not the way I needed it to.

When the tools were finally there, I built HAB Log from scratch, exactly the way I'd always wanted it. Late 90s cyberpunk aesthetic included. That part was non-negotiable.

![HAB Log — record screen](https://cdn.datanut.net/assets/images/hablogs_record.png){width=400}

---

## What it does

HAB Log is built around three ideas.

**No pause button.** You record in a single uninterrupted stream of video and audio. The friction is the point: talking through something without stopping is how you actually find out what you think. There's no editing mid-sentence, no second-guessing yourself, no cleaned-up version of the thought. Just you, speaking, in real time. Open a session, grant your camera and mic, and go. When you're done, save it and everything flows into your library automatically.

**Searchable by meaning.** Every session is automatically transcribed in the cloud with no software to install and no local processing to babysit. Once it's in your library, you don't have to scrub through video or hunt through a wall of text. Just ask, in plain English, what you're looking for. HAB Log searches the meaning of what you said, not just the exact words. It surfaces the relevant moments and, when it can, drafts a short answer grounded directly in your transcripts.

- "What did I say about the pricing decision?"
- "When did I talk about feeling stuck on this project?"
- "What was my thinking on hiring?"

**Yours alone.** Your recordings, transcripts, and search index are scoped to your account. HAB Log doesn't train on your content, doesn't analyze what you say, and has no feed, no community, and no reason to look at your entries. What you record stays yours, full stop.

![HAB Log — library view](https://cdn.datanut.net/assets/images/hablogs_library.png){width=400}

---

## The research behind it

Journaling isn't a wellness trend. It's one of the most studied self-improvement habits in psychology, with decades of peer-reviewed research behind it. A 2022 meta-analysis found that journaling produced measurable reductions in anxiety and PTSD symptoms. Research from James Pennebaker at the University of Texas, the psychologist who pioneered expressive writing, shows that people who regularly write about their thoughts report better emotional clarity, lower stress, and even fewer sick days.

The mechanism isn't mystical. Talking about what you're experiencing forces your brain to organize chaotic thoughts into coherent narrative. That process alone reduces the mental load of carrying unresolved feelings around.

The catch is that most people don't stick with it. Blank pages are intimidating. Typing feels clinical. HAB Log removes that friction entirely. You hit record and go.

![HAB Log — search screen](https://cdn.datanut.net/assets/images/hablogs_chat.png){width=400}

---

## How it's built

HAB Log runs entirely on Cloudflare's infrastructure, chosen for its performance, privacy guarantees, and global reach. All recording, transcription, and semantic search happen at the edge, with media and data isolated per account. There's no central server, no shared processing pipeline, and nothing that touches your content except the systems serving it back to you.

Built with TypeScript, React, and Cloudflare Workers AI.

---

## Try it

HAB Log is free to start. No credit card. No public profile. Just a private place to think out loud.

[hablogs.com](https://www.hablogs.com)
