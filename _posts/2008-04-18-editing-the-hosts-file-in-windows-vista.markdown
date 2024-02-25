---
author: emwilson
comments: true
date: 2008-04-18 21:49:03+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/18/editing-the-hosts-file-in-windows-vista/
slug: editing-the-hosts-file-in-windows-vista
title: Editing the HOSTS file in Windows Vista
wordpress_id: 23
categories:
- General Programming
tags:
- networking
- Windows Vista
---

Windows Vista keeps the HOSTS file locked down so only users with elevated permission can edit it.  I found the fastest way to add lines to this file in my own system was:



	
  1. Open the directory containing the file (C:\Windows\System32\drivers\etc\hosts)

	
  2. Copy the file to my Documents directory by right-clicking Send To...

	
  3. Editing the file in UltraEdit (or notepad, or whatever)

	
  4. Moving the file back to the _etc_ directory.


The act of moving it back causes Windows to prompt you for privilege elevation.  It's possible to raise your text editor's privileges, but going in through the command line - at least to me - seems like a much longer and more tedious way of doing the same thing.
