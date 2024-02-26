---
author: emwilson
comments: true
date: 2008-02-14 14:40:04+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/02/14/how-to-create-full-text-search-using-mysql/
slug: how-to-create-full-text-search-using-mysql
title: How to Create Full Text Search Using mySQL
wordpress_id: 10
categories:
- SQL
tags:
- e-commerce
- Google
- search
- SQL
---

Search is one of the most basic features visitors expect when they come to a web site.  This is especially true in e-commerce where your ability to make a sale is directly related to your customer’s ability to find the product they’re looking for.


## Using Third-Party Solutions


Many first-time site owner choose to go with Google Custom Search because of its easy setup and because of Google’s incredible indexing reach and results.  I don’t like the standard edition of the Custom Search because of the branding – your search results advertise Google and provide links to competing content.  For an e-commerce site to lose control of such a critical function, the results can be costly.


## Don’t Give Up Control


Especially in e-commerce, it is best to never give up control over any content.  Advanced users may choose to ignore your site’s search tool and use Google to get at your content anyway (via the site: directive) but in the general case there is great potential to keep selling useful products to your potential customers even while they search your site for other items.

Incorporating a decent search tool into a web site using PHP is dead simple.  All it involves is a database table with 3 or more rows and a little bit of an eye for layout.  Even if you don’t consider yourself a designer, having a look around other search engines will give you a feel for how results should display.


## Creating the Search Tables


Let’s get started.  Our simplistic database table (PRODUCTS) will consist of the following columns:

Column Name     | Data Type     | Description
----------------|---------------|-----------------------------
intID           | int           | Product ID and Primary Key
vcrName         | varchar(25)   | Product Name
txtDescription  | text          | Product Description
vcrPhoto        | varchar(40)   | Path (URL) to product photo

Obviously this is just a simplified example, but the product ID, name, description and photo should be enough for the purposes of our demonstration.

The SQL to create the table looks like this:
```sql
CREATE TABLE PRODUCTS
(
  intID int auto_increment,
  vcrName varchar(25),
  txtDescription text,
  vcrPhoto varchar(40),
  CONSTRAINT PRODUCTS_pk PRIMARY KEY ( intID )
);
```


## Add the Full-Text Index


In this example, we’re interested in searching the name and description fields of our products.  In order to add the full-text index to our table, we use the ALTER TABLE command:

```sql
ALTER TABLE PRODUCTS ADD FULLTEXT( vcrName, txtDescription );
```

Alternatively, we could have created the index along with the table in our original CREATE statement like this:

```sql
CREATE TABLE PRODUCTS
(
  intID int auto_increment,
  vcrName varchar(25),
  txtDescription text,
  vcrPhoto varchar(40),
  CONSTRAINT PRODUCTS_pk PRIMARY KEY ( intID ),
  FULLTEXT( vcrName, txtDescription )
);
```


## Searching For Text


Now that the index has been created, we can go ahead and search the database.  To activate full-text search, we use the MATCH () AGAINST () syntax like this:

```sql
SELECT intID, vcrName, txtDescription, vcrPhoto
FROM PRODUCTS
WHERE MATCH( vcrName, txtDescription ) AGAINST ( ‘search terms here’ );
```

That’s all there is to it!  Anyone with access to a mySQL database should be able to incorporate search into their sites without too much difficulty.  Of course this is a very basic introduction, but should be more the sufficient to get going with.
