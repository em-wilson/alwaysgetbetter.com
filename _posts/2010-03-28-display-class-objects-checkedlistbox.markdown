---
author: emwilson
comments: true
date: 2010-03-28 17:10:43+00:00
layout: post
link: https://www.alwaysgetbetter.com/2010/03/28/display-class-objects-checkedlistbox/
slug: display-class-objects-checkedlistbox
title: Display Class Objects in CheckedListBox
wordpress_id: 303
categories:
- C#
- SiteAssistant
tags:
- C#
- data types
- interfaces
- programming
---

If you want to use anything more complex than a list of strings in a ListBox, you're in luck because the control accepts all types of objects.

[caption id="attachment_304" align="aligncenter" width="644" caption="Custom Objects (Blog Posts) Displayed in a CheckListBox"][![Custom Objects (Blog Posts) Displayed in a CheckListBox](/images/2010/03/CheckedListBox.png)](/images/2010/03/CheckedListBox.png)[/caption]

In this case, I want to display a list of posts found in a blog. **Blog** is a class which contains _Posts_, an array of **Post** classes. To start, I created the CheckedListBox in the form designer, and I add the posts to it like this:

[sourcecode language="csharp" light="true"]
clbPages.Items.AddRange(_blog.Posts);
[/sourcecode]

If I do nothing else, the ListBox will call the Post's ToString() method and will display as:

    
    
    Post
    Post
    Post
    Post
    



We have two options for displaying this correctly:

1. Override the **ToString()** method. I don't recommend doing this because ToString() is much more appropriately used in a debugging context.

2. Add a string converter: This will automatically convert each post object to a usable string when called by an object like a ListBox. ListBox uses Convert.ToString() - this uses that converter more appropriately. ToString() should only be used as a fallback.

[sourcecode language="csharp" wraplines="false"]
<pre>
// Use System for the Type object
using System;
// Use ComponentModel for the TypeConvert base class
using System.ComponentModel;

namespace SiteAssistant.Blog
{
    /// <summary>
    /// Converts a post into a list-friendly string, for checkbox lists
    /// </summary>
    class PostConverter : TypeConverter
    {
        /// <summary>
        /// Indicates whether the Post can be converted to a destination type
        /// </summary>
        /// <remarks>
        /// We only support conversions to STRING at present
        /// </remarks>
        /// <param name="context"></param>
        /// <param name="destinationType"></param>
        /// <returns></returns>
        public override bool CanConvertTo(ITypeDescriptorContext context,
            Type destinationType)
        {
            if (destinationType == typeof(string))
                return true;
            else
                return base.CanConvertTo(context, destinationType);
        }

        /// <summary>
        /// Converts the post to the destination type. If the destination
        /// type is not supported, the base Conversion is applied.
        /// </summary>
        /// <remarks>
        /// We only support converting posts to strings at present.
        /// </remarks>
        /// <param name="context"></param>
        /// <param name="culture"></param>
        /// <param name="value"></param>
        /// <param name="destinationType"></param>
        /// <returns></returns>
        public override object ConvertTo(ITypeDescriptorContext context,
            System.Globalization.CultureInfo culture, object value,
            Type destinationType)
        {
            if (destinationType == typeof(string))
            {
                string text = "";
                Post p = value as Post;
                // Ensure that the Post is not null, avoid errors
                if (null != p)
                {
                    text = p.Title;
                }
                return text;
            }
            else
            {
                return base.ConvertTo(context, culture, value, destinationType);
            }
        }
    }
}
</pre>
[/sourcecode]

The code we write in .NET is like the meat inside a sandwich. The framework is the bread that wraps around our logic and keeps our application together. Our new Posts string converter will be called by the application without us needing to override the **Convert** function.

It doesn't happen by magic of course. The final change we have to make is to add information about our conversion function to the posts class:

[sourcecode language="csharp" wraplines="false"]
<pre>
    [System.ComponentModel.TypeConverter(typeof(PostConverter))]
    public class Post
    {
        // Rest of the code goes here
    }
</pre>
[/sourcecode]

That's all there is to it! Now we can pass a list of Posts to the CheckedListBox and manipulate each item directly. In this application, I will be using this technique to provide the Post object to the text editor with a double-click.
