---
author: emwilson
comments: true
date: 2011-12-20 19:08:08+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/12/20/fastcgi-nginx-performance-vm/
slug: fastcgi-nginx-performance-vm
title: Using FastCGI with Nginx for Performance on a VM
wordpress_id: 461
categories:
- General Programming
---

This weekend I decided to play around with the configuration on my Rackspace Cloud Server. Since our various websites have been doing well lately, the relatively low-powered machine I am running on is starting to fill up its available RAM. So far so good but as everyone quickly learns - running out of memory and [hitting the swap space is a performance killer](/blog/2011/04/12/performance-tuning-apache/). Since I want my sites to continue to do well, I decided to take action before they hit the RAM limit and start swapping to disk.

[![](/images/2011/12/agb_old.png)](/images/2011/12/agb_old.png) This is the old architecture I had deployed. Apache - the Internet's workhorse - to perform all of the PHP processing, with Nginx as a reverse proxy, passing dynamic (PHP) requests to Apache but serving static files directly to [give Apache a break](/blog/2010/09/25/give-apache-break-nginx/) and cut down its footprint.

I optimized MySQL with a large buffer, so it serves the vast majority of queries directly from memory.

The two places that hit the filesystem are Nginx and PHP - Nginx for static files (as mentioned, to take the load of Apache which would spin up a new instance for each file it serves) and PHP for session data (this is PHP's default setting).



[![](/images/2011/12/agb_new.png)](/images/2011/12/agb_new.png)This is the new setup, and is very similar to the old with two key differences: Apache is gone, and Memcached is now in the mix.

As site traffic increased I noticed that Apache was using up bigger and bigger chunks of the system RAM compared to all of the other processes. I could pare this down by putting further restrictions on the number of child processes, decrease the number of connections before recycling, and limiting the maximum memory for each process, but that seemed like a lot of work when I already had one foot into a more scalable solution.

Taking advantage of the new(ish) since PHP 5.3.3 FastCGI Process Manager (FPM), I updated Nginx to send PHP traffic directly to PHP without using Apache as a middleman. The default settings were too generous for my fairly weak server, and the memory usage shot up. But by tweaking it down to 3 processes recycling after 500 requests, I'm now using half the physical memory as I was with Apache.

Previously I wrote about using [Memcached as a PHP Session Handler](/blog/2011/04/09/memcached-session-handler/) and that's exactly what I did here. Now the Filesystem is only hit for static files and the first run of PHP scripts - everything else is served from memory.

It may seem a little counter-intuitive that I cut memory consumption by moving more services into memory, but the trade-off improvements realized by hitting the disk less means that responses are sent out 30% faster, meaning I can fit more traffic onto the same machine and expect the same responsiveness on the web site.

One thing I could do to improve this even more would be to put Varnish in front of Nginx and serve all static content - including rendered PHP - from memory, which would give some seriously (<100ms) fast performance on read-only Wordpress pages when users are not logged in. I may do that if traffic continues to rise, but for the moment the combination of Nginx's static file speed with [offloading most of my site's static files to a content delivery network (CDN)](/blog/2011/04/21/accelerate-site-content-delivery-network/) is giving me the performance I want to see.
