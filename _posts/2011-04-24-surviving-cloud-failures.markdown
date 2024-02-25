---
author: emwilson
comments: true
date: 2011-04-24 03:21:12+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/23/surviving-cloud-failures/
slug: surviving-cloud-failures
title: Surviving Cloud Failures
wordpress_id: 411
categories:
- internet
tags:
- cloud computing
- redundancy
- security
photo:
  source: /images/ai/00019-4089984304
  url: /images/ai/00019-4089984304.png
  description: Staying in control of the cloud
---

Amazon is in the news today for the failure their Elastic Block Storage (EBS) service suffered, resulting in loss of service and/or extreme latency for hundreds of sites including some of their largest customers like Foursquare and reddit. AWS has been widely regarded as the most stable and overall leader of the cloud providers, so it was a great shock to many observers that they were able to suffer such a large failure.

I think the failure is not surprising, but rather the fact that it hasn't happened before now is surprising. It underscores my message that [cloud computing is not magical](/blog/2011/04/05/cloud-computing-magical/) but is in fact an abstraction over very real hardware. There are bound to be flaws and issues just as with "real" hosting options, the difference is the end customer has less control over the hardware, hosting and networking environment.

Not every business can afford the overhead of a large dedicated solution, so what to do?

**Spread the Load**
The key is redundancy. Start by [spreading your content](/blog/2011/04/22/accelerate-site-content-delivery-network/) across the internet rather than relying on  single server to cough up all of your visitors' needs. Things like content delivery networks (CDNs) will reduce the incoming load on the server and help it stay online.

How can we tell if a website is offloading the right amount of content? [Perform regular speed testing](/blog/2011/04/19/tracking-website-speed-problems/) and identify problem areas using tools like YSlow.

**Redundancy! Eliminate Single Points of Failure**
Whenever you have a single system servicing part of your application, you expose the entire application to failure.

For example, suppose you have four Apache servers and [a load balancer](/blog/2011/04/16/small-site-big-footprint/) sending equal traffic to each. If one of the Apache servers fails, the other three are able to compensate for the loss with no downtime for your visitors. But what happens if the load balancer fails? Even though all four web servers are in fine working order, your site is knocked offline.

Some systems are difficult to cluster: replication schemes in the various SQL servers are a huge drain on performance - newer solutions like MySQL Cluster or [DrizzleDB](/blog/2011/03/24/drizzle-mysql-cloud/) aim to solve this problem, but at extra expense in terms of configuration and application design.

The key to successful redundancy is in scripting your software in such a way that failures can be recovered from fast and automatically. Having a hot spare in the group isn't useful if you need to reach an administrator at 4am to activate - by that point you've already lost your overseas customers for the day.

Twilio has [an excellent summary](http://www.twilio.com/engineering/2011/04/22/why-twilio-wasnt-affected-by-todays-aws-issues/) of the engineering process that goes into creating a scalable cloud-ready infrastructure.

**Avoid the Cloud? Never**
Despite some public failures, cloud computing has not suffered any kind of blow. Large organizations will always want their own private non-cloud hosting, small sites will always be looking for an inexpensive VWS. The middle-tier which is serviced by the cloud will continue to see cost savings that greatly outweigh any physical hosting options available at that level.

Because of the low server cost, cloud computing allows smart customers the freedom to build necessary redundancy without breaking the bank. Even though this pays off big time when catastrophic failures happen, there are longer term benefits of improved overall response times to the end users even when the hosting is working well.
