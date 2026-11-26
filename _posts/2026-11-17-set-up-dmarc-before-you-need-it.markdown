---
author: emwilson
comments: true
date: 2026-11-17 13:00:00+00:00
layout: post
slug: set-up-dmarc-before-you-need-it
title: "Set Up DMARC Before You Need It"
excerpt: "Gmail, Yahoo and Outlook.com all want proof that your mail really comes from you, and the rules keep tightening with little warning. A step-by-step guide to SPF, DKIM and DMARC for small businesses and side projects, from your first DNS lookup to a policy that stops impostors."
categories:
- Security
- General Programming
photo:
  source: /images/2026/11/dmarc-mail
  url: /images/2026/11/dmarc-mail.jpg
  description: Losing All Your Mail
tags:
- email
- dmarc
- spf
- dkim
- security
published: true
---

Suppose you run a small business or a side project with a few hundred users. Your invoices, receipts and password-reset emails go out automatically, and as far as your dashboard knows every one of them was sent. Then some of your customers with Hotmail or Outlook.com addresses stop getting them. Nobody complains right away because nobody knows anything is missing - the customer just never finishes resetting their password, and you find out a week later when an invoice goes unpaid.

_(I envy you for your clients who normally pay in only a week.)_

That happened for real this month. For about four days Microsoft's personal mail service [turned away some of the mail from domains that hadn't published one short line of email setup](https://mailroute.net/blog/outlook-dmarc-rejections-what-happened), and Microsoft later [blamed a faulty spam-rule update and rolled it back](https://www.theregister.com/applications/2026/11/09/outlookcom-bounces-legit-emails-as-microsofts-authentication-crackdown-goes-sideways/5308428). A few days later [an outage took down Outlook, Teams and Copilot for businesses around the world](https://www.bleepingcomputer.com/news/microsoft/massive-microsoft-365-outage-disrupts-outlook-teams-copilot-worldwide/) and everybody's attention went there, which is fair enough since there's nothing you can do about somebody else's data center. The smaller story is the one you can do something about. The domains that bounced were missing a setting the big mail providers have been asking for since 2024, and publishing even the most relaxed version of it was enough to get mail delivered again.

The fix is three short records in your domain's DNS, the public address book the internet uses to look up your domain. They cost nothing and take an afternoon to set up. Once they're tightened, the same records that keep your mail arriving also make it much harder for anyone else to send email that pretends to come from you.

## The Big Providers Already Ask for This

*Everybody sets up email the day they buy the domain and never looks at it again.*

Gmail and Yahoo started enforcing their sender rules in February 2024. Both require every sender to authenticate with at least one of two methods, and Google says unauthenticated mail might be marked as spam or rejected outright. Bigger senders get a stricter list: anyone sending more than 5,000 messages a day to Gmail accounts needs all three records, including the one Microsoft's bug tripped over. In November 2025 Google [ramped up enforcement](https://support.google.com/mail/answer/14229414), warning that mail failing its requirements would face temporary and permanent rejections instead of a quiet trip to the spam folder.

Microsoft brought Outlook.com in line in May 2025 with [its own requirements for domains sending over 5,000 emails a day](https://techcommunity.microsoft.com/blog/microsoftthreatprotectionblog/strengthening-email-ecosystem-outlook%E2%80%99s-new-requirements-for-high%E2%80%90volume-senders/4399730), and it rejects mail that doesn't measure up with a [550 5.7.515 error](https://support.microsoft.com/en-us/outlook/fix-ndr-error-550-5-7-515-in-outlook-com).

On paper a shop sending a few hundred receipts a day sits under those thresholds. The bug showed how little that protects you. Microsoft didn't announce anything and didn't change a published policy - an internal rule shifted for a few days and real mail from small domains bounced. Meeting the bigger senders' bar is cheap, and it means the next rule change, accidental or not, passes you by.

## The Holy Trifecta of Email Deliverability

All three records answer the same worry from the receiving server's side: *did this email really come from who it says it came from?* Each one checks a different piece of evidence.

* **SPF (Sender Policy Framework)** is a list of the servers allowed to send mail for your domain. It checks [the address used behind the scenes during delivery](https://www.rfc-editor.org/rfc/rfc7208.html) (called the envelope sender or "MAIL FROM"), which is not necessarily the From address your reader sees.
* **DKIM (DomainKeys Identified Mail)** adds a cryptographic signature to every message. The receiving server [checks the signature by looking up a public key published in your domain's DNS](https://www.rfc-editor.org/info/rfc6376/), which proves the message was signed by someone holding your key and that the signed parts haven't changed since.
* **DMARC (Domain-based Message Authentication, Reporting and Conformance)** ties the other two back to the From address people actually read. A message passes DMARC when SPF or DKIM passes *for a domain that matches the From address* - the DMARC spec calls this alignment, and by default "matches" means it shares the same parent domain, so `mail.example.com` lines up with `example.com`. DMARC also tells receivers what you want done with mail that fails and where to send you reports.

DMARC itself got an upgrade this year. The IETF published it as [RFC 9989](https://www.rfc-editor.org/rfc/rfc9989.html) in May 2026, which replaces the original RFC 7489 and moves DMARC onto the official standards track. Existing records keep working, and you'll see a few changes in the steps below.

Three questions to ask at every step:

* **Who is allowed to send email as me?**
* **Would I find out if my real mail stopped arriving?**
* **Would I find out if someone else started sending as me?**

## 1. Take Inventory

Before changing anything, find out what the world already sees when it looks up your domain. A receiving server checks these same public records on every message you send, so reading them yourself is the quickest way to know which of your emails are at risk.

```bash
# SPF lives in a TXT record on the domain itself
dig +short TXT example.com | grep -i "v=spf1"

# DMARC lives at a fixed name: _dmarc in front of your domain
dig +short TXT _dmarc.example.com

# No dig (Windows, locked-down laptop)? nslookup does the same job
nslookup -type=TXT _dmarc.example.com
```

DKIM is harder to look up because each sending service publishes its key under its own name, called a **selector**. The easiest way to find it is to send yourself a message from each service and open the raw headers. The `DKIM-Signature` header carries the signing domain in its d= tag and the selector in its s= tag, and you can look up the key with both:

```bash
# Replace "selector" with the s= value and example.com with the d= value
dig +short TXT selector._domainkey.example.com
```

While the headers are open, find the line that reports SPF, DKIM and DMARC results - Microsoft's own troubleshooting guide starts with exactly this check.

Then make a list of every service that sends email using your domain: your mailbox provider, newsletter tool, billing or invoicing system, the transactional email service behind your password resets, your helpdesk and your CRM. Most domains have more of these than their owners think. **That list is the real deliverable of this step** because every service on it has to pass before you can ever tighten the policy.

[EM: optional personal hook - what you found when you ran these checks on alwaysgetbetter.com or another domain you look after]

No SPF line, two SPF lines, or nothing at `_dmarc` each tell you something specific. Your mail is either one rule change away from bouncing or already broken in a way nobody has noticed (more on the two-SPF case in the gotchas).

## 2. Get SPF Into One Record

SPF protects the delivery route: it tells receivers which servers can hand off mail for your domain, so mail from anywhere else looks suspicious. Your domain gets exactly one SPF record listing every service from step 1:

```
; One TXT record on the root of the domain
; Each include: comes from that provider's setup docs
example.com.  TXT  "v=spf1 include:_spf.mailhost.example include:mail.billing.example ~all"
```

The values after `include:` are placeholders - every provider publishes the exact one to use. Keep whichever ending your main mail provider recommends.

Check it after the change has had time to spread:

```bash
dig +short TXT example.com | grep -i "v=spf1"
```

One line back means receivers can read it. Remember that SPF checks the behind-the-scenes envelope address, and many third-party services use *their own* domain there, so their mail can pass SPF and still fail to line up with your From address. Microsoft's guide asks you to confirm each service uses your domain for that envelope address, signs with DKIM using your domain, and appears in your SPF record. Look for it in each provider's domain or authentication settings.

## 3. Turn On DKIM for Every Sender

DKIM proves that a message was signed by you and that the signed parts weren't tampered with. The signature also survives the trip to the recipient, because, as the DKIM spec puts it, the relays in between typically make no substantive change to the content and so preserve it, which makes DKIM the record I lean on most when DMARC decides whether your mail is real.

Each service that sends as you should give you one or more DNS records to publish under a selector, usually on a page about domain authentication. Add them, then verify each one:

```bash
# One lookup per selector your services gave you
dig +short TXT selector1._domainkey.example.com

# Some providers have you publish a CNAME that points at a key they host
dig +short CNAME selector1._domainkey.example.com
```

Then send a test message and check the `d=` value in the `DKIM-Signature` header. If it says your domain, that service's mail can pass DMARC on its own. If it shows the provider's domain, the signature only vouches for them and does nothing for your From address. Go back and finish the custom-domain setup.

## 4. Publish DMARC in Monitoring Mode

This is the record whose absence bounced mail at Hotmail, and the starting version changes nothing about how your mail is handled. It announces that you use DMARC and asks receivers to tell you what they see:

```
; v=DMARC1  version tag, still DMARC1 under the new RFC 9989
; p=none    no preference: don't block or junk anything yet
; rua=      where receivers send daily summary reports
_dmarc.example.com.  TXT  "v=DMARC1; p=none; rua=mailto:dmarc-reports@example.com"
```

RFC 9989 calls a `p=none` record with reports switched on Monitoring Mode, and it notes that domain owners usually start there. It's also enough to meet the DMARC part of the bulk-sender rules: Google says the policy can be set to none, Yahoo asks for at least p=none and Microsoft asks for at least p=none, aligned with SPF or DKIM.

Point `rua` at a mailbox you'll actually check or a dedicated address that feeds a tool. Then confirm the record is live:

```bash
dig +short TXT _dmarc.example.com
```

Once that returns your record, you've closed the gap the Hotmail bug punished and opened a window into who sends mail as you. You haven't stopped anyone pretending to be you yet - that comes in step 6.

## 5. Read the Reports

*The first DMARC report always lists at least one sender nobody remembers signing up for.*

The reports protect you by showing what's happening. Each one is a summary from a receiving provider covering a stretch of time, and RFC 9989 says receivers should send them at least daily. They arrive as XML files, [usually gzip-compressed](https://www.rfc-editor.org/rfc/rfc9990.html), and the useful part is a list of records. Each one describes a batch of messages from one sending server, using elements defined in RFC 9990:

```xml
<record>
  <row>
    <source_ip>203.0.113.25</source_ip>  <!-- the server that sent the mail -->
    <count>42</count>                    <!-- how many messages in this batch -->
    <policy_evaluated>
      <disposition>none</disposition>    <!-- what the receiver did with them -->
      <dkim>fail</dkim>                  <!-- DKIM passed AND matched your From domain? -->
      <spf>pass</spf>                    <!-- SPF passed AND matched your From domain? -->
    </policy_evaluated>
  </row>
  <identifiers>
    <header_from>example.com</header_from>  <!-- the From domain readers saw -->
  </identifiers>
  <auth_results>
    <spf>
      <domain>example.com</domain>       <!-- raw SPF result, before alignment -->
      <result>pass</result>
    </spf>
  </auth_results>
</record>
```

For a small domain you can read these by hand for a while. Once there are more than a few a day, a report parser or DMARC monitoring service will save you a lot of squinting. Whatever the tool, sort every sending server into one of three piles:

1. **Yours and passing.** Leave it alone.
2. **Yours and failing.** This is a service from your step 1 list that isn't set up right. Go back to steps 2 and 3 for that service.
3. **Not yours.** Somebody else is sending mail with your name on it. Note it, but don't chase it - the tighter policy in step 6 handles them.

Keep reading until the second pile stays empty for a few weeks. RFC 9989 is frank that this can take many months of reports for a domain with a lot of senders. The question worth taking away is whether you recognize every server sending as you, because a report you can't explain points to a gap in your list.

## 6. Tighten the Policy

This step protects your customers from impostors. With `p=none`, mail that fails DMARC still gets delivered, so a scammer sending "your invoice is overdue" emails in your name gets through. Tightening tells receivers to treat that mail as fake. RFC 9989 is explicit that legitimate mail streams that don't line up must be fixed before any attempt at enforcement, which is why step 5 comes first.

Move one level at a time and wait for clean reports between each change:

```
; Stage 2: failing mail is treated as suspicious (usually the spam folder)
_dmarc.example.com.  TXT  "v=DMARC1; p=quarantine; rua=mailto:dmarc-reports@example.com"

; Stage 3: failing mail is refused outright
_dmarc.example.com.  TXT  "v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com"
```

Under `quarantine` you're telling receivers you consider failing mail suspicious, and under `reject` you're saying failure is a clear sign the mail isn't really from your domain. Keep the `rua` address the whole way through so you see right away if a forgotten service starts failing.

Older guides often suggest rolling out with `pct=10` to enforce on a fraction of mail first. RFC 9989 removed the pct tag because receivers rarely applied it accurately for anything other than 0 or 100, and replaced it with a test flag (`t=y`) that asks receivers to apply one level below the policy you publish. It's new enough that I'd rely on the step-by-step policy change instead and treat `t=y` as a bonus.

## Gotchas

**Two SPF records means no SPF record.** Every new service's setup page says "add this TXT record," and it's easy to end up with two lines starting `v=spf1`. RFC 7208 is blunt: if more than one SPF record exists, the check returns a permanent error and none of them count. Merge them into one.

**SPF has a lookup budget.** Every `include`, `a`, `mx`, `ptr`, `exists` and `redirect` makes the receiver do another DNS lookup, and the spec caps the total at 10 for the whole check, including lookups inside other people's includes. Go over and SPF fails with a permanent error for *all* your mail. Drop services you no longer use, or move a high-volume sender like a newsletter tool onto its own subdomain with its own SPF record.

**A third-party sender can pass SPF and DKIM and still fail DMARC.** If your newsletter tool signs with its own domain and uses its own bounce address, both checks pass for *them* and neither matches your From address. Finish the custom-domain setup on every service, which is exactly what Microsoft tells senders to verify.

**Don't jump straight to p=reject.** It's tempting to skip the slow part, but `reject` turns every forgotten sender into bounced mail - your billing system, your helpdesk, the contact form a contractor set up three years ago. You'd cause the same problem the Hotmail bug did, except this time on purpose.

**Reports sent to another domain need permission.** If your `rua` address is at a different domain (a monitoring service, or your main company domain for a side project), RFC 9990 says that domain has to publish a record authorizing it to receive your reports, or receivers won't send them. If you use a monitoring service, confirm it publishes this on its side, and if you're pointing one of your own domains at another, add it by hand.

**p=none is not protection.** The spec defines `none` as no expression of preference. It keeps your mail flowing and shows you what's going on, but impostors still get through until you move past it.

## Know Who Sends Mail as You

It all boils down to this: **you can't protect a domain if you don't know who sends mail from it.** The three records are short and the commands take minutes, but a simple inventory can change how you run things. Answer the three questions - who is allowed to send as me, would I notice if my mail stopped arriving, and would I notice if someone else started sending as me - and the next unannounced rule change at a big provider becomes a non-event.