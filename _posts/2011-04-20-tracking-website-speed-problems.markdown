---
author: emwilson
comments: true
date: 2011-04-20 02:43:10+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/19/tracking-website-speed-problems/
slug: tracking-website-speed-problems
title: Tracking Down Website Speed Problems
wordpress_id: 401
categories:
- Web Programming
tags:
- internet
- networking
- performance
photo:
  source: http://www.flickr.com/photos/8749778@N06/5611527033/
  url: http://farm6.static.flickr.com/5149/5611527033_6c1e37ccdc_m.jpg
  description: Tortoise
  cc_author: Eric Kilby
  cc_link: http://www.flickr.com/photos/8749778@N06/5611527033
excerpt: There are a few common culprits behind website speed issues. When diagnosing problems, the best bet is to start at the worst performers and move up.
---

Why is my website loading so slowly?!?

There are a few common culprits behind website speed issues. When diagnosing problems, the best bet is to start at the worst performers and move up. Some suggestions, in order from slowest to fastest, are:

**1. Internet Traffic**
If your web page is downloading anything over the internet during each page request, stop right now. This is the most expensive operation you can perform. Example: Downloading a photo from Flickr and loading it into memory in order to determine its width and height dimensions.

**2. Network Traffic**
Local network traffic is generally very fast, but still involves transmitting information outside your computer. In some cases, such as [web clusters with a shared session cache](/blog/2011/04/09/memcached-session-handler/), the network performance cost is worth it for the overall application.

**3. Database**
Databases are fast, particularly when the data you need is already stored in a memory cache - which you generally can't control. When paired with a [key-value memory store like memcache](/blog/2011/04/18/memcache-mysql/), the majority of your database calls can come straight from memory.

**4. Disk I/O**
Even with the incredible access times found in today's hard drives, reading and writing from the disk is an expensive operation (and why databases lose points, except for their memory caching abilities). Sometimes reading from disk is the better choice - YMMV.

**5. Script Caching**
Implement [a tool like xcache](/blog/2010/11/20/php-accelerator-speed-website/) (PHP). This will keep your code in binary bytecode format which is much faster to execute since it doesn't have to be re-processed by the web server.
