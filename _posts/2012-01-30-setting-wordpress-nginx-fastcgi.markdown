---
author: emwilson
comments: true
date: 2012-01-30 12:00:17+00:00
layout: post
link: https://www.alwaysgetbetter.com/2012/01/30/setting-wordpress-nginx-fastcgi/
slug: setting-wordpress-nginx-fastcgi
title: Setting up Wordpress with nginx and FastCGI
wordpress_id: 490
categories:
- PHP
- Ubuntu
tags:
- apache
- memcached
- MySQL
- nginx
- PHP
- varnish
---

All web site owners should feel a burning need to speed. Studies have shown that viewers waiting more than 2 or 3 seconds for content to load online are likely to leave without allowing the page to fully load. This is particularly bad if you're trying to run a web site that relies on visitors to generate some kind of income - content is king but speed keeps the king's coffers flowing.

If your website isn't the fastest it can be, you can take some comfort in the fact that the majority of the "top" web sites also suffer from page load times pushing up into the 10 second range (have you BEEN to Amazon lately?). But do take the time to download YSlow today and use its suggestions to start making radical improvements.

I've been very interested in web server performance because it is the first leg of the web page's journey to the end user. The speed of execution at the server level is capable of making or breaking the user's experience by controlling the amount of 'lag time' between the web page request and visible activity in the web browser. We want our server to send page data as immediately as possible so the browser can begin rendering it and downloading supporting files.

[![](/images/2011/12/agb_new.png)](/images/2011/12/agb_new.png)Not long ago, I described [my web stack](/blog/2011/12/20/fastcgi-nginx-performance-vm/) and explained why I moved away from the "safe" Apache server solution in favour of nginx. Since nginx doesn't have a PHP module I had to use PHP's FastCGI (PHP FPM) server with nginx as a reverse proxy. Additionally, I used memcached to store sessions rather than writing to disk.

Here are the configuration steps I took to realize this stack:

**1. Memcached Sessions**
Using memcached for sessions gives me slightly better performance on my Rackspace VM because in-memory reading&writing is hugely faster than reading&writing to a virtualized disk. I went into a lot more detail about this last April when I wrote about [how to use memcached as a session handler in PHP](/blog/2011/04/09/memcached-session-handler/).

**2. PHP FPM**
The newest Ubuntu distributions have a package **php5-fpm** that installs PHP5 FastCGI and an init.d script for it. Once installed, you can tweak your php.ini settings to suit, depending on your system's configuration. (Maybe we can get into this another time.)

**3. Nginx**
Once PHP FPM was installed, I created a site entry that would pass PHP requests forward to the FastCGI server, while serving other files directly. Since the majority of my static content (css, javascript, images) have already been moved to a content delivery network, nginx has very little actual work to do.

`
server {
listen 80;
server_name sitename.com www.sitename.com;
access_log /var/log/nginx/sitename-access.log;
error_log /var/log/nginx/sitename-error.log;
# serve static files
location / {
root /www/sitename.com/html;
index index.php index.html index.htm;`

# this serves static files that exists without
# running other rewrite tests
if (-f $request_filename) {
expires 30d;
break;
}

# this sends all-non-existing file or directory requests to index.php
if (!-e $request_filename) {
rewrite ^(.+)$ /index.php?q=$1 last;
}
}

location ~ \.php$ {
fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
fastcgi_param SCRIPT_FILENAME /www/sitename.com/html$fastcgi_script_name;
include fastcgi_params;
}
}

The **fastcgi_param** setting controls which script is executed, based upon the root path of the site being accessed. All of the requests parameters are passed through to PHP, and once the configuration is started up I didn't miss Apache one little bit.

**Improvements**
My next step will be to put a varnish server in front of nginx. Since the majority of my site traffic comes from search engine results where a user has not yet been registered to the site or needs refreshed content, Varnish can step in and serve a fully cached version of my pages from memory far faster than FastCGI can render the Wordpress code. I'll experiment with this setup in the coming months and post my results.
