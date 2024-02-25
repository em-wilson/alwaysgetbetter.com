---
author: emwilson
comments: true
date: 2011-04-17 01:47:57+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/16/small-site-big-footprint/
slug: small-site-big-footprint
title: Small Site, Big Footprint
wordpress_id: 391
categories:
- internet
tags:
- hosting
photo:
  source: /images/ai/00041-1492672567
  url: /images/ai/00041-1492672567.png
  description: Knight protects the little one
---

I like redundancy, to a fault. Part of it goes to [my need for comprehensive backup](/blog/2011/04/15/backup-time/) - as long as you have a backup prepared, you are less likely to lose anything. So it stands to reason that if you have two identical copies of your web site running, you are more tolerant to all kinds of failures - from your web server going down to an unexpected surge in traffic.

**Protection Against Hardware Failure**
If your server [experiences hardware issues](/blog/2011/04/23/surviving-cloud-failures/), nothing you can do (short of changing the faulty components) will keep your site online. If you have two web servers and one of them fails, the other can still pick up the slack.

This is an important consideration when you're using [a cloud provider](/blog/2011/04/05/cloud-computing-magical/). In many cases you only occupy a small portion of the hardware you are hosting on, which means if someone else messes up their VM instance it can potentially affect your application. Spreading across to two VMs increases the odds that you end up on different physical hardware, perhaps even a different server rack altogether. If your provider performs maintenance on one of their servers you will not necessarily get knocked offline.

**Protection Against Traffic**
Even if you're on a strong fast server, you can only support a certain number of concurrent users. A load balancing situation can reduce the average load per machine in your cluster, which improves the overal response rate, and eliminates the 'queuing' problem you would experience with a large numbers of users all trying to hit a particular machine.
