---
author: emwilson
comments: true
date: 2009-03-04 00:36:03+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/03/03/flex-3-downloading-url-clicking-button/
slug: flex-3-downloading-url-clicking-button
title: 'Flex 3: Downloading URL after Clicking on Button'
wordpress_id: 143
categories:
- General Programming
---

Suppose you have a button in your Flex program. When the user clicks on your button, they are prompted to choose where they would like to download a file. But... the file never comes:

[source:javascript]
protected function buttonClick():void
{
    var mpRequest:URLRequest = new URLRequest("soundbyte.mp3");
    var localRef:FileReference = new FileReference();  

    try
    {
        // Prompt and downlod file
        localRef.download(mpRequest);
    }
    catch (error:Error)
    {
        trace("Unable to download file.");
    }
} 
[/source]

The reason the file never downloads is because as soon as the function completes, it goes out of scope. As far as I know, this has to do with the event model using weak referencing by default when triggered by clicking on the button.

The simplest solution is to simply move the variables outside the button:

[source:javascript]
protected var mpRequest:URLRequest;
protected var localRef:FileReference;

protected function buttonClick():void
{
    mpRequest = new URLRequest("soundbyte.mp3");
    localRef = new FileReference();  

    try
    {
        // Prompt and downlod file
        localRef.download(mpRequest);
    }
    catch (error:Error)
    {
        trace("Unable to download file.");
    }
} 
[/source]
