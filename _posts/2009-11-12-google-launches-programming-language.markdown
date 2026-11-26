---
author: emwilson
comments: true
date: 2009-11-12 10:30:59+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/11/12/google-launches-programming-language/
redirect_from:
- /2009/11/12/google-launches-programming-language/
slug: google-launches-programming-language
title: Google Launches Its Own Programming Language
wordpress_id: 277
categories:
- General Programming
tags:
- Go
- Google
- linux
- programming
---

<p><strong>Update (November 2026):</strong> This post is from 2009, when Go was brand new. Go has since grown up: it hit 1.0 in 2012, is now widely used for servers and command-line tools, and installs natively on Windows. I've left the original text below and struck out what no longer applies, with updated wording in its place.</p>

Google has taken another step toward world domination with the launch of an <del>experimental new programming languages</del> programming language aptly named "Go". _Go_ <del>promises to pick up</del> picked up where C and Python left off, providing programmers with a <del>new</del> garbage-collecting <del>low level languages</del> language suited to efficient server programming.

<del>I spent the better part of last night looking for a way to get _Go_'s tool chain to run under Windows using Cygwin. Unfortunately the tools can't be created; even if they could, they would produce binary files which would be unusable within Cygwin/Windows due to their low-level nature.</del>

In 2009 I spent a night trying to get _Go_'s tool chain running under Windows with Cygwin, and it simply wasn't possible. That problem is long gone: there is now an official Windows installer, and no Cygwin required.

<del>If you are a Windows programmer hoping to give _Go_ a try, your best bet is to download the [andLinux distribution](https://www.andlinux.org/) - this is a native Linux distribution that runs similar to a virtual machine under Windows. Once you have set it up, go to the</del> If you are a Windows programmer hoping to give _Go_ a try, download the installer and follow the instructions on the [Installing Go](https://go.dev/doc/install) page <del>for instructions on getting started with the new language.</del> to get started. (andLinux, the Linux-in-Windows distribution I recommended in 2009, is no longer maintained. If you want a Linux environment on Windows today, WSL is the modern replacement.)
