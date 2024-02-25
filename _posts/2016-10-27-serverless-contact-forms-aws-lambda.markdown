---
author: emwilson
comments: true
date: 2016-10-27 07:29:06+00:00
layout: post
link: https://www.alwaysgetbetter.com/2016/10/27/serverless-contact-forms-aws-lambda/
slug: serverless-contact-forms-aws-lambda
title: Serverless Contact Forms with AWS Lambda
wordpress_id: 650
categories:
- CloudFront
- Lambdas
- Node.js
- S3
tags:
- aws
- cloudfront
- email
- https
- jekyll
- lambda
- nodejs
- s3
- static
photo:
  source: http://www.publicdomainpictures.net/view-image.php?image=88360&picture=lamb
  url: /images/2016/10/agneau-300x199.jpg
  description: LAMB-da - Serverless Contact Forms
---

Servers are a pain to run. They break, get hacked, need updating, and generally need your constant attention or that site you posted two years ago won't work when you need to make a change. Static sites are a beautiful dream, but what do you do when you need user input? You don't want to use a third-party service just to get rare contact forms from your visitors. It's stupid to run a web server to handle this; that completely eliminates the whole purpose of creating a static site. What you need are serverless contact forms.


## Architecture


I use Wufoo forms for most of my static sites but today I'm switching to Lambdas. To start, I have a [static Jekyll site](https://jekyllrb.com) uploaded to S3. I have a CloudFront distribution set up for [edge-caching and SSL](/blog/2016/02/05/https-on-static-sites-using-cloudfront/). Now I'm going to add a contact form that reaches through AWS' API Gateway to execute a node.js script hosted on Lambda.

Here is the general data flow:

![Serverless Contact Forms Architecture](/images/2016/10/aws-lambda-arch.png)



## Email Script



I first write a simple Node.js script that validates the contact form parameters then pumps the messages into the Sendgrid API. You can swap in your preferred client. AWS SES is a popular one for sure and a nice way to keep everything under one umbrella.





## Setting Up the Lambda



First create a blank template.

![Serverless Contact Forms start with a blank lambda](/images/2016/10/lambda-1-1024x387.png)

Next create an API Gateway trigger for the Lambda. I set my security to 'Open' because I am allowing anonymous traffic to send contact forms.

![Serverless Contact Forms API Gateway Settings](/images/2016/10/lambda-2-1024x715.png)

Finally, upload the contact form script. Since I've used libraries other than Amazon's own I need to zip everything up and send it as an archive. 

![Serverless Contact Forms Lambda Setup](/images/2016/10/lambda-3-710x1024.png)



## Configuring the API Gateway for Serverless Contact Forms



Now that the Lambda function has been created it's time to open it to the outside world. Go to the 'API Gateway' service, find your endpoint, and choose Actions -> Create Method. Add a POST method pointing to your Lambda function's name.

![Serverless Contact Forms API Gateway Setup](/images/2016/10/lambda-4-1024x553.png)

You need to enable CORS because you will be using AJAX to submit the form. This is a cross-domain request while we're testing it out.

[![lambda-cors](/images/2016/10/lambda-cors-300x164.png)](/images/2016/10/lambda-cors.png)

Then deploy the API. The default environment is prod, which is good enough for our purposes.

[![lambda-5](/images/2016/10/lambda-5.png)](/images/2016/10/lambda-5.png)

Finally you see the the endpoint URL you will use for your form.

[![lambda-6](/images/2016/10/lambda-6-300x75.png)](/images/2016/10/lambda-6.png)



## Serverless Contact Forms



That's the backend all set up now you can build out the contact form your visitors will use to contact you. Here's mine. It's super ugly but it gets the point across.



When you hit submit the send function executes, posting the email address and message to the endpoint and alerting the results. Nothing fancy, and obviously you would need to code in all the proper validation and UI stuff to make this pretty.



## CloudFront Configuration



I can get into the static site setup later, but for now I'm working from an existing distribution.

Since CORS is all set up you could use the endpoint as-is and just launch your contact form, but that's not as elegant as posting to the same domain as the form itself. You can achieve this illusion because CloudFront is able to forward POST requests now.

Add the origin for the API to the distribution.

[![lambda-7](/images/2016/10/lambda-7-300x259.png)](/images/2016/10/lambda-7.png)

Tell CloudFront to forward (and not cache!) your contact form by adding a behaviour for the path where you want to host the form. The path name should match your API resource name:

[![lambda-8](/images/2016/10/lambda-8-177x300.png)](/images/2016/10/lambda-8.png)

For this to work make sure you choose the Allowed HTTP Methods option with POST. Setting the Forward Headers option to ALL causes CloudFront to use the origin cache settings (no cache) which will let more than one person use your contact form.



## Profit



Overall this feels like it is a longer process than it should be. The first time I did this it took almost 5 hours, but now that I know the process I'm sure next time will be a lot faster. Figuring out the right folders and permission settings (for CORS) was the most finicky part of the process, but the API Gateway has an informative test tool that helped a lot.

This is only going to get better, and Lambda is a fantastic cost-effective tool for replacing on-demand tasks where you would once need to spin up and support a whole server. Serverless contact forms are definitely the way to go if your site is otherwise static.
