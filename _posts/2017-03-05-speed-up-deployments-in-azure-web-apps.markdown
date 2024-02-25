---
author: emwilson
comments: true
date: 2017-03-05 04:06:42+00:00
layout: post
link: https://www.alwaysgetbetter.com/2017/03/04/speed-up-deployments-in-azure-web-apps/
slug: speed-up-deployments-in-azure-web-apps
title: Speed up Deployments in Azure Web Apps
wordpress_id: 684
categories:
- Windows Azure
tags:
- azure
- deployment
- devops
- git
---

Is it possible to speed up deployments in Azure Web Apps? Without resorting to temporary fixes?

My biggest pain point with Azure is [the length of time it takes to push changes](/blog/2011/08/02/windows-azure-thoughts-months/). My e-commerce project has a simple architecture with a smallish number of dependencies but takes over 35 minutes to complete a build. Although there's no downtime during the process, you don't want to sit around waiting. Fortunately it's easy to speed up deployments in Azure.


## Problem: Starting from a Bad Place


Here's what I'm dealing with: **35 minutes** to push each commit.

[![35 minutes - yikes! Time to speed up deployments](/images/2017/03/Screen-Shot-2017-03-04-at-4.25.12-PM-300x164.png)](/images/2017/03/Screen-Shot-2017-03-04-at-4.25.12-PM.png)Keep in mind - this is a simple website. .NET Core MVC6 with some NuGet dependencies. On a local system it takes about 3 minutes to go from a fresh clone to a running app.

When you factor in configuration differences and thing you need to check off when setting up a live environment, that 35 minutes makes this project pretty unwieldily.

The problem stems from the way Azure deploys apps to the service fabric. Everything in your app is shared with mapped network folders which is why things like session state and file uploads are available to every instance. That's great if you have a complicated stateful app setup, but we're building totally RESTful services (right?) so we don't need that feature.

Building from a network drive is a problem for web apps because our npm and bower repositories generate **tons** of tiny files that absolutely suck to synchronize across a network drive.


## Solution: Speed Up Deployments by Skipping the Network


Instead of waiting for the same files to synchronize across the network, use the Git repository on the local machine. This skips the network latency and does all the deployment work on the local machine.

[![Using local cache for Git deployments](/images/2017/03/Screen-Shot-2017-03-04-at-4.46.41-PM-1024x46.png)](/images/2017/03/Screen-Shot-2017-03-04-at-4.46.41-PM.png)

In your Application Settings, add a key for **SCM_REPOSITORY_PATH** with the value **D:\local\repository**.

Now my deployments are 10 minutes - a big improvement.


## [![Speed up deployments on Azure by using local drive for builds](/images/2017/03/Screen-Shot-2017-03-04-at-4.49.06-PM-300x150.png)](/images/2017/03/Screen-Shot-2017-03-04-at-4.49.06-PM.png)Store the Right Things


10 minutes is a big improvement but I know we can do better. Looking through my project I find 100MB of content images that should really be part of a CDN. Now my repo is only 8MB and the deployment time is slashed again.

[![Respectable build times](/images/2017/03/Screen-Shot-2017-03-04-at-4.52.28-PM-300x139.png)](/images/2017/03/Screen-Shot-2017-03-04-at-4.52.28-PM.png)5 minutes, now _that_ is a respectable build time.


