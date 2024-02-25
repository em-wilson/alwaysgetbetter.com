---
author: emwilson
comments: true
date: 2011-04-11 12:46:05+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/11/play-framework-saves-world/
slug: play-framework-saves-world
title: How Play Framework Saves the World
wordpress_id: 367
categories:
- internet
- Java
- Play Framework
tags:
- efficiency
- play framework
- unit testing
---

Play framework must be the best-kept secret in the Java world. If you haven't had a chance to see their totally awesome demonstration video where they build a full app before your eyes in a matter of minutes, [go - go now](http://www.playframework.org/). Then come back.

Why do I like this framework so much? Put simply, it is an elegant solution for nearly every problem I've ever run into in developing websites, both single-server and multi-server applications. Don't take my word for it, see for yourself.

**The Goodness of Java**
To my mind, Java has the edge over more common scripting languages (like PHP) because it is compiled (fast) and statically typed (reliable). In the past, using compiled languages on the web was only possible if you were using ASP.NET or willing to put up with the hassles of existing Java frameworks and servers.

Play's first innovation comes from wrapping the Java runtime inside a Python web server; using a Play application is as easy as running a command line script and connecting with your web browser. Play's second innovation is its just-in-time compilation and display of error messages; if you make a mistake you will know in the amount of time it takes to hit refresh on your web browser.

Since it IS java, programmers can use libraries they have built for other applications or sourced from other vendors and plug directly into their code. This is one of the advantages Microsoft has had going for it and it is good to see it implemented so nicely in the open source world.

**The Ease of Rails**
Love it or hate it, Ruby on Rails has had an affect on the entire web world and its reach is definitely felt in the Play framework. Everything from the routing to JPA integration has that minimal-configuration design that is so prevalent in the Ruby world. Play has the edge though, due to Java annotations and the extra control you get as a developer.

**Baked-in Unit Testing**
Admittedly this is the first thing that drew me to the Play framework. Unit testing has to be one of the most important aspects of good programming; in fact, if your code is _not_ covered by unit tests, I argue it is incomplete. Play has terrific support for unit testing, functional testing and selenium-based web testing. In version 1.1, Play added a headless web testing mode, paving the way to run framework applications in the context of an automated build environment - smart move!

Although awkward at first, using YAML files for database fixtures makes a lot of sense. Managing database access in unit tests has always been a challenge but thanks to the in-memory database server and fixture files Play offers us database _integration testing_ - giving us the fresh-start benefits of mock frameworks along with the soundness of mind that comes from knowing you are testing the real database.

**Share Nothing Architecture**
Call it laziness, call it human error. At some point in the development cycle, the session always seems to end up carrying user data around. Even with data-sharing applications like memcached, that style of development does not scale well. With Play, sessions are stored in user cookies and consist of an encrypted key. The idea is the application takes care of loading any additional information it needs from this seed information, so the web cluster can be expanded to hundreds of nodes or reduced to a single server with no performance penalties on the other servers. Each Play instance operates as if it is the only one in existence, making it far easier to support complex site architectures.
