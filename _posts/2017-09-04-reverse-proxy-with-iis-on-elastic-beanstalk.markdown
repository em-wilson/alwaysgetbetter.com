---
author: emwilson
comments: true
date: 2017-09-04 04:15:58+00:00
layout: post
link: https://www.alwaysgetbetter.com/2017/09/04/reverse-proxy-with-iis-on-elastic-beanstalk/
slug: reverse-proxy-with-iis-on-elastic-beanstalk
title: Reverse Proxy with IIS and Elastic Beanstalk
wordpress_id: 693
categories:
- AWS
- DevOps
- Wordpress
tags:
- .net
- amazon
- aws
- devops
- elastic beanstalk
- IIS
- windows
---

Suppose your main website is a .NET/IIS stack running on AWS Elastic Beanstalk, and you decided to add a WordPress blog to the mix. Instead of having http://blog.yourdomain.com and http://www.yourdomain.com, you want to host the blog from a subdirectory at http://www.yourdomain.com/blog. There are a few ways you can go about this; this post will go through how to set up a reverse proxy to a dedicated blog server.

You have two options:

1. Host WordPress in IIS - Not fun. You need to configure IIS to run PHP, host a MySQL database, and manage your WordPress file directory and updates in an environment where user uploads and core files can get trashed at any minute when EB rolls out a new server. _It's possible to run an amazing HA WordPress install on Elastic Beanstalk (topic for another post) but in a subdirectory directly hosted in Windows/IIS?_ Not for the feint of heart)

2. Host WordPress on a Linux server somewhere else and reverse proxy - **Let's do this instead**.

Basically you set up WordPress and get it to look how you want, configure it to respond to your domain, then configure IIS to fetch your blog URLs and return them whenever a request comes through to the blog subdirectory on your main site. Every other directory is served from your .NET app as normal.

[![Reverse Proxy a WordPress blog with IIS](/images/2017/09/arch-300x238.jpg)](/images/2017/09/arch.jpg)

**Important:** The final step to this is the most important bit if you're running on Elastic Beanstalk. Make sure you follow it in order to actually enable IIS' proxy module, which does not come pre-installed on Elastic Beanstalk's AMI.


## Configure WordPress to Respond to Your Domain


Add this section to the bottom of wp-config.php, just before the require_once for wp-settings.php. This tells WordPress to ignore its default server settings and use your website as its base path.

    
    $hostname = 'www.yourdomain.com';
    $_SERVER['HTTP_HOST'] = $hostname;
    define('WP_HOME','https://www.yourdomain.com/blog');
    define('WP_SITEURL','https://www.yourdomain.com/blog');
    $current_url = 'https://' . $hostname . $_SERVER['REQUEST_URI'];
    




## Configure IIS Paths


The reverse proxy makes use of the **rewrite** in IIS. To turn this on for your blog, add the following to the directive in your web.config file:

    
     <rewrite>
       <rules>
         <rule name="Reverse Proxy to blog" stopProcessing="true">
           <match url="^blog/(.*)" />
           <action type="Rewrite" url="https://blog.yourdomain.com/{R:1}" />
         </rule>
       </rules>
     </rewrite>


This tells IIS to redirect all requests beginning with "blog/" to your WordPress blog. IIS will reach out to your blog server as if it is the requestor, fetch the page, and return it as if the blog were hosted from within IIS itself. The **{R:1}** variable carries forward any path information to the blog -- so your theme files, user uploads and static assets will all pass through.

If you deploy your site and try to access your blog page now, it won't work. You'll see a '404' response code even though the rules are definitely set up properly. The final step to this is enabling the Application Request Routing module on IIS - this is not enabled by default in Elastic Beanstalk's version of Windows.


## Enabling the Reverse Proxy Module on Elastic Beanstalk


You could Remote Desktop into your web server machine and manually enable the ARR module, but it would stop working the next time your environment flips, the server gets reloaded for any reason (which can happen even if you are doing all at once deployments and not adding/removing machines), or nodes get added to your environment.

We need to make sure the module gets installed and checked every time you deploy your files, so it's always present and available to use even when new machines come online.

To do that, we'll use the **.ebextensions** scripting hooks to download, install and configure ARR every time a deploy runs.


### 1. Download the ARR Installer


Download the 65-bit ARR installer ([from here](https://www.iis.net/downloads/microsoft/application-request-routing)) to S3 so it is available to your VM when it boots. We want to install to S3 instead of pulling directly off Microsoft's servers because we can't rely on outside links being available when we need to deploy, and if our VM happens to be inside a VM without a NAT then we can use Amazon's S3 internal endpoints without needing to configure any more advanced network.


### 2. Add an ebextension hook


In your .ebextensions folder (at the root of your unzipped deploy package), add a new config file (**install-arr.config**) to instruct Elastic Beanstalk how to install the extension:

    
    packages:
      msi:
        ApplicationRequestRouting: "pathtoyourinstallerons3"
    
    commands:
      01_enablearr:
        command: "C:\\Windows\\system32\\inetsrv\\appcmd.exe set config  -section:system.webServer/proxy /enabled:True  /commit:apphost"
    


The **packages/msi** lines tell Elastic Beanstalk to download and run the installer. Since you won't be physically present when that happens, the script will automatically accept all the license agreements and silently run.

The appcmd command instructs IIS to enable the reverse proxy module, which turns your rewrite instructions into actual reverse proxy commands. Now if you visit www.yourdomain.com/blog/, you will see your WordPress blog.


## Bonus: Trailing Slashes


If you visit www.yourdomain.com/blog without a trailing slash, you won't see the blog. You don't want to start the rewrite rule at this level because your reverse proxy will try to access blog.yourdomain.com// (with a double slash) for all dependent resources, which can cause problems with WordPress' routing.

For this case, I like to redirect to a trailing slash at the IIS level. Every time someone comes to yourdomain.com/blog, they will redirect with a clean 302 status command to yourdomain.com/blog/. Just add this rule in your section of web.config




    
    <rule name="Add trailing slash to blog reverse proxy" stopProcessing="true">
      <matchurl="^blog$"/>
      <action type="Redirect" redirectType="Permanent" url="{R:1}/"/>
    </rule>





The regular expression in the match url tells the web server this should only apply to the "blog" path, that is, nothing before or after the word "blog". This lets you have "blog" in other parts of your URL without accidentally redirecting valid pages.
