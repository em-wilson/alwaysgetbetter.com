---
author: emwilson
comments: true
date: 2011-04-25 11:15:01+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/25/ensure-sla-multiple-web-role-instances-windows-azure/
slug: ensure-sla-multiple-web-role-instances-windows-azure
title: Ensure SLA with Multiple Web Role Instances on Windows Azure
wordpress_id: 416
categories:
- Windows Azure
tags:
- high availability
- Windows Azure
---

As I dig deeper into the Windows Azure platform, I am becoming more and more impressed by the potential it offers. Microsoft has put a lot of money and resources into developing their infrastructure and have done an incredible job at creating an interesting and powerful architecture. If anything, their major fault is in advertising - with so many different technologies with ever-changing names, it's hard for a newcomer to wrap their mind around the services. It is pricier than some options so it's hard to really experiment in much depth, although they do offer a free option for developers to dig in and try the services.

**Ensuring 99.95% SLA**
One potential 'gotcha' is the SLA: it differs between their difference services but hovers in the 99.9% range. Most account properties include automatic scaling and failover, but Web Roles and Worker Roles do not - in order to qualify for the 99.95% SLA you need to deploy your solution to two or more instances. By doing that, you ensure that your application is load balanced across multiple fault domains therefore will remain available [even during a failure](/blog/2011/04/23/surviving-cloud-failures/).

Of course, this doubles the cost - each compute instance is billed separately.

**Fault Domain vs Upgrade Domain**
Azure's logical server space is segmented into fault domains and upgrade domains. Translated into physical terms, a fault domain refers to a single rack containing multiple computers, while an upgrade domain is one or more computers that receive software upgrades at the same time. Multiple upgrade domains may exist within any fault domain.

For our application to remain online, we need to consider where it is hosted. Although the fault and upgrade domains are largely abstracted away from developers, the SAL guarantees that 2 or more instances are split across two or more upgrade and fault domains. At a minimum, this means if you have two Web Role instances they will exist on different computers in different racks. Beyond that, Azure decides where instances are initialized and hosted from.

**High Availability Concerns**
Now let's consider our application which has been split across two instances. We are guaranteed to have 99.95% network availability to our application, but there are more factors to consider.

When one of the instances goes offline - due to a server fault or OS upgrade - Azure automatically starts it up on a working server. As long as the remaining instance is able to handle all of your application's needs, this process is transparent and results in no performance degradation.

What happens if your application is spread across two instances, each under 70% load? The response times might be good, but if one of the instances goes offline even temporarily your remaining healthy deployment will trying to handle 140% of the overall traffic. If you want your business to stay on line, you need to plan for these periods of downtime.

This is where the abstraction hurts - we can't control the number of fault domains in our cluster but we can control the number of upgrade domains (essentially forcing each instance to load up on a new machine). Worst case scenario is half of your cluster dies when the Azure fault domain goes down - something that _should_ never happen, but anything can happen online.

Note, however, that this kind of planning is what's needed in general cases when your traffic might suddenly jump due to customer interest. We're not just mitigating against host failure, but also against reputation damage from inadequate planning.

**Keep Sessions in Mind**
One last thing to mention: when we split our application into multiple instances, we need to consider how we handle session data. Since Azure uses a round robin situation for their load balancing meaning the end user could potentially hit a different instance each time they access a resource on your site. If you are using traditional session handlers, session data is not shared between the instances so user data is not guaranteed to be persisted on each request.

This is actually huge benefit to cloud computing, and something [I've praised other frameworks](/blog/2011/04/11/play-framework-saves-world/) for providing seamless support for. Our web application should be designed to avoid sharing data where possible. If session data is needed, we can use AppFabric's session cache providers to provide fast network-based session management.
