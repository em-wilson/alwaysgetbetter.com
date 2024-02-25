---
author: emwilson
comments: true
date: 2009-11-05 13:22:16+00:00
layout: post
link: https://www.alwaysgetbetter.com/2009/11/05/vista-fix-sql-server-express-error-create-database-permission-denied-database-master/
slug: vista-fix-sql-server-express-error-create-database-permission-denied-database-master
title: 'VISTA: How to fix SQL Server Express Error - CREATE DATABASE permission denied
  in database ''master'''
wordpress_id: 265
categories:
- SQL
- Windows Vista
tags:
- efficiency
- General Programming
- SQL
- Windows Vista
---

If you're using SQL Server Management Studio Express under Windows Vista and see either of these errors:

`CREATE DATABASE permission denied in database 'master'`

or

`The database [Name] is not accessible. (Microsoft.SqlServer.Express.ObjectExplorer)`

Here's the fix:





  1. Close SQL Server Management Studio Express


  2. Open your start menu and locate that program.


  3. Right-click on the Management Studio and choose 'Run as Administrator'


  4. Fixed!



I swear the simplest solutions can be the hardest to find - hopefully this saves someone (or my forgetful self!) some aggravation.
