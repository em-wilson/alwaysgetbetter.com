---
author: emwilson
comments: true
date: 2008-11-24 12:00:31+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/11/24/drupal-stuck-at-database-configuration/
slug: drupal-stuck-at-database-configuration
title: Drupal Stuck at Database Configuration
wordpress_id: 68
categories:
- Drupal
tags:
- apache
- drupal
- MySQL
- windows xp
---

When configuring Drupal 6.6 on a Windows XP/Apache/MySQL box, I ran into an issue whereby I would enter the database information on the **Database Configuration** screen, press the advance button, but be constantly redirected back to the Database Configuration screen.

The Drupal community indicates this is a problem with permissions - Drupal needs to be able to write to your site's _settings.php_ file.  All permissions appeared to be correct in my setup but I was still unable to continue.

The solution was to edit the **settings.php** file, putting in my database information manually.  Just look for this line:


<blockquote>$db_url = 'mysql://username:password@localhost/databasename';</blockquote>


And change the _username_, _password _and _databasename _parts.

Then return to the Database Configuration screen, enter the information again and continue.  The correct database information will be read from the settings file and the configuration will continue to the next step.

Happy hunting!
