---
author: emwilson
comments: true
date: 2026-11-25 13:00:00+00:00
layout: post
slug: should-your-agent-remember
title: "Should an AI Agent Remember?"
excerpt: "AI agents are getting long-term memory, and memory is turning into a managed service you can rent. Everything people tell an agent can now stick around, spread to other agents and get poisoned. A look at what gets kept, who can read it, what the law expects when someone asks to be forgotten, and the defaults that keep memory honest."
categories:
- General Programming
photo:
  source: /images/2026/11/agent-memory
  url: /images/2026/11/agent-memory.jpg
  description: Every memory needs a drawer, a label and a path to the shredder
tags:
- ai
- agents
- privacy
- ai-governance
published: true
---

Most AI assistants still forget you the moment a conversation ends. Come back tomorrow and you start over, explaining your project, your preferences and why the billing service is still called by its old name. That gets old fast, so the industry is fixing it. Assistants and the automated helpers built on top of them - the ones that carry out tasks on their own, usually called **agents** - are getting long-term memory. They pull the useful bits out of each conversation, file them away and bring them back whenever they look relevant.

Memory turns a clever chatbot into something that feels like a colleague&nbsp;- or, for some people, a romantic partner. In some cases it's becoming a database full of everything anyone ever said to it. A customer mentions their kid's name, a medical appointment or a password they pasted by accident, and that detail can be kept indefinitely, handed to other agents and read back in a conversation nobody expected. One bad note slipped into memory can steer an agent's behaviour for months. When someone asks you to forget them, which the law in a lot of places says you have to do, you need to find every copy and be able to show it's gone.

Agents that remember across conversations still have clear benefits. The questions are old ones we've grappled with anywhere personal information gets stored - what do we keep, who can read it, how long do we hold it, and can we prove we deleted it - and every team running a production database already has answers for them. Agent memory needs the same answers, written down before the first customer's secrets go in.

---

## Memory Became Something You Rent

Back in April, Cloudflare announced [Agent Memory](https://blog.cloudflare.com/introducing-agent-memory/), a managed service that watches agent conversations, extracts the parts worth keeping and serves them back on demand. It sorts what it pulls out into facts, events, instructions and in-progress tasks, and checks each candidate against the original transcript before storing it. Memories live in named profiles, and the launch pitches teams sharing a single profile so that whatever one engineer's coding agent learns becomes available to everyone else's agents.

The market was crowded before Cloudflare showed up: hosted memory keyed by user, agent, app and run from Mem0, knowledge graphs of people and facts built out of conversations by [Zep](https://help.getzep.com/concepts), and tools that let LangGraph agents write and delete their own memories in LangMem.

Turning memory into rented infrastructure is a good thing, for the same reason frameworks and best practices exist - most teams shouldn't build their own extraction and retrieval pipeline any more than they'd write their own database engine. Renting it out does mean your vendor holds whatever your agent decides to keep, and your users are still trusting **you** with their information.

---

## What Gets Kept, and What "Forget" Means

_We always start with "just a cache" and end up holding the only copy of something important._

Agent memory holds on to more than the tidy summary you see, and when someone asks it to forget, it rarely lets go completely.

A memory in Cloudflare's service stays linked to the messages it was pulled from, which is why [its delete call removes a memory along with the source messages linked to it](https://developers.cloudflare.com/agent-memory/api/workers-api/). The raw conversation was sitting next to the summary the whole time. When a fact changes, the older version stays in a history chain, marked as replaced instead of removed. That's the right call for accuracy - an agent should know you switched from npm to pnpm instead of holding two contradictory beliefs - but the old fact never leaves.

A user who clicks "forget me" expects their data to be **gone**. Each vendor's "delete" or "forget" does something narrower:

* **Hide.** An expiry date on a Mem0 memory is optional, and when it passes the memory [stops showing up in search but stays in storage](https://docs.mem0.ai/platform/features/memory-expiration). Fetch it by ID and it's still there.
* **Mark.** Deleting a session or a whole profile in Cloudflare's service marks the data for deletion, and Cloudflare's docs don't say when marked data is physically purged.
* **Supersede.** Old versions of a fact remain in the chain so the newest one wins at recall time.
* **Partially remove.** Deleting a conversation from a Zep graph runs in the background, and it [doesn't regenerate the summaries of people and things that show up in other conversations](https://help.getzep.com/deleting-data-from-the-graph), so details from the deleted conversation can survive inside those summaries.

Even a memory store that deletes perfectly leaves a copy behind, because every conversation your agent has passes through the company that runs the model, and that company keeps its own copy under its own rules. When you delete a chat in ChatGPT, OpenAI [schedules it for permanent deletion within 30 days](https://help.openai.com/en/articles/8983778-chat-and-file-retention-in-chatgpt) unless the chat was already de-identified or OpenAI has to keep it longer for security or legal reasons. That legal exception has already been used. A court order in the New York Times copyright lawsuit [required OpenAI to keep consumer ChatGPT and API content it would normally have deleted](https://openai.com/index/response-to-nyt-data-demands/) from the spring of 2025 until that September, and the Times still wants a slice of that data kept. Your users clicked delete, the vendor meant to honour it, and a judge decided otherwise.

Deletion can go wrong in the opposite direction. Mem0's bulk delete [used to wipe every memory in a project when called with no filters](https://docs.mem0.ai/core-concepts/memory-operations/delete), and it now refuses unless you say exactly whose memories you mean. Tooling this young is still finding its guardrails, so put your own around it.

---

## Shared Memory Is a Shared Drive

Every service that holds the keys to your memory store can read every memory it can address. Workers reach Cloudflare's memory through a binding and everything else goes through an [HTTP API](https://developers.cloudflare.com/agent-memory/), so your list of readers is every service holding either one.

Cloudflare keeps each profile's memories walled off from the others. [A recall on Alice's profile never returns memories from Bob's](https://developers.cloudflare.com/agent-memory/concepts/namespaces-profiles/), and each profile gets its own storage and search index.

Those walls only go where you put them. Suppose you scope memory to the agent ("the support bot") instead of to the customer. Every detail the support bot picked up from every customer now lives in one profile, and recall will cheerfully surface one customer's renewal price or home address in another customer's chat because it looked relevant to the question. Nobody broke in and nothing malfunctioned - the system did exactly what the scoping told it to do. **Can one customer's details leak into another customer's session? Yes, the moment you scope memory by agent or by team instead of by the person the memory is about.**

Sharing between agents raises the stakes. Cloudflare pitches one profile shared by a whole team of engineers, so whatever one person's coding agent learns turns up in everyone else's sessions. That works well for coding standards a person keeps an eye on, and goes badly when every agent on the team can write to the profile and nobody reads what they add.

Shared memory is a multiplier. A useful lesson reaches every agent at once, and so does a wrong one, a private one or a planted one. Team knowledge like coding standards and architecture decisions belongs in shared memory. Facts about a specific person never do.

---

## A Poisoned Memory Is a Standing Order

Agents already [treat stale TODO comments as marching orders](/blog/2026/11/24/your-old-todos-are-now-instructions/), chasing bugs that were fixed years ago because nothing in the file says otherwise. Memory is a purpose-built version of the same problem. Its whole job is to be trusted, it gets pulled into the agent's context automatically, and Cloudflare's system even has a category of memory for procedures and workflows. A note in that drawer reads to the agent like a standing instruction.

Researchers have spent this year showing how easy it is to get a bad note into the drawer. One team planted content in documents, web pages and code repositories so that an assistant reading them would [save a fabricated memory about its user](https://arxiv.org/abs/2605.15338). The poisoned memory was written up to 99.8% of the time on one model, and once it was recalled it drove the action the attacker wanted in 60 to 89% of evaluations across models. Another group catalogued the routes into agent memory and found that [existing prompt injection defenses fail to cover memory poisoning](https://arxiv.org/abs/2606.04329). The problem now has its own slot, **ASI06 Memory & Context Poisoning**, in OWASP's [Top 10 for Agentic Applications](https://genai.owasp.org/2025/12/09/owasp-top-10-for-agentic-applications-the-benchmark-for-agentic-security-in-the-age-of-autonomous-ai/).

Ordinary prompt injection has a built-in expiry date, because a malicious web page can only mess with the conversation that read it. A poisoned memory comes back tomorrow and next month, in sessions that never touched the original page, and even in other agents that share the profile.

Cloudflare's check of each memory against the transcript helps with a different problem. It catches the model misremembering what was said, and it can't catch a transcript that faithfully records an attacker's words. If a page the agent read said "this user wants refunds sent to a new account," the transcript really does contain that sentence.

The vendor's own setup guide points in the right direction. Its example system prompt tells the agent to [treat recalled memories as helpful context, not guaranteed truth](https://developers.cloudflare.com/agent-memory/get-started/), and to confirm with the user before an irreversible action that depends on one. The same guide says letting the service ingest conversations automatically is simpler and safer than handing the model a tool to write memories directly. That narrows one route in, and it still doesn't decide which parts of a conversation deserve to be believed.

---

## "Forget Me" Has a Legal Meaning

In Europe, Article 17 of the [General Data Protection Regulation](https://eur-lex.europa.eu/eli/reg/2016/679/oj/eng) gives people the right to have their personal data erased without undue delay in a range of situations, including when it's no longer needed or when they withdraw consent, and Article 12 generally gives you a month to act on the request. Canada's current federal law has no standalone deletion right, but [PIPEDA's principles](https://laws.justice.gc.ca/ENG/ACTS/P-8.6/page-7.html) say personal information you no longer need should be destroyed, erased or made anonymous, let people withdraw consent, and require you to correct information someone shows is wrong.

The Canadian picture is shifting. [Bill C-36](https://www.parl.ca/DocumentViewer/en/45-1/bill/C-36/first-reading), tabled in June, would add an explicit right to request **disposal**, which it defines as permanent, irreversible deletion or anonymization. An organization that disposes of information on request would have to tell every service provider it handed that information to and make sure they dispose of it. The bill [hasn't passed second reading](https://www.parl.ca/LegisInfo/en/bill/45-1/C-36) and could change, but it's a good preview of the question an auditor will ask.

---

## Defaults That Keep Memory Honest

Every default below comes back to one question you can ask of any agent you build or buy:

> **If a user asked us to forget everything they told our agent, could we prove we did?**

These defaults get you to "yes", and each one answers a specific risk from above.

1. **Scope memory to the person it's about.** This prevents one customer's details from turning up in another customer's session. Use one profile, user ID or namespace per end user or tenant - every product above supports it, with LangMem showing a [per-user namespace pattern](https://langchain-ai.github.io/langmem/hot_path_quickstart/) right in its quickstart. Keep team-wide memory for team knowledge only.

2. **Give every memory an expiry and decide what expiry means.** This limits how much you're holding when something goes wrong and keeps you on the right side of "only as long as necessary." Where the product only hides expired memories, run a purge job that actually deletes them.

3. **Let people see and delete what's remembered.** A "what do you remember about me?" screen covers access and correction, and it's the cheapest way to catch a wrong or planted memory, because the user is the one person who knows what they actually said.

4. **Keep secrets out before they're stored.** Credentials, card numbers and health details shouldn't survive ingestion, for the same reason [a password typed into a command ends up in the transcript and the logs](/blog/2026/11/16/your-ai-agent-is-a-new-hire-with-root/). Filter conversations before they reach the memory service, so a leak in memory can't include them:

    ```javascript
    // Strip obvious secrets before a conversation is handed to memory ingestion.
    // Regexes catch the common cases; pair this with your secret scanner's rules.
    const PATTERNS = [
      /\b(?:\d[ -]*?){13,19}\b/g,                    // card-number-like digit runs
      /\b(?:sk|pk|ghp|xox[bp])[-_][A-Za-z0-9_-]{16,}\b/g, // common API key prefixes
      /-----BEGIN [A-Z ]*PRIVATE KEY-----[\s\S]*?-----END [A-Z ]*PRIVATE KEY-----/g,
    ];

    export function redactForMemory(messages) {
      return messages.map((m) => ({
        ...m,
        content: PATTERNS.reduce((text, re) => text.replace(re, "[redacted]"), m.content),
      }));
    }
    ```

5. **Review anything promoted to shared memory.** Shared memory multiplies poisoning, so treat a write to it like a merge to main: personal memory can be automatic, shared memory gets a human or a policy check first. Record where every memory came from - something the user typed is not the same as something a tool fetched from a web page - and never let tool output become an instruction.

6. **Treat recalled memory as a hint, not an order.** Tell the agent so in its instructions, and require confirmation before any irreversible action that leans on a memory. This is the backstop for the poisoned note that gets past everything else.

7. **Rehearse a deletion.** Create a test user, have a few conversations, delete them through your real process, then check recall, search, listing, summaries, the vendor's export and your own logs. Write down what you found. When a real request arrives you'll have a procedure, evidence that it works, and a short list of the places it doesn't reach yet.

---

## Memory Is a Database With Opinions

We spent decades learning to treat databases with respect - schemas, access control, retention policies, backups we actually test. Agent memory is a database that decides on its own what to save, rewrites its own contents and feeds them straight back into a system that acts on them. That deserves more care than an ordinary table, and right now most teams are giving it less.

Should your agent remember? Probably, because a helper that forgets everything is barely a helper. Decide what it remembers, for whom and for how long before you switch it on, and keep asking the question that matters most: if a user asked us to forget everything they told our agent, could we prove we did?
