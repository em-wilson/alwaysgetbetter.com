---
author: emwilson
comments: true
date: 2009-02-07 23:31:09+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/02/07/atoi-in-flexactionscript-converting-strings-to-numbers/
slug: atoi-in-flexactionscript-converting-strings-to-numbers
title: atoi in Flex/ActionScript - Converting Strings to Numbers
wordpress_id: 177
categories:
- Web Programming
tags:
- ActionScript
- Flash
- Flex
- flex builder
---

It couldn't be easier to convert a string to a number in Flex Actionscript. The language doesn't have (or need) an atoi() function as seen in languages like C - it's as simple as creating a cast:

[source:JavaScript]
var myString:String = "15";
var myNum:Number = Number( myString );
[/source]

The framework will convert any data type, not just strings. If it is unable to do so, it will return 0 (for ints) or NaN (for floating point).
