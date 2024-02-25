---
author: emwilson
comments: true
date: 2008-02-09 02:15:36+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/02/08/breaking-javas-rules-instantiating-an-interface/
slug: breaking-javas-rules-instantiating-an-interface
title: 'Breaking Java''s Rules: Instantiating an Interface'
wordpress_id: 9
categories:
- Java
tags:
- interfaces
- Java
- polymorphism
- programming
---

Here's a geeky party trick:

Abstract classes in Java cannot be instantiated.  Here we're going to consider ways in which a button can be programmed within a JPanel.  ActionListener is used to react to the button event, but ActionLister is an interface (pure abstract class)  so in order to use it you have to derive a class from it and implement actionPerformed.

For example, you can't do this:

[source:java]
ActionListener buttonListener = new ActionListener();
[/source]

You have to do this:

[source:java]
public class ButtonListener
{
public void actionPerformed( ActionEvent event )
{
}
}

ButtonListener buttonListener = new ButtonListener();
ourJButton.addActionListener( buttonListener );
[/source]

However, this code _is_ valid:

[source:java]
ourJButon.addActionListener( new ActionListener() {
public void actionPerformed( ActionEvent event )
{
}
} );
[/source]

What's going on?  It would _appear_ that we are instantiating a new ActionListener and giving it an actionPerformed() method.  We've succeeded in giving our button a listener without first creating a class to handle the event.


## Anonymous Inner Classes


Of course we aren't _really_ instantiating ActionListener - it was a trick.  What this does is create an **anonymous inner class** only in this part of our code.  Check it out - when you compile your code, javac will create extra class files for our ActionListener.

For one-off buttons, using anonymous inner classes is an excellent way of reducing code bloat and improving the readability of your programs.  There are drawbacks of course, which I'll go into detail in another article, another day.
