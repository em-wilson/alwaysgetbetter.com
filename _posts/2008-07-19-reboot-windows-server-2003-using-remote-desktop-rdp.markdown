---
author: emwilson
comments: true
date: 2008-07-19 00:25:18+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/07/18/reboot-windows-server-2003-using-remote-desktop-rdp/
slug: reboot-windows-server-2003-using-remote-desktop-rdp
title: Reboot Windows Server 2003 Using Remote Desktop (RDP)
wordpress_id: 37
categories:
- Windows Server 2003
tags:
- command line
- RDP
- remote desktop
- server administration
- Windows Server 2003
---

Since Microsoft updated their Windows Server 2003 software, administrators relying on their Remote Desktop Connection are having difficulties rebooting.

When connected via RDP, if you reboot normally you will be disconnected from the server and all will appear well.  Sometimes, however, the server won't actually reboot!  Furthermore, it will block all incoming connections to RDP, and to everything else.  The only means of recovery is rebooting from the console.


## Solution


It is possible to use our knowledge of the command line to perform a proper system reboot using the **shutdown** command.

The relevant switches are:

/f - Force the shutdown even if other users are connected

/r - REBOOT, rather than shutdown

/t 0 - Set the timer to 0 seconds, i.e. perform the command right away

Example:

**reboot /f /r /t 0**

I hope this helps prevent some head-scratching!
