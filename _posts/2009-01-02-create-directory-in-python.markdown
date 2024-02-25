---
author: emwilson
comments: true
date: 2009-01-02 00:00:41+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/01/01/create-directory-in-python/
slug: create-directory-in-python
title: Create Directory in Python
wordpress_id: 147
categories:
- General Programming
tags:
- file system
- python
---

I needed to learn how to create directories using Python and found this [great resource](http://desk.stinkpot.org:8080/tricks/index.php/2006/07/create-a-directory-in-python/) (digression: check out this author's page headers - way cool!)

Although I didn't really add anything new to the code, I ended up creating the following commented function which I share here in case it helps more users to understand.

[source:python]
# If directory (path) doesn't exist, create it
def createPath(path):
    if not os.path.isdir(path):
        os.mkdir(path)

# Example of Windows file path
createPath( "C:\\Python\\myNewDirectory" )

# Example of Linux file path
createPath( "/home/username/myNewDirectory" )
[/source]
