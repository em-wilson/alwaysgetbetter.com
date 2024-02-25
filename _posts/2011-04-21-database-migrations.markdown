---
author: emwilson
comments: true
date: 2011-04-21 01:39:32+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/20/database-migrations/
slug: database-migrations
title: Database Migrations
wordpress_id: 404
categories:
- SQL
tags:
- Employment
- planning
- SQL
---

Maintaining database schemas across development environments (especially in teams) and in production can be a real nightmare. Fortunately there are a number of solutions which make database management easier.

**Migrations**
This can be done manually or automatically. As database changes are made by developers, scripts are generated which can be run against a master database to bring it in line with the developer's version. The most basic way to accomplish this is by writing a script manually, but frameworks like Django and Rails have built-in migration tools which manage this process. Rails in particular allows developers to move back and forth between snapshots of database schemas.

**Evolutions**
Evolutionary systems detect database schema changes against program code definitions. As of April 2011, [Play Framework supports Evolutions](/blog/2011/04/11/play-framework-saves-world/).

**Schema Versions**
Microsoft SQL Server supports schema versions; wherein the underlying data remains the same, but multiple versions of the database schema rest on top and can be accessed simultaneously. This keeps older versions of the application or supporting clients working with the existing data set.

**Keep Tracking...**
Managing database changes can be a challenge for organizations of any size. The correct tool depends on a wide range of factors including your project size, number of team members, release schedule.

What kind of tools and processes do you use to manage database changes?
