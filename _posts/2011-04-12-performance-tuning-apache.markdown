---
author: emwilson
comments: true
date: 2011-04-12 10:31:21+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/12/performance-tuning-apache/
slug: performance-tuning-apache
title: Performance Tuning Apache
wordpress_id: 376
categories:
- internet
tags:
- apache
- efficiency
- performance
photo:
  source: http://www.flickr.com/photos/23408922@N07/5596445582/
  url: http://farm6.static.flickr.com/5141/5596445582_6f5f41dbff_m.jpg
  description: Project 365 #95: 060411 The View From Below
  cc_author: comedy_nose
  cc_link: http://www.flickr.com/photos/23408922@N07/5596445582
---

One of my favourite aspects of [the cloud](/blog/2011/04/05/cloud-computing-magical/) is the ease with which we can create new VMs to test our wacky architecture theories. It's so easy (and cheap!) to spin up a small server cluster for some serious load testing, and then destroy it again when done.

If nothing else, it provides a safety net and teaches you how to squeeze every ounce of performance out of big and small server instances. Let's examine ways in which we can make our dynamic Apache settings much faster.

**Turn Off Modules You're Not Using**
This should be fairly obvious, but Apache ships with a number of modules which can affect performance but which most of us never need. Check your _/etc/apache/mods-enabled_ folder to see what can be removed.

**Never Trust Defaults**
The default Apache settings are optimized for a website serving static files only. Booorrring! Never be afraid to question what you see in the configuration files; the more you understand about the inner workings of the system, the better you will be able to improve its performance.

**RAM is good, Swap is Bad**
Running out of physical memory (RAM) and hitting the hard drive's swap space is bad, especially in the Virtual Machine world. When this happens your performance will nose dive; your machine may even crash. The simplest solution is to increase the amount of RAM available to your server, but if that is too costly or impossible, read on.

**Kill the KeepAlive**
Whenever a request is made to the web server, it keeps the network connection open for a small amount of time (often 15 seconds). During that time, if the visitor's web browser needs to get another file, it goes through the same connection thereby avoiding wasting time re-connecting to your server. The problem is the open connection will use up space in your connection pool so if your site is under heavy load new visitors will get queued up and may experience slowdowns trying to access your content.

If Apache is your front-end web server, set the _KeepAliveTimeout_ to 2 seconds. This will keep the number of requests fluid even under heavy load.

If your server is behind a firewall like nginx or HAProxy where KeepAlives are not honoured, turn this setting off entirely.

**Don't Serve Static Files**
Apache is a memory hog. Since each hit to the server is relatively heavy in terms of threads and memory, we are in better shape when we serve non-changing static content like images, stylesheets and javascript [using a single-threaded server like nginx or lighttpd](/blog/2010/09/25/give-apache-break-nginx/) or even a memory server like varnish (bonus points for using a CDN to serve static files, avoiding the hit to your server at all).

**Turn off HostnameLookups**
This should already be done by default in your Apache configuration; if it isn't, do it now. When _HostnameLookups_ is on, Apache checks every incoming request's IP address for its host name. This can dramatically increase your latency, and isn't healthy for DNS servers either.

**Disable AllowOverride**
It is tempting to set _AllowOverride_ to All in order to give your .htaccess files free reign to do as they please. The downside of this directive is that every time anything is requested Apache will need to check that folder and every one of its parents all the way down to the site root in order to check for .htaccess commands. Apache recommends setting _AlloverOverride_ to **none** globally, enabling access for .htaccess files that can't be set in the site configuration.
