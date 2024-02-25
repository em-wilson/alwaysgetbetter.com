---
author: emwilson
comments: true
date: 2011-04-10 10:31:18+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/10/memcache-mysqls-hero/
slug: memcache-mysqls-hero
title: Memcache as MySQL's Hero
wordpress_id: 374
categories:
- Web Programming
tags:
- e-commerce
- efficiency
- MySQL
- programming
- standards
---

It's hard not to love memcache. As soon as you manage a web site that has more than a few concurrent visitors, the performance benefit of caching becomes immediately obvious. MySQL is a fast database and can outperform a lot of its competitors, but no matter how quickly it can pull results it can never outperform the retrieval speed of the server's RAM.

The basic premise is: instead of pulling a model out of the database, see if it has already been loaded into memory by checking a key-value diction (for example: User5677). If the user has not been read from the database yet, the key-value store will be empty and we can fetch the record. Next time we need that data we check the key-value again and avoid querying the database.

This really saves us whenever we have data that changes infrequently. Take, for example, an ecommerce website: since the products and categories on the site will change very rarely, it makes a lot of sense to store them in memory for fast recovery. Even more volatile information (like user data) can be stored in the cache, as long as the application knows to empty that cache key when the data gets changed.

Memcache is an ideal tool for managing these kinds of caches, and provides a lot of flexibility for growth.

**History Lesson**
Earlier this week [I promised to go deeper into memcache's origins](/blog/2011/04/09/memcached-session-handler/). Memcache was originally developed at Danga as a way to reduce the database load and improve the speed of LiveJournal.

Rather than developing a standalone server application, Danga's engineers designed memcache to sit on lower-end hardware and on web servers where it would use a small amount of the overal memory. Memcache instances don't talk to each other: the client machines are aware of all the memcache instances and attempt to write their information evenly to each. This allows memcache to scale almost limitlessly without adding significant overhead to the caching process.

**When to Use**
Quite simply: if you're building an application for the LAMP stack, build in memcache support. When treated as a necessary component from the beginning, caching support adds almost zero overhead to development; however [it will always pay off](/blog/2011/04/18/memcache-mysql/) as soon as real world traffic is coming to your site.
