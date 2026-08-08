---
title: "Payment Amount Tampering: A Regression Worth Flagging Twice"
tags: [payment, business-logic, writeup]
thumb: payment
severity: Critical
---

This finding matters less for the technique — client-side price tampering is one of the oldest tricks in the book — and more for what it says about how "fixed" vulnerabilities need to be re-verified, not just trusted.

## The vulnerability

The platform's checkout flow calculates the order total client-side and sends it to the payment gateway's initialization request as part of the payload. Intercepting that request in a proxy and editing the `amount` field before it reaches the gateway was enough:

```
"amount": 3098   →   "amount": 100
```

The gateway accepted the modified value, generated a valid payment session for the tampered amount, and the checkout completed successfully — full order fulfilled for a fraction of the listed price. The server never re-validated the submitted amount against the actual price of the item in its own catalog before handing off to the payment provider.

## Why "regression" is the important word here

This exact class of bug had already been reported and remediated once before on this platform. Finding it again meant one of two things happened: either the fix was scoped too narrowly and missed a parallel code path, or a later change reintroduced the original behavior without anyone re-testing the payment flow.

Both are more concerning than a first-time finding. A first-time bug means something was missed. A regression means the fix wasn't durable — and if it happened once without being caught, it can happen again silently after the next unrelated deploy, unless the fix is backed by a test that specifically asserts server-side price validation.

## The fix

- Recalculate the order total server-side from the authoritative product catalog immediately before initializing any payment session — never trust a client-submitted amount.
- Add an automated regression test asserting that a tampered client-side amount is rejected, so this specific class of bug can't silently reappear after a future refactor.
- Treat "previously patched" vulnerability classes as permanent additions to the regression test suite, not one-off fixes that are trusted to stay fixed.

## Takeaway

When you're reporting a bug that's been seen before on the same target, say so explicitly and say it loudly. A regression changes the conversation from "here's a gap" to "here's a process problem" — and the fix that actually matters is the one that prevents a third occurrence, not just the second patch.
