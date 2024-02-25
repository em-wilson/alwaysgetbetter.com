---
author: emwilson
comments: true
date: 2011-08-02 10:00:51+00:00
layout: post
link: https://www.alwaysgetbetter.com/2011/08/02/windows-azure-thoughts-months/
slug: windows-azure-thoughts-months
title: Windows Azure Thoughts - First Six Months
wordpress_id: 434
categories:
- Windows Azure
---

Having played with Windows Azure for about six months or so, I think I have a good handle on its pros and cons for the tasks I've been trying to do. I definitely have a lot of positive conclusions about the platform as a whole, and a few pain points I can see Microsoft working hard to eliminate.

Starting with the good:

**Seamless Deployments - No Client Downtime**
One of the trickiest things to set up while deploying a web cluster is the ability to deploy new builds without causing downtime, and reverting to the last build version in the event of a discovery of some critical flaw.

Azure really takes the pain out of this part of the process. Just deploy to staging, test the staging area, and hit 'Swap VIP' to reverse the staging & production instances. This is a great way to increase and decrease capacity as long as the end points remain the same (same number of ports, etc). ([Remember, you need to have 2 or more instances in order to invoke the SLA.](/blog/2011/04/25/ensure-sla-multiple-web-role-instances-windows-azure/))

**Scalable Computing as it is Meant to Be**
Most of the developer confusion I have encountered during these first months of development has turned out to be related to scalable programming in general and not Azure programming in particular. I haven't been training people to use Azure so much as I have been promoting the importance of properly separating aspects of code to make use of data sources in a non-limiting way. Caching, database access, blob storage, table storage are not new concepts, but using them from the ground-up in every project has been new to most people.

We can't develop with one web server in mind and then scale out, we now have to think about scaling right from the beginning, which is resulting in a much cleaner architecture overall. Developers become more connected to the practical results of their decisions when they have to [choose whether to use NoSQL, SQL or both for data access](/blog/2011/04/24/azure-table-storage-azure-sql/) based on factors ranging from speed to overall cost.

**Tight Code Cohesion**
Working with Azure for me has meant a return to .NET and C# programming. Combined with the ASP.NET MVC 3 framework and latest SQL database versions, the experience has been overwhelmingly fun. It has been great to have code that just works nicely in an environment that has been clearly optimized for exactly what I'm trying to do. Not having to deal with server setup and maintenance has freed me to dive more deeply into the capabilities of the system without needing to worry about the configuration. In short, I'm loving this.

And the bad:

**Long Deploy Times**
At one point our build times were pushing 45 minutes. This is a huge problem when it is 11 o'clock at night on launch day and you have a team of developers waiting around in order to verify their fixes.

During development the situation can be made easier with incremental pushes; since each Azure instance is a VM, it isn't hard to upload the changed files through RDP and see changes immediately. For devving this is fine, but the changes are not persistent therefore the full deploy process of uploading the cspack file, provisioning instances and starting up is needed when moving production code into a live environment.

The process can definitely be sped up by reducing the overall project size; at one point we had several hundred megabytes of supporting files which didn't need to be part of our project. Removing these basically eliminated our upload times, but the web role instantiation still clocks in at 15-20 minutes; a long time when you want to go home to your family.

**Slow System Responses**
When I say slow system response, I am referring to the management API which hits the AppFabric, not the response times of our applications which have been incredibly strong.

Any time I manage our Azure account, I find myself waiting around for a lot of information. During deployments especially, the status messages are unhelpful 'Creating deployment', 'Initializing instance', 'Instance busy', etc. This is really frustrating given the long deployment times; having more transparency into the process would be a real confidence builder.

**Unclear Management Console**
This is a bit nit-picky; the Silverlight-based management console is pretty cleanly laid out. Some of the configuration options are not clear, though. For example, to grant API access to your applications, you need to upload your certificate to the 'Management Certificates' section, which is separate and unrelated to the Certificates folder subordinate to each hosted service. This makes sense for veterans of the system as both certificate groups serve different roles, but new users are easily confused by the distinction and can spend hours trying to figure out how to deploy their projects from Visual Studio.

Similarly, after uploading your certificate, you can get the subscription ID by clicking on the subscription and copying the ID from the info panel at the right-hand side of your screen. If you right-click on the subscription ID in the menu pane and choose the 'Copy' option, it copies the creation date & name of the subscription. Hardly a major problem, but potentially a stumbling block for developers new to the platform.
