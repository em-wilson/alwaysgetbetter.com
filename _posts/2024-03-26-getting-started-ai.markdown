---
author: emwilson
comments: true
date: 2024-03-26 10:06:00+00:00
layout: post
link: https://www.alwaysgetbetter.com/2024-03-26-adventures-ai/
slug: adventures-ai
title: "Adventures in AI: From Boredom to Binary"
excerpt: Free tools and programs you can use today to jump into AI.
categories:
- General Programming
photo:
  source: /images/ai/OIG4
  url: /images/ai/OIG4.jpg
  description: Robot Comedian
published: true
---

So, picture this: Three months of yawning boredom, countless Netflix binges, and a growing suspicion
that my brain cells were staging a protest. What better way to beat the blues and upskill than by diving
headfirst into the weird world of Artificial Intelligence (AI)?

So, here I am, armed with determination, a laptop, and a questionable amount of caffeine. My journey
into the digital wilderness begins. Time to buckle up, because we’re about to explore neural networks,
deep learning, and maybe—just maybe—unlock the secrets of the universe. 🚀

Where to Start?
===============

The main thing holding me back from getting into these tools was a general confusion about the
various terms and buzzwords surrounding artificial intelligence. Our opinions are shaped by
the "magical" things we see in the products and movies we consume, but the reality about AI is
not some weird alien intelligence, it's 1s and 0s planted firmly in the computers we already
use and understand.

I thought I needed some obscure math knowledge or deep theory, and that just wasn't the case.
I assumed ML engineers had a lock on some important knowledge unavailable to me... nope.
So let's peel back some of the layers to understand what we're working with.

Free is the Way
---------------
In 2024, you don't have to pay for access to decent AI. You can benefit from tons of free resources
that give you access to the latest commercially available models without paying. If you do want to go
the paid route (e.g. ChatGPT Pro or Midjourney) your cost layout is relatively minor.

I don't feel like I know enough about this subject yet to commit to paying for a particular implementation
so I've been leaning toward open source projects and freely available commercial offerings.
Most of the tech big players want in on this space and they make a ton of resources available
to use for free.

Buzzwords vs Ideas
------------------
It's never good when executives start talking about tech trends before you do, but that was
the case for me when it came to **machine learning (ML)**. It was some kind of miracle technology
that was going to understand our customers' sentiments and shortcut our internal processes for us.
*What is actually was*, was a fancy way of saying *big expletive Excel spreadsheet*.

Essentially, this form of AI is a prediction engine that gets better with **training data**.
Based on millions of examples, it can take input and guess what the output is. That's it.

Combine ML with **natural language processing (NLP)** and you get a chatbot that appears
to understand written human speech. This is what gives chatbots the uncanny valley where
you might think (*at first*) you are talking to a real human. As we've all seen, these
apps are [easy to "fool" into giving you out of bounds responses](https://venturebeat.com/ai/a-chevy-for-1-car-dealer-chatbots-show-perils-of-ai-for-customer-service/), since they tend to have
very little training outside their specialty area.

The terms **neural networks** and **deep learning** refer to flavours of the same thing,
and are just ways of describing the complexity of the data beneath the ML. Deep learning
in particular differentiates itself by having hundreds of millions, even trillions, of
connecting nodes from which to draw inferences. This is the technology behind popular tools
like ChatGPT; **large language models (LLMs)** are a particular type of neural network
built on a transformer model that produces *generative text* based on queries.

Doing Things
============
What does it all *mean*? Knowing what the words are is one thing, actually doing something
useful seems tricky at first.

The trick to using AI effectively is understanding what it does and doesn't excel at.

Searching
---------
If you ask AI to search for something you'll get a Google-like result with a bunch of
inaccuracies, so a lot of people give up at this step. Either the bot gives you a bunch
of information that seems useful but you have to verify, or you are a subject matter
expert and can immediately see all the problems in the results.

The reason for this is AI doesn't actually know anything. All it can do is synthesize
the data it was trained on. Any missing gaps get filled in to sound intelligent but
really aren't (this process is called **hallucination**).

Hallucinating sounds bad, but it's actually an important part of how we can leverage
these tools to do unique and creative things.

Writing
-------
AI can absolutely write articles and papers for you. It's even capable of writing in
different voices and styles, and producing surprisingly unique-sounding prose. *But*
as a finished product these works are garbage.

Although it's good at mimicking aspects of human writing, there is a deep uncanny
valley and "off" factor in articles written by machiens that don't have actual
personalities and don't truly understand the concepts they're writing about.

In particular, text generation is done word-for-word with no real knowledge of
what came before and what will come later. Longer works especially will not flow,
and the coherent text we do get fails to live up to the expectations of a native
reader.

All that isn't to be down on AI as a tool for writing. Some of this article, in
fact, was written by ChatGPT. Can you tell which parts? AI is a fantastic tool
to help inspire and speed up the creative process; it won't replace a human entirely.
You still have to edit the work and be prepared to describe, in great detail, what
you want.

Which brings us to...

Idea Generation
---------------
If you're stuck, describe your problem to an AI and it will spit back a list of ideas
that will kick-start your next adventure.

Not sure what to cook for dinner? Take a picture of your refridgerator's contents and
ask for 5 meal ideas based on what it sees.

Cooking for picky houseguests? Ask for a mealplan tailored to their dietary preferences
and tastes.

Working on the [latest spacecraft for NASA](https://www.nasa.gov/science-research/nasa-turns-to-ai-to-design-mission-hardware/)?
AI can shortcut the design of totally alien structures that perform better than traditional
manual processes.

Specific Software
=================

So what does it all mean? How do you actually jump in and do something *useful* with all this?

I kept getting stuck on tutorials that wanted me to install a bunch of Python libraries and
code my problems, but that isn't necessary at all. Here are my three favourite pieces of
software you can use right now to dive into this.

Bing Copilot
------------

Finally, a use for Bing! Remember Bing? That's the search engine you get when you
accidentally open Edge instead of Chrome. As it turns out, it's being used as a clever
gateway into AI - accessible and totally free.

Go to [https://www.bing.com/chat](https://www.bing.com/chat) and start talking to the AI.
Ask it to make you a meal plan. A picture of a dog balancing on a circus ball. You need a (free)
Microsoft Live account to sign into this but you can begin right away and get some impressive
results.

Today Copilot uses a mix of GPT 3.5 and GPT 4 for language and Dall-E 3 for image generation.
It advances fast, of course, and by the time you read this they've no doubt moved on to
something even newer and better. Microsoft is able to flex their relationship with OpenAI (they
are a partner and their largest investor) and has chosen to double down on their research
in its products including Bing.

{% include image.html
      url="/images/2024/03/diabetic-ai.jpg"
      description="Asking Bing for a diabetic-friendly meal plan and shopping list"
%}


LM Studio
---------

{% include image.html
      url="/images/2024/03/lmstudio-ui.jpg"
      description="Generating weird and wonderful hallucinations from the comfort of your living room"
%}


Stable Diffusion
----------------

{% include image.html
      source="/images/2024/03/00002-3611064529"
      url="/images/2024/03/dog-diffusion.jpg"
      description="Generating weird and wonderful hallucinations from the comfort of your living room"
%}


Just Dive In
============

Learning AI has been transformative so far. The software in this article are not just gateways to
understanding and getting practical use out of complex algorithms; they are the keys to unlocking
a future of possibilities. By downloading and engaging with these tools, you're taking the first
step towards shaping that future. Whether creating intelligent solutions that tackle real-world problems
or understanding the technology that’s changing the face of every industry, don't just witness the
AI revolution—be a part of it.

**AI is not coming for your job. People who effectively use AI to make their work more valuable are
coming for your job.**

Seize this opportunity to learn, innovate, and lead the charge into a smarter, more connected world.

P.S. If you’re wondering whether I’ve accidentally created a sentient AI during my late-night coding
sessions, fear not. My chatbot still thinks “banana” is the answer to everything. 🍌