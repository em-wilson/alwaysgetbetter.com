---
author: emwilson
comments: true
date: 2024-02-25 19:20:00+00:00
layout: post
link: https://www.alwaysgetbetter.com/2024/02/25/github-pages/
slug: github-pages
title: GitHub Pages
excerpt: I'm tired of running my own server just to host a blog, and GitHub seems like the perfect place to store all these thoughts.
categories:
- General Programming
photo:
  source: /images/ai/00048-1370084240
  url: /images/ai/00048-1370084240.jpg
  description: In the zone
published: true
---

I got a Rackspace server a decade and a half ago in order to host my blog and satisfy my need to
learn about server administration, security, and all that good stuff. Over the years I moved around,
first to Media Temple then Digital Ocean then to AWS until finally getting tired of the serve
management and throwing everything up into a shared hosting service.

That was fine for a few years, but I'm not doing any hosting work lately, and it seemed like a
wasteful expense for a blog I haven't been updating.

I've been making static sites with Jekyll for years and decided to throw my blog into it and
host directly from my [GitHub account](https://pages.github.com/). The pages environment is free,
fast, and lets you use your custom domains, so it doesn't really make sense to keep paying for
hosting.

As a programmer, I am getting satisfaction from seeing all my posts in a Git repository,
stable and versioned. No more [tweaking PHP](/blog/2011/12/20/fastcgi-nginx-performance-vm/)
or worrying about WordPress and plugin vulnerabilities.

Pros
----
* Free!
* Static sites are *fast* - like, crazy fast
* Cross-linking posts is way easier - searching my file system for post content is fast
  and straightforward
* Markdown for programming articles works so much better than editing in WordPress
* The workflow for adding new articles and pages is simple
* It's easier to think about the files and resources I'm using in my build

Cons
----
* Less control. If GitHub doesn't support it, I can't do it
* Static, so no more comments/discussion. I haven't seen anything other than spam in years, so
  I'm not feeling much pain from this.
* If GitHub decides to stop making pages free, I'll have to move
  again. Whatever, I have the source code
* If Jekyll changes I won't be able to build the site. Rebuilding to something else
  from source would be painful
* Doing complicated layout things is *slooowww*. I don't love the templating engine