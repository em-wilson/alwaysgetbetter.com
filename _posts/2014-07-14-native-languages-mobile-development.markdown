---
author: emwilson
comments: true
date: 2014-07-14 04:57:01+00:00
layout: post
link: https://www.alwaysgetbetter.com/2014/07/14/native-languages-mobile-development/
slug: native-languages-mobile-development
title: Native Languages are Best for Mobile Development
wordpress_id: 562
categories:
- Mobile
tags:
- design
- efficiency
- mobile
- standards
---

Like it or not your clients want mobile and if you are a developer without experience in this realm you are behind the curve.

Having been mobile-centric for the past three years of my professional life, I have heard all of the different lines of thought around toolsets and approaches for creating software targeting these devices. Moreover, I've lived through a lot of those toolsets and approaches, and have spent time thinking about which worked well and which ones, well... sucked.

It all boils down to this:

**Build native apps using the tools and languages provided by your platform.**

[![](/images/2014/07/plantronicsmarque_2blackandroidiphone.jpg)](https://www.flickr.com/photos/40899823@N02/8178621076)Yes, this is the same position I held when [I wrote about this almost two years ago](/blog/2012/09/10/observations-mobile-development/), but I stand by it. I've had a chance to play and build with the likes of Adobe Air, PhoneGap/Cordova, SenchaTouch and Xamarin, but I always end up going back to Java (Android) or Objective-C (iOS) because...

**Cross-Platform Isn't Good for Anybody**

Your app is iOS only... are your installed customers complaining about it not being on Android? No! Why would they care?

When talking about a new project one of the first things to come up is the idea of using X tool so we can build to a lot of platforms after writing the code once. I guess the thinking behind this is somewhere along the lines of "if we make our product available to more people, then it follows that more people will obtain it and we will make more money". I don't buy this.

Look at it this way - you're up against a million apps. What sets you apart? How do you communicate that to all of your potential customers? Are downloads from the Windows Store equivalent to downloads from Google Play? Do they use their phones in the same way and monetize the same way? Are the demographics the same?

If everyone was the same, it would _still_ be a bad idea to build your app around a cross-platform framework. Invariably these work in a lowest-common-denominator fashion meaning you get universal support by trading off native features. Yes, your app runs the same on all the platforms, but Android users are accustomed to navigating using their back button, which iPads don't have. Instead of providing beautiful context-sensitive navigation using activities for Android and NavigationController hierarchies for iOS, you get uniform views.

**"Universal" is a Lie**

Okay, so most development kits come with a way to plug in your own native code for the device-specific goodness (better GPS, camera gestures, whatever). Now you're writing native code anyway, and you're on the hook to support all those platforms. So why would you want to go through the pain of hooking all that into a cross-platform tool instead of going directly to your platform in the first place.

Worse, your universal framework has bugs. I'm sure it's great software but all software has bugs. When you hit a bug that stops you from moving forward, what are you going to do? When the underlying operating system software changes and your framework hasn't been updated yet, are you not going to deploy until the author gets around to supporting the new requirements? When you choose a cross platform tool you are choosing someone else's opinions and priorities and ceding control of your own product.

My recommendation is to pick a release target and excel at it. Will it be in the Apple ecosystem? If so, don't be afraid to go all in. Learn how iPhone users are going to interact with your software and do everything you can to speak to them, please them, and turn them into paying customers. They don't want a great Android experience, they want software they don't even think about. They won't patiently deal with your buggy software because you were so afraid of missing out on customers that you greedily deployed a boring app just to support some other platform. The "smaller" number of users is still a lot of users!

I get the fear factor, but the cross-platform tools are designed to make life easier for developers, not customers. The developer isn't buying my product - I don't care so much if he's worried about synchronizing features in a future Android port of the app.

**Speed is King**

I want my app to load fast and I don't want to wait around for it. You can't get more performance than a well-written app build from native languages and tools. Don't be afraid of XCode, Visual Studio and IntelliJ - embrace them and enjoy the barebones software you can build with them.

**Thinking in New Paradigms**

Suppose you do want to put your app on two different kinds of devices. Now you need to maintain your program logic in two programming languages, adding new features and squashing bugs in both places. That's not really such a bad thing.

What if you have an ENORMOUS code base with hundreds of thousands of lines of code - how will you ever keep that synchronized between two development tracks? Well, you might a good candidate to actually use one of those cross-platform tools. Or you might be asking yourself if your work load is really appropriate for a mobile experience (something they won't tell you -- not every app belongs on a mobile device).

Apps don't need to behave the same on different device formats. Every modern operating system has a different focus and enormous capability list that becomes adopted by its users - so evaluate what it is really like to use your app on each device. Especially for UI stuff - do you need backward navigation on Android when there is a hardware button for that? What buttons can you eliminate on iOS in favour of multi-touch gestures?

In the past 5 years I've added Java, Scala, Ruby, JavaScript (specifically, Node.js-style callbacks), Objective-C and lately Swift to the list of languages I've used extensively and thought deeply about. Each one has challenged me to look at programming differently and apply new practices to older thought patterns. Working withing multiple native environments is not a hindrance, it's a huge boost to the quality of software you can create.

**Streamlined Learning**

When you use a cross-platform tool, you have an extra learning curve - that of the tool itself. I don't buy into the thought that it improves your productivity, but I definitely see where it decreases your productivity. You need to be aware of bugs in your tool (as we mentioned earlier, your tool is great but it's still software and software has bugs), you need to be aware of the capabilities of your tool including all the new features they add (which, by the way, just expose native features you should have been working with directly all along).

If you can avoid it, skip the cross-platform stuff and go right to your _actual_ platform.

**Putting it all together**

As a programmer your job is not about writing code, it's about connecting businesses with their objectives. A good technologist doesn't get caught up in ideology over which approach they ought to take to build software - they decide which implementation detail will get them to their goal. So if a cross platform tool makes sense for your project, use it. Just be sure that you're picking the right tool, and not the comfortable (for now) one
