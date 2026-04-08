---
title: "Remember VAX MAIL? I Built an Emulator."
description: "A working browser-based emulator of the VAX MAIL system from DEC's VMS operating system — the email interface that defined university computing in the late 1980s and early 1990s."
date: "2026-04-07"
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

The live emulator at [vax.aluchi.com](https://vax.aluchi.com) is a faithful recreation of that experience — and it's fully functional, not just a demo. Messages can be sent between registered users on the system, or to and from real internet email addresses via `@vaxmail.net`.

### Mail commands

| Command | Description |
| ------- | ----------- |
| `DIR` | List all messages with number, sender, date, subject |
| `READ [n]` | Read a message (defaults to next unread) |
| `SEND` | Compose a new message |
| `REPLY` | Reply to current message (pre-fills To and subject) |
| `DELETE [n]` | Delete a message |
| `SEARCH <term>` | Search subject, sender, and body |
| `DUMP` | Generate a plain-text download of all messages |
| `PURGE` | Delete all messages (requires confirmation) |
| `WHOAMI` | Display your account details and VAX address |
| `WHOIS` / `FINGER` | Look up another user |
| `PASSWD` | Change your password |
| `HELP` | List all commands |
| `LOGOUT` | End the session |

**Getting started:** At the `Username:` prompt, type `NEW` to create an account. You'll be walked through choosing a username, full name, and password. New accounts receive a welcome message from `SYSTEM`. Once you're in, type `HELP` at the `MAIL>` prompt for a full list of commands, or just start with `DIR` to see your mailbox.

### Internet mail

Enabling external mail with `SET EXTERNAL ON` assigns you a real `<username>@vaxmail.net` address. Outbound messages to any address containing `@` route via Mandrill transactional email. Inbound mail sent to your VAX address is delivered to your mailbox via Cloudflare Email Routing. `REPLY` on an internet message routes back to the real sender. The directory listing marks inbound internet messages with `[EXT]` so you know where they came from.

---

## How it's built

The frontend is [xterm.js](https://xtermjs.org/) — a full VT100/ANSI terminal emulator that runs in the browser over a WebSocket connection. It handles character-by-character rendering, escape sequences, and cursor behavior.

The backend runs entirely on Cloudflare's serverless infrastructure:

| Layer | Technology |
| ----- | ---------- |
| Runtime | Cloudflare Workers |
| Session state | Durable Objects (hibernatable WebSockets) |
| Database | Cloudflare D1 (SQLite) |
| Rate limiting | Cloudflare KV |
| Outbound email | Mandrill transactional email API |
| Inbound email | Cloudflare Email Routing |
| Email parsing | postal-mime |

Each browser session is a persistent Durable Object. The WebSocket routes to the same instance via a session ID, so refreshing the page restores the session without re-login.

### Security

Passwords are hashed with a strong KDF, compared using constant-time comparison to prevent timing attacks, and accounts lock after repeated failed attempts. Connections are throttled per IP.

Message bodies are encrypted at rest. The encryption model is designed so that changing your password requires no message re-encryption regardless of mailbox size, and session keys are never written to persistent storage.

### The hard parts

The infrastructure was straightforward. The fiddly things were:

Line discipline — implementing backspace, line buffering, and cursor handling character by character, including backspace that works across line boundaries and restores the previous line for editing.

Full-screen composition — the `SEND` command needs a minimal text editor experience, with `Ctrl+Z` to send and `Ctrl+C` to cancel, that feels authentic rather than bolted on.

VT100 accuracy — getting the escape sequences right so the terminal behaves like the real thing, not a rough approximation of it.

No-echo password input — a small detail that matters enormously to the feel. The cursor doesn't move while you type your password. It's correct because it has to be.

---

## Why build this

This is what email looked like when I was in college. Everyone had to go to the computer lab and use the mainframe terminals. It was communal in a way that inboxes on phones never have been. You sat next to people. You waited your turn. The interface imposed a slowness that, looking back, felt almost deliberate.

I wanted to build something that let people experience that — or remember it. The VAX MAIL prompt is a specific kind of nostalgia: not for a simpler time, but for a time when computing still felt like something you had to learn, and learning it meant something.

Go try it: [vax.aluchi.com](https://vax.aluchi.com)
