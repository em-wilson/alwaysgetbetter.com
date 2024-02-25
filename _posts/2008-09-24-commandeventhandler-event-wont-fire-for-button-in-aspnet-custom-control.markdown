---
author: emwilson
comments: true
date: 2008-09-24 23:03:04+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/09/24/commandeventhandler-event-wont-fire-for-button-in-aspnet-custom-control/
slug: commandeventhandler-event-wont-fire-for-button-in-aspnet-custom-control
title: CommandEventHandler Event Won't Fire for Button in ASP.NET Custom Control
wordpress_id: 55
categories:
- ASP.NET
- C#
tags:
- ASP.NET
- C#
- VB.Net
---

**Problem**: I created a custom control with a dynamic button and attached an event handler to that button.  When I run the control, clicking the button causes a postback but the event is not fired.

**Solution:** Changed the class inheritance from _WebControl_ to _CompositeControl_.  Re-compiled and it worked like a charm.
