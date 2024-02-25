---
author: emwilson
comments: true
date: 2008-11-08 16:04:32+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/11/08/aspnet-service-unavailable/
slug: aspnet-service-unavailable
title: ASP.NET "Service Unavailable"
wordpress_id: 57
categories:
- ASP.NET
tags:
- ASP.NET
- IIS
---

If you have threaded processes called by your ASP.NET code, and those processes crash, your site may start giving **Service Unavailable** errors.

Use **iisreset** to quickly get back up and running.

In order to prevent this from occurring at all, I recommend putting _try { } catch { }_ around any statements inside a threaded function.
