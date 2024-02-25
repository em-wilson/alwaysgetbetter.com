---
author: emwilson
comments: true
date: 2008-08-12 23:51:21+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/08/12/internet-explorer-url-character-limit-is-2083/
slug: internet-explorer-url-character-limit-is-2083
title: Internet Explorer URL Character Limit is 2,083
wordpress_id: 46
categories:
- Web Browsers
tags:
- firefox
- Internet Explorer
- Safari
---

Today I learned that Internet Explorer limits the site of GET requests to 2,083 characters.  Any URL longer than this cannot be used by the web browser.

http://support.microsoft.com/kb/208427

I can't help but wonder where that number comes from?  The closest power is 11 (2^11=2048), which doesn't correspond at all to this limit.  Is it arbitrary?  Other web browsers (Firefox, Safari) do not have this limitation.
