---
author: emwilson
comments: true
date: 2008-04-11 16:30:54+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/11/select-top-n-in-oracle/
slug: select-top-n-in-oracle
title: SELECT TOP N in Oracle
wordpress_id: 20
categories:
- SQL
tags:
- oracle
- sql server
---

Being used to SQL Server, I get messed up when moving to Oracle.  For reference, here are equivalent top N queries in both environments:

**SQL Server**:
```sql
SELECT TOP 10 book_name, price*sales
FROM tblBooks;
```

**Oracle**:
```sql
SELECT book_name, price*sales
FROM tblBooks
WHERE ROWNUM <= 10;
```
