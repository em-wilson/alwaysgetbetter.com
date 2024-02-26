---
author: emwilson
comments: true
date: 2011-04-09 12:24:26+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/09/memcached-session-handler/
slug: memcached-session-handler
title: Memcached as Session Handler
wordpress_id: 369
categories:
- PHP
tags:
- apache
- hosting
- linux
---

By default PHP loads and saves sessions to disk. Disk storage has a few problems:

1. Slow IO: Reading from disk is one of the most expensive operations an application can perform, aside from reading across a network.
2. Scale: If we add a second server, neither machine will be aware of sessions on the other.

**Enter Memcached**
I [hinted at Memcached](/blog/2010/11/20/php-accelerator-speed-website/) before as a content cache that can improve application performance by preventing trips to the database. Memcached is also perfect for storing session data, and has been supported in PHP for quite some time.

Why use memcached rather than file-based sessions? Memcache stores all of its data using key-value pairs in RAM - it does not ever hit the hard drive, which makes it F-A-S-T. In multi-server setups, PHP can grab a persistent connection to the memcache server and share all sessions between multiple nodes.

**Installation**
Before beginning, you'll need to have the Memcached server running. I won't get into the details for building and installing the program as it is different in each environment, but there are good guides [on the memcached site](http://memcached.org/). On Ubuntu it's as easy as **aptitude install memcached**. Most package managers have a memcached installation available.

Installing memcache for PHP is hard (not!). Here's how you do it:

```
pecl install memcache
```

Careful, it's **memcache**, without the 'd' at the end. Why is this the case? It's a long story - let's save the history lesson for another day.

When prompted to install session handling, answer 'Yes'.

**Usage**
Using memcache for sessions is as easy as changing the session handler settings in PHP.ini:
session.save_handler = memcache
session.save_path = "tcp://127.0.0.1:11211" (assuming memcached is set up to use default port)

Now restart apache (or nginx, or whatever) and watch as your sessions are turbo-charged.
