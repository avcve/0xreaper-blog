---
title: "How People Know They've Been Hacked Within Minutes (And How I Set Mine Up for Free)"
date: 2026-11-04
tags: [monitoring, honeypots, automation, web3]
thumb: default
thumb_img: /assets/img/thumbs/monitoring.gif
---

With everyone getting hacked recently in October, I'm glad I took steps earlier to set up alerts, honeypots and MCP automated reporting systems to protect the projects I audit. I'll be breaking down how you can do what I did for free with Manus and some other tools.

When I was really inexperienced I often wondered how people would know they'd been pwned minutes after getting hacked. It always made me really curious, and I'm glad I have the answer to that question now.

Let's get into it.

## So how do they know so fast?

Nobody is sitting in front of a screen waiting for it to happen. Something is watching for them.

Most of the "we noticed within minutes" stories come down to an alert firing on something that should never happen. A contract owner changing. A proxy getting upgraded. Funds leaving in a pattern that doesn't match normal use. An attacker's first move is usually loud, and the alert just has to be listening when it happens.

My setup has three layers. Alerts tell me something happened. Honeypots tell me someone is poking around. The automated reporting tool tells me what the project looks like every single day, so I can spot when something changes.

## Layer 1: Alerts

Start with one question: what are the five things that, if they happened right now, would mean something is wrong?

For a smart contract, mine look like this:

- Ownership transferred
- Implementation or proxy upgraded
- Pause or unpause called
- A new role or admin granted
- A single outflow above a threshold I set

For the off-chain side:

- A DNS record changed
- A frontend deploy I didn't make
- A new collaborator or deploy key on the repo
- A secret showing up in a commit

Plenty of tools have free tiers that cover this. Tenderly alerts and the Etherscan address watch list handle the on-chain events, and GitHub's built-in secret scanning and security alerts cover the repo side. A simple uptime monitor on the site catches the rest. Free tiers change, so check the current limits before you build anything around them.

One tip: send everything to one place, like a Telegram channel or one inbox. An alert you have to go and look for is an alert you will miss.

## Layer 2: Honeypots

The idea is simple. Put something out there that no legitimate user would ever touch. If it gets touched, you know it's not a legitimate user, and you get almost zero false alarms.

A few ways to do it:

- A canary token (canarytokens.org is free) hidden in a fake `.env` file or a document with a name an attacker would go looking for
- A decoy endpoint like an old admin path that nothing real uses, set to log and alert on any request
- A wallet holding dust only, with its "key" sitting in a fake config file. If that wallet moves, someone has been through your files

Some rules I stick to. Only use your own infrastructure. Keep the bait at dust level. Never put bait in front of real users. And only do it on projects you're authorized to work on.

## Layer 3: MCP automated reporting

MCP is a standard way for an AI tool to talk to another app through a connector. In my setup, Manus does the scanning work and Notion is where everything lands.

![MCP automated reporting tool]({{ '/assets/img/posts/mcp-reporting-tool.jpg' | relative_url }})
*Manus scans, MCP structures the results, Notion stores them.*

Each run follows the same flow:

1. A project goes in, either a GitHub repo or a contract address
2. Manus runs the scan and looks for vulnerabilities and exposed surface
3. MCP processes the output into a structured report
4. The report gets logged into Notion as its own page
5. The team gets notified

Here's how to set it up:

1. In Notion, create a database called something like Threat Intel Reports. Give it properties for Name, Project, Severity, Status and Created At.
2. Connect Notion to Manus through its connector so it can write into that database.
3. Give Manus a standing prompt. Mine is close to this:

```
Run a daily security check on the project below. Look for new issues since the last report, check the public surface for exposed files, outdated dependencies and misconfigured headers, and summarize what you find. Create one new page in the Threat Intel Reports database with the project name, a severity rating, a short summary, and a list of findings with recommended fixes. If anything is high or critical, say so in the first line.
```

4. Schedule it to run daily.
5. Set a Notion notification so a high severity page pings you straight away.

After a couple of weeks you end up with a database with one page per day. That is the part I like most. You can see the trend at a glance, and a page that suddenly looks different from yesterday's is its own kind of alert.

## Where it falls short

- An AI scan is a first pass, not an audit. It still gives false positives, and it still misses things a manual review catches.
- Never paste secrets, private keys or client data into the reports. A Notion page is only as private as your workspace sharing settings.
- Only scan what you're authorized to scan.

## Wrapping up

None of this is magic. It's a handful of free tools wired together so that something is always watching, even when I'm not. That's the answer to the question I used to wonder about. Nobody caught the hack by luck. Somebody had an alert listening for exactly that moment.

Thanks for reading. If you want a deeper breakdown of any of the three layers, tell me which one.
