---
author: emwilson
comments: true
date: 2017-09-19 09:13:38+00:00
layout: post
link: https://www.alwaysgetbetter.com/2017/09/19/5-ways-keep-server-secure/
slug: 5-ways-keep-server-secure
title: 5 Ways to Keep Your Web Server Secure
wordpress_id: 699
categories:
- Hardware
- internet
- Operating Systems
tags:
- programming
- security
- server
---

[Equifax recently revealed that they were hacked ](https://arstechnica.com/information-technology/2017/09/so-equifax-says-your-data-was-hacked-now-what/)and exposed the personal information of over 143 million people. You may not be sitting on such identity-theft rich material, but keeping your server secure is absolutely a must for any business. Fortunately it really isn't very hard to achieve and maintain a decent level of protection.


## 1. Hire a Competent Developer


Cloud computing makes web servers super accessible to everyone; unfortunately that means it's really easy  to get a website running and get a false sense of security thinking everything is right when it's not. A lot of developers claim they can do it all for you when all the really know is how to install Apache but not how to lock it down.

A good giveaway is: If your developer can't get by without using a gui tool or web interface to set up and administer the website, they don't have any business on your server. These are great tools for setting up local development environment but they take shortcuts to make things more convenient for the user - not to improve security.

So if your guy doesn't have deep command line knowledge and the ability to work without these tools, fine. He's still a great developer, he can build  you a secure website following all the security best practices. He just doesn't have any business touching your web server; have someone else set up the "live" environment.


## 2. Lock Down Ports


When you're setting up a web server, lots of supporting programs get started that don't directly affect your website. Things like email, ICMP, DNS, time and DHCP are important to keep the system running but have no business leaving the local network. Everything that you don't absolutely need to access should be locked down. No access except from inside the server.

Web services like Apache and nginx are specifically designed to prevent people from using them as attack vectors to control your system, and they get compromised routinely. MySQL has no chance at all - don't open it to the outside world... ever.


## 3. Separate Database Servers


It's super common to find database servers improperly configured so they become a major security hole. On MySQL, most people don't know to add users better than "GRANT ALL PRIVILEGES ON x.* TO y@z;". Since the SQL server itself is often running with elevated system access, it only takes a single unsecured query to let people create files wherever they want on your server.

The easiest way to prevent this from affecting your websites is to move SQL to another server. Not only do you get the bonus of having a machine configured exclusively for web work and another exclusively for DB work, but bad things happening on one won't mean bad things happening on the other.


## 4. Keep Up With Software Patches


If you want to keep your server secure, keep it updated right away when vendors release updates for their software.

In a world full of zero-day exploits, any software with a security update is definitely a risk. Maybe even part of a malware package being sold in some dark corner of the Internet.

Don't be a victim, keep your server secure by keeping it up to date.


## 5. Enforce User Permissions


One of the most compelling reasons to use Linux traditionally has been the strong separation between services using user permissions. Even Windows Server supports it these days.

If you're using PHP, don't use Apache's modphp, [use php-fpm](/blog/2011/12/20/fastcgi-nginx-performance-vm/). Set up your pools to give each website its own user. Again it's all about compartmentalization. Bad things will happen and a good sysadmin makes sure the damage done by those bad things gets contained to a small area.


## BONUS #6: Keep Your Server Secure by Never Logging In


Never allow yourself or anyone else to log into the web server.

There's no reason for it. If you need to deploy a website update, use a CI tool like Jenkins. If you got rooted, trash the server and launch a new one.

People make mistakes, people forget about config changes, people can't be trusted. You don't need to worry about password scheme, RSA keys, iptables goofs or any of a million common problems if you just acknowledge the human risk factor and remove it from the equation.

When we move to programmed servers, we make it easier to bring new people on board, faster to verify and test new software versions, more repeatable in the case of a data failure, and more secure across our entire network. Don't make humans do the work of computers, automate all the things!
