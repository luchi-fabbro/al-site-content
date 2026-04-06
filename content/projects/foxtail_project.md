---
title: "Foxtail Technology"
description: "A FileMaker hosting company built from a single colocated server in 2004, grown to a multi-datacenter operation over 12 years, and merged with Proof+Geist in 2021."
image: "https://cdn.datanut.net/assets/images/foxtail0.png"
demo: "https://www.ottomatic.cloud"
order: 2
featured: true
date: "2009-03-01"
tech:
  - FileMaker
  - Hosting
  - Cloud
  - AWS
  - Vultr
  - Infrastructure
  - Business
draft: false
---

Foxtail Technology was a FileMaker hosting and infrastructure company I founded in 2009 and operated for twelve years before merging with Proof+Geist in 2021. What started as a side project supporting a handful of clients grew into a multi-datacenter operation serving the FileMaker community worldwide. The company ran entirely on word of mouth, no advertising, ever.

Today Foxtail lives on as [Ottomatic](https://www.ottomatic.cloud), the platform that runs FileMaker alongside AI-based tools, as part of Proof+Geist.

<img src="https://cdn.datanut.net/assets/images/foxtail0.png" alt="Foxtail Technology" width="100%" style="border-radius: 4px;" />

---

## How it started

In 2004, a Boston realty company needed a FileMaker database to manage their property listings and agent schedules, and they needed it hosted remotely so agents could connect from anywhere. There was no real cloud in 2004. You bought hardware, you found a data center, and you figured it out.

I bought a single 1U server: dual CPU, 8GB RAM, two 200GB drives in a RAID mirror. I colocated it in a Boston data center on a burstable 3MB commit over a 100MB dedicated line. FileMaker Server 7, Plesk, and a mail server shared that single machine. That was the entire stack. That was the beginning.

The client was happy, the server held up, and a handful of other FileMaker users started asking if I could host their solutions too. A side project was quietly becoming something more.

---

## The cloud evolution

The infrastructure story of Foxtail is essentially a decade-long tour through the evolution of cloud hosting.

**2007 — Amazon Web Services.** I moved everything to AWS early, when EC2 was still new and most people had never heard of it. The upside was elastic compute on demand. The downside was a hard lesson in ephemeral storage: if you accidentally powered off an instance, you lost everything on it. Performance-to-cost was also difficult to justify for a hosting operation built on reliability. AWS was not yet the platform it would become.

**2008 — GoGrid.** Looking for better performance and more predictable infrastructure, I landed on GoGrid, which launched in March 2008 as one of the early cloud providers competing directly with AWS. GoGrid gave users full root access, multiple public IPs per server, and a hybrid model that blended cloud and dedicated servers on the same network — a much better fit for hosting FileMaker environments where persistence and reliability mattered more than elastic scaling.

**2009 — The push to go independent.** The 2008 financial crisis hit hard. The consulting firm I was working for, SolutionMakers, had to lay off its entire staff. In March 2009, with enough hosting revenue already coming in from dedicated and shared servers to give it a real shot, I made the call to brand and launch Foxtail Technology properly. I built a website, built a management panel, and reached out to everyone I knew in the FileMaker community to let them know I was open for both development and hosting work.

Revenue was tight at first. To build relationships and visibility, I found myself crashing FileMaker DevCons — showing up, meeting people, and letting the quality of the work speak for itself. It did.

**2012 — Ubiquiti.** Looking for an alternative to GoGrid, I moved to Ubiquiti, a smaller cloud hosting provider similar in positioning to what Vultr would become. For a time it was a solid fit. That changed when Ubiquiti was acquired by a larger IaaS provider and service quality declined noticeably. It became clear I needed to move again.

**2014 — Vultr.** The degradation in Ubiquiti's service pushed me to find something better, and I landed on [Vultr](https://www.vultr.com). Founded in 2014, Vultr offered straightforward pricing, fast NVMe-backed compute, and global data center coverage without the complexity and cost overhead of the hyperscalers. It was built for operators who wanted control without abstraction. Foxtail ran on Vultr infrastructure across multiple regions for the remainder of its independent life and the move proved to be the right one.

<div style="display: flex; gap: 1rem; margin-top: 1.5rem; flex-wrap: wrap;">
  <img src="https://cdn.datanut.net/assets/images/foxtail1.png" alt="Foxtail Technology management panel" style="border-radius: 4px; min-width: 200px; flex: 1; width: 48%;" />
  <img src="https://cdn.datanut.net/assets/images/foxtail2.png" alt="Foxtail Technology interface" style="border-radius: 4px; min-width: 200px; flex: 1; width: 48%;" />
</div>

---

## Growth and the merger

No advertising, ever. Foxtail grew entirely through reputation and personal relationships in the FileMaker community. Quality hosting and responsive support turned clients into advocates, and the network compounded over time.

By 2020, Covid presented new challenges in staffing and operations. I began a trial alignment with Proof+Geist to explore whether a closer partnership made sense. It did — more than expected. In 2021 we made the merger official. Foxtail's infrastructure, client base, and twelve years of operational knowledge folded into Proof+Geist, where I became Director of Infrastructure Operations.

The hosting platform that started as a single racked server in a Boston colo now operates as Ottomatic, running FileMaker infrastructure alongside modern AI-based tooling for a global client base.

---

## Infrastructure timeline

| Year | Platform | Notes |
| ---- | -------- | ----- |
| 2004 | Colocation (Boston) | Single 1U server, FileMaker Server 7, Plesk |
| 2007 | Amazon Web Services | Early EC2 adoption; ephemeral storage limitations |
| 2008 | GoGrid | Better performance, persistent storage, hybrid hosting |
| 2012 | Ubiquiti | Smaller IaaS provider; quality declined after acquisition |
| 2014 | Vultr | Primary hosting partner through to the merger |
| 2021 | Proof+Geist / Ottomatic | Merger; platform continues as Ottomatic |
