---
author: emwilson
comments: true
date: 2012-05-20 04:08:16+00:00
layout: post
link: https://www.alwaysgetbetter.com/2012/05/20/datetime-play-framework/
slug: datetime-play-framework
title: Using DateTime in the Play! Framework
wordpress_id: 500
categories:
- Java
- Play Framework
tags:
- data types
- database
- Java
- play framework
- time
---

Which data type should you use for time information on your Java models? The _Date_ class is mostly deprecated, pushing us into the direction of the heavyweight _Calendar_ class. Storing the milliseconds since Epoch in a _Long_ is both database-friendly and easy to perform math on and convert at runtime. But if you really want a good experience, you are using _Joda Time_.

Joda Time is built into the [Play! Framework](/blog/2011/04/11/play-framework-saves-world/), and there really is no excuse to use anything else. But when it comes to saving dates in your JPA Models, there is a big "gotcha" in that there is no default type converter to move a DateTime object into and out of the database. Oops.

But that can be fixed with an annotation in your model, like this:

    @Type(type="org.joda.time.contrib.hibernate.PersistentDateTime")
    public DateTime created;


Unfortunately, Play does not ship with Hibernate support for the DateTime object. So to make this work you need to include the [Joda-Time-Hibernate](http://joda-time.sourceforge.net/contrib/hibernate/index.html) library in your dependencies.yml file:

```
require:
    - play
    - joda-time -> joda-time-hibernate 1.3
```

After updating the dependencies file, run the **play deps --sync** command to pull in the libraries from maven. Your models will now save their date and time into MySQL, and your programming experience will be smooth as silk - at least as far as time-based functionality is concerned.
