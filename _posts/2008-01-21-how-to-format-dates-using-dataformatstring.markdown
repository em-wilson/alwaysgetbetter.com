---
author: emwilson
comments: true
date: 2008-01-21 04:34:23+00:00
layout: post
link: https://www.alwaysgetbetter.com/2008/01/20/how-to-format-dates-using-dataformatstring/
slug: how-to-format-dates-using-dataformatstring
title: How To Format Dates Using DataFormatString
wordpress_id: 5
categories:
- ASP.NET
tags:
- ASP.NET
- DataGridView
- DetailsView
- GridView
---

During a recent project I was setting up a DetailsView control for a record's tombstone information [note: this logic is the same in DataGridView, GridView, and anywhere else that DataFormatString is used].

By default, my dates were coming in this format:

    
    
    04/18/2008 12:00:00 PM


I wanted to display dates without any time information and without leading zeroes on the month like this:

    
    
    4/18/2008


So I create my DetailsView like this:



<asp:DetailsView runat="server" ID="dvPatientInformation" DataSourceID="sqlPatientInformation">
    <Fields>
        <asp:BoundField HeaderText="Title:" DataField="strTitle" />
        <asp:BoundField HeaderText="First Name:" DataField="strFirstName" />
        <asp:BoundField HeaderText="Last Name:" DataField="strLastName" />
        <asp:BoundField HeaderText="Date of Birth:" DataField="dtBirthdate" HtmlEncode="false" DataFormatString="{0:M/dd/yyyy}" NullDisplayText="Not Yet Entered" />
    </Fields>
</asp:DetailsView>

There are a few key issues to consider here:


## Data Format String


Using one capital M for the month causes ASP.NET to display the month as a minimum digit - so for months like April the leading 0 will be removed - displaying "4" rather than "04". 

More information about data string format options can be found at the MSDN reference: [http://msdn2.microsoft.com/en-us/library/system.globalization.datetimeformatinfo(vs.71).aspx](http://msdn2.microsoft.com/en-us/library/system.globalization.datetimeformatinfo(vs.71).aspx)


## Null Display Text


The business rules for this organization allows for the patient's birth date to be NULL in the system.  Rather than display a blank field when NULL, I chose to indicate textually that the patient's birth date was not yet on file.


## HTMLEncode


This is the critical part - if I did not set HTMLEncode to **false**, my date would not have formatted at all.

Why?  Because when rendering content, ASP.NET automatically takes data bound content and converts it into an HTML Encoded string in order to prevent pages from serving unintended - potentially malevolent - code along with expected text strings.

The problem - once a string has been encoded, the server no longer considers it a number or date/time information type, so applying the date format is useless.

The only way to get around this is by turning off HTML Encoding for fields whose contents must be formatted.

Yes, this does seem like quite an odd design issue - one would expect the HTML Encoding to come into play _after_ all of the formatting had been completed.  That must be why .NET programmers get paid the big money - having to know tidbits like this eats away at your soul.
