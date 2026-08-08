---
title: "An Unauthenticated Staff Portal, Sitting in Plain Sight"
tags: [recon, access-control, writeup]
thumb: recon
severity: High
---

Not every finding needs a clever payload. Sometimes the vulnerability is that a login screen was never wired up to the routes it was supposed to protect, and the only skill involved is noticing.

## How I found it

Early in an engagement I run subdomain and endpoint enumeration against the target's infrastructure. Nothing exotic, just casting a wide net before doing anything targeted. One of the hits was a subdomain that didn't appear anywhere in the platform's public-facing navigation:

```
staff.example-platform.test
```

Loading it in a browser went straight to a dashboard. No redirect to a login page, no session check, no "please authenticate" interstitial. Just the internal staff tooling, open to anyone who found the subdomain.

## What was actually exposed

The portal included functionality for viewing customer orders, adjusting ticket statuses, and issuing refunds: real administrative actions, not a read-only status page. None of it required a credential of any kind.

## Why this keeps happening

In my experience this pattern shows up most often on subdomains that were spun up for internal use and assumed to be "hidden" because they weren't linked from the main site. Security by obscurity holds up right up until someone runs a subdomain enumeration tool, which takes minutes and requires no special access.

The other recurring cause is a reverse proxy or load balancer configuration that terminates auth at one layer (say, a gateway in front of the main app) while a secondary internal service sits fully exposed behind a different, unprotected route.

## The fix

- Authentication needs to be enforced at the application layer for every route that performs a privileged action, not assumed to be handled upstream by network placement or obscurity.
- Internal tooling should sit behind a VPN, IP allowlist, or SSO layer, not just an unlisted DNS name.
- Regular subdomain enumeration should be part of the platform owner's own recurring security hygiene, not something they only learn about when a pentester runs it first.

## Takeaway

Recon is not the boring part before the "real" testing starts. For platforms with any amount of internal tooling, it's often where the highest-severity finding of the entire engagement comes from. This one took longer to write up than it did to find.
