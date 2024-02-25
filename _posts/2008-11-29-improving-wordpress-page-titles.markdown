---
author: emwilson
comments: true
date: 2008-11-29 12:00:11+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/11/29/improving-wordpress-page-titles/
slug: improving-wordpress-page-titles
title: Improving Wordpress Page Titles
wordpress_id: 77
categories:
- Web Programming
tags:
- Google
- SEO
- wordpress
---

Anyone half-serious about running a large blog knows that search engine ranking is critical to getting properly indexed.  Over 90% of the traffic to **Always Get Better** originates from search engine traffic - therefore the ease of which Google (among others) is able to index this site is the life-blood of its continued success.

Wordpress is a terrific platform with out-of-the-box support for many of the critical elements necessary for site building: CSS layouts, RSS feeds, track backs.  But the glaring problem with Wordpress is the default title format: "Blog Name > Blog Archive - Post Title".  What is that!?

Many search engines only catalogue the first several characters of web page titles.  If the title of your post is at the end of the page title, it may get cut off in favour of your blog's name!

The simple solution is to reverse the code as explained at the [Wordpress](http://wordpressgarage.com/code-snippets/hard-coding-an-seo-friendly-title-in-the-wordpress-header-file/) garage (and quoted below for convenience):


<blockquote>You can switch it through the use of plugins, but if you want to avoid using another plugin you can fix this in the header.php file. Find the code that starts with <title>, and replace what is currently there with this:

`<title><?php if ( is_single() ) { ?> <?php wp_title(''); ?> &raquo; <?php } ?><?php bloginfo('name'); ?></title>`</blockquote>
