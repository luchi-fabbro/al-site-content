---
title: "ifconfig.help — IP Lookup and Network Troubleshooting"
description: "A clean, fast tool for checking your public IP, geolocation, ASN, TLS details, and more — with curl-friendly plain-text endpoints for scripting and terminal use."
date: "2026-03-30"
tags:
  - Cloudflare
  - Networking
  - Tools
  - Infrastructure
  - DevOps
draft: false
---

I built [ifconfig.help](https://ifconfig.help) as a straightforward network diagnostic tool — the kind of page I found myself reaching for constantly while troubleshooting infrastructure, and never quite happy with what existed. It shows your public IP, geolocation, ASN, protocol details, TLS information, and client context in one place, with no ads and no friction.

---

## What it shows

Hit the page in a browser and you get a full diagnostic snapshot — your public IP and protocol version, geolocation down to city and timezone, ASN and organization, HTTP protocol version, Cloudflare colo, TLS version and cipher suite, and client details like OS and device type.

A typical lookup looks something like this:

```
IP      203.0.113.42
VER     IPv4
ASN     AS12345
ORG     EXAMPLE ISP
PROTO   HTTP/3
COLO    EWR
TLS     TLSv1.3
CIPHER  AEAD-AES256-GCM-SHA384
```

---

## Terminal use

The root URL returns plain text — just the IP, nothing else. One command, one result, ready to pipe wherever you need it.

```bash
curl ifconfig.help
```

Force IPv4 or IPv6 with `-4` or `-6`. The page documents additional options for wget, PowerShell, Python, Node.js, shell aliases, DNS-layer lookups via `dig`, and local interface commands for Linux and macOS — all available at [ifconfig.help](https://ifconfig.help).

---

## Troubleshooting tips

**Slow curl?** Add `-4` or `-6` to force a protocol. Happy eyeballs negotiation can stall if one IP stack is broken on your network.

**VPN leaking IPv6?** Run `curl -6 ifconfig.help`. If you get a real IPv6 address while on VPN, your tunnel isn't handling IPv6.

**Behind CGNAT?** If your router's WAN IP is in the `100.64.0.0/10` range, you're behind carrier-grade NAT. Port forwarding won't work without your ISP's help or an outbound tunnel.

**DNS vs HTTP?** Use the `dig` commands on the page to check whether your DNS and HTTP traffic are exiting from the same IP — they often aren't on split-tunnel VPNs.

---

[ifconfig.help](https://ifconfig.help) — built on Cloudflare.
