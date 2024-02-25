---
author: emwilson
comments: true
date: 2009-04-04 15:48:29+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/04/04/move-wordpress-database/
slug: move-wordpress-database
title: How to Move a Wordpress Database
wordpress_id: 205
categories:
- Wordpress
tags:
- Blogging
- MySQL
- SQL
- wordpress
---

One of the most common requirements for web developers is the ability to switch code from development servers to live production environments. This can be tricky if you're working with Wordpress; moving the files is dead simple, but since Wordpress uses canonical URLs you have to be careful if you are trying to transfer any of your database content.

Canonical URLs force your site to use the same base path (www.yourdomain.com rather than yourdomain.com). But if you are working in a development environment - e.g. the development site's address isn't the same as your web site, but is rather something like 127.0.0.1 - you need to be able to make the switch to Wordpress without bringing your site down.

Since I end up having to look for this information so often, here are the steps I use to accomplish this amazing feat:



## Download The Database


From the shell prompt of your server, dump Wordpress' MySQL database into a backup file:

`mysqldump –-add-drop-table -uusername -ppassword databasename > mysqlbackup.DATE.sql`

Move it over to the new server and run this command to overwrite your target:

`mysql -udb##### -p -hinternal-db.s#####.gridserver.com db#####_dbname < mysqlbackup.DATE.sql `



## Update the Database Paths


Log into your MySQL database and issue this update command to ensure Wordpress redirects to the new server:

[source language=":sql"]
UPDATE wp_options SET option_value = replace(option_value, 'http://www.old-domain.com', 'http://www.new-domain.com') WHERE option_name = 'home' OR option_name = 'siteurl';
[/source]

Next update the post URLs:

[source language=":sql"]
UPDATE wp_posts SET guid = replace(guid, 'http://www.old-domain.com','http://www.new-domain.com');
[/source]

Finally, update your posts' content to fix any internal links:

[source language=":sql"]
UPDATE wp_posts SET post_content = replace(post_content, 'http://www.old-domain.com', 'http://www.new-domain.com');
[/source]

That's all!  Repeat these steps when moving from production to development and vise-versa.

As I said I typically search for this information whenever I need to move Wordpress sites. I find the SQL queries at: [http://www.mydigitallife.info/2007/10/01/how-to-move-wordpress-blog-to-new-domain-or-location/](http://www.mydigitallife.info/2007/10/01/how-to-move-wordpress-blog-to-new-domain-or-location/)
