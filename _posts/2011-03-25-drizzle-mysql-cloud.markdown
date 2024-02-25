---
author: emwilson
comments: true
date: 2011-03-25 02:14:45+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/03/24/drizzle-mysql-cloud/
slug: drizzle-mysql-cloud
title: Drizzle - MySQL for the Cloud
wordpress_id: 356
categories:
- SQL
tags:
- Drizzle
- memcached
- MySQL
- oracle
- Percona
photo:
  source: /images/ai/00045-3676919805
  url: /images/ai/00045-3676919805.png
  description: If Drizzle were an entity not a database
---

Drizzle - a lightweight fork of MySQL - has reached general availability. Drizzle's design goals are to create a highly performant and module database engine tailored for cloud computing.

Some of the interesting features this database has to offer are:





  * **No Views, Triggers, or Stored Procedures** - I consider this a huge advantage. Stored Procedures were once a good thing when query optimizers were all but non-existant, but modern database systems perform this task extremely efficiently. Using stored procedures adds a second level of APIs to your application for what I would consider to be a rather dubious potential security benefit.


  * **Sharding** - The client protocol is designed to decide which database server to target based upon a hashing key. This is similar to how memcached handles its linear horizontal scaling - leaving this calculation to the client is a huge advantage to systems hosting a high number of concurrent visitors


  * **Gearman Support** - This is a terrific tool for spreading workload across machines. Use Gearman to handle logging in Drizzle, keeping the actual DB server available for database work



**How Does Drizzle Compare to Percona?**
Percona is a high-performance build of MySQL which promises to offer better performance than "stock" MySQL. It is built from a branch of publicly-available MySQL source code and enhanced by the folks at Percona. Percona maintains the same functionality as "real" MySQL, but attempts to achieve faster speeds.

**How Does Drizzle Compare to MariaDB?**
MariaDB is a different database engine started by the original creator of MySQL intended to replace MySQL using non-Oracle licensing. Anyone concerned with Oracle's development practices or plans for the future of MySQL should consider switching to MariaDB. MariaDB implements all of the existing MySQL functionality plus more and provides a drop-in replacement for MySQL.
