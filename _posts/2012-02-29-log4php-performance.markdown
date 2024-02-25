---
author: emwilson
comments: true
date: 2012-02-29 11:33:41+00:00
layout: post
link: https://www.alwaysgetbetter.com/2012/02/29/log4php-performance/
slug: log4php-performance
title: log4php Performance
wordpress_id: 492
categories:
- PHP
tags:
- debugging
- logging
- performance
- PHP
photo:
  source: /images/ai/00033-2075869658
  url: /images/ai/00033-2075869658.png
  description: Server Log Pile
---

We can take for granted that whenever we introduce a library or framework to our application, we incur an overhead cost.  The cost varies depending on what we're trying to do, but we generally accept that the lost performance is worth it for the increased maintainability, functionality or ease of use.

For many teams, logging is something that gets thrown in the mix at the last minute rather than through of all the way through. That's a shame because a well-implemented logging solution can make the difference between understanding what is going on in your system and having to guess by looking at the code. It needs to be lightweight enough that the overall performance is not affected, but feature-rich enough that important issues are surfaced properly.

Java programmers have had log4j for a long time, and log4net is a similarly mature solution in the .NET world. I've been watching log4php for awhile and now that it has escaped the Apache Incubator it is impressively full-featured and fast. But how much do all its features cost?

**Benchmarks**
I'll be looking into different options as I go, but let's consider a very basic case - you append all of your events to a text file. I've created a configuration that ignores all 'trace' and 'debug' events so only events with a severity of 'INFO' or above are covered.

In 5 seconds, this is what I saw:

Test                      | Iterations
--------------------------|--------------------------
BASIC (direct PHP)        | 45,421
INFO STATIC               | 45,383
INFO DYNAMIC              | 41,847
INFO STATIC (no check)    | 51,801
INFO DYNAMIC (no check)   | 47,756
TRACE STATIC              | 310,255
TRACE DYNAMIC             | 213,554
TRACE STATIC (no check)   | 271,043
TRACE DYNAMIC (no check)  | 196,653

**Tests**
What is all that? There are two ways to initialize the logger class - statically, meaning declared once and used again and again; and dynamically, meaning declared each time. With log4X, we typically perform a log level check first, for example isTraceEnabled() to determine whether to proceed with the actual logging work.

**Results**
I was surprised by how little log4php actually lost in terms of speed versus raw PHP. The authors have clearly done a thorough job of optimizing their library because it runs at 90% of the speed of a direct access.

I've always intuitively used loggers as static variables - initialize once and use over and over. This seems to be the right way by a huge margin.

Checking for the log level before appending to the log was a big win for the INFO messages, which are always logged to the file due to the configuration settings. The intended use is to allow programmers to sprinkle their code with debug statements which don't get processed - and therefore slow down - the production code. I would be very happen with this in my project. In the INFO metrics, the check slowed things down a bit - explained because the actual logging function performs the same check - so we are taking a double hit. But wait, there is a good reason...

The TRACE metric is interesting - these are events which are NOT appended to the log. In that case, when the check is not performed, we pass through the code more times. When the check is performed, the code has to execute deeper on the call stack before it figures out we aren't doing any actual logging, taking more time.

**Conclusion**
If you know you will be logging every single event, don't do a check. Otherwise do the check - it will save a lot of wasted cycles.
