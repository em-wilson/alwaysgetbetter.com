---
author: emwilson
comments: true
date: 2009-01-02 11:00:32+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/01/02/python-file-upload/
slug: python-file-upload
title: Python file upload
wordpress_id: 146
categories:
- General Programming
tags:
- apache
- cgi
- HTML
- python
---

I recently needed to handle file uploads from a Flash form post using CGI and Python.  I made two discoveries:



	
  1. Python's CGI library ignores query string variables on POST requests.

	
  2. After you've done it once, working with POST variables whether uploaded files or otherwise is dead simple!



Here is the file I came up with:

[source:python]
#!/usr/bin/python
import cgi, sys, os

UPLOAD_DIR = "/home/user/uploads"

postVars = cgi.FieldStorage()

if postVars.has_key("myFile"):
    fileitem = postVars["myFile"]

    # If myFile doesn't contain a FILE, exit
    if not fileitem.file:
        return

    # Strip file extension
    (name,ext) = os.path.splitext( fileitem.filename )
        
    # If a binary file, ensure write flags are binary
    if ext == ".jpg" or ext == ".png" or ext == ".gif":
        ioFlag = "wb"
    else:
        ioFlag = "w"
        
    # Save file data to disk stream        
    fileObj = file(os.path.join(self.path, fileitem.filename),ioFlag)
    while 1:
        chunk = fileitem.file.read(100000)
        if not chunk: break
        fileObj.write(chunk)
    fileObj.close()
[/source]

Bonus points for [checking for and creating a new directory](/blog/2009/01/01/create-directory-in-python/) to store the uploaded file in, if needed.
