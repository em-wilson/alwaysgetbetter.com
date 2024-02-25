---
author: emwilson
comments: true
date: 2011-04-26 11:20:59+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/04/26/protect-ssh-server-rsa-keys/
slug: protect-ssh-server-rsa-keys
title: Protect Your SSH Server with RSA Keys
wordpress_id: 418
categories:
- Hardware
---

If it's possible to log into your web server over SSH with a username and password, you may not be properly secured. Even if root access is impossible, a username and password combination can be broken with brute force; once your server has been compromised it's only a matter of time before a rootkit installation attempt is successful.

Even password-less RSA keys provide better protection than a password because they are long encrypted strings that cannot be guessed from a dictionary. Although a brute force attack against an RSA key is still possible, it requires a much more sophisticated attacker and takes many more attempts. As encryption technologies improve, the length of keys need to increase; but even a key with no password attached is way more secure than a human-typed password.
