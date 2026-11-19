---
author: emwilson
comments: true
date: 2026-11-20 13:00:00+00:00
layout: post
slug: runtime-standard-library-is-a-security-feature
title: "Your Runtime's Standard Library Is a Security Feature"
excerpt: "Node.js just absorbed debounce, throttle and PKCS#12 parsing. Every tiny package you stop installing is one less stranger who can push code to your servers. Here's the case for leaning on core, where it falls short, and a script to find what you can drop."
categories:
- Node.js
- Security
photo:
  source: /images/2026/11/runtime-tools
  url: /images/2026/11/runtime-tools.jpg
  description: Your Runtime's Standard Library Is a Security Feature
tags:
- node.js
- npm
- supply-chain
- security
- dependencies
published: true
---

Your runtime's standard library is a security feature.

Almost every app built today is mostly made of code somebody else wrote. You pull in small ready-made pieces (one formats dates, another waits for you to stop typing before it searches) and they come from strangers who publish them to a public library anyone can download from. Each of those strangers can release a new version whenever they like, and if someone steals one of their passwords, the next version is whatever the thief wants it to be. Your build downloads it, your servers run it, and nobody on your team ever reads it.

The pieces that ship with the language itself work differently. The same people who build the platform write them, review them and release them on a published schedule, with a security team on call when something breaks. So every time that built-in toolbox grows, you get to stop trusting a stranger with the keys to your production servers.

This fall the Node.js toolbox grew, and three jobs that used to mean a download now come in the box.

## Small Packages Are Where Attackers Go Shopping

*Everybody has a folder full of other people's code that they've never opened and never will.*

This month alone gave us two reminders. On November 3, attackers [published booby-trapped releases of ten packages](https://www.sonatype.com/resources/videos/polinrider-campaign) on npm, the public registry most JavaScript projects download from, and some of them had gone years without an update before somebody took them over. The malicious code [didn't rely on an install script](https://www.sonatype.com/blog/polinrider-loader-reappears-in-hijacked-npm-packages) (the hook that runs while a package is being installed) - it ran when the app loaded the file, so the old advice of installing with scripts turned off wouldn't have saved you.

The day before, someone [broke into the update system for cPGuard](https://thehackernews.com/2026/11/cpguard-update-channel-hijacked-to.html), a security product for Linux hosting servers, and pushed a script that about 2,950 customer servers ran. It collected password hashes, the keys administrators use to log in as root, and database root passwords. That one isn't an npm package, but it's the same shape of problem: **every dependency is an update channel into your server, and you trust whoever controls the other end.**

None of this is new. In 2016 a developer unpublished his [left-pad package along with 272 others](https://blog.npmjs.org/post/141577284765/kik-left-pad-and-npm), breaking many thousands of projects for a couple of hours until npm took the unprecedented step of re-publishing the original version from a backup. Two years later an attacker [talked his way into maintaining event-stream](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident), added a new dependency, and used it to go after the Copay bitcoin wallet. That's the pattern worth noticing. A small utility becomes so common that everybody depends on it, the platform eventually absorbs it, and the stranger drops out of your supply chain.

(By the way, the left-pad story has a happy ending because the fix arrived in the language itself - [String.prototype.padStart()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padStart) has worked in every major browser since April 2017, and nobody needs to download a package to pad a string anymore.)

## What Node Just Absorbed

[Node.js 26.10.0](https://nodejs.org/en/blog/release/v26.10.0) shipped on September 22 with three additions that each used to mean another entry in your package.json:

* **`util.debounce()`** waits until calls stop for a set time before running your function, which is how you avoid firing a search on every keystroke.
* **`util.throttle()`** caps how often a function can run in a given window.
* **`crypto.parsePKCS12()`** pulls the private key and certificates out of a `.p12` or `.pfx` bundle.

All three were backported to [Node.js 24.22.0](https://nodejs.org/en/blog/release/v24.22.0) on October 14, and on October 28 [Node 26 became an LTS release under the codename Lithium](https://nodejs.org/en/blog/release/v26.13.0), with updates promised through the end of April 2029. You can use them in production today on the two newest long-term support lines.

The pull requests read like a confession from all of us. James Snell [wrote that he'd been pulling debounce from npm](https://github.com/nodejs/node/pull/65899) while testing other work until he realized how useful it would be to just have it built in. Brian Muenzenmeyer's [PKCS#12 pull request](https://github.com/nodejs/node/pull/65627) points out that reading one of these bundles from JavaScript meant shelling out to the openssl command line tool or adding node-forge - even though Node had been parsing PKCS#12 internally for years to support the TLS `pfx` option, and simply never handed the result to your code.

The packages these replace are not niche. At the time of writing, npm shows [lodash.debounce](https://www.npmjs.com/package/lodash.debounce) at about 58 million weekly downloads, [node-forge](https://www.npmjs.com/package/node-forge) about 54 million and [throttle-debounce](https://www.npmjs.com/package/throttle-debounce) close to 11 million. Those counts include every install pulled in by some other package along the way, which is exactly the problem.

Here's what the swap looks like for debounce:

```js
// Before: one more package, one more maintainer account you're trusting
import debounce from 'lodash.debounce';

const save = debounce((doc) => store.write(doc), 500);
editor.on('change', save);
```

```js
// After: built into Node 26.10+ and 24.22+
import { debounce } from 'node:util';

const save = debounce((doc) => store.write(doc), 500);
editor.on('change', (doc) => {
  // The debounced function returns a promise. Handle it, or a failed write
  // (or a later save.cancel()) becomes an unhandled rejection.
  save(doc).catch((err) => log.error('save failed:', err.message));
});
```

That `.catch()` is not decoration, and I'll come back to it. PKCS#12 gets the same treatment:

```js
import { parsePKCS12 } from 'node:crypto';
import { readFileSync } from 'node:fs';

const { privateKey, certificate, additionalCertificates } = parsePKCS12(
  readFileSync('client.p12'),
  { passphrase: process.env.P12_PASSPHRASE },
);
```

You get back a real `KeyObject` and `X509Certificate`, so they plug straight into the rest of `node:crypto` without a format conversion in between.

## "Small Modules Are the Node Way"

They were, and I was there for it. I wrote two books about it.

Node's own documentation used to say it out loud. The `util` docs in the Node 4 era told you that if these functions were lacking, [you were encouraged to write your own utilities, and that the project was not interested in future additions to `util`](https://nodejs.org/download/release/v4.3.1/docs/api/util.html) that Node didn't need internally. A small core with a huge ecosystem around it was the whole philosophy, and for a young platform it worked - npm filled every gap faster than any core team could have.

That philosophy was written for a world where the danger was a package that might break. Today the danger is a package that might get stolen, and those aren't the same risk. A tiny module that does one thing well is still a tiny module with its own maintainer, its own npm account, its own password and its own publishing pipeline. Ten one-function packages are ten separate front doors. **Small modules made each piece easy to understand but made the whole tree impossible to watch.**

## "Userland Moves Faster"

It does, when there's somewhere to move to. Debounce has nowhere left to go - that same lodash.debounce package was last published ten years ago and still pulls tens of millions of downloads a week. That's not a fast-moving project, it's a finished one, and a finished utility that everybody depends on belongs in core.

Speed also cuts the wrong way in an incident. When something goes wrong in core there is a process behind it: Node shipped [security releases for the 26.x, 24.x and 22.x lines on November 11](https://nodejs.org/en/blog/vulnerability/november-2026-security-releases), coordinated, announced and documented. When something goes wrong in a one-person package, you're hoping that one person notices, still has access to their account, and has time this week.

## Core Isn't a Free Lunch

*Every migration guide has a section titled "drop-in replacement" and every one of them is lying a little.*

Before you go deleting packages, there are caveats:

* **`util.throttle` is not lodash's throttle.** It takes [`(fn, limit, interval)`](https://nodejs.org/api/util.html) and behaves like a rate limiter - by default calls over the limit are queued and run later instead of being dropped. If your code assumed extra calls simply vanish, it'll behave differently. There's an `overflow: 'drop'` option, but you have to ask for it.
* **The debounced function returns a promise.** Calling `cancel()` rejects pending calls with an `AbortError`, and if nobody is listening, Node treats that as an unhandled rejection - a fire-and-forget debounced call followed by `cancel()` will crash the process under Node's default settings. Catch the promise, or use `util.markPromiseAsHandled()` - added in the same release - when you genuinely don't care.
* **node-forge does a lot more than PKCS#12.** Its README describes a [full TLS implementation in JavaScript plus a broad set of cryptography utilities](https://github.com/digitalbazaar/forge). If you only use it to crack open `.p12` files, swap it out. If you use the rest, `parsePKCS12()` doesn't change much for you.
* **You only control your direct dependencies.** A copy buried three levels down leaves when the package that pulled it in drops it, and not before.
* **Library authors have to wait.** If your package still supports Node versions older than 24.22, you can't call these APIs without a fallback.

## Let the Maintainers Do the Work

Core isn't magic and it isn't free, but it is **accountable** in a way a ten-year-old one-function package can't be. Your runtime ships with a security team, a release schedule and a published support window, and every dependency you replace with it moves one more piece of your app under that umbrella.

You won't get to zero dependencies and you shouldn't try - npm is still the reason Node won. The goal is narrower than that: when core can do the job, let it, and save your trust for the packages that actually earn it.