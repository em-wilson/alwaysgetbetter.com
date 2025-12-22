---
author: emwilson
comments: true
date: 2025-12-21 09:00:00 +0000
layout: post
title: "CodeIntensor: Private AI Code Search"
excerpt: "As projects grow, understanding code quickly becomes a challenge that simple text searches can't solve. Introducing CodeIntensor, a new tool designed to semantically understand your codebase. It moves beyond keyword matching to deliver faster, more accurate insights, making complex code instantly clearer."
slug: codeintensor-private-code-search
categories:
- Development
- Tools
- AI
photo:
  source: /images/2025/12/neural-network-search
  url: /images/2025/12/neural-network-search.png
  description: Neural Network Search
tags:
- code search
- semantic search
- AI
- developer tools
- CodeIntensor
---

Every developer faces the challenge of navigating complex codebases. You're diving into a new codebase—or even one you wrote six months ago—and you need to find "that function that handles user authentication somewhere." You try `grep`, maybe `ripgrep` if you're fancy, cycling through search terms: "auth", "login", "authenticate", "user session"... 

Modern AI assistants face the same limitations. They become significantly less effective when a codebase grows too large to fit into a context window. This forces a reliance on powerful remote servers or complex tooling just to reason about the logic, often missing crucial details in the process.

What if you could just *ask* your codebase what you're looking for?

## Introducing CodeIntensor

CodeIntensor is a local, semantic code search tool that understands what your code *does*, not just what it's called. It builds a vector index of your entire codebase—including your dependencies—and lets you search using natural language.

The best part? Everything runs locally. No code leaves your machine, no API keys required.

## Get Started in 60 Seconds

```bash
pip3 install git+https://github.com/em-wilson/codeintensor.git

# Initialize in your project
cd your-project
codeintensor init

# Build the index
codeintensor index
```

That's it. CodeIntensor will chunk your code semantically (detecting functions, classes, and logical blocks), generate embeddings, and store everything in a local SQLite database.

## Search Like You Think

Now the fun part. Instead of guessing keywords, just describe what you're looking for:

```bash
$ codeintensor search "handle failed payment retry logic"

[1] src/billing/payments.py (lines 145-189)
    Score: 0.8432
    function: process_payment_with_retry
    Language: python
    Source: user

[2] src/billing/webhooks.py (lines 67-94)
    Score: 0.7651
    function: handle_payment_failure
    Language: python
    Source: user

[3] node_modules/stripe/lib/retry.js (lines 12-58)
    Score: 0.5765
    function: retryRequest
    Language: javascript
    Source: vendor (stripe)
```

Notice that third result? CodeIntensor indexes your dependencies too, with source type tagging. Your code scores higher by default, but you can see how libraries solve similar problems—incredibly useful when debugging integrations or learning new packages.

## Filter When You Need To

Sometimes you want precision:

```bash
# Just your code
codeintensor search "database connection" --source user

# How does SQLAlchemy handle it?
codeintensor search "connection pooling" --source vendor --package sqlalchemy

# Only Python files
codeintensor search "async handler" --language python
```

## Built for AI Workflows

CodeIntensor shines when paired with AI coding assistants. Add an agent integration with one command:

```bash
codeintensor agents add claude
```

This creates a `CLAUDE.md` file that instructs Claude to search your codebase before suggesting changes. No more hallucinated function names or missed existing implementations.

## Why Local Matters

Cloud-based code search tools are powerful, but they require uploading your code to external servers. For proprietary codebases, side projects you're not ready to share, or just simple privacy preference—local-first is the answer.

CodeIntensor uses `sentence-transformers` for embeddings, runs entirely offline after initial model download, and stores everything in a `.codeintensor` folder you can gitignore or delete anytime.

## What's Next?

Give it a try:

```bash
pip3 install git+https://github.com/em-wilson/codeintensor.git
cd your-favorite-project
codeintensor init && codeintensor index
codeintensor search "the thing that's been bugging you"
```

You might be surprised what you find—or rediscover—in your own code.

---

*CodeIntensor is open source and available on [GitHub](https://github.com/em-wilson/codeintensor). Contributions welcome!*
