---
author: emwilson
comments: true
date: 2011-05-10 02:20:02+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/05/09/accessing-configuration-parameters-play-frameworks-template-engine/
slug: accessing-configuration-parameters-play-frameworks-template-engine
title: Accessing Configuration Parameters using Play Framework's Template Engine
wordpress_id: 430
categories:
- Play Framework
tags:
- Java
- play framework
- templating
---

Suppose we are building a Facebook application and have a variable called **fb.appId** containing our Application's ID. We want to use that to initialize an FBJS call, but obviously don't want to hard-code it into our page's template.

Using the template engine in Play! framework, we can make direct calls to the underlying Java framework as long as we use the full namespace path. Our Facebook Application Id is accessible like this:

```
${play.Play.configuration.get("fb.appId")}
```

If we're making a lot of configuration calls, we can simplify our lives by aliasing the namespace path:

```
%{
cf =  play.Play.configuration
}% 
${cf.get("fb.appId")}
```

**Conditional Statements**
Let's go a step further. Suppose we have Google Analytics and only want the JavaScript to run when our site has been deployed to production (I covered a similar technique earlier this year [using Ruby on Rails](/blog/2011/03/03/displaying-productiononly-markup-rails/)).

```
#{if play.Play.configuration.get("application.mode") == 'PROD'}
GOOGLE ANALYTICS CODE - WILL ONLY DISPLAY IN PRODUCTION
#{/if} 
```
