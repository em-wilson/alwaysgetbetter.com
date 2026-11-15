---
author: emwilson
comments: true
date: 2026-10-25 12:00:00+00:00
layout: post
slug: how-public-key-cryptography-works
title: "How Public-Key Cryptography Works"
excerpt: "Every HTTPS page and SSH login starts with two strangers agreeing on a secret in public. How that works and why a quantum computer breaks it."
categories:
- Security
- General Programming
tags:
- cryptography
- post-quantum
- security
- tls
- ssh
published: true
---

Every time you log into your bank, push code or install an update, your computer is trusting a stranger over wires that belong to coffee shop routers, internet providers and companies in countries you've never visited. Anyone along that path can copy what goes by, so the real puzzle is how two strangers agree on a secret while the whole world listens in.

Your passwords and your customers' card numbers travel through hands you don't control. If the secret leaks someone reads along, and if the stranger isn't who they claim to be, someone gets to pretend to be your bank, your server or you.

That puzzle was solved decades ago with an idea simple enough to explain with a padlock, and the part now wearing out is small enough to find and replace.

## The Open Padlock

Imagine I hand out open padlocks to anyone who asks. You put a message in a box, snap one of my padlocks shut and mail it back - closing it needs no key but opening it needs the one I keep in my pocket, so anybody can watch that box travel and nobody but me can read what's inside.

Every person or server gets a matched pair: the padlock, called the **public key**, which they hand out freely, and the **private key**, which never leaves home. Studying the padlock doesn't help you build the key.

The analogy breaks in two places: these padlocks are only as strong as a math problem we'll get to shortly, and a padlock can't prove who you are, so for that job the picture has to run backwards.

## Job One: Agreeing on a Secret

The first job is letting two strangers hold the same secret without sending it in the open, say by my browser locking a fresh random key in a box with the server's padlock. Modern systems use a cleverer exchange where the finished secret never travels at all, but the result is the same: a shared secret an eavesdropper can't rebuild. This is called **key exchange**. [This video is a fantastic visual explanation](https://youtu.be/aoag03mSuXQ?t=724) of how two servers negotiate a secure connection without exposing their secrets to the outside world or to each other.

Key exchange protects privacy, so if it fails, everything said afterward is readable by whoever was listening.

## Job Two: Proving Who You Are

*A secret shared with the wrong person is worse than having no secret at all.*

The second job is identity, and here the padlock runs backwards: only I can produce a **signature** with my private key, and anyone holding my public key can check it. Change one byte of the signed message and the check fails.

Skipping that check is how, in 2015, [malware shipped inside App Store apps used by more than 500 million people]({% post_url 2015-11-13-malware-itunes-store %}) - developers tired of waiting on a 3GB download from Apple grabbed an unsigned copy of Xcode from file-sharing sites, and every app they built carried the infection.

To know a public key really belongs to your bank, you need a **certificate**: a public key plus a name, signed by an organization your browser already trusts.

That makes a certificate exactly as trustworthy as whoever signed it, and your browser trusts a long list of signers you've never heard of. In October 2026 an Indian certificate authority called eMudhra turned out to have [private keys so weak](https://www.helpnetsecurity.com/2026/10/21/emudhra-certificates-private-keys-compromised/) that anyone could recover them from its public certificates in under a second - one of them was literally the number 4 - which meant an attacker could forge a certificate for any website. Chrome, Firefox, Apple and Microsoft blocked the certificates within days and eMudhra walked away from the certificate business, but nothing about the padlock itself would have warned you. **Trust in a signature is borrowed from the signer, so when the signer fails, every certificate it vouched for fails with it.**

Signatures protect against impersonation, so if they break, someone can pose as your bank, your server or your software vendor.

## Why It Only Opens the Conversation

Public-key math is expensive - encrypting data with it [generally takes about a thousand times longer](https://www.open.edu/openlearn/digital-computing/network-security/content-section-4.6) than with **symmetric encryption**, the ordinary kind where both sides use the same key (AES is the name you'll see in configuration files). So real systems use the padlock once to agree on a key and check identity, then switch to fast symmetric scrambling.

TLS, the protocol behind HTTPS, works exactly this way. [The current standard](https://www.rfc-editor.org/info/rfc9846/) describes a handshake that authenticates both sides and establishes shared keys, followed by a record layer that protects every piece of traffic with those keys. **The padlock does the introductions while the scrambling does the talking, and the scrambling is only as safe as the key those introductions produced.**

## Where You Meet It Every Day

The padlock in your browser's address bar is the obvious one. When I wrote about [free certificates on CloudFront]({% post_url 2016-02-06-https-on-static-sites-using-cloudfront %}) in 2016 I sold HTTPS mostly as a signal that you'd thought things through, which undersells it - every HTTPS visit does a key exchange and checks a certificate before the page arrives, so that little lock means nobody on the network can read the page or pose as the site.

With SSH, your `id_ed25519.pub` is a padlock you've copied onto servers, and the server proves itself with its own host key, which is what that "authenticity of host" prompt asks you to confirm.

The rest is quieter. On Debian and Ubuntu, [apt checks a signature on the repository index](https://manpages.debian.org/unstable/apt/apt-secure.8.en.html) and refuses unsigned archives by default, which is the check those Xcode downloads skipped. A login token signed with `RS256` or `ES256` uses [RSA or elliptic-curve signatures](https://www.iana.org/assignments/jose/jose.xhtml), so whoever can forge one can log in as anyone, while `HS256` is a shared-secret HMAC and a different animal altogether.

To see a padlock for yourself:

```bash
# Make a throwaway key pair in the current folder (no passphrase - demo only)
ssh-keygen -t ed25519 -C "me@laptop" -f ./demo_key -N ""

# Print the public half: the padlock you hand out
cat demo_key.pub

# Print its fingerprint: a short summary you can compare by eye
ssh-keygen -lf demo_key.pub
```

The first command writes `demo_key`, the key that stays in your pocket, and `demo_key.pub`, the padlock. The second prints a line like `ssh-ed25519 AAAAC3Nza... me@laptop` that's safe to paste anywhere, and the third prints something like `256 SHA256:AjUq... me@laptop (ED25519)`: key size, fingerprint and type. **The type is the part worth noticing** because it tells you which math problem guards that door, and the [current ssh-keygen manual](https://man.openbsd.org/ssh-keygen) already lists a hybrid post-quantum type, `mldsa44-ed25519`. Delete both files when you're done.

## Easy to Do, Hard to Undo

Every public-key system rests on a problem that's easy one way and brutally hard to undo. RSA is built on multiplication: multiplying two enormous primes takes a blink, but finding them again from the product alone would take today's best machines impractically long. Elliptic-curve cryptography (ECDSA, ECDH, Ed25519, X25519) uses a different one-way street built on points on a curve, getting the same strength from much smaller keys.

Think of how a correction works when you're parenting. It only holds if enforcing it costs you less effort than challenging it costs your kid, because a rule that takes an hour of arguing to uphold gets dropped by dinnertime while one you can enforce with a raised eyebrow sticks. Public-key cryptography runs on the same economics: generating a key, signing a message and checking a signature stay cheap for the honest side, while undoing any of it costs an attacker so much that challenging it isn't worth the prize.

A kid eventually tires out, though, and a math problem never does. **The private key can be worked out from the public key in principle, and the only thing stopping anyone is the cost of the work** - so if someone finds a shortcut, every padlock built on that problem opens at once.

## What a Quantum Computer Changes

That shortcut is exactly what a large quantum computer would provide. A known quantum algorithm solves both the factoring problem and the curve problem efficiently, which is why the UK's NCSC expects [most traditional public-key algorithms to be vulnerable](https://www.ncsc.gov.uk/whitepaper/next-steps-preparing-for-post-quantum-cryptography) once such a machine exists. Symmetric encryption fares far better - the same guidance says AES with 128-bit or longer keys can stay in use, and [Cloudflare](https://blog.cloudflare.com/pq-2025) says the quantum speedup doesn't even practically force bigger keys.

When I [argued for SSH keys over passwords]({% post_url 2011-04-26-protect-ssh-server-rsa-keys %}) back in 2011, my reasoning was that a long key can't be guessed from a dictionary and that as computers got faster we'd just make keys longer. That held up for fifteen years against ordinary computers, but a quantum computer doesn't guess faster - it takes the shortcut - so the fix is different math rather than longer keys.

The catch is that recordings keep - someone who copies your encrypted traffic today can wait for a capable machine and read it then. NIST calls this **harvest now, decrypt later**, and the NCSC says organisations with data that needs long-term protection should treat a future quantum computer as a threat today.

A recording is scrambled with a key the handshake produced, so a quantum computer that breaks the handshake recovers that key and unlocks everything captured with it. That makes key exchange the urgent half, and the fix is [hybrid key exchange]({% post_url 2026-11-10-how-hybrid-key-exchange-works %}), which runs the old math and the new quantum-resistant math side by side so an attacker has to break both.

Signatures have more runway because they can only be forged once the machine exists, though Cloudflare notes they're slower to migrate. The replacements for both jobs already exist: [NIST published its first post-quantum standards in August 2024](https://csrc.nist.gov/projects/post-quantum-cryptography) and plans to remove the vulnerable algorithms from its standards by 2035.

## Good Enough Has an Expiry Date

In the late 90s - before version control, back when websites still had 'under contstruction' signs with animated worker gifs - I was building _A Dark Portal MUD_ in glorious C over a telnet connection with the server it was hosted on. Our host rightfully set a deadline for us to move to SSH, which I found challenging to find a decent free client for on my Windows 98 connection. It felt like a lot of hoops to jump through to keep a text-based game alive. In those days the idea of hackers infiltrating systems minutes after connecting to a network seemed like stuff out of science fiction even though it was already happening.

The padlocks we built kept logins, card numbers and server sessions private for a very long time, and the engineers who chose them in the 2000s made a sound call with what they had to work with. Being secure has always meant being secure against the attackers and machines of the day, and the day keeps changing whether anyone is watching or not. Passwords gave way to keys and plain HTTP gave way to HTTPS, and each switch looked like overkill to somebody right up until it didn't.

The 2030s will have their own version of this. The new math is young, the migration will be uneven, and a good share of what we deploy this decade will probably need replacing again once somebody finds the next shortcut, which is (of course) how this field has always worked.
