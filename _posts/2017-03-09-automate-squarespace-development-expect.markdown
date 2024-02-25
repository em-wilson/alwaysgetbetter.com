---
author: emwilson
comments: true
date: 2017-03-09 22:39:10+00:00
layout: post
link: https://www.alwaysgetbetter.com/2017/03/09/automate-squarespace-development-expect/
slug: automate-squarespace-development-expect
title: Automate Squarespace Development with Expect
wordpress_id: 689
categories:
- General Programming
tags:
- automation
- squarespace
---

We have a love/hate relationship with Squarespace's developer platform. I think they really need to decide whether they're a design company or a web hosting platform and stop doing 50% of each. But anyway...

Sometime in the last year they added a local development server that really helps speed up the process of building things for their platform. It doesn't fully replace the "make change and deploy live" workflow, but it is super useful for testing basic layout updates for side effects.

Since I use a password generator, logging into my Squarespace account to do any development work involves cracking open 1Password and copy/pasting into the terminal. After a few weeks of that I decides there had to be a better way so I created this shell script to take care of the setup.

`
#!/usr/bin/expect
spawn squarespace-server squarespacewebsiteaddress --auth
expect "Squarespace email:"
send "squarespace username\r"
expect "Squarespace password:"
send "password\r"
interact
`

Hope this helps out someone who is as lazy as I am.
