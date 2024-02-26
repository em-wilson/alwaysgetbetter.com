---
author: emwilson
comments: true
date: 2010-02-15 21:54:24+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/02/15/css-sanity-remember-practices-tools/
slug: css-sanity-remember-practices-tools
title: 'CSS Sanity: Remember Best Practices When Using New Tools'
wordpress_id: 299
categories:
- Web Programming
tags:
- best practices
- CSS
- design
- HTML
---

Now that the rebellion against IE6 has hit mainstream, a brave new world of CSS3 and HTML5 has been opened to web professionals. 

**Beware Mixing Purposes**
CSS is intended to define the appearance of elements, not their behaviour. Be very careful about "overloading" CSS to accomplish tasks best performed by JavaScript.

A popular example of poor CSS usage is drop-down menu lists. Some web programmers use CSS' :hover selector to instruct the web browser to display sub-navigation when the user hovers over a list item.
```css
ul#menu li:hover ul { display: block; }
```

This is much better accomplished using a touch of jQuery:
```javascript
jQuery("ul#menu ul").css({display: "none"}); // For Opera
	jQuery("ul#menu li").hover(function(){
		jQuery(this).find('ul:first').css({visibility: "visible", display: "none"}).show(268);
		},function(){
		jQuery(this).find('ul:first').css({visibility: "hidden"});
		});
```
Not only is this example less dependent on consistent web browser support for the CSS :hover selector, we've even thrown in a spiffy little roll-out animation.

**Keep It Simple**
The [nth-child selector is one that stands to be abused](http://css-tricks.com/how-nth-child-works/) by overzealous developers. Imagine this: change the display of every nth element as defined through an algebraic expression.

Why is this a bad thing? CSS is run in the same memory space as the general web page - that's why it's so fast. JavaScript tends to be isolated; meaning if you make an infinite loop in JavaScript, the web browser will eventually stop it from running. If the same thing happens in CSS, your web session is probably toast.

**Separate Logic from Presentation**
CSS was a leap forward because it separated presentation from structure; rather than programming font and colour elements, designers were able to explicitly control the way their web page appeared on screen. HTML was being used to serve the purpose CSS was designed to cover.

More recently, CSS has become a crutch to enable functionality better suited for JavaScript. When unsure about which to use, ask yourself: Does this solution affect only the display, or is some action happening?
