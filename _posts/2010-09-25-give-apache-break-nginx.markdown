---
author: emwilson
comments: true
date: 2010-09-25 20:21:40+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/09/25/give-apache-break-nginx/
slug: give-apache-break-nginx
title: Give Apache a Break with nginx
wordpress_id: 331
categories:
- Hardware
- internet
tags:
- apache nginx linux
---

One of the things I've learned about Apache is that as good as it is, it suffers from its monolithic "do-everything" nature. The modules and tuning required for effective operation just doesn't fit into a lean, quick package. That said, I find it beats out the alternatives hands-down when it comes to running web applications of any complexity.

One very simple and effective method for improving Apache's performance involves off-loading static content to another server. Since Apache spawns a new process (and the memory allocation that goes with that) for every request, it is pretty wasteful to serve your images, css, javascript and similar files this way.

For larger applications, we could run a second web server that makes use of a single-threaded polling process like lighttpd, or use a content delivery network to move our content physically closer to our users for even more speed.

For smaller applications and organizations, we can use nginx as a proxy to serve static content to our visitors. This can be a separate physical web server, or it can be a service running on the same server as Apache. I use the same-server proxy approach at alwaysgetbetter.com, and it has made a significant difference in my server load and memory usage.

To start, change Apache's settings so it listens on a separate port (rather than the default port 80).

Next, set up nginx to listen on the default http port 80. We will let Nginx decide whether each request should be served directly from the hard drive, or whether it should pass through to Apache.

The config file for nginx looks like this:
`
server {
listen 80;
server_name mysite.com www.mysite.com;
access_log /var/log/nginx/website-access.log;
error_log /var/log/nginx/website-error.log;
# serve static files
location ~* ^.+.(jpg|jpeg|gif|png|ico|css|zip|tgz|gz|rar|bz2|doc|xls|pdf|ppt|txt|tar|wav|bmp|rtf|js|mp3|avi|mov)$ {
root /var/www/html;
expires 30d;
}

# pass requests for dynamic content to site
location / {
proxy_pass http://127.0.0.1:8080;
proxy_redirect off;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
client_max_body_size 128m;
client_body_buffer_size 256k;
proxy_connect_timeout 60;
proxy_send_timeout 60;
proxy_read_timeout 60;
proxy_buffer_size 4k;
proxy_buffers 32 256k;
proxy_busy_buffers_size 512k;
proxy_temp_file_write_size 512k;
}
}
`

It is also possible to serve PHP and other dynamic content using nginx, but for our purposes it makes a lot more sense to use Apache for scripting and nginx as the web-facing proxy.
