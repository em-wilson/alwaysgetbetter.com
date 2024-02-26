---
author: emwilson
comments: true
date: 2008-05-27 14:19:08+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/05/27/replacing-adding-line-breaks-in-gridview-text/
slug: replacing-adding-line-breaks-in-gridview-text
title: Replacing / Adding Line Breaks in GridView Text
wordpress_id: 34
categories:
- ASP.NET
- C#
tags:
- ASP.NET
- C#
- HTML
---

The GridView is a powerful control for quickly and easily displaying tables of data.  However, a raw dump of information is not always good - when displayed by a web browser, normal line breaks are simply rendered as spaces.

For long blocks of text, it may be desirable to have your GridView insert HTML line breaks into your data.  This can be accomplished either programatically or declaratively.


## Programatically


As a programmer, my first instinct is to try to solve the problem using code behind.  I add a RowDataBound event handler to my GridView and create the command this way:

```csharp
protected void gvMessageList_RowDataBound(object sender, GridViewRowEventArgs e)
{
  GridViewRow row = e.Row;
  if (e.Row.RowType == DataControlRowType.DataRow)
  {
    row.Cells[2].Text = row.Cells[2].Text.Replace("\n", "<br />");
  }
}
```

Although it works, it has several drawbacks:



	
  * This solution uses a _magic number_ to cause the compiler to replace the third column in the row.  If the structure of the GridView were to change, this function may break

	
  * This solution requires the developer to be aware of the final layout of the GridView and to make the connection between the control's declaration and its logical code.




## Use The Design


By far, the better solution is to simply declare the formatting changes in the same place as the GridView.  Using a Template field, I can add line breaks to my message by adding this:

```csharp
<%# Eval("Message").ToString().Replace("\n", "<br />") %>
```


## More Information


This solution assumes the contents of "Message" are not null.  For more information about this technique (including how to deal with null values), I recommend the ASP.NET message boards: http://forums.asp.net/p/1027728/1403884.aspx
