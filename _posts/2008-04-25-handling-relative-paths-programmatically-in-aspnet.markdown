---
author: emwilson
comments: true
date: 2008-04-25 15:28:27+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/25/handling-relative-paths-programmatically-in-aspnet/
slug: handling-relative-paths-programmatically-in-aspnet
title: Handling Relative Paths Programmatically In ASP.NET
wordpress_id: 26
categories:
- ASP.NET
- C#
tags:
- ASP.NET
- C#
---

One of the nicest features in ASP.NET is its out-of-box support for relative paths in hyper links and other controls.  This is very important for developers whose code resides within the root of their testing environment but within a sub-directory of the production server.

Whereas one would have to carefully program things like image links to point at **"/applicationbasepath/images/"**, in ASP.NET we can simply use **"~/images"**.  Genious!

When writing custom code, however, the work isn't done for us automatically.  We have to pass our URL to a server-side function in order to convert the "**~/**" into a base path usable by our visitors.

There are two ways of doing this:


## Using Relative Paths From a Web Page or Control


If we are programming a page template or a server control, we can use the **ResolveUrl()** function provided by our environment context.

```csharp
string resolvedUrl = ResolveUrl( "~/index.php" );
```

On AlwaysGetBetter.com, "~/index.php" would resolve to **http://www.alwaysgetbetter.com/blog/index.php**.  On another site, it might resolve to the root such as **http://www.abetterblog.com/index.php**.  Programming this way makes our solution more portable.



## Using Relative Paths From Outside a Web Page


Sometimes we need to use relative paths from outside the context of a web page.  For example, if we were to make a change in our Global.asx file, the program code we use may not be considered to be within a web page scope.

Fortunately, .NET provides us with the static helper class _VirtualPathUtility_.

```csharp
string redirUrl = VirtualPathUtility.ToAbsolute("~/redirPage.aspx");
Response.Redirect( redirUrl );
```
