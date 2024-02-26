---
author: emwilson
comments: true
date: 2008-04-06 12:49:55+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/06/how-to-sum-bit-fields-in-sql/
slug: how-to-sum-bit-fields-in-sql
title: How to SUM Bit Fields in SQL
wordpress_id: 19
categories:
- SQL
tags:
- SQL
---

By default, SQL Server doesn't allow an operation like this:

```sql
SELECT SUM(blnBitColumn) FROM tblTable;
```

In order to achieve this result, you must first convert the bit column to a numeric type:

```sql
SELECT SUM(CONVERT(int,blnBitColumn)) FROM tblTable;
```

This counts the number of times the bit is **true**.

If you want to get the flip-side of that to see how many times the bit is **false**, just subtract the total number of bits from the positive:

```sql
SELECT COUNT(blnBitColumn)-SUM(CONVERT(int,blnBitColumn)) FROM tblTable;
```
