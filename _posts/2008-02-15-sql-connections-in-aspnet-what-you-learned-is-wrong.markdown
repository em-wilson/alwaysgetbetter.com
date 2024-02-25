---
author: emwilson
comments: true
date: 2008-02-15 19:22:49+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/02/15/sql-connections-in-aspnet-what-you-learned-is-wrong/
slug: sql-connections-in-aspnet-what-you-learned-is-wrong
title: SQL Connections in ASP.NET - What you learned is WRONG!
wordpress_id: 11
categories:
- ASP.NET
- Blogging
- SQL
tags:
- ASP.NET
- interfaces
- SQL
---

When we learn how to open and use a database connection with ASP.NET, as with any other programming concept in any other programming language, the simplified version used to explain what’s going on is not truly representative of the quality professional code we will one day be expected to write.
Opening and Closing Connections

Case in point: managing of sql database connection resources. How many of us learned to write something like this:

`
// Create a new SQL Connection object
SqlConnection conn = new SqlConnection( connectionString );

// Open the connection to the database
conn.Open();

// Create a new SQL Command
SqlCommand cmd = new SqlCommand( “DELETE FROM BabyNames;”, conn );

// Execute the command
cmd.ExecuteNonQuery();

// Close the database connection
conn.Close();
`

Sure it’s easy to follow, but if you deploy that on a moderately busy server you are going to make your client very unhappy.



## Dispose Resources



SQLConnection and SQLCommand objects reference unmanaged resources, meaning the C# garbage collector has no framework knowledge about your object. Since these classes both implement the disposable interface it is important to call the Dispose() method in order to correctly free your application’s used memory.

So our code gets updated to look like this:

`
// Create a new SQL Connection object
SqlConnection conn = new SqlConnection( connectionString );

// Open the connection to the database
conn.Open();

// Create a new SQL Command
SqlCommand cmd = new SqlCommand( “DELETE FROM BabyNames;”, conn );

// Execute the command
cmd.ExecuteNonQuery();

// Dispose of the command
cmd.Dispose();

// Close the database connection
conn.Close();

// Dispose of the connection object
conn.Dispose();
`



## Trap for Errors



What happens if there’s a problem, and your code fails to complete? If your application crashes before your objects are disposed, you are left with the same effect as if you had never disposed your objects at all!

Fortunately, C# includes the try … finally reserved words. If anything within the try { } block fails, the finally { } still executes. We can easily apply this to our program code:

`
// Create a new SQL Connection object
SqlConnection conn = new SqlConnection( connectionString );

try
{
// Open the connection to the database
conn.Open();

// Create a new SQL Command
SqlCommand cmd = new SqlCommand( “DELETE FROM BabyNames;”, conn );

try
{
// Execute the command
cmd.ExecuteNonQuery();
}
finally
{
// Dispose of the command
cmd.Dispose();
}

// Close the database connection
conn.Close();
}
finally
{
// Dispose of the connection object
conn.Dispose();
}
`

For my own part, I prefer the using keyword. We can include a using call anywhere we would ordinarily use a disposal object. When the code is compiled, it behaves the same as try … catch, but leaves our program code much more readable.

Even better, we don’t even have to bother calling Dispose() because it does it for us!

`
// Create a new SQL Connection object
using ( SqlConnection conn = new SqlConnection( connectionString ) )
{
// Open the connection to the database
conn.Open();

// Create a new SQL Command
using ( SqlCommand cmd = new SqlCommand( “DELETE FROM BabyNames;”, conn ) )
{
// Execute the command
cmd.ExecuteNonQuery();
}

// Close the database connection
conn.Close();
}
`

Slick.


## Open Late, Close Early (like a bank)



There is one more thing I would add to this. Creating objects in memory takes time. Although it happens in fractions of a second too fast to be detectable by us, it’s important not to waste processing time wherever possible.

Whenever we Open() a database connection, we expect to use the database right away. If we then create an SqlCommand, we’re wasting the open connection’s time. It’s as if we pick up the phone and listen to the dial tone while we then flip through the white pages looking for the number we want to call.

Let’s change our example code so we will now Open() at the last possible opportunity, and Close() right away when we’ve made our call:

`
// Create a new SQL Connection object
using ( SqlConnection conn = new SqlConnection( connectionString ) )
{
// Create a new SQL Command
using ( SqlCommand cmd = new SqlCommand( “DELETE FROM BabyNames;”, conn ) )
{
// Open the connection to the database
conn.Open();

// Execute the command
cmd.ExecuteNonQuery();

// Close the connection to the database
conn.Close();
}
}
`

In the end, the program code we wrote is very similar to the newbie code we started with. However, we’re now protecting our system from memory leaks, and we’re protecting our database from wasted clock cycles. Our code is easy to read and stable.
