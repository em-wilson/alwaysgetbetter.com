---
author: emwilson
comments: true
date: 2011-11-05 01:19:42+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/11/04/multiple-development-environments/
slug: multiple-development-environments
title: Multiple Development Environments
wordpress_id: 454
categories:
- Web Programming
tags:
- efficiency
- performance
- planning
- standards
---

Hopefully when you do web work, you're not developing code on the same server your users are accessing. Most organizations have at least some kind of separation for their development and production code, but it's possible to go far further. Separating environments allows you to achieve [multiple threads of continuous integration](/blog/2011/04/22/rely-continuous-integration/) for all kinds of cool.

These normally break down as follows:

**Development**
Working code copy. Changes made by developers are deployed here so integration and features can be tested. This environment is rapidly updated and contains the most recent version of the application.

**Quality Assurance (QA)**
Not all companies will have this. Environment for quality assurance; this provides a less frequently changed version of the application which testers can perform checks against. This allows reporting on a common revision so developers know whether particular issues found by testers has already been corrected in the development code.

**Staging/Release Candidate**
This is the release candidate, and this environment is normally a mirror of the production environment. The staging area contains the "next" version of the application and is used for final stress testing and client/manager approvals before going live.

**Production**
This is the currently released version of the application, accessible to the client/end users. This version preferably does not change except for during scheduled releases. [There may be differences in the production environment](/blog/2011/03/03/displaying-productiononly-markup-rails/) but generally it should be the same as the staging environment.

Having separation between the different environments is not tricky, but managing your data environment can be. There are, of course, [all kinds of ways to solve the problem](/blog/2011/04/20/database-migrations/).
