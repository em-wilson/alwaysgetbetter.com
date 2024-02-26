---
author: emwilson
comments: true
date: 2010-12-29 04:05:55+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/12/28/rails-generators-mongodb/
slug: rails-generators-mongodb
title: How to Use Rails Generators with MongoDB
wordpress_id: 349
categories:
- NoSQL
- Ruby on Rails
tags:
- MongoDB
- ruby on rails
---

The MongoDB classes do not come with default generators for Rails applications. In order to use the rails generate commands, you can use the indirect rails3-generators project available at https://github.com/indirect/rails3-generators/#readme

Installation:
1. Download the generators into your application's lib folder:
```
cd myapp
git clone git://github.com/indirect/rails3-generators.git lib/generators`
```

2. Install the rails3-generators gem
```
gem install rails3-generators
```

3. Add this to your project's Gemfile
```
gem 'rails3-generators'
```

Usage:
```
rails generate model ModelName --orm=mongo_mapper
```
