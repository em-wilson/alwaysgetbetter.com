---
author: emwilson
comments: true
date: 2024-03-21 12:49:00+00:00
layout: post
link: https://www.alwaysgetbetter.com/2024-03-21-memory-management-topdown/
slug: memory-management-topdown
title: Memory Management Demanded from the Very Top
excerpt: Is your favourite programming language a security risk? The White House thinks so.
categories:
- General Programming
photo:
  source: /images/2024/03/rusty-crab
  url: /images/2024/03/rusty-crab.jpg
  description: Rawr
published: true
---

When I was a kid I learned how to program so I could write CPU-bound clones of my favourite games. My
CPU wasn't the greatest, but it was certainly better than the paltry RAM I had available, and those early
days of 3D games and "photorealistic" 2D adventures relied on pre-rendered assets to get around the absence
of any kind of standard API that would reliably work across consumer machines.

I learned two things. First, I have no talent for graphics. Second, commercial software is created with
development speed in mind, not runtime speed. As a result it was trivial to create apps that ran well on
my machine. When your software is lovingly crafted and every detail obsessed over, you can write a program
that effectively manages memory. The CD-ROMS I would buy to put in my computer, on the other hand, would
end up making me choose between rebooting and grinding to a halt over time.

A memory leak on a consumer machine running games is irritating, but a similar memory leak on a banking
mainframe or a communication protocol or a traffic control system is expensive and potentially fatal.
Delivering bug-free software on schedule is hard (impossible?) work, and years of mistakes have left
us with a digital infrastructure so riddled with potoles in the form of security flaws and memory safety
issues that doesn't just border on national security concerns - it crosses firmly into the danger zone;
so much so even [the White House](https://stackoverflow.blog/2024/03/04/in-rust-we-trust-white-house-office-urges-memory-safety/)
has taken notice.

**What are the implications of the White House's declaration that memory issues caused by languages like C++ are a national security concern?**

The face of the government is (hopefully) taking it serious that there may be vulnerabilities in the code
written in these languages. If exploited by malicious actors they can lead to data breaches abd other
security threats. Since culture runs from the top down, setting the expectation from the very top of
government that language choice and security is a priority can lead to reforms in development processes
across government.

When government and regulatory bodies, and those interfacing with them, have standards in place, it sets
a level playing field for everyone else. It's much more powerful than a bunch of programmers trying to
tell everyone the same thing.

**Why should the average programmer care about this situation?**

If you're not working for the government this is still important because it highlights the importance
of secure coding practices regardless of the language used. Memory issues lead to vulnerabilities that
can be exploited by hackers, which can result in data breaches and other security threats, sometimes
[years after the bug was introduced](https://heartbleed.com/). By following best practices for memory
allocation and using secure coding techniques, programmers can help protect their systems and the data
they contain from these types of attacks.

Even if you're working in one of these so called "insecure" languages and not going to change it up
any time soon, it's a good reminder to review your programming practices and put measures in place
to update your security regime.

**What makes languages like Rust, Python and C# more desirable from a security standpoint?**

Languages like Rust, Python, and C# are often considered more desirable from a security standpoint
because they have built-in memory management systems (I know Rust doesn't have garbage collection but
let's be generous and count its memory ownership system) that help prevent common issues like buffer
overflows and null pointer dereferences. This makes it easier for programmers to write secure code
without having to manually manage memory.

Second, these languages tend to have stronger emphasis on security best practices such as input
validation and error handling which can help prevent common coding errors that lead to security
issues in the first place.

Finally, these languages have large and active communities of developers who are constantly working
to improve the security of their language and its associated libraries and frameworks. This makes it
easier for programmers to stay up-to-date on the latest security patches and best practices.

**No one is suggesting C or C++ should be dropped in favour of "better" languages.**

Obviously using languages like C and C++ doesn't mean your code is automatically insecure. These
languages have a long history and are widely used *everywhere*, and most of the time they're not
destroying the nation.

 In order to improve program security, developers need to constantly update their knowledge and
 techniques. (*Always Get Better*!) Stay up to date with the latest threats and vulnerabilities,
 use secure coding practices such as input validation and sanitization, and incorporate security
 testing and analysis into the development process. By continuously improving their technique
and staying vigilant about security, we can create more secure software programs that are less
susceptible to attacks and breaches.