---
author: emwilson
comments: true
date: 2011-08-06 18:57:43+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/08/06/upgrade-firefox-ubuntu/
slug: upgrade-firefox-ubuntu
title: How to Upgrade Firefox using Ubuntu
wordpress_id: 441
categories:
- internet
- Ubuntu
- Web Browsers
tags:
- firefox
- Ubuntu
photo:
  source: http://www.flickr.com/photos/48600078653@N01/164408290/
  url: http://farm1.static.flickr.com/51/164408290_c1404c94fe_m.jpg
  description: Mochila Firefox
  cc_author: jmerelo
  cc_link: http://www.flickr.com/photos/48600078653@N01/164408290/
---

So I got tired of using Firefox 3.6 in my Ubuntu machine and decided to upgrade to the newest version (5.0). It's understandable that the package maintainers responsible for Ubuntu don't put <del>bleeding-edge</del> cutting-edge releases in the distribution due to the possibility of introducing unstable elements into the user experience. But Firefox 4 has been out for over a year, and the migration to 5 is well underway.

Fortunately, it couldn't be much easier to get the newest official release using our good friend **aptitude**.

In a terminal window, add the Mozilla team's stable Firefox repository by issuing the following command:

```
sudo add-apt-repository ppa:mozillateam/firefox-stable
```

Next, perform an update to get the package listing, and upgrade to install the newest browser:

```
sudo apt-get update
sudo apt-get upgrade
```

That's it - you're done! Your shortcuts are even updated, and any bookmarks or open tabs you might have had on the go are carried forward.

I was pleasantly surprised at how easy this process was.
