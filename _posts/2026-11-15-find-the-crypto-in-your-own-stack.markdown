---
author: emwilson
comments: true
date: 2026-11-15 13:00:00+00:00
layout: post
slug: find-the-crypto-in-your-own-stack
title: "Find the Crypto Hiding in a Web Stack"
excerpt: "Post-quantum migration starts with a boring question: what cryptography are you actually using? Here's a one-afternoon audit you can run on your own servers, repos and keys."
photo:
  source: /images/2026/11/crypto-audit
  url: /images/2026/11/crypto-audit.jpg
  description: Crypto Audit
categories:
- Security
- General Programming
tags:
- post-quantum
- cryptography
- security
- tls
- ssh
published: true
---

Nearly everything private you do online is protected by math problems that are brutally hard for today's computers to solve. A new kind of computer built on quantum physics is expected to crack some of them with ease - nobody knows exactly when, maybe in ten years, maybe in twenty, or with how much faster everything is moving with AI these days it might be sooner than we think.

That sounds like a problem for later but the clock has already started. Attackers can record your encrypted traffic today and simply wait, which means anything you'd hate to see unlocked down the road (medical records, customer data, business secrets) is sitting in a queue for the day the lock stops working.

The replacement already exists though, and it's called post-quantum cryptography. Standards bodies have [published the first official versions](https://cloudmersive.com/article/How-to-Get-Prepared-for-Post-Quantum-Cryptography-Migration) and companies like Google are [already switching over](https://www.darkreading.com/application-security/google-2029-deadline-quantum-safe-cryptography), so the hardest part of the research is behind us. What's left is the practical work of finding out what we're using, which is a bigger unknown than it should be because most of us never chose our encryption in the first place - it just came along with the tools. NIST's advice for smaller organizations keeps it pleasantly small: look at what you have and ask your vendors about their plans.

Looking at what you have takes only a handful of commands - the harder part is knowing what you're looking for and why it matters, since an audit without that is just a pile of algorithm names nobody can act on.

## Two Problems, Not One

Zoom in and the risk splits into two parts, and it helps to keep them apart.

* **Secrets recorded now and read later.** An attacker copies your encrypted traffic today, stores it, and unlocks it once a powerful enough quantum computer exists, which makes this the one to worry about for data that has to stay private for years.
* **Forged identities.** Encryption also proves who you're talking to, and website certificates, SSH keys, signed software and login tokens all depend on it. Forging them needs a working quantum computer to exist first but the switch to something safer has to happen *before* it does - Google says it moved authentication up in its own threat model.

Audit both but keep them on separate lists. The first is mostly a matter of updating software while the second is slower and tangled up with everything else.

The part a quantum computer breaks is [public-key cryptography]({% post_url 2026-10-25-how-public-key-cryptography-works %}), the math that lets your browser trust a server it has never met and agree on a secret with it before a single password is typed. Everything after that handshake is protected by much sturdier scrambling, which means the opening moments of every connection and the identity checks behind them are where this audit needs to look.

## Three Questions to Keep Asking

Every step below is really asking the same three questions about a different part of your system:

* **What does it protect?** The thing somebody would want to read or fake.
* **How long does it need to stay protected?** A secret that expires in an hour is a very different problem from one that has to hold for fifteen years.
* **Who can change it?** That decides whether you can fix it this week or have to wait on a vendor.

Keep them in your head as you work. The commands only gather facts, and the questions are what turn those facts into priorities - they're also the same questions worth asking the next time anyone hands you a piece of security to adopt.

## 1. Check What Your Servers Negotiate

Start with the front door. Everything your visitors and users send to your servers - logins, form submissions, API calls - travels over a secure connection set up with a protocol called TLS (it's the thing behind the padlock next to the address), so this is where an attacker who records traffic would start. The part we care about is the handshake where both sides agree on a secret, called the "key exchange," since that decides whether a recording made today could be unlocked later. You'll need OpenSSL 3.5 or newer to test it because that release [made hybrid post-quantum key exchange part of the default setup](https://openssl-library.org/news/openssl-3.5-notes/index.html). 

A [hybrid exchange]({% post_url 2026-11-10-how-hybrid-key-exchange-works %}) exists because the new quantum-resistant math is young and has had far less time to be attacked than the old math, so cryptographers don't want to rely on it alone yet. It [runs both methods and combines the results into one secret](https://blog.cloudflare.com/post-quantum-visibility/), which means an attacker has to break both to read the traffic. Browsers and big providers have already adopted it, with about 70% of browser traffic to Cloudflare's network now using hybrid post-quantum key exchange. The catch is that both sides have to know the new method - when a connection starts the two machines compare what they support and settle on the best match, so if either one has never heard of the hybrid option they quietly settle on the old method and the connection works fine with no error and no warning.

That quiet fallback is why we test what was actually negotiated instead of assuming it. Start by checking which OpenSSL you have:

```bash
openssl version
```

If it says LibreSSL you're on the macOS default and it won't work. Install a current OpenSSL with Homebrew and call that binary instead. Then:

```bash
# What kind of certificate does the site present?
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null \
  | openssl x509 -noout -text | grep -E "Public Key Algorithm|Signature Algorithm"

# What key exchange did the connection actually use?
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null \
  | grep "Negotiated TLS1.3 group"
```

The first command shows what kind of certificate the site presents, and an ECDSA one looks like this:

```
        Signature Algorithm: ecdsa-with-SHA256
            Public Key Algorithm: id-ecPublicKey
    Signature Algorithm: ecdsa-with-SHA256
```

The signature line appears twice, which is normal, and an RSA certificate would show rsaEncryption and sha256WithRSAEncryption instead. Either one is normal for now, and since a certificate is how your server proves who it is, it goes on your identity list.

The second command prints the key exchange the connection settled on, and the name tells you what you got:

```
Negotiated TLS1.3 group: X25519MLKEM768
```

That's a hybrid. The name pairs a classical curve with a post-quantum method, X25519 plus ML-KEM-768 in this case, and it's the combination browsers prefer. A plain curve name like X25519 or secp256r1 would mean the old method on its own. A hybrid result means a recording made today stays locked even if a quantum computer shows up later, while a plain curve leaves that connection exposed to the record-now-read-later problem. **No output at all means the connection didn't use TLS 1.3,** and since post-quantum key exchange needs TLS 1.3 that counts as classical too.

What that's worth depends on what travels over the connection. Ask how much it would hurt if everything sent through here leaked in ten years - a login system or a customer portal deserves real attention, while a site where visitors only read public pages has very little worth recording.

If your site sits behind a CDN or host you don't control the answer is whatever *they* negotiate. This blog is a static site on [GitHub Pages]({% post_url 2024-02-25-github-pages %}) so my audit here is short - I find out what they do and I ask, which isn't fun but it's an honest answer. Visitors only read public pages here so there isn't much worth recording anyway, and that tells me this one belongs near the bottom of my list.

## 2. Check SSH

SSH is how you get into your servers and push your code, so what's at stake is someone watching or imitating your sessions: a recording can reveal what you did on the machine and a forged key lets someone walk in as you. OpenSSH 10.0 [made the hybrid mlkem768x25519-sha256 key exchange the default](https://www.openssh.org/txt/release-10.0), which covers the recording half. Find out where you stand:

```bash
ssh -V

# Which key exchange did this connection actually pick?
ssh -v user@yourserver 2>&1 | grep "kex: algorithm"
```

A [hybrid name]({% post_url 2026-11-10-how-hybrid-key-exchange-works %}) pairs x25519 with a post-quantum method, like mlkem768x25519-sha256 (the current default), and means that session is protected against recording, while curve25519-sha256 or something older means it isn't yet. Both ends need to be new enough or an old server will quietly give you a classical connection. Once you've checked that, list the keys you've collected over the years:

```bash
for f in ~/.ssh/*.pub; do ssh-keygen -lf "$f"; done
```

Every RSA and ED25519 entry goes on your identity list. For each one ask what it would get someone into - a key that opens production is a very different thing from one that opens a throwaway test box - and if you can't remember what a key opens at all, that's the finding.

## 3. Grep Your Code and Dependencies

*Nobody remembers which services sign tokens with RS256 until something forces them to look.*

Your own code is where you made choices nobody else can make for you, like which algorithm signs your tokens, which one encrypts the data you store, and which one proves a webhook really came from who it claims. Those choices are the hardest to change later because they're buried in business logic, so this step is less about finding scary algorithms and more about finding the ones that are **hard-coded**. You're hunting for the algorithms a quantum computer breaks: RSA, ECDSA, ECDH, Diffie-Hellman, Ed25519 and X25519 (the AES encryption that scrambles the data itself is not on the list).

```bash
grep -rniE "rsa|ecdsa|ecdh|secp256|prime256v1|ed25519|x25519|diffie|RS256|ES256|EdDSA" \
  --include="*.rb" --include="*.py" --include="*.js" --include="*.ts" \
  --include="*.rs" --include="*.go" --include="*.java" --include="*.cs" \
  --include="*.yml" --include="*.json" \
  --exclude-dir=node_modules --exclude-dir=.git --exclude-dir=vendor --exclude-dir=_site .
```

Expect noise. For each hit ask whether it's hiding data or proving who sent something (that's the recorded-later versus identity split again), and whether you could change it in one place or would be hunting through the whole codebase. Then check which libraries do the work for you:

```bash
npm ls 2>/dev/null | grep -iE "crypto|jwt|jose|forge|tls"
pip list 2>/dev/null | grep -iE "crypt|jwt|ssl|nacl"
bundle list 2>/dev/null | grep -iE "ssl|jwt|crypt"
cargo tree 2>/dev/null | grep -iE "rustls|ring|openssl|rsa"
```

Library versions matter too since two apps can use the same algorithm and have very different upgrade paths - and a library you can update is a lot better than a choice you wrote by hand in forty places.

## 4. Check Your Cloud Account

Cloud accounts quietly hold the keys behind your stored data and your certificates, and stored data is the clearest case of recorded-later because someone who copies a backup today can sit on it for as long as it takes. If you're on AWS these two commands cover a lot of ground:

```bash
# Certificates and their key types
aws acm list-certificates --query "CertificateSummaryList[].[DomainName,KeyAlgorithm]" --output table

# What kind of key is behind a KMS key?
aws kms describe-key --key-id YOUR_KEY_ID --query KeyMetadata.KeySpec
```

The certificate list goes on your identity list. The KMS result is the more interesting one: SYMMETRIC_DEFAULT is plain AES and not the concern, while an RSA key spec means something is relying on quantum-vulnerable math. Find out what, and if it's protecting long-lived data like backups it belongs near the top of your list.

I wrote about [free ACM certificates on CloudFront]({% post_url 2016-02-06-https-on-static-sites-using-cloudfront %}) years ago and the certificates I set up that way are exactly the kind of thing that ends up forgotten. Check yours.

## 5. Write It Down

*The most useful cell in the spreadsheet is the one that says "no idea."*

This is where the thinking becomes visible. Each row answers the same three questions - what is this protecting, how long does it need to stay protected, and who can change it - so filling one in forces the right conversation, and a cell you can't fill in is a finding by itself because it shows where you're relying on something you don't understand yet.

The inventory itself can be a spreadsheet or a markdown table. Fancier shops call it a Cryptographic Bill of Materials but I just call it a table. The Kind column marks each row as recorded-later, identity or both so the two problems stay separate:

| What | Where | Algorithm | Kind | Protects | How long it matters | Who controls it |
|------|-------|-----------|------|----------|---------------------|-----------------|
| Public site TLS | CDN | ECDSA cert, hybrid KEX? | Both | Visitor traffic | Short | Provider |
| Deploy SSH | Laptop to server | ED25519, hybrid KEX | Both | Server access | Short | Me |
| API tokens | Auth service | RS256 | Identity | Who can act as a user | Until the signing key changes | Me |
| Backups | Cloud storage | AES + RSA-wrapped key | Recorded later | Customer data | Years | Me + vendor |

The column that matters most is "how long it matters," which brings us to the ranking.

## 6. Rank It

You will not fix everything at once so sort the list:

1. **Long-lived secrets traveling over a network.** Anything that has to stay private for years can be recorded today and unlocked later (security people call this "harvest now, decrypt later"), so these go first.
2. **Things you control and can update.** Most of the key exchange work is plain old patching, which is exactly why [keeping your software up to date]({% post_url 2017-09-19-5-ways-keep-server-secure %}) is still the biggest lever you have.
3. **Things a vendor controls.** Ask them now because they set the timeline whether you like it or not. The UK's NCSC expects large organizations to finish discovery and planning by 2028 and migrate their highest-priority systems by 2031, and your vendor's answer tells you how much of that applies to you.

Notice that this order comes straight from the three questions - what's protected and for how long, then who can change it - and not from which algorithm sounds scariest.

## Gotchas

**Don't trust a green checkmark.** Both sides settle on the best method they have in common, so a single machine that doesn't know the hybrid option quietly drags the connection back to the old math while the padlock looks exactly the same. Check what was actually negotiated, the way we did with the TLS and SSH tests, instead of assuming an upgrade took effect.

**Post-quantum key exchange is only half the story.** A connection can use quantum-safe key establishment and still authenticate with a quantum-vulnerable signature, which is why we track the two problems separately.

**Hard-coded algorithm names are tomorrow's migration project.** Algorithms change - NIST has already selected a backup standard called HQC in case someone finds a weakness in ML-KEM (the new standard for key exchange) - so if swapping one means touching business logic you have a problem. Pull the choice out into configuration now. Security people call this crypto agility but I call it not hard-coding things.

**Don't panic.** Nobody knows when a quantum computer capable of breaking this will exist, and most of the early work is updating packages and asking vendors questions.

## What You'll Have When You're Done

It all boils down to this: **you can't migrate what you can't see.** Once the table is filled in it shows what you use and where and who owns it and which parts need attention first, but the bigger win is the habit underneath it. The questions that filled in the table - what is this protecting, for how long, and who can change it - will make the next migration, whatever triggers it, a lot less painful. Everything after this, like post-quantum certificates and signature migration, is a bigger project...but that's another story altogether.

Pick one row and start there.
