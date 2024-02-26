---
author: emwilson
comments: true
date: 2008-07-01 02:22:23+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/06/30/optimize-sql-queries-by-using-aliases/
slug: optimize-sql-queries-by-using-aliases
title: Optimize SQL Queries by Using Aliases
wordpress_id: 35
categories:
- SQL
tags:
- efficiency
- SQL
---

When joining tables in SQL, we often use aliases to shorten table names.  Consider this query joining order lines (details) with orders inside the database:

```sql
SELECT O.OrderID, FirstName, LastName, ItemName, Quantity
FROM Orders O
INNER JOIN OrderDetails OD
ON O.OrderID = OD.OrderID;
```

The above may work, but behind the scenes the database's query analyzer has to associate each of the selected columns with their respective tables.  Although this is a fast process, it can be skipped entirely by simply fleshing out the query like this:

```sql
SELECT O.OrderID, O.FirstName, O.LastName, OD.ItemName, OD.Quantity
FROM Orders O
INNER JOIN OrderDetails OD
ON O.OrderID = OD.OrderID;
```
