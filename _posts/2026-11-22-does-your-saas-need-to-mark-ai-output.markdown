---
author: emwilson
comments: true
date: 2026-11-22 13:00:00+00:00
layout: post
slug: does-your-saas-need-to-mark-ai-output
title: "Does Your SaaS Need to Mark AI Output by December 2?"
excerpt: "Since August, a European law has required AI-generated content to carry a hidden 'made by AI' tag and chatbots to say they're bots, and on December 2 the grace period for older products runs out. Most small teams wrapping an AI service don't know whether the rules apply to them or to their vendor. A plain-language walkthrough of who owes what, how the marking works, and a checklist you can run this week."
categories:
- General Programming
photo:
  source: /images/2026/11/ai-labels
  url: /images/2026/11/ai-labels.jpg
  description: Every output needs a tag, and somebody has to stick it on
tags:
- ai
- regulation
- eu-ai-act
- compliance
- saas
published: true
---

Suppose you run a small software business. A year ago you added a button that writes product descriptions for your customers, and later a feature that turns a rough sketch into a polished banner image. Both work by sending a request to one of the big AI companies and passing the answer back. Your customers love it, and some of them are in Europe.

Since August 2 a European law has said that content made by AI has to carry a hidden tag a computer can read, saying "a machine made this." People chatting with an AI have to be told they're talking to one, and certain AI-made images, videos and articles need a label a person can see. Products that were already on sale before August got extra time for the tag, and that extra time ends on **December 2, 2026** - ten days from now.

It's tempting to assume the AI company you call handles all of this, and it does handle some of it. The law puts the tagging duty on whoever offers the AI feature *under their own name*, and for features you sell, that's probably you. Get it wrong and the fines go up to [€15 million or 3% of worldwide turnover](https://digital-strategy.ec.europa.eu/en/faqs/transparency-obligations-under-article-50-ai-act), and the law reaches companies outside Europe whenever their AI's output is used there, so Canadian businesses with European customers are in scope.

The big model vendors have already built most of what you need, the tags are invisible to your users, and the work left for a small team is mostly bookkeeping: knowing what your app generates, turning on the right settings, not throwing the tags away by accident, and putting a short notice in the right places on screen.

## Provider or Deployer: Who Made This?

*Everybody assumes the vendor handles compliance, and every vendor's docs tell you to check your own obligations.*

The rules come from [Article 50](https://ai-act-service-desk.ec.europa.eu/en/ai-act/article-50) of the [EU Artificial Intelligence Act](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng), and they split the work between two roles. The Commission's questions and answers spell them out:

* A **provider** develops an AI system, or has one developed, and puts it on the EU market or into service *under its own name or trademark*, whether or not the company is in the EU.
* A **deployer** uses an AI system under its own authority, other than for purely personal, non-professional use.

The phrase that matters for SaaS is "under its own name." You didn't train the model, but when you wrap it in a feature called "Acme Smart Banner" and sell it, the AI *system* your customers use is yours. Plenty of advice online tells API customers they're "just deployers," so read the definition yourself before believing it.

Some examples:

| Situation | Your likely role |
|---|---|
| Your app has a "generate image" button that calls a model API | Provider of that system |
| Your support widget answers customers with an AI model, under your brand | Provider of that chatbot |
| You fine-tune or self-host an open model and ship it to customers | Provider |
| Your marketing team makes blog art with someone else's image tool | Deployer |
| Your customer uses your tool to publish a news-style article | They're the deployer, you're the provider |

You can hold both roles at once - provider of the feature you sell and deployer of the tools you use in-house. One question sorts most of it out, and I'd ask it of every output:

> For every piece of content my product generates, who would a regulator say made it, me or my vendor?

## Marking for Machines, Labels for People

The law asks for two different things, and mixing them up causes most of the confusion.

**Machine-readable marking (providers).** Providers of AI systems that generate synthetic audio, images, video or text have to make sure the output is marked in a machine-readable format and detectable as artificially generated or manipulated. Nobody has to see this mark - it exists so software, platforms and investigators can check. The law exempts systems that only perform an assistive function for standard editing, or that don't substantially alter what the user put in. The Commission's FAQ lists more cases that don't need marking: a short sequence of numbers, symbols or letters, source code, output that only goes machine-to-machine without reaching a human, closed-loop industrial use, and a narrow business-to-business exemption.

**Visible disclosure (for people).** Three cases need something a human can perceive:

1. **Chatbots and other AI that talks to people (providers).** Users have to be told they're interacting with an AI unless it's obvious to a reasonably well-informed, observant person. That "obvious" exception is meant to be read narrowly, so a chat bubble with a friendly human name doesn't qualify.
2. **Deepfakes (deployers).** Image, audio or video that resembles real people, places or events and would falsely appear authentic has to be disclosed as AI-made. Deployers can't rely on the provider's invisible mark for this - the disclosure has to be perceivable without technical tools.
3. **Text published to inform the public on matters of public interest (deployers).** Think politics, public health, consumer safety. This text needs a label unless it went through human review or editorial control and a person or company holds editorial responsibility for publishing it. Spell-checking and grammar fixes don't count as review - someone with relevant knowledge has to examine the substance.

Whatever the case, the information has to be clear and distinguishable, provided no later than the first interaction or exposure, and accessible.

## How the Hidden Tag Works

The law doesn't name a technology. It only says the mark has to be machine-readable and detectable. The technical detail lives in a voluntary [Code of Practice on Transparency of AI-Generated Content](https://digital-strategy.ec.europa.eu/en/policies/code-practice-ai-generated-content) published in June, and the Commission's [guidelines on who the rules cover](https://digital-strategy.ec.europa.eu/en/library/guidelines-transparency-obligations-providers-and-deployers-ai-systems) followed in July. Following the Code is the simplest way to show you comply, because the Commission and the AI Board have assessed it as adequate. You can use other methods, but then it's up to you to prove they work, and each country's regulator decides case by case.

The Code's [marking commitment](https://bundesnetzagentur.de/EN/Areas/Digitalisation/AI/04_Transparency/code_of_practice/start.html) comes down to five points:

* **At least two layers of machine-readable marking**, because no single technique currently meets all the requirements.
* **Signed metadata.** Where the file format can carry metadata (images, audio, video, containerised text), it records whether the content is AI-generated or manipulated, digitally signed and time-stamped so tampering shows. In practice that means **Content Credentials**, the C2PA standard that OpenAI and Anthropic already attach to generated images.
* **An imperceptible watermark** woven into the content itself, so it survives when someone strips the metadata.
* **Optional fingerprinting or logging** on the provider's side.
* **Very short text** doesn't need a watermark.

Plain text has nowhere to attach metadata, so how does it get watermarked? A text watermark doesn't add hidden characters or use any weird tricks; the text you see is all there is, and anyone can inspect every bit of it. Instead, as the LLM works it always calculates which words are most _likely_ to appear next, and when several are equally good the model's algorithm nudges which one gets picked, leaving a pattern that a detector can spot but a reader never notices. That pattern needs a fairly large amount of text to show up which is why very short text is less likely to yield matches in services looking for AI-generated content.

For the visible side, the Commission published a [free set of EU icons for labelling AI-generated content](https://digital-strategy.ec.europa.eu/en/policies/eu-icons-labelling-ai-generated-content): a basic icon, "Fully AI-Generated" and "Partially AI-Modified." They come as SVG and PNG with no attribution needed, and in user testing people recognized them better when the icon sat next to a short text label such as "modified." Using them is optional, and the Commission says using them doesn't by itself prove compliance.

## Open Source Doesn't Get You Out

Self-hosting an open-weight model doesn't exempt you from these rules. The Act does [exclude most open-source AI](https://ai-act-service-desk.ec.europa.eu/en/ai-act/article-2), but that exclusion specifically doesn't cover systems that generate content or chat with people, so the transparency rules apply in full.

Self-hosting changes one practical thing: there's no upstream vendor marking the output for you. If you run an open model behind your own brand, the watermark and the signed metadata are your engineering problem.

## What Happens on December 2

The July "AI Omnibus" amendment, [Regulation (EU) 2026/1744](https://eur-lex.europa.eu/eli/reg/2026/1744), set the deadline: AI products that generate images, audio, video or text and were already on the market before August 2, 2026 have until December 2 to get their marking in place.

That is an aggressively narrow grace period!

**December 2 applies to** providers of generative features that were already on sale in the EU before August 2, and only for machine-readable marking.

**December 2 doesn't apply to:**

* Generative features launched since then. Those needed marking from day one.
* Chatbot disclosure, which has applied all along.
* Deepfake and public-interest text labels, deployer duties that started at the same time.

Content generated before the rules started doesn't need retroactive labels, though the Commission encourages it. The amendment leaves some questions open, like whether a substantially reworked feature you shipped in, say, September counts as "already on the market."

**Being outside the EU isn't an excuse** because the Act covers providers placing AI systems on the EU market whether or not they're established in the Union, and providers and deployers in other countries where the system's output is used in the Union. Having EU customers is enough.

## Getting Your Product Ready

None of this is hard, but it goes a lot faster in order. It's much easier to sort out who's responsible for what once everything is written down in one place.

### 1. Inventory Every AI Output

Go through your app and write down every place it produces content with AI, including the ones nobody thinks of as "AI features": the auto-summary on a ticket, the suggested reply, the generated alt-text.

A spreadsheet is plenty. One row per thing you generate:

| Feature | What it makes | Who sees it | AI vendor | On sale in the EU since |
|---|---|---|---|---|
| Smart banner | Images | Customers, who download them | Image API | March 2026 |
| Support chat | Text, live conversation | Anyone on the help page | Chat model API | November 2025 |
| Release notes draft | Text | Our team, edited before publishing | Chat model API | September 2026 |

The last column tells you which deadline you're working against. Most teams will find more entries than they expected.

### 2. Decide Your Role for Each Output

This is the step that keeps you from finding out the hard way that a job you thought belonged to your vendor was yours all along. Go down the spreadsheet and ask the same question for every row: if a regulator looked at this, who would they say made it? If it goes out under your brand, you're the provider. If it's something your own team uses to put content out into the world, you're the deployer for that use.

Once you know your role, the duties mostly sort themselves out. Anything that makes images, audio, video or text for someone to read, watch or hear needs **marking**. Anything that chats needs a **notice** that it's an AI. If your users can make a convincing fake of a real person or event and post it, the **deepfake label** is legally their job (step 4 has a way to help them). And if you publish articles on matters of public interest with AI help, you need either a **visible label** or genuine editorial review, which step 5 covers.

Some rows won't need anything, because they only produce code or never reach a human. That's fine, but write the reason down next to the row. Six months from now nobody will remember why the release-notes tool got a pass, and an exemption you can't explain is about as useful as not having one.

| Feature | Your role | What you owe | Why not, if nothing |
|---|---|---|---|
| Smart banner | Provider | Marking by December 2 | |
| Support chat | Provider | AI notice at the start of every chat (already due), and marking by December 2 | |
| Release notes draft | Deployer | Nothing | Release notes aren't public-interest news, and the AI tool isn't ours, so marking is the vendor's job |

### 3. Check What Your Vendor Already Marks

This step protects you from both doing double work and trusting a mark that isn't there. The Commission's FAQ says marking techniques built in by general-purpose model providers can make downstream compliance easier - _easier, not automatic_. Look up exactly what your vendor does for each content type and each model you call, because the answers vary:

* **OpenAI:** images from the API [carry both Content Credentials and a SynthID watermark](https://help.openai.com/en/articles/8912793), except from models planned for imminent deprecation. **API text watermarking is something you turn on** in your project or organization settings, and outputs under about 200 tokens and code snippets aren't expected to carry it. A [content provenance API](https://developers.openai.com/api/docs/guides/content-provenance) can check images and audio, but text checking is limited to approved organizations.
* **Anthropic:** supported Claude models [watermark generated text and put Content Credentials on supported files](https://support.claude.com/en/articles/16266773) such as PNG and JPEG. The listed exceptions include very short passages and, for certain models, a non-default `temperature`, `top_p` or `top_k`. Anthropic tells developers to work out their own obligations independently.
* **Google:** there's no official guarantee from Google that text from the Gemini API carries a SynthID watermark. In Google's developer forums, people who appear to work for Google seem to confirm that it does, but there's no public tool to check text from the API yourself. Until Google puts it in writing, treat the gap as yours.

Once you know what your provider does for you, make sure your own code isn't throwing the marks away. Signed metadata lives in the file, and resizing, re-encoding, a CDN's "optimize images" setting or a screenshot will strip it. OpenAI's docs warn that editing, converting or sharing a file can remove its metadata. Add a check to your pipeline that runs on the files you serve, not the ones the vendor sent you.

If your pipeline has to transform images, re-sign them with your own credentials after the last change instead of hoping the vendor's metadata survived.

### 4. Add Visible Disclosure in the UI

While the hidden marks are for software, the visible notices get at what the law is after: making sure nobody is fooled into thinking they're talking to a person when they're talking to a bot, or that a fake photo or video shows a real event. People make different decisions when they know a machine is on the other end, and they deserve the chance to make them.

For a chatbot, that means telling users up front, in plain words, before they've typed anything. Something like:

> You're chatting with an AI assistant, not a person. It can make mistakes, so check anything important with our team.

Put it in the chat window itself, not in your terms of service. A notice nobody sees until they go looking for it doesn't pass muster here.

For images and video that could pass as real, the label belongs on the content itself, so it's still there after someone downloads the file and shares it somewhere else. A small "AI-generated" tag in the corner, or the EU icon with a word next to it, does the job. The EU icon page asks for the label to be visible from the first moment someone sees the content, not covered up by other elements, and backed by alt text so screen reader users get the same information. Even when the label is legally your customer's job, turning it on by default saves them a problem and saves you an awkward support ticket.

### 5. Record Human Editorial Review

This step protects your exemption for public-interest text. If you publish AI-assisted articles, reports or summaries on matters of public interest without a label, you're leaning on the editorial-review exception, and you need to be able to show the review happened. The Code's deployer commitments ask for an internal policy that names the person or entity holding editorial responsibility, with contact details, and gives an overview of the review measures and people assigned to it. Keeping a small record per published piece costs almost nothing:

```json
{
  "content_id": "post-2026-11-22-pricing-update",
  "ai_assisted": true,
  "reviewed_by": "J. Editor",
  "review_type": "substantive - facts and figures checked against sources",
  "editorial_responsibility": "Acme Software Inc.",
  "approved_at": "2026-11-21T16:40:00Z"
}
```

If nobody with the right knowledge actually reads the substance, don't write the record. Label the content instead.

## Gotchas

**A tuning change can switch the watermark off.** If your vendor's text watermark doesn't hold up at non-default sampling settings, then the engineer who bumps `temperature` to make answers "more creative" just changed your compliance posture. Pin those settings in config and put a comment next to them explaining why.

**The vendor's mark isn't your label.** An invisible watermark meets the provider's marking duty and nothing else. Chatbot notices, deepfake labels and public-interest text labels all have to be visible.

## Know Who Made It

The AI companies have done the heavy engineering. What's left is the part nobody else can do for you: **the inventory, the settings, and checking that the right mark or label survives the trip to the person on the other end.**

Ask the question again every time you add an AI feature, swap models or change a sampling setting: *for every piece of content my product generates, who would a regulator say made it, me or my vendor?* If you can't answer it quickly, that's the feature to fix first - and if you sell into the EU, that's the list to bring to your lawyer.
