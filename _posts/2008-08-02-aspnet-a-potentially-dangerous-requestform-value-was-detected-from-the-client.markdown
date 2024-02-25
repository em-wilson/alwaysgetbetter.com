---
author: emwilson
comments: true
date: 2008-08-02 16:31:52+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/08/02/aspnet-a-potentially-dangerous-requestform-value-was-detected-from-the-client/
slug: aspnet-a-potentially-dangerous-requestform-value-was-detected-from-the-client
title: 'ASP.NET: A potentially dangerous Request.Form value was detected from the
  client'
wordpress_id: 42
categories:
- ASP.NET
tags:
- ASP.NET
- security
---

This error is caused by the presence of HTML in the fields returned by a form post.  In many cases, for example page management tools, you may want to allow your users to enter text formatted with HTML.  By default, ASP.NET doesn't like this.

To turn it off, add **ValidateRequest="false"** to the top of your aspx file.  This turns off validation of form results.

Honestly, I would rather if this property were available for individual form controls, because in my mind validation is still desirable overall even if one or two fields should allow HTML.  But there you have it.
