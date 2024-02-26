---
author: emwilson
comments: true
date: 2009-05-15 02:45:25+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/05/14/statement/
slug: statement
title: 'C#: using Statement'
wordpress_id: 228
categories:
- C#
tags:
- C#
- memory management
---

One of my absolute favourite statements in C# is the _using_ statement (not to be confused with the using directive, which is what we use to import libraries like System.Web into our projects).

_using_ forces us as programmers to be honest about releasing memory to the CLR. Whenever we use an unmanaged resource like an SQL connection or file IO handler, the garbage collector will eventually eliminate any open streams or connections associated with that resource. However, "eventually" doesn't cut it when we're dealing with SQL connections on a production server - we need to make sure the connections are released no later than [when we're done](/blog/2008/02/15/sql-connections-in-aspnet-what-you-learned-is-wrong/) with them.

If you come from the C++ world, you're probably (hopefully) used to calling **delete** to deallocate any memory you reserved. You also know that forgetting the **delete** (or **delete []** on arrays) results in a memory leak. You might think of **Dispose()** as C#'s implementation of the **delete** statement.

```csharp
using ( SqlCommand cmd = new SqlCommand( sqlStatement, sqlConnection ) )
{
   // Do something
}
```

_using_ acts as a try...catch...finally block, so if your code fails your object will still be disposed. The using statement keeps everything wrapped into a neat little package so you don't forget to keep your local variables in scope.

Like I said, this is one of my favourite features in .NET (_lock { }_ is similarly beautiful). You can use the same construct in VB as well.
