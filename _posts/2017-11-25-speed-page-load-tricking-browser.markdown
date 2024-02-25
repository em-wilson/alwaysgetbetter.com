---
author: emwilson
comments: true
date: 2017-11-25 06:20:48+00:00
layout: post
link: https://www.alwaysgetbetter.com/2017/11/25/speed-page-load-tricking-browser/
slug: speed-page-load-tricking-browser
title: Speed Up Page Load By Tricking the Browser
wordpress_id: 428
categories:
- General Programming
- Web Programming
tags:
- CSS
- HTML
- js
- networking
---

Nettiquette is built into web browsers. When you go to download a page, its contents will load in at a max of 2 files at a time (by default). So if there are 12 CSS and JS files, you'll only get 2 at a time until you load them all.

The good news is you can trick browsers into doing more at a time.

Enter subdomains.

Offload your CSS to css.yoursite.com, and your JavaScript files to js.yoursite.com. Now your website will load 6 files at a time (2 CSS files, 2 JavaScript files, 2 HTML/Image files from your main site).

It doesn't matter if each subdomain is pointing to the same website. Web browsers go by the site URL and nothing more.

HTTP/2 is supposed to eliminate this problem, of course, but in the meantime doing this helps when you can't crunch down your files any more.
