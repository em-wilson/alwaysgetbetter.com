---
author: emwilson
comments: true
date: 2010-01-25 02:38:32+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/01/24/wamp-server-crashes-installing-xcart/
slug: wamp-server-crashes-installing-xcart
title: Wamp Server Crashes Installing X-Cart
wordpress_id: 294
categories:
- PHP
- Windows Vista
tags:
- e-commerce
- x-cart
---

When running a default installation, WAMP Server's Apache crashes when installing X-Cart. The error happens immediately after setting up the MySQL tables and is caused by the curl extension in PHP.

To resolve, click on the WAMP Server icon in the task tray, go to Apache -> Version -> Get More. Download one of the 2.0 series Apache servers and install it.

Repeat the process for PHP; download one of the 5.0 series PHP versions and install it.

Switch WAMP Server to the new versions and run the install again - it should work with no problems.
