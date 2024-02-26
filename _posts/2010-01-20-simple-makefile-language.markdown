---
author: emwilson
comments: true
date: 2010-01-20 02:14:01+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/01/19/simple-makefile-language/
slug: simple-makefile-language
title: A Simple Makefile for the Go Language
wordpress_id: 290
categories:
- Go Language
tags:
- efficiency
- General Programming
- Go Language
- Google
---

Hey folks, it's been awhile!

I've been playing with Google's _Go_ language, and will be sharing what I've learned over the coming weeks.

First off, which seems like the easier way to compile your source code? This:
```
6g fib.go
6l fib.6
mv 6.out fib
```

or this?

```
make
```

Personally, I prefer using a Makefile, even for a small project with one source file.

Without further adieu, a simple Makefile for the Go Language:

```
GC      = 6g
LD      = 6l
TARG    = fib
O_FILES = fib.6

all:
        make clean
        make $(TARG)

$(TARG): $(O_FILES)
        $(LD) -o $@ $(O_FILES)
        @echo "Done. Executable is: $@"

$(O_FILES): %.6: %.go
        $(GC) -c $<

clean:
        rm -rf *.[$(OS)o] *.a [$(OS)].out _obj $(TARG) *.6
```
