---
title: "Checkout Success IDOR: How an Order Number Was All You Needed"
tags: [idor, access-control, writeup]
thumb: idor
thumb_img: /assets/img/thumbs/checkout.gif
severity: High
---

This one started as a completely routine part of a checkout flow test, following the redirect after a successful purchase, and turned into full read access on every attendee's personal information across the platform.

## What I was actually testing

After completing a purchase, the platform redirects to a "checkout success" page that confirms the order and displays the ticket details: buyer name, contact info, and a QR code for entry. Standard post-purchase confirmation pattern.

The URL looked roughly like this:

```
https://example-platform.test/checkout/success/11482
```

That trailing number is an order ID. Sequential, incrementing, and, critically, never checked against the session making the request.

## Confirming it

I changed `11482` to `11481` and reloaded. A different buyer's full confirmation page loaded: name, email, phone number, and a valid QR code for their ticket. No login, no session token, no ownership check of any kind. Just an integer in a URL.

From there the impact is obvious: the ID space is small enough and sequential enough to enumerate every order the platform has ever processed. That's every attendee's PII, plus a usable QR code that could, in principle, be redeemed by someone who was never at the event.

## What made this worse than a typical IDOR

Two things compounded the severity here:

1. **No rate limiting or anomaly detection** on the endpoint, so enumeration could run unattended and unnoticed.
2. **The exposed data included an entry credential (QR code)**, not just informational fields. This wasn't "read someone's name." It was "read someone's name and also be able to walk in wearing their identity."

## The fix, prioritized

- Bind the checkout success page to the authenticated session or a single-use, unguessable token generated at checkout, not a sequential database ID.
- If a public, no-login confirmation link is a product requirement, generate a long random token (UUID or equivalent) per order instead of exposing the primary key.
- Add authorization checks server-side regardless of URL guessability. "Hard to guess" is not the same as "protected."

## Takeaway

Any time a purchase, booking, or account flow redirects to a page identified by an incrementing ID, that ID is worth testing before anything else. It's one of the fastest ways to go from "functional walkthrough" to "full customer data exposure" in a single request, and it's still one of the most common things I find in checkout flows specifically, probably because the success page usually gets built last, under time pressure, after the auth-heavy parts of the flow are already done.
