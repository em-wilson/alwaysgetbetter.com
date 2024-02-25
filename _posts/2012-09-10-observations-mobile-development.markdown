---
author: emwilson
comments: true
date: 2012-09-10 10:00:39+00:00
layout: post
link: https://www.alwaysgetbetter.com/2012/09/10/observations-mobile-development/
slug: observations-mobile-development
title: Observations From Mobile Development
wordpress_id: 511
categories:
- internet
- Mobile
tags:
- interfaces
- mobile
- performance
photo:
  source: /images/2012/09/google_nexus_android_mobile_unboxing1.jpg
  url: /images/2012/09/google_nexus_android_mobile_unboxing1.jpg
  description: Nexus unboxing
---

With just a single mobile release under my belt now, I'm hardly what you might call an expert on the subject. What I can say for certain is the past year has been an eye opener in terms of understanding the capabilities and limitations of mobile platforms in general.

So having gone from "reading about" to "doing" mobile development, these are some of the "aha" moments I've had:

**Design for Mobile First**
The biggest revelation: Even if you're not setting out to develop a mobile application, your design _absolutely must_ start from the point of view of a handheld.

Think about it for a second - how much information can you fit on a 3.5" screen? Not much! So when you design for that form factor you have to go through a savage trimming exercise - everything from type, to layout, to navigation must communicate their intent in as tiny a space as possible. In other words, there's no avoiding the need to effectively communicate your message.

When you build all of your applications this way, mobile or not, it's hard not to come up with a user experience that has laser focus and goes straight for the mark. Once you have a bare minimum experience, _now_ you can augment it with additional navigation and information (including advertising) for larger form factors like desktop and tablets.

**Don't Fret the Framework**
Just as in web development, frameworks are powerful tools that come with sometimes painful trade-offs. Especially when you're getting started, don't worry about which framework you choose - just pick the one that caters most to the style of development you're already familiar with and jump in. If that means Adobe Air for ActionScript, cool. If it means PhoneGap for JavaScript, great.

Most of the new devices coming onto the market have more than enough memory and processing horsepower to handle the massive extra overhead incurred through cross-platform development tools. If you're starting a new project or iterating a prototype, don't hesitate to jump on board a tool that will get you to a product faster. This is one of those areas where the short term gain is worth the longer term pain...

**Native Wins**
We've known since the 80s, when developers had to release to a boatload of PC platforms - IBM, Commodore, Amiga, Tandy, etc - that software written directly for a particular platform outperforms and outsells similar software written for a generic platform and ported across to others. The same idea is definitely the case now, even though our cross-platform tools are far more advanced, and our globally-usable app much higher in quality that what we could produce 30 years ago.

Some of the compelling reasons why you would want to take on the expense of building, testing and maintaining you app natively:



	
  * The UI of your application will integrate seamlessly into the underlying operating system - iOS widgets look like they belong, Android layouts are laid out consistently compared to other applications

	
  * Raw speed - you don't need to go through someone else's API shim to get at the underlying operating system features, you don't have to develop custom native code since all the code is native; all CPU cycles are devoted to your application, resulting in much higher performance, particularly for graphic-intensive applications

	
  * Operating system features - each mobile operating system has its own paradigm and set of best practices which cross-platform tools gloss over to give you as a developer a consistent response. So your application misses the subtleties of the user's hardware experience - for example Android uses Activities as its interaction model, but the Adobe Air framework squashes that instead of forcing developers to program in an Activity-centric way


In other words, cross-platform tools exist in order to give developers the best experience, not to give the user the best experience. Your customer doesn't care if your app is available on Android, Windows, iPhone, Playbook and WebOS if all they have is an iPhone.

I believe cross-platform tools are the best way to get your project off the ground and usable fast, but right from the beginning you need to be thinking about converting your application to native code in order to optimize the experience for your customers.

**Market Fragmentation**
I bought an Android phone and have been enjoying developing for it. But I don't think I would enjoy developing for Android at large because of the massive amount of devices and form factors I would need to support. This is where Apple has an edge - although the learning curve for Objective-C is higher, once I have an iOS application, I know it will run on an iPod Touch, and iPhone or and iPad. Not only that, but my guess is people are more likely to want to spend small amounts of money on app purchases, since they've been trained to do so from years of iTunes.

**Backward Compatibility**
Moving to the web was a huge advantage in terms of software support because if you ever found a bug in your program you could patch it and release it without affecting any of your users. No one has to upgrade a web page.

This isn't true of mobile applications - ignoring mobile web applications - once someone downloads your app they may be slow to upgrade to newer verions, or they may never upgrade at all. This means any bugs that get released with your bundle are going to remain in the wild forever. If you have any kind of server-based presence, your server code needs to handle requests from all of those old app versions - so you need to make sure you get it right, and have good filtering and upgrade mechanisms in place.

**Choosing a Platform**
One thing that held me back from diving into mobile development was my hesitation to start. This is totally my fault - instead of just programming for WebOS when I had the Palm Pre, I thought it would be better/more accessible to use a more open JavaScript toolset so I could deploy my app to other phones. But really, what would have been the point? I only had a Palm Pre to run my software on and I definitely wasn't going to buy new hardware to test other versions. Instead of getting locked in analysis paralysis I should have just started programming for the Pre right away and transferred those skills to a more mainstream platform later.

So if you don't have a smartphone, go get one - it will change your life, or at least the way you interact with your phone. Then start building apps for it. That's all it takes to get into the game. Don't wait another second.
