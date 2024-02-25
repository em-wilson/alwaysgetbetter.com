---
author: emwilson
comments: true
date: 2016-02-06 01:21:18+00:00
layout: post
link: https://www.alwaysgetbetter.com/2016/02/05/https-on-static-sites-using-cloudfront/
slug: https-on-static-sites-using-cloudfront
title: HTTPS on Static Sites using Cloudfront
wordpress_id: 626
categories:
- internet
---

It seems like every time I log into my AWS account there are a ton of new services waiting to be discovered. (Not to mention dozens in early preview that don't even show up in the main list.) I feel like keeping up with what's going on in that space is becoming a full-time job all by itself. The most exciting new update is the Certificate Manager - pushing us one step closer to "https everywhere".

S3's static website hosting is a fascinating service. If you are able to design your site entirely using third party APIs (or even better, none at all), it's possible to fully host your site through CloudFront. Think unlimited scalability, super fast edge-based response times. [A parenting blog](http://www.theparentsnook.com) I manage as a static site (using Jekyll) has consistent response times all around the world. Improving response times for off-continent visitors has definitely had a big impact on session times.



## HTTPS and Static Sites



Amazon has done an incredible job making certificate provisioning easy. To add a new SSL certificate to your Cloudfront-based static site:

1. Go into your Distribution
2. Under the 'General' tab, click 'Edit'
3. Choose 'Custom SSL Certificate' and click the button "Request an ACM Certificate"
4. Make sure to type both your naked domain and "www." variant in the certificate
5. After confirming the certificate, go back to your distribution's general settings and select it
6. Wait 15 minutes and profit(?!)

Of course if you're starting a new site the same steps mostly apply, you can add a certificate right from the creation wizard.

What about cost? **FREE**. You do not have to pay a thing for SSL certificates using this service.



## Redirect Non-HTTPS Traffic to HTTPS



The next thing you should think about doing is redirecting all non-HTTP traffic to HTTPS. Yours is a professional site, or at least should look like it was created by a professional. Having that little lock icon next to your address says "I've thought this through and know what I'm doing". Anyone can toss up a website, not everyone is advanced enough to know about secure sites.



## Error on Existing Sites



If you're already running a (non-HTTPS) website through Cloudfront using S3 websites, the default origin behaviour is to "Match Viewer" when it comes to HTTPS. S3 sites do not support HTTPS so you will get a big ugly error.

The fix it:

1. Go to your Distribution
2. Under the "Origins" tab, choose your S3 origin and press the "Edit" button
3. Find "Origin Protocol Policy" and choose "HTTP Only"

while you're there, make sure SSLv3 is NOT selected under "Origin SSL Protocols". SSLv3 is bad news bears insecure - we don't want that!



## Limitations



The biggest drawback to Cloudfront's SSL solution is you are pretty much stuck with server name indication (SNI), sharing your SSL connection with hundreds of other sites. For most people this shouldn't be a problem, but if your audience is stuck in 2005 and hasn't upgrade to Vista or better by now, their browsers are not going to be able to access your SSL site this way.

Cloudfront offers IP-based SSL for $600 a month, which is overkill unless your company has seriously deep pockets (seriously, if your goal is free SSL certificates you aren't going down this option). If SNI is out of the question for you, your best bet will be an elastic load balancer pointing at a small server - you still get the benefit of free SSL certificates that never need maintenance and you get your own IP address.
