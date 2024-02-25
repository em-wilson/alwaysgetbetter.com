---
author: emwilson
comments: true
date: 2008-04-05 20:28:53+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/04/05/c-instantiating-a-class-of-unknown-type/
slug: c-instantiating-a-class-of-unknown-type
title: 'C#: Instantiating a class of Unknown Type'
wordpress_id: 18
categories:
- C#
tags:
- C#
- polymorphism
- programming
---

The project I am currently working on has several dozen different types of **Form** classes, each of which is accessible from a common menu strip.  Rather than repeatedly instantiating each of the forms from the menu item handlers, I wanted to funnel the request to a single function.

The problem is: _How do you instantiate a form when the type is unknown?_

The code snippet is:

[source:c#]
CreateForm( typeof(CustomFormType) );

Form CreateForm( Type formType )
{
return (Form)Activator.CreateInstance(formType);
}
[/source]
