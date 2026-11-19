---
author: emwilson
comments: true
date: 2026-11-10 16:00:00+00:00
layout: post
slug: how-hybrid-key-exchange-works
title: "How Hybrid Key Exchange Works"
excerpt: "X25519MLKEM768 looks like a cat walked across the keyboard, but it tells you exactly how your connection is protected. What hybrid key exchange is, why the internet chose it, where it quietly fails, and what it still doesn't cover."
photo:
  source: /images/2026/11/many-keys
  url: /images/2026/11/many-keys.jpg
  description: More Keys Than Ever
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

Before a browser and a server can exchange a single private word they have to settle on a shared secret, and the technique they use for that step, [public-key cryptography]({% post_url 2026-10-25-how-public-key-cryptography-works %}), is being upgraded across the internet right now. It has held up for decades but a big enough quantum computer would eventually crack it, and traffic copied off the wire today can sit in storage until that day arrives. The replacement, called post-quantum cryptography, was built to withstand quantum computers, but its official standard is just over two years old and young security tools have a habit of failing in ways nobody saw coming.

That leaves an old method that will eventually be cracked and a new one nobody has finished testing. Bet on the wrong one and your customer records, login sessions and the commands you type into production are readable by whoever kept a copy.

So the industry stopped choosing. A hybrid connection runs the old public-key technique and the new post-quantum one side by side and blends the two results into a single secret, which means an attacker has to break both to read anything, and the cost is a couple of extra kilobytes when the connection opens. If you've ever checked how a connection was secured and run into a long, ugly name, you were probably looking at a hybrid, and the name spells out the two techniques it combined.

## Two Padlocks on One Box

Imagine key exchange is like an open padlock that anyone can snap shut on a box, where only the owner of the matching key can open it. Hybrid key exchange puts two padlocks on the same box, made by two different manufacturers. One is the old, well-understood design a quantum computer would defeat, and the other is built to resist one but has had far fewer years of people trying to pick it. An attacker has to open both.

If the new lock has a hidden flaw the old one still stops today's attackers, and if a quantum computer shows up the new one still stops it. **Whatever goes wrong has to go wrong with both locks before your traffic is readable.**

## How It Works

Both machines run both exchanges side by side inside the same handshake. Your browser sends its half of the old elliptic-curve exchange along with a public key for the new one, and the server answers with its half of the old exchange plus a fresh secret wrapped in that public key. When it's done, each side holds two secrets, one from each method.

The specification, [RFC 10024](https://www.rfc-editor.org/rfc/rfc10024), joins those two 32-byte secrets into one 64-byte value and feeds it into the normal process that produces the session key. It uses "hybrid" in a specific sense: the result stays secure **as long as one component remains secure**. Recovering the session key means recovering both secrets, which means breaking both kinds of math.

That protects everything sent over the connection, including traffic recorded today and attacked years from now.

## Why Not Just Switch to the New Math?

*Every new algorithm is unbreakable right up until somebody gives a conference talk about it.*

The new algorithm, ML-KEM, was published by NIST as [FIPS 203](https://csrc.nist.gov/pubs/fips/203/final) in August 2024 after years of public scrutiny. In 2022 a post-quantum candidate called SIKE, which had made it deep into NIST's selection process, was [broken on a single ordinary processor core in about an hour](https://www.feistyduck.com/newsletter/issue_92_the_end_of_sidh_and_sike), only months after another candidate called Rainbow was broken too.

Now weigh the two ways of being wrong. If the new algorithm turns out to be breakable, as SIKE did, anything protected by it alone is readable by an attacker with an ordinary laptop - no quantum computer required. If the old algorithm is the one that falls, which a quantum computer is expected to manage eventually, anything protected by it alone goes the same way. A hybrid survives either failure, and that insurance costs only a few dozen extra bytes and a little computation. OpenSSH puts it plainly on its [post-quantum page](https://www.openssh.org/pq.html): the hybrid is "no worse" than the best conventional option even if the post-quantum part turns out to be completely broken.

The standards bodies agree. The IETF marks the X25519MLKEM768 hybrid as recommended in RFC 10024, while the [pure ML-KEM options for TLS](https://www.ietf.org/archive/id/draft-ietf-tls-mlkem-11.html) are still a draft marked "not evaluated."

## Reading the Names

X25519MLKEM768 is three parts glued together. **X25519** is the elliptic-curve exchange that has carried most TLS 1.3 connections on its own. **MLKEM** is the new post-quantum method. **768** picks the ML-KEM parameter set: FIPS 203 places ML-KEM-768 in security category 3, roughly as hard to break as a 192-bit AES key, sitting between the lighter 512 and the heavier 1024.

SSH uses the same ingredients in a different order. In mlkem768x25519-sha256, the trailing sha256 names the hash used to mix the results together.

| Name | Where | What it means |
|------|-------|---------------|
| X25519MLKEM768 | TLS | Hybrid, the default in Chrome since version 131 and in OpenSSL 3.5 |
| SecP256r1MLKEM768, SecP384r1MLKEM1024 | TLS | Hybrid using NIST curves, aimed at FIPS-regulated environments |
| X25519Kyber768Draft00 | TLS | Hybrid built on a pre-standard draft, now obsolete |
| mlkem768x25519-sha256 | SSH | Hybrid, the OpenSSH default since 10.0 |
| sntrup761x25519-sha512 (or ...@openssh.com) | SSH | Older hybrid, the OpenSSH default from 9.0 |
| MLKEM768 | TLS | Pure post-quantum, still a draft |
| X25519, secp256r1, curve25519-sha256, ecdh-sha2-nistp256 | Both | Old method only, no post-quantum protection |

The rule of thumb for any name you meet: **a post-quantum name glued to a curve name is hybrid, a bare curve or Diffie-Hellman name is the old method on its own, and a post-quantum name on its own is pure post-quantum.**

## How the Two Sides Agree

At the start of a connection the client lists every method it supports in order of preference, usually with a guess or two already attached, and the server picks the best one it also supports. That keeps old and new software talking, and it's also why an upgrade can go missing unnoticed. If the server has never heard of the hybrid it picks plain X25519, the connection works, the padlock looks the same, and nothing logs an error.

**The oldest software on either end decides your protection,** so a CDN, an old client library or an appliance you can't patch quietly keeps that connection on the old method and its recordings exposed. OpenSSH 10.1 and later [print a warning](https://www.openssh.org/txt/release-10.1) when a session settles on a non-post-quantum method, but on the web you only find out by looking.

## What Breaks in the Wild

*Every network has a firewall nobody has touched since the person who installed it left.*

Hybrid is the default almost everywhere now, and nearly all the rough edges come from old gear that can't handle a bigger first message. The key share a client sends is 1,216 bytes against 32 for X25519 alone, which pushes the first handshake message past what fits in a single network packet. When Chrome 124 turned on the hybrid by default, [the rollout exposed bugs in several middlebox products](https://blog.google/chromium/advancing-our-amazing-bet-on-asymmetric/) and split that message across two packets for a 4% median latency increase. Firewalls and inspection proxies that expect the whole message in one packet drop the connection, which is the loud failure - users simply can't reach the site. Cloudflare's checker now [flags these split-message bugs](https://developers.cloudflare.com/changelog/post/2026-05-29-radar-pq-tls-bug-detection/) alongside servers that choke on key exchange methods they don't recognize.

Chrome gave administrators a temporary switch to turn the hybrid off while vendors fixed their gear. Someone flips it to get one cranky firewall working, everybody behind that firewall quietly drops back to the old method, and the switch is still flipped long after the firewall is gone.

Version matters too. Cloudflare notes that [post-quantum encryption isn't available in TLS 1.2 or earlier](https://blog.cloudflare.com/post-quantum-visibility/), since the hybrid groups are defined for TLS 1.3. Anything still speaking TLS 1.2 gets the old method no matter how new its libraries are.

## What Hybrid Doesn't Fix

Hybrid key exchange solves the "record now, read later" problem. Proving who's on the other end is a separate job done by [certificates and signatures]({% post_url 2026-10-25-how-public-key-cryptography-works %}), which are still RSA or elliptic-curve almost everywhere. A quantum computer could forge them only once it exists, so nothing recorded today is at risk from that, but OpenSSH notes today's signature keys still have to be retired before that day.

**A hybrid connection to an impostor is still a connection to an impostor.** Certificates and SSH host keys are their own, slower migration, so track them separately from key exchange.

## One Name at a Time

A name like X25519MLKEM768 looks like noise until you read it as two techniques working together, one proven over decades and one still earning its reputation, with a rule that both have to fail before anything leaks. That reading tells you whether a connection is safe from recordings made today and what it leaves open: who is on the other end, and whether every machine along the way was new enough to get the hybrid at all. Hybrid is cheap insurance against being wrong about either technique, and the rest of the migration, certificates and signatures included, mostly comes down to knowing [what cryptography you're running]({% post_url 2026-11-15-find-the-crypto-in-your-own-stack %}) in the first place.
