---
author: emwilson
comments: true
date: 2008-04-05 00:16:12+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/04/c-formclose-vs-formdispose/
slug: c-formclose-vs-formdispose
title: 'C#: Form.Close() vs Form.Dispose()'
wordpress_id: 17
categories:
- C#
tags:
- C#
- programming
---

When working with a Windows GUI, it may seem unclear whether to use Form.Close() or Form.Dispose() to get rid of a dialog at runtime.

**Form.Close()** removes the dialog from sight and calls the Closing() and Closed() methods.  You can still access the form and bring it back later on.

**Form.Dispose()** destroys the dialog and frees its resources back to the operating system.   _It does not call the form's Closing() and Closed() methods. _Once disposed, you may not recall a form.

Which to use? If you have no logic in the form's close methods and don't intend to re-use the form, go with Dispose().  Otherwise, go with Close().  Some programmers aren't sure which to use, and they use both - Close() then Dispose()!
