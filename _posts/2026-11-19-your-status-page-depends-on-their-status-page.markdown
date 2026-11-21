---
author: emwilson
comments: true
date: 2026-11-19 13:00:00+00:00
layout: post
slug: your-status-page-depends-on-their-status-page
title: "Your Status Page Depends on Their Status Page"
excerpt: "When Microsoft 365 went dark for most of a day, products built on top of it went dark with it. Your uptime can't beat the services you quietly lean on. Here's how one bad gateway rolled downhill, and the questions to ask about your own product before it happens to you."
categories:
- General Programming
photo:
  source: /images/2026/11/status-dominoes
  url: /images/2026/11/status-dominoes.jpg
  description: Your Status Page Depends on Their Status Page
tags:
- reliability
- outages
- dependencies
- incident-response
published: true
---

On November 12, Microsoft's email and chat service for businesses went down around the world for close to a full day, and it took other companies' products down with it. Apps that read people's Outlook mail or calendars stopped working even though their own servers were fine and their own status pages were green. Nobody at those companies had changed a line of code - somebody else had a bad day, and their customers felt it.

Most products work this way now. Sign-in runs through someone else's accounts, receipts and password resets go out through someone else's mail servers, and the clever features call someone else's AI. Your customers never see that chain. They see your logo on the error, so they come to you, and if you haven't said anything yet they go looking for answers somewhere louder.

You can't stop another company's outage. You can know which ones would hurt you, decide ahead of time what your product does when they happen, and make sure your customers hear about it from you first.

## Three Small Problems Made One Big Outage

*Every architecture diagram has a box labelled "the cloud" that nobody is allowed to ask questions about.*

Microsoft's [preliminary post-incident report](https://next.ink/wp-content/uploads/2026/11/PostIncidentReport.pdf) has it starting late Thursday morning on November 12 and ending late the next morning. The trouble was in a networking gateway - a fleet of servers in front of many Microsoft 365 services that accepts incoming connections, like the front door of a building. Three things went wrong at the same time:

1. **A bug that had been waiting since July.** A fix made for the gateway servers had run worldwide for months without trouble, but under the right load a Windows process on those servers got stuck fighting itself for access to shared data and ate up their processing power and memory. Microsoft's lab tests missed it because the labs didn't use the same traffic mix or hardware as the real servers.
2. **A traffic surge.** The evening before, Teams had moved its traffic onto the primary gateway, the same one that carries Exchange Online, Microsoft's business email. That meant more load and a different traffic pattern.
3. **Less room to absorb it.** Nearly 14% of gateway capacity was already offline because of an unrelated problem the day before, which on its own was well within the service's safety margin.

Once servers started choking, traffic was redirected away from the sick ones onto the healthy ones and made them sick in turn. The walls meant to keep a problem inside one part of the network didn't hold, and at the worst point more than 80% of the gateway servers were unresponsive. Not every service could step around the jammed door, and the report says plainly that Exchange Online is too big to move off that gateway. Most people couldn't reach their mailboxes and incoming mail backed up, though Microsoft stretched its delivery window so delayed messages were held instead of bounced.

None of those three problems would have taken the service down by itself. The outage that gets you is usually a few ordinary things lining up, and the automatic recovery you built to help can be the thing that spreads it. (Microsoft's final report is due November 20th, so we'll see if it changes anything.)

## The Outage Rolled Downhill

Products built on Microsoft 365 started failing within the first two hours. [The connector that lets Claude read a user's mail, calendar, SharePoint files and Teams chats started returning errors around lunchtime](https://status.claude.com/incidents/sqn3fg1l96dl), about an hour and a half after the start time in Microsoft's report. The status page for Claude blamed an upstream provider within the hour, named the Microsoft 365 incident a few hours later, and didn't close its own incident until late Friday afternoon, still warning that Microsoft was finishing its recovery. There was nothing on Anthropic's side to fix, so the useful thing to do was say so and keep saying it.

Source hosting has bad days of its own. The day before Microsoft's trouble, [Actions, Pages, Copilot and webhooks on GitHub all degraded at once](https://www.githubstatus.com/incidents/dvy0t3mvh1lg) for a little over half an hour. If your deploys run through Actions, you couldn't ship a fix during that window no matter how healthy your own servers were.

On the same day Microsoft went down, [people couldn't sign in to GitHub, verify email addresses or reset passwords](https://www.githubstatus.com/incidents/nwbjzl7nfrc2) because an email provider GitHub relies on had degraded. A login failure caused by an email problem, two hops away from anything GitHub's engineers wrote, is the reason I keep saying [email is infrastructure you need to look after](/blog/2026/11/17/set-up-dmarc-before-you-need-it/), not a checkbox you tick when you buy the domain.

## Your Uptime Has a Ceiling

If your product needs five outside services to work and each one is up 99.9% of the time, the best you can promise is about 99.5%. That's roughly 44 hours of possible downtime a year instead of under 9, before you count a single bug of your own. That math assumes each vendor fails on its own schedule, and real outages don't work that way. One bad cloud region can take three of your vendors down in the same afternoon, and an AI provider's bad day might only grey out one button while an identity provider's bad day locks everyone out. Some of those services you never chose on purpose. A login library that loads its sign-in screen from its maker's servers puts that company on your list, even if nobody on your team ever signed up for an account.

Years ago, when AWS suffered a major failure, I wrote an article about [surviving cloud failures](/blog/2011/04/23/surviving-cloud-failures/) that turns out to have aged well. Back then the big fear was one provider's storage falling over. The advice to remove single points of failure still holds, but most of today's single points of failure aren't servers you can double up&nbsp;- they're companies.

Can you answer these questions for your app?

1. **What stops working for my customers when this service is down?**
2. **What should my product do instead?**
3. **Who finds out first, us or them?**

## How an Outage Gets Past Your Defences

**Failover can spread the fire.** Microsoft's own recovery pushed traffic onto healthy servers until those failed in turn. Your retries, load balancers and automatic fallbacks can do the same to a struggling vendor or to your own fallback provider. Back off with jitter, and stop calling a failing service for a while instead of hammering it.

**A dependency's dependency counts.** GitHub's sign-in broke because of an email provider, and Claude's connector broke because of Microsoft. Read the component list on each vendor's status page and ask your important vendors who they depend on. You may find that two of your "independent" providers sit on the same cloud.

**Rehearse with the dependency actually gone.** Turn off a vendor in a staging environment - revoke its API key, or point its hostname at an address that goes nowhere - and watch what your product does. The first rehearsal almost always finds a page that hangs for a minute before it errors.

**A green status page isn't proof.** Status pages are updated by people, and people are busy during outages. Trust your own error rates first and their status page second.

## Know What You Lean On

**Your uptime is capped by services you don't control, but your response isn't.** Knowing which vendors you lean on, what your product does when each one fails, and how your customers will hear about it turns somebody else's bad day into a short, well-handled one on yours.

Take one question back to your team:

> Which single vendor outage would make our product useless, and would our customers find out from us or from Twitter?
