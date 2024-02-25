---
author: emwilson
comments: true
date: 2008-02-11 13:50:07+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/02/11/maintaining-sql-connections-in-aspnet/
slug: maintaining-sql-connections-in-aspnet
title: Maintaining SQL Connections in ASP.NET
wordpress_id: 7
categories:
- ASP Classic
- PHP
tags:
- ASP.NET
- SQL
---

There is a lot of conflicting advice with regard to the proper way of using SQL connections in web work.  The two main schools of thought are:




 







  1. Open a      connection once, use it for everything and free it when the page request      is complete.


  2. Open a      connection only when it is needed, then fre it right away.




 





## It All Comes Down to Speed




Any operation performed by the server has a cost associated with it.  Loops and counts happen on the CPU and are incredibly fast.  Caches are stores in system memory and seem fast but are in fact thousands of times slower than direct CPU operation.




 




As far as internal server access goes, reading and writing to the hard drive is representative of the most expensive operation performed by applications.  If possible, memory is oftent he best storage medium for program instructions.




 





## Consider Database Access Costs




Databases live in a realm somewhere between hard drives and memory.  A good DBMS reacts to requests by storing execution plans and data in system memory thereby reducing the amount of time applications have to wait for information to load off the hard drive.




 




In terms of execution costs, connecting to the database is up there with reading from the hard drive.  Is a database connetion isn’t needed, don’t create one.




 





## Which is better?




Considering the high price of connecting to a database, which of the following makes the most sense for our web application?




 







  1. Creating      the connection to the database, performing all operations, and closing the      connection after the is complete, org;


  2. For      every request, creating a connection, executing a query, then closing the      connection.  Repeat every time a new      piece of information is required.




 




In classic ASP’s ADO, #1 was the best option.  In modern ASP.NET’s ADO.NET, #2 is the way to go.  That was a trick question.




 





## Classic ASP – ADO




Using classic ASP, the developer has a great deal of control over the sequence in which their code will run.




 




It’s very easy to open a single connection at the top of a web page and use it for all of that page’s database operations.  At the end of the page, the developer writes code logic to free all record sets used, close memory objects, and release the connection.




 





## ADO.NET




When writing code for ASP.NET, we strive to be “good” .NET citizens.  We are given a lot of power to write application logic never before possible on the web, and we are asked to relinquish some of our finer-grained control over resources.




 




Generally, managed resources benefits the programmer by freeing them to focus more on the business requirements of their project rather than their specific environment’s implementation.  Database connectivity, however, is a concept that has to be updated to fit the .NET paradigm.




 




Whereas before we controlled when database resources were released to the system, now the garbage collector has taken responsibility for the task.  If we open a database connection when the page request begins, we have no real guarantee it will be released in a timely manner when the page request completes.




 




This means the only way to prevent the server from running out of connections is by creating, using, and immediately freeing them every time we want to retrieve information from the database.




 




What about the cost of execution?  One would assume we’ve taken two steps back but ASP.NET introduces connection pooling for this situation.




 





## Connection Pooling is your friend




Any time a connection is requested, the server looks to a special pool of memory objects where it keeps previously used database connections.  If there are any connections no longer being used, the server will return those unused connections before creating a new one.




 




Essentially the server only incurs the cost of creating a connection the first time it is used, or whenever demand for a particular application spikes.




 




When our program is done with the database, it releases the connection back into the pool for other requests.




 





## Never Create Global Connection Objects




The morale of the story is: work with the .NET framework and let it do its job.  As long as we are willing to let go of old ways of thinking and adapt to the new technologies, our programming lives will become ever simpler.




 




Every day web applications grow in their complexity.  Fortunately the power and capabilities of the tools we have available also continue to improve.




 




Expect databases to continue driving bigger and better web presences.  The more skilled we become at working with them the more likely we are to succeed in creating stable, reliable and scalable applications to take advantage of them.
