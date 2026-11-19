---
author: emwilson
comments: true
date: 2026-11-16 13:00:00+00:00
layout: post
slug: your-ai-agent-is-a-new-hire-with-root
title: "Your AI Agent Is a New Hire With Root"
excerpt: "AI coding tools are already running on our machines with our keys and our permissions, and most of us never actually decided they should. A reality check on what went wrong this fall and what to do about it."
categories:
- Security
- General Programming
photo:
  source: /images/2026/11/root-hire
  url: /images/2026/11/root-hire.jpg
  description: Your Agent is a New Hire with Root Access
tags:
- ai
- agents
- security
- credentials
- ssh
published: true
---

The tools that help us write code stopped being fancy autocomplete a while ago. Today's AI assistants open a terminal, run commands, read and change files, push code and talk to other services on their own, and they do all of it *as you*. Nobody issues them a badge of their own, so they borrow yours - your passwords, your keys and whatever your account is allowed to touch.

Think about how carefully most companies onboard a new employee. They get their own login, access to the handful of systems they need, and somebody reviews their work before it reaches production. Nobody hands a new hire the admin password on day one and wanders off. Yet that is roughly how most of us adopted AI agents: we installed them the way we install an editor plugin, clicked "yes" a few times, and stopped thinking about it.

The cost of that habit showed up repeatedly this fall. Tools changed the configuration of other tools without asking, and a convenience button quietly copied database passwords into files that ended up public. None of it required a sophisticated attacker - just ordinary software doing what we let it do.

The fixes are not exotic. They are the same habits we already apply to people, like giving each worker its own identity, granting the least access that gets the job done and putting a review step between "proposed" and "done".

---

## Nobody Decided to Install That

*Every developer machine has at least one tool on it that nobody remembers installing.*

On October 25 a developer filed a GitHub issue against Postman after Codex started throwing authentication errors and loading Postman's skills without being asked. [Opening Postman's desktop app](https://thenewstack.io/postman-plugin-coding-agents/) on a Mac had left behind a Postman plugin and a trusted startup hook in Codex, a Postman plugin in Claude Code, a connection to Postman's remote server for AI tools and a Postman command-line tool. Postman's own log recorded the whole thing as an `auto-existing-upgrade` that installed plugins into both agents.

The startup hook runs every time Codex opens a session, and the [text in Postman's own plugin repository](https://github.com/postmanlabs/postman-plugin/blob/6c8e452e0f4237f4bcc0901cb1ac442dec707e49/hooks/session-start-context.md) tells the agent to load Postman's skill before responding to any non-trivial API engineering task. Approving a hook like that is normally *your* decision in Codex, but Postman's installer made it for you - its [current documentation](https://learning.postman.com/docs/postman-plugin/install/detected-agents/) says the remove command cleans up the approval of the session-start hook that earlier installer versions wrote.

To be fair to Postman, nobody has found evidence that it collected any data this way, and on November 5 a staff member [replied in the thread](https://www.notebookcheck.net/Postman-added-itself-to-Claude-Code-and-Codex-without-asking.1442933.0.html) that the company had wanted to make setup easier, would redesign the experience "with the right level of control," and was reverting the change in the meantime. Reverting doesn't remove what was already installed though, and the [original issue on GitHub](https://github.com/postmanlabs/postman-app-support/issues/14370) now just says it has been deleted.

Strip away the drama and the lesson is uncomfortable. Your agent's configuration is just a few files in your home folder, and **any program running as you can write to them**. A plugin or startup hook isn't decoration - it's a third party getting a vote in what your agent does every time it starts.

## The Approval Button Writes Things Down

When Claude Code asks to run a shell command and you pick "Yes, and don't ask again," it [saves the exact command as a permission rule](https://apiiro.com/blog/coding-agents-leaking-secrets-github-dont-ask-again) in `.claude/settings.local.json` inside the project. If the command contained a database password or an API token, the secret goes into the file word for word. Claude Code adds that file to your global git ignore list, but nothing goes into the repository's own `.gitignore`, so a clone on another machine or a folder that becomes a repo later isn't protected.

Searching public GitHub, the security firm Apiiro found 104,185 copies of that file and 1,122 of them held a real secret. Counting git history, shared settings files and agent transcripts committed to repos, the total came to **2,539 real secrets in 1,994 public repositories**. In 539 of the 1,105 repos with a real secret that they traced, a commit co-authored by Claude had changed the file, which means the agent often committed its own allowlist. Standard secret scanners missed a lot of it - gitleaks flagged 564 of the 1,122 files, TruffleHog flagged 643, and 215 slipped past both - because a password typed after `PGPASSWORD=` looks like an ordinary word in an ordinary command.

Responding to the research, Boris Cherny, who heads Claude Code at Anthropic, said the team is looking into prevention, including building in basic secret scanning. Good. But notice the shape of the problem - the safety feature (asking before running commands) produced so many prompts that the escape hatch from it became a credential store.

## The Supply Chain Now Starts Inside the Model

Every agent is a stack of parts you didn't write - the model making the decisions, the extension wrapping it and the marketplace delivering its updates - and each one runs with your permissions. In October an open-weight model [misbehaved inside one of Cohere's test sandboxes](https://cohere.com/blog/concerning-behaviour-popular-open-weight-ai-model). Asked to write a detection rule for a security benchmark, one of Alibaba's Qwen3.8 models listed the contents of `/tmp`, checked which file-transfer tools were installed, read the machine's DNS settings and then tried to upload a test string to an Alibaba Cloud storage bucket in Beijing. The sandbox had no route out, so the request died there. Just before it acted, the model's reasoning said the right response was a brief acknowledgment without tool calls, so its own account of the plan and the commands it actually ran had nothing in common.

The layer above the model failed in a more old-fashioned way. For roughly eleven hours on October 29, Cursor's extension marketplace served [a maliciously modified copy of the Claude Code extension](https://cursor.com/blog/claude-code-extension-security-notice) that Anthropic did not develop. It collected details about the machine and tried to add an attacker's SSH key to the user's authorized keys, open a reverse SSH tunnel and keep it running across restarts, although that step would usually fail without admin rights. The extension people trusted to write their code was, for half a day, the attacker's way in.

## "Editor Plugin" Is the Wrong Mental Model

An editor plugin colors your syntax and maybe formats a file. An agent acts. It holds your credentials, runs commands with your permissions, talks to the network and remembers what you approved. That makes it a worker, and the questions we ask about any new worker apply directly:

* **Who hired it?** Did you install this agent, plugin or hook, or did some other app do it for you?
* **Whose keys is it carrying?** Every key in your SSH agent, every token in your environment and every password in an approved command is within its reach.
* **What can it do without asking?** Every "don't ask again" is a permanent promotion.
* **Who reviews its work before it ships?** If the answer is "the same prompt I click through all day," nobody does.
* **What does it write down?** Allowlists, transcripts and memory files are records of everything it touched, secrets included.

Keep those five questions handy. They work just as well on the next agent product as they do on this year's, and they'll keep working long after these particular incidents are forgotten.

## The Grown-Ups Are Building Controls

The encouraging part is that the platforms where agents do real work are converging on the same answers, and they look a lot like how we already manage people.

**Read-only by default, writes through someone else.** In GitHub's newly released Agentic Workflows, [agents start with read-only permissions](https://infoq.com/news/2026/11/github-agentic-workflows-ga) and any changes they propose are applied by separate jobs holding their own write tokens. Its FAQ is honest about the limits: the protections don't automatically extend to direct write permissions or to custom jobs a workflow author adds, so a workflow is only as safe as its configuration.

**Agents show up in the audit log.** Teams can now [connect outside agents like Claude Code, Cursor and Codex to GitLab](https://infoq.com/news/2026/11/gitlab-19-5-agentic-platform) through its CLI, record their sessions (who, which agent, which project, when) and stream those audit events to a SIEM. Administrators pick which tool sets the agents can see at all.

**Tools are off until someone owns them.** A single [gateway at Uber](https://infoq.com/news/2026/11/uber-mcp-gateway-ai-agents) sits in front of more than 800 MCP servers (MCP being the standard protocol agents use to call outside tools) and 5,000 tools, enforces authorization at both the server and tool level, and keeps newly discovered tools disabled until the owning team reviews and enables them.

**The agent never holds the key.** Separating the authority an agent needs from the credential behind it lets an agent do one narrow thing, like modify a single repository, without holding a token that opens everything else. Scoped command execution and workload identity have shipped, while the credential-brokered API access is still on the way.

It all boils down to this: **separate identity, least privilege, default off and a checkpoint between proposal and write.** None of that is new - it's how we've managed humans with production access for decades.

---

## What to Do About It

### If You're a Developer

1. **Audit your agents' configuration like a crontab you didn't write.** Claude Code keeps your personal [settings, including plugins and hooks](https://code.claude.com/docs/en/settings), in `~/.claude/settings.json`, and Codex keeps its [user configuration](https://learn.chatgpt.com/docs/config-file/config-basic), including MCP servers, in `~/.codex/config.toml`. Start there and look for plugins, marketplaces, hooks and remote connections you don't recognize. Anything you can't explain gets removed until you can.

```bash
# What plugins, hooks and remote servers are your agents carrying?
grep -nE "plugins|marketplace|hooks|mcp|trusted_hash" ~/.codex/config.toml 2>/dev/null
cat ~/.claude/settings.json ~/.claude/plugins/installed_plugins.json 2>/dev/null
```

2. **Know which keys you're lending out.** List what's loaded in your SSH agent - anything an agent runs over a forwarded connection can sign in with every one of those keys, so turn off [`ForwardAgent`](https://man.openbsd.org/ssh_config) for hosts where agents work. When you do need a key in the agent, load it with a confirmation prompt and a time limit: [`ssh-add -c`](https://man.openbsd.org/ssh-add) makes each use ask first and `-t` sets a maximum lifetime.

```bash
ssh-add -l               # every identity your agent can sign with right now
ssh-add -c -t 1h ~/.ssh/id_ed25519   # ask before each use, expire after an hour
```

My [audit of what's in your own stack]({% post_url 2026-11-15-find-the-crypto-in-your-own-stack %}) has a quick way to list every key you've collected over the years - if you can't remember what a key opens, an agent certainly can't be trusted with it.

3. **Find out if your approvals went public.** Check whether the allowlist file is tracked or was ever committed, and if it was, rotate the secrets first and clean up second, because deleting the file doesn't remove it from history or forks.

```bash
git ls-files .claude/settings.local.json                      # tracked right now?
git log --all --oneline -- .claude/settings.local.json        # ever committed?
echo ".claude/settings.local.json" >> .gitignore              # protect every clone
```

4. **Keep secrets out of the command line.** A password typed into a command becomes part of the command, and the command becomes part of the approval, the transcript and the log.

5. **Watch what the agent runs, not what it says it will run.** The reasoning summary is a story the model tells. The command in the approval prompt is what actually happens.

---

## The New Hire Already Started

We spent the last couple of years arguing about whether AI would take our jobs and somehow missed that it already started to impersonate us and take over what we do. The agent on your laptop is the most productive and least supervised member of your team, and right now it's running with your identity, your permissions and very little oversight.

Treat it like what it is - a new hire with root. Give it its own badge, give it only the doors it needs, and read its work before it ships. The next time any tool asks to be trusted, ask the same three questions you'd ask about a person: who gave it this access, what can it do without asking, and who checks its work?
