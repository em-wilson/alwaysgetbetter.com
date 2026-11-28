---
author: emwilson
comments: true
date: 2026-11-27 13:00:00+00:00
layout: post
slug: a-little-copying-is-better-than-a-little-dependency
title: "A Little Copying Is Better Than a Little Dependency"
excerpt: "Every package you install is a stranger who can push new code to your servers, and attackers keep proving it. Now that AI can write a twenty-line helper in seconds, the tiny dependency has to earn its place. Here's when copying wins, when it really doesn't, and what you take on when you copy."
categories:
- General Programming
photo:
  source: /images/2026/11/copy-dependency
  url: /images/2026/11/copy-dependency.jpg
  description: One small gear you made yourself beats a ceiling full of strings you didn't
tags:
- dependencies
- npm
- supply-chain
- maintenance
published: true
---

> A little copying is better than a little dependency.
>
> \- Rob Pike, [Go Proverbs](https://go-proverbs.github.io/)

When you build an app, you rarely write every piece yourself. You install small ready-made pieces of code from public libraries that anyone can publish to&nbsp;- one formats a date, another splits a list into pages&nbsp;- and from then on your app depends on them. Those pieces come from strangers. Each stranger can publish a new version whenever they like, and your build pulls it in and your servers run it, usually without anyone on your team reading a line.

You don't have to take that deal every time. If you only need twenty lines, you can write them yourself or copy them into your project, read them and put a test around them. From then on they only change when someone on your team changes them.

There are good reasons we stopped copying code. Don't Repeat Yourself (DRY), the principle that every piece of logic should live in exactly one place, saves you from the classic regression where someone fixes a bug in one copy and the other three copies keep it. Followed blindly, though, DRY becomes a reflex to install a package instead of writing ten lines, and that reflex fills your project with dependencies that don't serve you. Copying a helper once into your own shared module doesn't break DRY at all, because there's still exactly one copy&nbsp;- it just lives inside your walls instead of a stranger's.

Choosing between the two takes critical thought, and for years effort tipped the scales toward the package. Why spend an afternoon writing and testing a helper when someone already published one? That argument is mostly gone. An AI assistant will write those twenty lines in seconds, and the cost to you is the ten minutes it takes you to read them. **When the code is that cheap to write, the dependency has to justify itself more than ever.**

---

## Every Dependency Is a Door Someone Else Holds the Key To

*Everyone has a project with a package in it that nobody on the team can explain.*

On November 17, attackers [published two poisoned releases of Microsoft's official agent-framework package](https://github.com/advisories/GHSA-764m-3ppc-5m73) on PyPI, the public registry where Python developers get their libraries. This wasn't a look-alike name waiting for someone to make a typo. It was the real package under its real name, and the [two releases went up 33 minutes apart](https://www.endorlabs.com/learn/agent-framework-compromised-software-supply-chain-attack).

The malicious code didn't wait to be used. Python runs a certain kind of startup file on every launch, so the stealer ran whenever Python started in that environment, even if your code never imported the framework. It went hunting for cloud and CI credentials: Azure tokens, Azure DevOps pipeline tokens, GitHub Actions tokens in the second release, and files like `.npmrc`, `.git-credentials` and Docker config. Meanwhile [the framework kept working normally](https://www.ox.security/blog/breaking-hijacked-releases-of-microsofts-official-agent-framework-steals-azure-and-ci-credentials/), because both releases still pulled in the genuine core package. Nothing broke, so nothing looked wrong.

Both versions were gone from PyPI within an hour but an hour was plenty of time to do damage. Anyone who ran an unpinned install in that window got a malicious version, and that includes every CI job that rebuilt its environment from scratch.

How the attacker got in is still being worked out. The leading theory is that the attacker [used a stolen client certificate and key](https://www.wiz.io/blog/malicious-version-of-microsofts-agent-framework-published-to-pypi) for the pipeline Microsoft uses to sign and publish the package.f

None of this is unusual anymore. The Shai-Hulud worm first tore through npm, JavaScript's public registry, in September 2025, and [compromised over 500 packages](https://www.cisa.gov/news-events/alerts/2025/09/23/widespread-supply-chain-compromise-impacting-npm-ecosystem) by stealing developer tokens, logging in as each victim and publishing poisoned versions of everything that developer maintained. It came back in [at least seven waves or offshoots through October 2026](https://socket.dev/glossary/shai-hulud). The August wave alone [compromised more than 1,300 package versions with a combined 2 billion monthly downloads](https://www.csa.gov.sg/alerts-and-advisories/advisories/ad-2026-009/), and the newest variant plants hooks in editor and AI-assistant settings files that run when someone simply opens the project.

Look at what these attacks have in common. None of them needed a bug in your code. They needed you to install a new version of somebody else's code without reading it, which is exactly how dependencies are designed to work. **A dependency isn't a fixed block of code&nbsp;- it's a standing agreement to run whatever its publisher ships next.** A function you copied into your own repository can't be swapped out by a stranger at 20:35 UTC on a Tuesday.

[Leaning on your runtime's standard library](/blog/2026/11/20/runtime-standard-library-is-a-security-feature/) shrinks your list of strangers when the platform already does the job. Copying handles the small things the platform doesn't cover.

## The Twenty Lines Got Cheap

My early days of programming were spent in the source code of a MUD, one of the text-based multiplayer games people played over the internet before graphics took over. It was written in C, and everything it depended on came with it. The networking code and the memory management lived right there in the source, and anything else came in through header files. There was no package manager and no list of dependencies to audit, because there was nothing to download. The whole game was portable and easy to reason about, since every line it ran was a line we could open and read.

Nobody needs to go back to writing their own memory management, but it's worth remembering that an app with nothing to download used to be the normal state of things.

The Go proverb was always a trade. Copying cost you time, and the dependency cost you trust. For most of the npm era the time felt expensive and the trust felt free, so we installed packages for everything.

AI flipped the price tags. Ask any coding assistant for a function that chunks an array, retries a promise with backoff or turns a title into a URL slug, and you'll have it before you finish your coffee. Trust only got pricier, because attackers now automate their side of the work.

So the question changes. It used to be "is this worth writing myself?" Now it's "**would I approve a pull request that added this code directly?**" If the answer is yes, you already trust the code more than you trust the stranger, and there's no reason to import their copy of it.

There's one catch. AI-written code isn't automatically *your* code. Assistants sometimes reproduce chunks of public code close to word for word, and those chunks carry somebody's licence. GitHub's coding agent now [flags generated code that matches a public repository](https://github.blog/changelog/2026-02-18-copilot-coding-agent-supports-code-referencing/), with a link to the original so you can see which licence applies. Copying responsibly means copying the paperwork along with the code.

## When Copying Wins

Copying is the right call when the code is small, stable and boring. In practice that means three kinds of code:

* **Tiny utilities.** Array chunking, clamping a number, deep-equal for plain objects, a sleep helper. If you can read the whole thing on one screen and explain every line, a package adds risk and gives you nothing in return.
* **Stable algorithms.** Levenshtein distance, binary search, a topological sort, the Fisher-Yates shuffle. These were finished decades ago. The code isn't going to improve, so the only thing a new release can add is a surprise.
* **Code you'd accept in a pull request anyway.** If a teammate wrote it and you'd approve it after a normal review, it's fine as your own code. The package author has no special authority over twenty lines that you've read.

Here's what copying responsibly looks like. The comment at the top is not decoration:

```js
// Adapted from <package-name>@<version> - <link to the exact file and commit>
// Copyright (c) <year> <author>. MIT License; full text in THIRD_PARTY_LICENSES.md
// Changes from upstream: removed the options object, throw on size < 1.
// Last compared with upstream: <date>
export function chunk(items, size) {
  if (!Number.isInteger(size) || size < 1) {
    throw new RangeError(`chunk size must be a positive integer, got ${size}`);
  }
  const out = [];
  for (let i = 0; i < items.length; i += size) {
    out.push(items.slice(i, i + size));
  }
  return out;
}
```

```js
// chunk.test.js - the tests are how you prove you actually understood it
import { chunk } from './chunk.js';

test('splits evenly', () => expect(chunk([1, 2, 3, 4], 2)).toEqual([[1, 2], [3, 4]]));
test('keeps the remainder', () => expect(chunk([1, 2, 3], 2)).toEqual([[1, 2], [3]]));
test('rejects nonsense sizes', () => expect(() => chunk([1], 0)).toThrow(RangeError));
```

The header records where the code came from, under what licence and what you changed. The tests prove that somebody on your team read it closely enough to know how it should behave at the edges. Both protect you later, when someone asks whether this function is safe to touch.

## When the Dependency Wins

*Every developer who has written their own date parser has a story about February.*

Some code you should never copy, and never let an AI write for you from scratch. **Code that faces attackers or follows a spec written by a committee belongs to the people whose full-time job it is.**

* **Cryptography.** Hashing passwords, encrypting data, signing tokens, comparing secrets in constant time. The difference between correct and catastrophically wrong is invisible in a code review and invisible in your tests. Use your platform's crypto library or a well-maintained one, and keep it updated.
* **Parsers.** HTML sanitizers, XML, YAML, URL parsing, CSV with quoting, anything that reads input a stranger controls. Attackers spend their days crafting the input you never imagined, and a popular parser has already taken years of those hits and been patched after each one.
* **Protocols.** HTTP clients, TLS, OAuth flows, email handling, database drivers. The rules for each one fill documents hundreds of pages long, and a good library gets updated every time those documents do.
* **Anything with a security patch stream.** If a package has a history of security advisories, that history is the point. Somebody is finding the bugs and someone is fixing them, and when you depend on the package you get those fixes. When you copy it, you don't.

The clearest framing of this trade-off is still [Our Software Dependency Problem](https://research.swtch.com/deps), written by Russ Cox while he led the Go project. He frames the cost of a dependency as the damage it could do multiplied by how likely that damage is, and he recommends checking a package's tests, maintenance, security history and licence before adopting it. A crypto library can do enormous damage whether you copy it or depend on it, but a maintained one cuts the odds dramatically. A one-line string helper has almost no upside to offset even a small chance of a hijacked release.

## You Own the Bugs Now

Copying isn't free, and the cost doesn't show up until something goes wrong.

When a dependency has a vulnerability, a whole machine kicks in. Someone files an advisory, the advisory goes into a database, and tools like Dependabot compare it against your project's manifest and lock files to tell you which packages are affected. GitHub's [dependency graph parses the package manifest files in your repository](https://docs.github.com/en/code-security/reference/supply-chain-security/troubleshoot-dependabot/vulnerability-detection) to work out what you depend on.

Copied code isn't in any manifest, so no scanner knows it came from somewhere. If the original had a bug&nbsp;- a regular expression that hangs on a crafted string, an off-by-one on empty input&nbsp;- and upstream fixes it next spring, **nobody sends you an advisory.** You find out the way you'd find out about any other bug in your own code: in production, at a bad time.

So when you copy, take on the job the package manager was doing for you:

1. **Record the origin in the file.** The package, the version, the exact commit link and the date. This is how anyone (including future you) can go back and check whether upstream changed.
2. **Write the tests.** If you can't write tests for it, you don't understand it well enough to own it, and you should keep the dependency.
3. **Keep a list.** A short `THIRD_PARTY.md` listing every copied snippet and where it lives turns an invisible risk into one you can review in five minutes.
4. **Recheck on a schedule.** Once or twice a year, compare each copied piece with its upstream. For twenty lines of stable code this takes minutes, and usually nothing has changed.

Each of those steps replaces a piece of the warning system you gave up when you stopped depending on the package.

## Copy the Licence Along With the Code

Open source code is free to use, but it isn't free of conditions. When you copy code you take on its licence terms the same way you would if you installed it, except now nothing does the bookkeeping for you.

The common permissive licences ask for very little, but they do ask:

* **MIT** requires that [the copyright notice and permission notice be included in all copies or substantial portions of the software](https://opensource.org/license/mit). Keep the notice. A single `THIRD_PARTY_LICENSES.md` with the full text, plus a pointer in the file header, covers it.
* **Apache 2.0** [asks for more in section 4](https://www.apache.org/licenses/LICENSE-2.0): include a copy of the licence, mark the files you changed, keep the original copyright and attribution notices, and carry along anything in the project's `NOTICE` file.
* **Copyleft licences** like the GPL are a different conversation. Copying GPL code into a closed-source product can obligate you to release your own code under the same terms. If the snippet you want is GPL and your project isn't, write your own from scratch or keep it as a dependency, and talk to whoever handles licensing at your company.

The same rules apply to code an AI assistant hands you. If your tooling says a suggestion matches a public repository, treat it exactly as if you'd copied it from that repository, because effectively you did.

## Copying Has a Ceiling

The proverb says *a little* copying, and the word "little" carries most of the weight.

Copy too much and you've built a private fork of half of npm that nobody on your team maintains. Every copied piece is a promise to keep it working, and promises add up. Russ Cox's essay argues that delaying upgrades is often riskier than taking them, and a pile of copied code is one more way to delay upgrades forever.

Dependencies do things copying can't. They let a thousand teams share one fix. They give a security researcher one place to report a bug. A well-run project with a release process, signed builds and a security contact is one of the best deals in software, and abandoning it to copy-paste the parts you use would throw that away.

The goal isn't zero dependencies. The goal is that **every dependency you keep is one you chose on purpose**, for something you couldn't reasonably own yourself&nbsp;- the crypto, the parser, the protocol&nbsp;- and not for twenty lines you could have read before lunch.

Next time you reach for `npm install` or `pip install` for something small, stop and ask:

> Would I approve a pull request that added this code directly? If yes, why am I importing a stranger's version of it?

If you'd approve the pull request, copy it, credit it, test it and own it. If you wouldn't&nbsp;- because it's too big, too subtle or too dangerous to get wrong&nbsp;- then you've found a dependency that earns its place.
