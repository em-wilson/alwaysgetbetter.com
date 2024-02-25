---
author: emwilson
comments: true
date: 2010-11-20 17:04:37+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/11/20/php-accelerator-speed-website/
slug: php-accelerator-speed-website
title: Use a PHP Accelerator to Speed Up Your Website
wordpress_id: 344
categories:
- PHP
tags:
- broadband
- hosting
- PHP
- speed
---

I like PHP because it makes it really easy to quickly build a website and add functionality, and is generally lightning fast when executed without needing to wait for compilation as with ASP.NET or Java (Yes, we always pre-compile those languages prior to putting our applications into production, but with PHP we don't even have to do that).

Even though compilation is very fast, it still has a resource and time cost, especially on high-traffic servers. We can improve our response times by more than 5x by pre-caching our compiled opcode for direct execution later. There are a few PHP accelerators which accomplish this for us:

**Xcache**
Xcache is my favourite and is the one I use in my own configurations. It works by caching the compiled PHP opcode in memory so PHP can be directly executed by the web server without expensive disk reads and processing time. Many caching schemes also use Xcache to store the results of PHP rendering so individual pages don't need to be re-processed.

**APC (Alternative PHP Cache)**
APC is a very similar product to XCache - in fact XCache was released partially as a response to the perceived lag APC's support for newer PHP versions. APC is essentially the standard PHP Accelerator - in fact, it will be [included by default in PHP 6](http://davidwalsh.name/php6). As much as I like XCache, it will be hard to compete with built-in caching.

**MMCache**
Turck MMCache is one of the original PHP Accelerators. Although it is no longer in development, it is still widely used. An impressive feature of MMCache is its exporter which allows you to distribute compiled versions of your PHP applications without the source code. This is useful for those companies that feel they need to protect their program code when hosting in client environments.

**eAccelerator**
eAccelerator picked up where MMCache left off, and added a number of features to increase its usability as a content cache. Over time, the content caching features have been removed as more efficient and scalable solutions like memcache have allowed caches to be shared across web servers.

**Keep Optimizing**
One major consideration that often goes forgotten when optimizing website speeds that not all of your visitors will be using a high-speed connection; [some users will be using mobile or worse connections](/blog/2010/05/24/cell-phone-backup-internet/), even for non-mobile sites. Every ounce of speed will reflect favourably on you and improve your retention rates - and ultimately get more visitors to your 'call to action' goals. I'll go into more detail about bigger speed improvements we can make in a later post.
