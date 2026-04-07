---
title: "Remember VAX MAIL? I Built an Emulator."
description: "A working browser-based emulator of the VAX MAIL system from DEC's VMS operating system — the email interface that defined university computing in the late 1980s and early 1990s."
date: "2026-04-06"
tags:
  - Development
  - Cloudflare
  - Nostalgia
  - VAX
  - Email
draft: false
---

Before you had an inbox, before you had a client, before you had a browser — you had a terminal. You sat down at a workstation in the computer lab, logged into the mainframe, and waited for the cursor to blink.

If you were in college in the late 1980s or early 1990s and your school ran Digital Equipment Corporation hardware, you know exactly what I'm talking about. You know the `MAIL>` prompt. You know the `%MAIL-I-DELIVERED` confirmation. You know the specific dread of accidentally typing `DELETE` on the wrong message number.

I built an emulator: [vax.aluchi.com](https://vax.aluchi.com)

---

## A little history

Digital Equipment Corporation introduced the VAX (Virtual Address eXtension) line of 32-bit minicomputers in the mid-1970s, and they dominated university and research computing through the 1980s. The accompanying operating system, VMS, was ahead of its time — it offered peer-to-peer networking, clustering, and a fully integrated office suite including email long before those things were commonplace.

The email system was called VAX MAIL, built on the Mail-11 protocol. It was one of the most widely used email systems of the 1980s, running on DEC's DECnet networking infrastructure and present at universities, government agencies, research labs, and enterprises worldwide. Mail-11 messages had `To:`, `Cc:`, and `Subject:` headers, timestamp-stamped entries, and a directory listing that numbered every message in your mailbox.

This was email before email felt like email. You didn't check it on your phone. You walked to the computer lab, sat down at a VT100 terminal, and typed commands.

---

## What it was like

The experience was nothing like a modern inbox. You logged in at the `Username:` prompt, waited for `Password:` (with no echo — the cursor didn't move while you typed), and were greeted with something like:

```
You have 3 new messages.
MAIL>
```

From there, everything was commands. `DIR` to list your messages. `READ` or `READ 3` to open one. `SEND` to compose — which dropped you into a minimal text editor where you typed your message line by line and pressed `Ctrl+Z` to send. `REPLY`, `DELETE`, `FORWARD`. `HELP` if you were lost.

The whole interaction was text in, text out. No mouse. No icons. Just the VT100 escape sequences painting characters on a green or amber screen.

---

## The emulator

The live emulator at [vax.aluchi.com](https://vax.aluchi.com) is a faithful recreation of that experience, built on a modern Cloudflare stack.

**What works at the `MAIL>` prompt:**

| Command | Action |
| ------- | ------ |
| `DIR` / `DIRECTORY` | List all messages |
| `READ [n]` | Read a message (defaults to first unread) |
| `SEND` | Compose a new message |
| `REPLY` | Reply to the current message |
| `DELETE [n]` | Delete a message |
| `HELP` | Show available commands |
| `LOGOUT` | End the session |

At the `Username:` prompt, type `NEW` to register an account. New accounts receive a welcome message from `SYSTEM`.

---

## How it's built

The frontend is [xterm.js](https://xtermjs.org/) — a full VT100/ANSI terminal emulator that runs in the browser over a WebSocket connection. It handles the character-by-character rendering, escape sequences, and cursor behavior that make the terminal feel authentic rather than simulated.

The backend runs entirely on Cloudflare's infrastructure. Each session is managed by a Durable Object, which maintains the terminal state — login status, current prompt, which menu the user is in — on a per-connection basis. Durable Objects are a near-perfect fit for this kind of stateful, persistent session pattern. User accounts and mailboxes live in a relational database, with session tokens and ephemeral state handled separately.

The trickiest parts were not the infrastructure — they were the small fiddly things that make the experience feel right. Line discipline: implementing backspace, line buffering, and cursor handling character by character. Full-screen composition for the `SEND` command, which needs to behave like a minimal text editor. VT100 escape sequence accuracy. And no-echo password input, which is a small detail that matters enormously to the feel.

The session state machine looks roughly like this:

```
login_username
    ↓
login_password  ──── wrong creds ──→ login_username
    ↓
mail_prompt  ←─────────────────────────────────────┐
    ├─ DIR          → display, return               │
    ├─ READ n       → display, return               │
    ├─ DELETE n     → delete, return                │
    ├─ REPLY        → compose (pre-filled To:)      │
    ├─ SEND         → compose_to                    │
    │                    ↓                          │
    │               compose_subject                 │
    │                    ↓                          │
    │               compose_body ──Ctrl+Z──────────→┘
    │                            ──Ctrl+C──────────→┘
    └─ LOGOUT       → session cleared, WS closed

NEW at login_username:
    register_username → register_password → register_verify → mail_prompt
```

---

## Why build this

This is what email looked like when I was in college. Everyone had to go to the computer lab and use the mainframe terminals. It was communal in a way that inboxes on phones never have been. You sat next to people. You waited your turn. The interface imposed a slowness that, looking back, felt almost deliberate.

I wanted to build something that let people experience that — or remember it. The VAX MAIL prompt is a specific kind of nostalgia: not for a simpler time, but for a time when computing still felt like something you had to learn, and learning it meant something.

Go try it: [vax.aluchi.com](https://vax.aluchi.com)
