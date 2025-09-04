---
layout: post
title:  "[EN] [TEMPORARY] dn42 node peering list"
date:   2025-09-04 16:40:00 -0500
tag: personal dn42
---

Welcome, all dn42 people who are interested in peering with me, AS4242423797!

This page will temporary serve as a node directory where I have server presence there. I'll make a better page when I have a nice landing page (if I stopped procastinating, that is 😅).

## Desired peering requirements

- Latency should be <=20ms
- Have a public WireGuard endpoint to connect (if you don't have this, it's fine)
- BFD is configured (again, if you don't have this, it's also fine)
- MP-BGP with Extended next hop (IPv4-over-IPv6)

## Node directory

(Region code: `<country>-<nearest airport><identifier num>`, region code can be reassigned to different servers)

| Server code | Region code | Endpoint                   | IPv4 | IPv6   | Notes                                                              |
| ------------| ----------- | -------------------------- | ---- | ------ | ------------------------------------------------------------------ |
| A00         | vn-lth1     | `vn-lth4.rc.badaimweeb.me` | Yes  | Yes    | Residental network. A04-A06 goes through A00. A00 does not have a public hostname yet, endpoint is using A05 for pingable address.
| C02         | us-dfw1     | `us-dfw1.rc.badaimweeb.me` | Yes  | Tunnel |
| C04         | us-ord1     | `us-ord1.rc.badaimweeb.me` | Yes  | Tunnel |
| C05         | de-fra2     | `de-fra2.rc.badaimweeb.me` | Yes  | No     | Weak resources warning.
| C06         | vn-sgn1     | `vn-sgn1.rc.badaimweeb.me` | Yes  | Yes    |
| C07         | my-jhb1     | `my-jhb1.rc.badaimweeb.me` | Yes  | Yes    | Singapore servers can peer with this server since it has SG transit.
| C08         | vn-han1     | `vn-han1.rc.badaimweeb.me` | Yes  | Yes    |
| C09         | de-fra1     | `de-fra1.rc.badaimweeb.me` | Yes  | Yes    |
| C10         | au-syd1     | `au-syd1.rc.badaimweeb.me` | Yes  | Yes    |
| C11         | us-lax1     | `us-lax1.rc.badaimweeb.me` | Yes  | No     |
| C12         | br-gru1     | `br-gru1.rc.badaimweeb.me` | Yes  | Yes    |
| C13         | hk-hkg1     | `hk-hkg1.rc.badaimweeb.me` | Yes  | Yes    |
| C14         | us-tpa1     | `us-tpa1.rc.badaimweeb.me` | Yes  | Yes    |
| C15         | tw-tpe1     | `tw-tpe1.rc.badaimweeb.me` | Yes  | Yes    |
| E03         | vn-sgn2     | `vn-sgn2.rc.badaimweeb.me` | Yes  | Yes    |
| H01         | vn-dad1     | `vn-dad1.rc.badaimweeb.me` | NAT  | Yes    | Residental network. Hardware operated by MICHIOXD-MNT. Alternatively, you can contact him for peering on this location.

## Contact
Email me at `dn42@badaimweeb.me`. Alternatively, see my [contact page](/contact/) for more platforms.
