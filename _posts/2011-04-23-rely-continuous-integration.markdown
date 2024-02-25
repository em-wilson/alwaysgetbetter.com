---
author: emwilson
comments: true
date: 2011-04-23 02:46:33+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/22/rely-continuous-integration/
slug: rely-continuous-integration
title: Rely on Continuous Integration
wordpress_id: 408
categories:
- Design Patterns
tags:
- continuous integration
- Employment
- General Programming
---

If your testing process involves copying files over to a development environment and hoping you have the most up-to-date version of everything, stop now. There is a better way, and it is **continuous integration**.

CI works by constantly and automatically testing and publishing everyone's code into a staging environment. Any missing files or major problems are caught immediately so the team doesn't run into major conflicts at the end of the development cycle. Although continuous integration does not eliminate code bugs, it improves the software quality by allowing developers to identify and fix problems immediately, resulting in a final product with much fewer defects.

Although there are a lot of different parts to a successful CI process, it is possible to start up slowly and add to the regime as your team becomes more comfortable with their improved success rates.

**1. Establish a centralized source code repository**
A solid source code manager (SCM) is the backbone of a professional development setup. There are so many tools available for every possible organization, both paid and free. [I like git](/blog/2010/08/05/installing-git-ubuntu-1004/), but Subversion is used in a lot of environments. The key thing to look for is something that can track and rollback code changes over time and is shared between everyone working on the project.

**2. Automate the build**
After a good SCM has become part of development procedure, automate the build What this means, is every time someone commits to the SCM, have a tool like buildbot pull the fills and deploy them to a testing environment. From this stage on disallow anyone from making changes directly to the testing environment.

**3. Automate Testing**
Next add unit testing. When the build agent runs the unit tests, it should prevent the source code from going into the testing environment if any error conditions exist in the code. This will let the team make more daring changes to the source code without having to worry about time-consuming reversion testing.

**4. Automate Deployment**
At this point we're already have an automated way of deploying our code to a testing area - take it a step further and automate your deployment process. This will let you get your work into the hands of your customers faster and involve them in the planning process earlier - all resulting in an improved product and higher likelihood of staying on-time and on-budget.
