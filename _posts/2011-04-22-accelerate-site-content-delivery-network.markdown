---
author: emwilson
comments: true
date: 2011-04-22 03:30:38+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/21/accelerate-site-content-delivery-network/
slug: accelerate-site-content-delivery-network
title: Accelerate Your Site with a Content Delivery Network
wordpress_id: 406
categories:
- Blogging
tags:
- networking
- performance
- wordpress
---

The best way to keep visitors engaged in your website is by delivering your experience in as little time as possible. The average visitor will only stick around for a few seconds, so it is important to get them interacting with your content fast. The first thing to check for, of course, is any [bottlenecks in the initial page generation](/blog/2011/04/19/tracking-website-speed-problems/). Once the web page is being generated quickly, we can turn our attention to the next biggest culprit: the connection to your client.

Downloading files directly from a web server is costly, even if you're using an efficient server [like nginx for static files](/blog/2010/09/25/give-apache-break-nginx/).

A content delivery network (CDN) can help speed up the process by storing your content in data centres around the world so they get served to your visitors from locations that are physically close to them. This results in fewer network hops which makes the files download faster, and reduces the overall load on your web server so you can focus on doing more interesting dynamic application stuff.

At one point, CDN services were only available to companies with deep pockets and huge websites, but these days anyone can set up and use an inexpensive service with their regular hosting provider.

Check with your host to see if they offer a content delivery solution. The two providers I use for my blog, [Media Temple](http://mediatemple.net/webhosting/procdn/) and [Rackspace](http://www.rackspace.com/cloud/cloud_hosting_products/files/) both have excellent services. If you are using a Wordpress site, check out [W3 Total Cache](http://wordpress.org/extend/plugins/w3-total-cache/), which provides an all-in-one package for managing your files and optimizing the overall speed of your site.
