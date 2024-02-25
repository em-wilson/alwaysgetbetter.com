---
author: emwilson
comments: true
date: 2011-04-19 00:12:35+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/18/memcache-mysql/
slug: memcache-mysql
title: Using Memcache with MySQL
wordpress_id: 395
categories:
- SQL
tags:
- memcached
- MySQL
- performance
---

MySQL+Memcache have been bedfellows for awhile and at this point are the de facto standard for highly-available, scalable websites. Even with [other SQL](/blog/2011/03/24/drizzle-mysql-cloud/) and NoSQL solutions starting to become popular, this pair holds on as the winner for LAMP programmers. Is the complexity of working with this technology pair worth the investment?

**Read vs Write**
Traditional relational databases place the burden of computation on read operations. In mainframe environments with powerful servers and relatively few users, this made sense. Database normalization prevents redundancy, and data can be joined together when needed to produce the desired results.

In a web application with 1,000,000 users, the normalized transactional model does not perform. Generally speaking it is way faster to make two queries to a small subset of data rather than attempt expensive joins in a client-facing web site.

Enter memcache: by storing the result of our SQL queries in memory, we improve the speed of subsequent requests by pulling the data from memory as well as avoiding a hit to the database entirely, freeing it to process urgent or real-time requests.

**Anatomy of an SQL Query**
When we run an SQL query, we are actually asking the server to perform a lot of work:



	
  1. Break down the query into object references: The DBMS needs to understand which tables, columns, and filters you are using by tokenizing your SQL (by breaking out names from the keywords like SELECT, FROM, WHERE).

	
  2. Identify which indexes (if any) are most relevant to the data: This is harder with more complex queries which must be broken down or which depend on outer tables for their values.

	
  3. Read the source tables from the hard drive: Most DBMS implementations include some kind of memory caching which partially avoids this expensive read step, but some disk IO is a normal part of operation

	
  4. Join Columns: If we specify a join, especially a LEFT or RIGHT join, the DBMS has to create a pseudo table from the joined sources in memory before it can do any additional processing.

	
  5. Sub-selects: Any sub-select statements need to be processed. Depending on how the statement was written, this needs to be done for every row returned in the result set.

	
  6. Filter and sort: Anything in the 'WHERE' clause needs to be filtered out of the result set. This is where we are going to start seeing performance improvements by narrowing our result set.

	
  7. Aggregation: Once the database has its final result set it can do all of the aggregation we ask of it, both calculations and grouping


As we can imagine, this can be a time-consuming process. If it is repeated thousands of times in a short period, we will see significant performance loss.

**Anatomy of a Cache Request**
By contrast, when we perform a request for data from a cache like memcache, we do this:



	
  1. Check the index for the presence of the supplied key

	
  2. If a result exists, return it


In the case of memcache, this happens entirely in memory with no hit to the database whatsoever, resulting in a blindingly fast result set.

**Speed Over Persistance**
The reason [memcache and MySQL work well as a pair](/blog/2011/04/10/memcache-mysqls-hero/) is because they provide the tools needed to have reliable, persistent transactional storage (through MySQL) along with lightning fast data retrieval (through Memcache) especially for rarely-changing results.
