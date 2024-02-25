---
author: emwilson
comments: true
date: 2013-09-29 18:14:38+00:00
layout: post
link: https://www.alwaysgetbetter.com/2013/09/29/comparing-strtolower-strcasecmp-caseinsensitive-string-checking-php/
slug: comparing-strtolower-strcasecmp-caseinsensitive-string-checking-php
title: Case-insensitive string comparison in PHP
wordpress_id: 546
categories:
- PHP
tags:
- bencharmking
- performance
- PHP
- planning
- testing
---

This is a common situation: I needed to compare two strings with unknown capitalization - "country" versus "Country". Since these words should be considered equal even though the second "Country" has a capital "C", we can't do a straight comparison on the two - we need to do a case-insensitive comparison.

**Option 1: strcasecmp**
Whenever possible developers should try to use built-in functions which are compiled code and (in general) run much faster than anything you could write. PHP has _strcasecmp_ to case-insentively compare two strings. Sounds like a perfect match!

`
if ( strcasecmp('country','Country') != 0 ) {
  // We have a match!
}
`

**Option 2: strtolower**
Always read the documentation, but draw your own conclusions. One commentator in the PHP documentation suggested developers never use strcasecmp, and use strtolower with regular equality like this:
`
if ( strtolower('country') === strtolower('Country') ) {
  // We have a match
}
`

**Test the Speed**
Both methods accomplish the same thing, but do we really want to skip using strcasecmp()? Which is the better option?
I wrote this short script to run each option for 10 seconds and see which is faster:


And the results:

    
    
    strtolower: Done 18440869 cycles in 10 seconds
    strcasecmp: Done 22187773 cycles in 10 seconds
    



So _strcasecmp_ has the edge speed-wise, but not so huge that I would care to favour one over the other.

Apparantly _strcasecmp_ does not support multi-byte (e.g. Unicode) characters, but I haven't tested this. Presumably that would give _strtolower_ an advantage over projects dealing with non-English input, however that is not the case at all in my particular use case so I did not explore this avenue any further. I also didn't try against non-ascii characters, such as latin accents; including those would be an improvement on this test.
