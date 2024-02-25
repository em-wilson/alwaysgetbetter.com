---
author: emwilson
comments: true
date: 2011-04-24 11:30:54+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/24/azure-table-storage-azure-sql/
slug: azure-table-storage-azure-sql
title: Azure Table Storage vs Azure SQL
wordpress_id: 414
categories:
- Windows Azure
tags:
- data types
- hosting
- networking
- planning
---

There is a lot of debate among newcomers to Azure whether to use Azure SQL or file-based Table Storage for data persistence. Azure itself does not make the differences very clear, so let's take a closer look at each option and try to understand the implications of each.

**Azure SQL**
Azure SQL is very similar to SQL Server Express. It is meant as a replacement for SQL Server Standard and Enterprise, but the feature set is not there yet. Reporting services, in particular, are in community technology preview (CTP) phase and at the time of writing are not ready for prime time.

Programmatically, Azure SQL is very similar to SQL in existing web applications; any ODBC classes you already wrote will work directly on Azure with no changes.

Size and cost are the major limitations with Azure SQL in its current incarnation. The largest supported data size is 50GB which runs for $499/month. Any databases larger than 50GB would need to be split across multiple instances - this requires knowledge of sharing at the application level and is not easy surgery to perform.

**Table Storage**
Table storage uses a key-value pair to retrieve data stored on Azure's disk system, similar to the way [memcache and MySQL](/blog/2011/04/18/memcache-mysql/) work together to provide the requested data at fast speeds. Each storage container supports 100TB of data at incredibly cheap ($0.15/GB) rates.

Working with table storage involves accessing them directly from your application differently than you may be accustomed to with SQL. Going all-in ties you to the Azure platform - which is probably not a problem if you're already developing for Azure as you will likely be trying to squeeze every ounce of performance out of all areas of the platform anyway.

Table storage does not support foreign key references, joins, or any of the other SQL-stuff we usually use. It is up to the programmer to compensate by making wide de-normalized tables and build their lists in memory. If you're already building clustered applications, this is not a new design pattern as developers typically want to cache data in this manner.

Besides the larger space limits, table storage affords us automatic failover. Microsoft's SLA guarantees we will always be able to access the data, and this is accomplished by replicating everything across at least three nodes. Compared to self-managing replication and failover with the SQL service, this is a huge advantage as it keeps the complexity out of our hands.

**Difference in Evolution**
If Azure SQL seems somewhat stunted compared to Table Storage, it's not an accident: it is a newcomer who was not planned during the original construction of the Azure platform. Microsoft carefully considered the high-availabilty trends used for application development and found that the NoSQL way would most easily scale to their platform. Developer outrage prompted the company to develop Azure SQL, and its service offerings are improving rapidly.

Depending on your storage needs, your course of action may be to store as much data for cheap in Table Storage, and use SQL to index everything. Searching the SQL database will be incredibly fast, and can be done in parallel with any loads against persistent tables - everybody wins.
