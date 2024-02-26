---
author: emwilson
comments: true
date: 2008-04-29 11:43:58+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/29/c-finding-the-number-of-days-between-two-datetime-items/
slug: c-finding-the-number-of-days-between-two-datetime-items
title: 'C#: Finding the Number of Days Between Two DateTime Items'
wordpress_id: 27
categories:
- C#
- General Programming
tags:
- C#
- General Programming
- programming
---

One very common requirement is for the number of days elapsed since a particular Date and Time.  In C# this can be accomplished through the use of the **TimeSpan** class.

The easiest way to create a TimeSpan is like this:

```csharp
TimeSpan tsMySpan = DateTime.Now.Subtract( dtCompareTime );

// The number of days elapsed can be accessed like this:
// tsMySpan.Days
```
