---
author: emwilson
comments: true
date: 2026-11-19 13:00:00+00:00
layout: post
slug: your-old-todos-are-now-instructions
title: "Your Old TODOs Are Now Instructions"
excerpt: "Stale comments and outdated docs used to be harmless clutter. Now AI coding agents read them as marching orders, and Google has measured what that costs. A reality check on tech debt when the bottleneck is reading code, not writing it."
categories:
- General Programming
photo:
  source: /images/2026/11/everything-asked
  url: /images/2026/11/everything-asked.jpg
  description: LLMs will do everything you asked, even if you didn't intend it
tags:
- ai
- technical-debt
- code-review
- career
published: true
---

Every long-lived software project is full of notes from the past. Reminders to "fix this properly later," pointers to problems that were solved years ago, design documents describing how things worked before three reorgs and a rewrite. People who work in the code every day learn to read right past them&nbsp;- we know the reminder is ancient and the doc is stale, so we ignore them and get on with the job.

(My favourite unused feature in PHPStorm was always the view of TODO comments as a list. I swore I would go back and clean them up "when I had time" for decades... I don't think I've checked on it since COVID.)

The new teammates don't know what's _actually_ "to do", they need to trust that lazy version of you that came before. More and more of us hand work to AI assistants that can open files, run commands and make changes on their own and they read every one of those notes at face value. An old reminder about a long-fixed problem looks to them like an open task, so they go investigate it, and sometimes they change code nobody asked them to touch. That costs time, money and the patience of whoever has to review the result.

The fix is about as unglamorous as engineering gets, and it finally comes with numbers attached. Google measured exactly how much those dead notes cost its own agents and then cleaned them out at scale, which turns "tidy up the comments" from a chore you apologize for into work with a measurable return.

---

## The Notes Nobody Reads Anymore

_Everyone has written "TODO: fix this properly" and nobody has ever come back to fix it properly._

Earlier this month, Google senior staff software engineer Sam Saccone and senior staff data scientist Madhuri Garikapati published [a write-up on what they call **agent distraction**](https://developers.googleblog.com/agent-distraction/). They mined traces from thousands of engineers and hundreds of millions of agent steps, focusing on one very specific kind of clutter: `TODO(bug/...)` and `FIXME(bug/...)` comments that point at a ticket in the bug tracker. Those tickets are the official record, and the comments tend to linger long after the bug is fixed or abandoned.

The numbers are worth reading slowly:

* **About 1 in 7 sessions (13%)** put an agent in front of one of those inline bug references.
* Most of the time the agent ignored it, but in **6% of encounters it went and did something about the bug**, and **83% of those follow-ups were unprompted** - the developer never mentioned that bug.
* Of the unprompted detours, **41% chased tickets that were already closed**, mostly bugs that had been fixed. Another **57% chased bugs that were still open but had nothing to do with the request**, and 39% of those involved editing code. That is scope creep with commit access.
* Sessions with a detour ran **70% more steps, 77% more tokens and 105% more waiting time** for the developer, after controlling for things like the developer, codebase complexity, task type and model tier.

In one instance, the agent stumbled on a TODO for a bug that was [fixed back in 2023](https://developers.googleblog.com/agent-distraction/) and burned several steps digging into it. In another, it found a TODO inside the bug it was working on and quietly modified unrelated code until the human pushed back and the agent admitted the extra scope was wrong. The authors put it bluntly: agents treat the things people skim past ["as a call to action."](https://developers.googleblog.com/agent-distraction/)

The measurements cover one company's internal traces and one kind of clutter. They name [obsolete specs and lagging documentation](https://www.infoq.com/news/2026/11/google-agent-distraction/) as the same problem, but the figures only count comments with a ticket number in them. My guess is the real bill is bigger than the one on the page.

---

## Your Codebase Is Part of the Prompt

The reason this matters more now than it did five years ago is mechanical. When an agent picks up a task, it explores the code the same way you or I would - searching text, opening files and following references. JioHotstar (a massive Indian streaming service, for my North American friends) recently [benchmarked six ways of helping agents find their way around a large codebase](https://www.infoq.com/news/2026/11/jiohotstar-codingagent-retrieval) and found that fancy retrieval layers didn't consistently improve results, and that agents gravitated toward plain text search and file reads over higher-level tools. Whatever is sitting in those files becomes the agent's working context, stale bits included.

So **every comment, README, design doc and instructions file in your repository is now effectively part of every prompt anyone on your team writes**. A human reads a three-year-old doc with three years of context in their head. The agent reads it as the current truth, because nothing in the file says otherwise.

A stale TODO is about the purest example you could ask for of a note frozen at the moment someone understood the problem one way and then left. Agentic coding lets software production outpace human understanding, with the code becoming a record of decisions the agents made while people stay on the hook for the consequences.

---

## Writing Got Faster, Reading Didn't

GitHub summed up the situation in [a guide to agentic engineering published this month](https://github.blog/ai-and-ml/github-copilot/beyond-code-generation-a-developers-guide-to-agentic-engineering/): "Writing code got faster. The work around it didn't." The same post says GitHub saw more than 1.2 billion commits in a single week in October, over 400% more than a year earlier, and cites a Faros AI study in which pull requests spent 441% longer in review as teams moved from low to high AI adoption.

Anthropic said much the same thing when it launched its AI review tool back in March: ["Code review has become a bottleneck"](https://claude.com/blog/code-review), and code output per Anthropic engineer had grown 200% in a year. By this fall the Claude Code team had rebuilt its whole review process around that reality. [InfoQ reports](https://www.infoq.com/news/2026/11/claude-code-review/) that merged pull requests to the Claude Code repository more than doubled between June and September while the share of changes needing a revert fell by over 40%, using layers of automated checks and AI reviewers with human approval scaled to how risky each change is. Low-risk changes like documentation may need no human sign-off at all, and high-risk ones need two.

GitHub, for its part, built [ReviewBench](https://github.blog/ai-and-ml/github-copilot/reviewbench-an-open-benchmark-for-ai-code-review/), an open benchmark for AI code reviewers modeled on more than 100 million real pull requests. Companies don't build benchmarks for problems they consider solved.

_Everyone says they reviewed the pull request; most of us reviewed the description and the green checkmark._

Put those together and the constraint is obvious. Generating a change is nearly free, and **reading, verifying and trusting a change** is where the time goes. Every unrequested side-quest an agent takes makes that worse twice: once when it burns tokens and wait time, and again when a human has to notice the diff contains work nobody asked for and figure out whether it's safe.

---

## Small Debt Finally Has a Price Tag

For most of my career, "clean up the stale comments" sat at the bottom of every backlog because the payoff was vague. A tidier codebase, slightly less confusion for the next new hire - nice, but never more important than the feature due Friday. Lame, but rational.

Agents change that math. Google's answer to its own findings was to point agents at the problem: scanner agents found bug references marked fixed or obsolete, then dug through bug history, file history and sometimes [years of changes](https://developers.googleblog.com/agent-distraction/) before proposing a deletion, because the presence of a reference alone didn't prove a TODO was dead. Over six months that cleaner removed thousands of obsolete references, and human reviewers accepted more than 95% of its proposals. The authors explicitly recommend revisiting cleanup projects that were written off as low value, since agents now operate across the whole codebase at scale.

Notice the asymmetry. A deleted dead comment is about the cheapest change there is to review - exactly the kind of low-risk change Anthropic's process waves through - while every agent that trips over that comment pays for it in tokens, wall-clock time and reviewer attention, over and over, for as long as it stays. **Small debt used to cost a little, once in a while. Now it costs a little on every single agent run.**

The same logic extends past TODOs even where nobody has published the numbers yet. Stale READMEs, design docs describing the architecture you replaced, onboarding guides that reference the old deploy script, and especially the instruction files you wrote for your AI tools six months ago and never touched again - all of it gets read, believed and acted on.

---

## The Missing Rungs

There's a career angle here that I don't think gets enough attention.

Alasdair Allan's QCon London talk, [The Ladder is Missing Rungs](https://www.infoq.com/presentations/ai-software-engineering-pipeline), argues that the routine work AI now handles - bug fixes, boilerplate, glue code, documentation - is how junior engineers used to build intuition for how systems behave, including how to debug and how to read legacy code. In [InfoQ's interview with him](https://www.infoq.com/news/2026/08/ai-disrupts-engineering-progress/), he says 80 to 90% of engineering questions now go to AI rather than to colleagues, which quietly removes the learning that used to come from asking a senior "hey, what's this thing for?"

His recommendations include deliberate rotations through fundamental work, a bit like a medical residency, measuring understanding instead of velocity, and treating codified context like docs and architectural decisions as infrastructure.

Now look at what Google's cleaner had to do to prove a TODO was dead: read the ticket, trace the file's history, follow changes across years and decide whether the intent behind a comment still holds. That's software archaeology, and it's one of the best ways I know to learn a codebase. Debt cleanup is a ready-made residency rotation, and agents are making it more valuable at exactly the moment juniors are losing the other ways to learn.

---

## What to Do About It

**If someone with no memory of this project read this line today, would they do the right thing?** That someone used to be the new hire. Now it's also every agent your team runs, dozens of times a day.

### If You're an Individual Engineer

* **Stop leaving orphaned TODOs.** If it's real work, it belongs in the tracker with enough context to act on. If it's not, don't write it. A comment should explain why the code is the way it is, not hold a to-do list.
* **Delete as you go.** When you close a ticket, search for references to it and remove them in the same change. It takes seconds:

```bash
# Find every comment that still points at the ticket you just closed
git grep -n "ENG-1234"

# Find out how old a suspicious TODO is before deciding whether to trust it
git blame -L 88,88 src/billing/invoice.ts
```

* **Treat agent detours as bug reports about your codebase.** When an agent wanders off on a side-quest, look at what it read just before. Nine times out of ten there's a note that misled it, and that note will mislead the next one too.
* **Keep your AI instruction files under the same scrutiny as code.** They get read on every run, which makes a stale line in them the most expensive stale line you own.

### If You're a Tech Lead

* **Make legibility a review criterion.** "Does this change leave behind anything that will mislead the next reader?" belongs on the checklist next to "are there tests?"
* **Budget for cleanup and measure it.** Google's numbers give you the argument you never had before: unrequested detours cost real tokens and real developer wait time. Track agent token spend and pull request review time, then run a cleanup and compare.
* **Automate the boring part.** Close the loop between your tracker and your code so closed tickets flag the comments that reference them. Google built [presubmit analyzers, whole-corpus scanners and cleanup agents](https://developers.googleblog.com/agent-distraction/) for exactly this, and it's a great first job for your own agents because the changes are small and cheap to review.
* **Put review capacity where the risk is.** Anthropic's tiered approach is worth stealing - not every change needs the same scrutiny, and pretending otherwise just means everything gets skimmed.

### If You're Hiring or Growing Juniors

* **Give juniors the archaeology.** Assign "prove this TODO is dead" and "is this doc still true?" tickets on purpose. They're low risk, they force people to read history and talk to whoever wrote the original code, and they produce cleanup your agents will benefit from.
* **Ask them to explain, not just ship.** Allan's advice to measure understanding over velocity is easy to apply - ask a junior to walk you through why a piece of code exists before they're allowed to change it.

---

Technical debt used to be a tax only humans paid, and we paid it slowly enough that it was easy to ignore. Now we've added a tireless, literal-minded reader to every team, and it pays that tax on every run and sends the bill to whoever reviews its work. It all boils down to this: **writing code is cheap now, and keeping the codebase honest is where the leverage is.** The engineers and teams who take the boring cleanup seriously are going to get a lot more out of their agents than the ones still waiting for a better model to fix it for them.
