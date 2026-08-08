---
title: "Cross-Event Ticket Validation Bypass: A Business Logic Flaw Scanners Can't Find"
tags: [access-control, business-logic, writeup]
thumb: access-control
severity: Critical
---

During a recent engagement testing an event ticketing platform, I found a flaw that no automated scanner would ever flag, because there was nothing technically broken. Every request was well-formed. Every response was a 200. The bug was in what the system assumed rather than what it checked.

## The setup

The platform sells tickets to multiple events run by different organizers. Each ticket has a unique token, and at the door, staff scan that token through a validation endpoint that returns whether the ticket is valid.

A simplified version of the validation query looked like this:

```
SELECT * FROM tickets
WHERE token = :token
AND status = 'active'
```

That query answers one question: *does this token belong to a real, unused ticket?* It never asks the second, more important question: *does this ticket belong to the event being scanned right now?*

## The bypass

I bought the cheapest ticket available, a free RSVP-only event, and scanned its token against the validation endpoint for a separate, paid event. It came back valid. The scanner had no way to know the ticket wasn't meant for that door.

In practice, that means a single free ticket, or any valid ticket from any event on the platform, would grant entry anywhere. On a platform whose entire business model is charging for entry, that's a direct path to bypassing payment altogether.

## Why this matters more than a technical bug

A misconfigured header or an exposed endpoint is usually the result of a mistake. This wasn't a mistake in the traditional sense: the validation endpoint worked exactly as it was written. It just was never written to enforce a scope that the business obviously requires. That's the difference between a vulnerability scanner catching something and a person sitting down and asking "what should this system actually be allowed to do?"

This is also why manual, business-logic-driven testing still matters even with mature scanning tooling in the pipeline. Nothing about this flaw would trip an OWASP Top 10 signature. It only shows up when you model the system the way its owner would describe it, and then check whether the code agrees.

## The fix

One additional clause:

```
SELECT * FROM tickets
WHERE token = :token
AND event_id = :event_id
AND status = 'active'
```

Scope the validation to the event being scanned, not just the token's existence. Simple in hindsight, easy to miss in a codebase where "does this ticket exist" and "is this ticket valid for this door" were never treated as two separate questions.

## Takeaway

When you're auditing anything with multiple tenants, events, or scopes sharing one validation path, don't just test whether a resource exists. Test whether it's *scoped correctly to the context it's being used in*. That question is almost never covered by default in an ORM's generated queries, and it's rarely covered by a scanner either.
