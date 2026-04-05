---
title: "FMGV — Google Voice for FileMaker"
description: "A custom Groovy plugin and FileMaker interface that brought Google Voice calling and SMS into FileMaker Pro — featured at FileMaker DevCon 2010 and presented by John Sindelar of SeedCode during the keynote."
date: "2010-11-04"
tags:
  - FileMaker
  - Google Voice
  - Java
  - Groovy
  - ScriptMaster
  - API
  - SMS
draft: false
---

FMGV was a custom plugin and FileMaker interface that connected Google Voice directly to FileMaker Pro, allowing users to place calls and send SMS messages to any mobile phone from inside a FileMaker solution. It was featured at FileMaker DevCon 2010 and presented during the keynote by John Sindelar of SeedCode — which was a genuinely exciting moment for a side project built on the edge of what was technically possible at the time.

<video controls width="100%" style="margin: 1.5rem 0; border-radius: 4px;">
  <source src="https://cdn.datanut.net/assets/videos/FileMaker-DevCon-TV-360Works-_-The-Drool_480p.mp4" type="video/mp4" />
  Your browser does not support the video tag.
</video>

*Jesse Barnum, President of 360Works, and Angelo Luchi discuss and demonstrate ScriptMaster 4 with Google Voice integration. FileMaker DevCon, November 4, 2010.*

---

## A little Google Voice history

Google Voice launched on March 11, 2009, built on top of GrandCentral — a call-forwarding service Google had acquired in 2007. It gave users a single phone number that could ring multiple devices simultaneously, added voicemail transcription, and introduced SMS management through a web interface. At the time, that was genuinely novel: texting was still entirely tied to your carrier and your handset, and the idea of sending or receiving messages from a browser felt like the future.

Through most of 2009 the service was invitation-only. It only dropped that requirement in June 2010, shortly before DevCon. By the time FMGV was built and presented, Google Voice had around 1.4 million users — and critically, it still had no official public API.

That last part is important.

---

## The technical challenge: no API

Google Voice in 2010 did not offer a developer API. There was no OAuth flow, no documented endpoints, no supported way to interact with it programmatically. What existed instead was the web interface — and a community of developers willing to reverse-engineer it.

The foundation I built on was [pygooglevoice](https://pypi.org/project/pygooglevoice/), a Python library by Joe McCall and Justin Quick, first released in July 2009. It worked by authenticating with Google's login system and then screen-scraping the Google Voice web interface — simulating what a browser would do, parsing the responses, and extracting the data. It was fragile by nature: any change to Google's HTML could break it. But it worked, and it was the only option available.

Rather than run Python directly, I ported the approach into Groovy — a JVM language that runs natively inside the 360Works ScriptMaster plugin. That meant I could implement the same screen-scraping and HTTP session logic in Groovy, compile it inside ScriptMaster, and surface it as callable functions inside FileMaker scripts. No external processes, no Python runtime to manage, no bridge between environments. The whole stack ran inside FileMaker.

---

## What it could do

Once the Groovy layer was working, the FileMaker integration handled the full workflow:

- Place outbound calls via Google Voice to any mobile number
- Send SMS messages directly from a FileMaker layout
- Authenticate with a Google account from within the interface
- Manage contacts and call targets from inside FileMaker

The FileMaker UI was designed to feel native — not like a technical demo, but like something a real business could deploy. The goal was a practical communication tool built entirely inside the FileMaker ecosystem.

---

## The DevCon moment

Being featured at FileMaker DevCon during the keynote — presented by John Sindelar of SeedCode alongside Jesse Barnum of 360Works — was a highlight. It validated the approach of using ScriptMaster as a bridge to the broader Java/Groovy ecosystem, and showed the FileMaker community that integrations once considered out of reach were genuinely achievable.

In 2015, Google deprecated their legacy login system and moved to OAuth. That was the end of FMGV.

The screen-scraping approach had always depended on being able to authenticate with a simple username and password against Google's login flow. OAuth replaced that with a token-based authorization model that required a registered application, a client ID, and a proper API integration — none of which Google Voice officially supported for third-party developers. There was no path forward that didn't require Google to open an API they had no interest in opening.

Since FMGV was always free, there was no revenue to justify the engineering investment of rebuilding the auth layer around an unsupported service. It had a good five-year run, got its DevCon moment, and went out on its own terms. Sometimes that's the right ending for a project.
