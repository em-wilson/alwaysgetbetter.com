---
author: emwilson
comments: true
date: 2010-11-14 17:31:44+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/11/14/cheap-file-replication-synchronizing-web-assets-fsniper/
slug: cheap-file-replication-synchronizing-web-assets-fsniper
title: 'Cheap File Replication: Synchronizing Web Assets with fsniper'
wordpress_id: 341
categories:
- Ubuntu
- Wordpress
tags:
- apache
- high availability
- hosting
- linux
- nginx
- replication
---

Awhile ago I wrote about how I was [using nginx to serve static files](/blog/2010/09/25/give-apache-break-nginx/) rather than letting the more memory-intensive Apache handle the load for files that don't need its processing capabilities. The basic premise is that nginx is the web-facing daemon and handles static files directly from the file system, while shipping any other request off to Apache on another port.

What if Apache is on a different server entirely? Unless you have the luxury of an NAS device, your options are:

**1. Maintain a copy of the site's assets separate from the web site**
There are two problems with this approach: maintainability, and synchronization. You'll have to remember to deploy any content changes separately to the rest of the site, which is counter-intuitive and opens up your process to human error. User-generated content stays on the Apache server and would be inaccessible to nginx.

**2. Use a replicating network file system like GlusterFS**
Network-based replication systems are advanced and provide amazing redundancy. Any changes you make to one server can be replicated to the others very quickly, so any user generated content will be available to your content servers, and you only have to deploy your web site once.

The downside is that many NFS solutions are optimized for larger (>50Mb) filesizes. If you rely on your content server for small files (images, css, js), the read performance may decline when your traffic numbers increase. For high availability systems where it is critical for each server to have a full set of up-to-date files, this is probably the best solution.

**3. Use an rsync-based solution**
This is the method I've chosen to look at here. It's important that my content server is updated as fast as possible, and I would like to know that when I perform disaster recovery or make backups of my web site the files will be reasonably up to date. If a single file takes a few seconds to appear on any of my servers, it isn't a huge deal (I'm just running Wordpress).

**The Delivery Mechanism**
rsync is fast and installed by default on most servers. Pair it with ssh and use password-less login keys, and you have an easy solution for script-able file replication. The only missing piece is the "trigger" - whenever the filesystem is updated, we need to run our update script in order to replicate to our content server.

Icrond is one possible solution - whenever a directory is updated icrond can run our update script. The problem here is that service does not act upon file updates recursively. **fsniper** is our solution.

The process flow should look like this.
1. When the content directory is updated (via site upload or user file upload), fsniper initiates our update script.
2. Update script connects to the content server via ssh, and issues an rsync command between our content directory and the server's content directory.
3. Hourly (or whatever), initiate an rsync command from the content server to any web servers - this will keep all the nodes fairly up-to-date for backup and disaster recovery purposes.
