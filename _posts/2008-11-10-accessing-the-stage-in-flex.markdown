---
author: emwilson
comments: true
date: 2008-11-10 03:23:04+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/11/09/accessing-the-stage-in-flex/
slug: accessing-the-stage-in-flex
title: Accessing the stage in Flex
wordpress_id: 61
categories:
- Flash
- Flex
tags:
- ActionScript
- flex builder
---

When creating components in Flex, designers sometimes need to attach events to the main stage.  Unless the application has reached **creationComplete**, the **_stage_ **property of custom components will be _null_.

If you need to, for example, attach a MOUSE_MOVE event to the application stage from a component that doesn't include a **creationComplete **override, you have two options.


## Wait for Event.ADDED_TO_STAGE



[source:jscript]
addEventListener( Event.ADDED_TO_STAGE, function(e:Event):void
{
stage.addEventListener( MouseEvent.MOUSE_MOVE, myMouseMoveEvent );
});
[/source]



## Attach Through the System Manager Object


The system manager parents all displayable elements within the Flex application.  It is an elegant way of accessing the stage through custom flex components which have not yet been added to it.

[source:jscript]
systemManager.stage.addEventListener( MouseEvent.MOUSE_MOVE, myMouseMoveEvent );
[/source]
