---
author: emwilson
comments: true
date: 2011-03-04 01:23:03+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/03/03/displaying-productiononly-markup-rails/
slug: displaying-productiononly-markup-rails
title: Displaying Production-Only Markup in Rails
wordpress_id: 351
categories:
- Design Patterns
- Ruby on Rails
tags:
- ruby on rails
---

If you are running something like Google Analytics on your website, you probably don't want its associated JavaScript code to appear in your web browser while you're developing (it would skew your statistics). In Rails, it is incredibly simple to block off a segment of markup for specific environments by using the **Rails.env** variable.

If you are using Passenger, your rails environment is set to 'production' by default, making it very easy to do something like this:


