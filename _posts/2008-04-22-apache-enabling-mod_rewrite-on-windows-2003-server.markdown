---
author: emwilson
comments: true
date: 2008-04-22 01:29:58+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/21/apache-enabling-mod_rewrite-on-windows-2003-server/
slug: apache-enabling-mod_rewrite-on-windows-2003-server
title: 'Apache: Enabling mod_rewrite on Windows 2003 Server'
wordpress_id: 24
categories:
- PHP
- Web Programming
tags:
- apache
- Windows Server 2003
- wordpress
---

Recently I had to set up a Wordpress blog on a Windows 2003 server, which involved installing Apache, PHP and MySQL.  Everything went pretty well - except for the typical PHP.ini problems I shall write about later.

Once the database was set up and I had the blog running, I tried to adjust the permalink structure using .htaccess rewrite rules so they would be more human-readable.

Out of the box, mod_rewrite isn't enabled on Windows Apache.

The steps I took to resolve the problem were:



	
  1. Uncomment the line containing '**LoadModule mod_rewrite modules/mod_rewrite.so**'

	
  2. In the VirtualHost directive hosting the blog, add '**AllowOveride All**'

	
  3. Restart Apache


It works like a charm, now.  All the tools ship with Apache, it's just a matter of configuring them as needed.
