---
author: emwilson
comments: true
date: 2008-07-02 03:06:12+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/07/01/getting-real-power-in-vista/
slug: getting-real-power-in-vista
title: Getting Real Power in Vista
wordpress_id: 36
categories:
- Operating Systems
- Windows Vista
tags:
- security
- Windows Vista
---

Windows Vista hides the **administrator** user.  In order to access it, open a command prompt and issue the command:

net user administrator /active:yes password

Password can be anything you want, and will set up the administrator user.

Although you can access much of your system's setting under the default super user account, Vista implements a User Access Control System that effectively makes **administrator** the only _real_ super user.  In particular, the "Local Users and Groups" interface is normally hidden from you.

One more note - the command listed above can't be run from a regular shell - you must open the shell as administrator.
