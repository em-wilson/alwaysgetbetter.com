---
author: emwilson
comments: true
date: 2017-11-20 01:07:35+00:00
layout: post
link: https://www.alwaysgetbetter.com/2017/11/19/fun-amazon-x-ray/
slug: fun-amazon-x-ray
title: Fun With Amazon X-Ray
wordpress_id: 708
categories:
- AWS
tags:
- aws
- data
- prime
- x-ray
---

While riding on the train the other day, I was watching _The Man in the High Castle_ on Prime and thought I recognized the actor on screen. _"Too bad they only show the main actors. It would be nice if we could get a listing of who is in each scene,"_ I thought to myself. Imagine my surprise when I found out the app does exactly that.


## So Cool

[![](/images/2017/11/IMG_0244-300x169.png)](/images/2017/11/IMG_0244.png)

The info screen doesn't just show the main actors for the show like I thought, it shows who is _in the current scene_!

[![](/images/2017/11/IMG_0246-300x169.png)](/images/2017/11/IMG_0246.png)

It even changes as the scene changes. If you leave the info screen on, the actor list updates in real time as actors enter and leave the screen.


## Trivia

The info screen doesn't just show which actors are in the scene, it shows trivia about things going on in the scene; either actions of the characters or story background based on what's being said.

[![](/images/2017/11/IMG_0247-300x169.png)](/images/2017/11/IMG_0247.png)

As the action unfolds, the info changes to provide even more context.

[![](/images/2017/11/IMG_0245-300x169.png)](/images/2017/11/IMG_0245.png)


## How It Works

As best I can tell (maybe someone has better information?) the x-ray data is filled in large part algorithmically.

The main actor data comes from IMDB. I assume Amazon is using facial recognition technology to match profile photos with actors in each frame. Background actors aren't included so there's some intelligence there to either filter out people by screen time or dialogue.

There's no way everything is done automatically, so there has to be a human component in there as well. Very likely they are using something like mechanical turk to have humans verify the raw data and add contextual tagging.


## Your World Augmented

When I think about "augmented reality" this is the kind of thing I'm excited to see. Computer systems that provide more context about the world around us, not try to replace it entirely. Humans augmented by machines to make our whole experience better.

Next time you ask "who is that actor!?" try checking the app you're watching - it may be able to tell you more authoritatively than a Google search or the person you're sitting with.
