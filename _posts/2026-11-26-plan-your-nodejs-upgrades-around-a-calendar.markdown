---
author: emwilson
comments: true
date: 2026-11-26 13:00:00+00:00
layout: post
slug: plan-your-nodejs-upgrades-around-a-calendar
title: "Plan Your Node.js Upgrades Around a Calendar"
excerpt: "Most teams install a Node.js version once and never touch it again, then find out during a security release that nobody is fixing their version anymore. Node now ships one release a year on fixed dates, which means every upgrade deadline is published years ahead."
categories:
- Node.js
photo:
  source: /images/2026/11/node-calendar
  url: /images/2026/11/node-calendar.jpg
  description: Plan Your Node.js Upgrades Around a Calendar
tags:
- node.js
- upgrades
- lts
- maintenance
published: true
---

On November 11, Node.js [published fixes for a batch of security holes](https://nodejs.org/en/blog/vulnerability/november-2026-security-releases). Seven were rated high severity, and the fixes only went out for the three newest versions, so anyone still running an older one got a detailed public description of every hole and no patch.

Every version stops getting fixes on a published date, and after that day each new security hole stays open on it forever. Plenty of apps are past that date, because their version got picked the week somebody wrote the first deploy script and nobody has had a reason to touch it since. Nobody sends you an email when your version runs out and the app keeps working exactly as before, so the first sign of trouble is usually a security release you can't install, followed by a rushed upgrade on the same week everyone else is scrambling.

It happens because "upgrade Node" never has a priority or date attached, so it never makes it onto anyone's list. For Node, that excuse just went away. The project now releases on the same months every year, and every version gets the same long support window, which means you can write the next five years of upgrades on a calendar today and actually know when each one is due.

## Old Versions Get Skipped

The November releases patched Node 26, 24 and 22, with most of the high-severity fixes in the HTTP/2 and TLS code. Older versions got nothing, and any team still using them are on the hook to upgrade urgently: they remain vulnerable and will not be getting the update. Node 20 is the newest version in that old-timers group. It [reached the end of its support on April 30 of this year](https://github.com/nodejs/Release/blob/HEAD/schedule.json), so it's open to every hole announced since then and will stay that way.

Most teams don't stay on an old version on purpose. They fall so far behind on their dependencies that a version bump turns into a refactor, and the upgrade keeps getting pushed to next quarter. It's a pattern Marco Ippolito, who shipped most of the Node 20 releases, sees every day in his work at HeroDevs, which sells security fixes for end-of-life Node versions.

## Node Now Ships Once a Year

For a decade Node shipped two major versions a year. The even-numbered ones (18, 20, 22, 24) got long-term support. The odd-numbered ones were short-lived previews that most people skipped.

The project is retiring that model. [Starting with Node 27](https://nodejs.org/en/blog/announcements/evolving-the-nodejs-release-schedule), there's one major release a year and every one of them becomes LTS. Most users skipped the odd releases and waited for LTS anyway, the odd/even split confused newcomers, and the volunteers who maintain Node were struggling to backport security fixes across four or five release lines at once.

Every major release now moves through the same four phases on the same months:

| Phase | When | What it's for |
|---|---|---|
| Alpha | October to March | Early testing for library authors and CI. Breaking changes still allowed. Not for production. |
| Current | April to October | The new major is official and stabilizing. |
| LTS | October, for 30 months | Security and bug fixes. This is where production should live. |
| End of life | Forever after | No more fixes of any kind. |

That adds up to 36 months from the April release to end of life. Version numbers line up with the year of that April release, so Node 27 arrives in 2027 and Node 28 in 2028.

Node 26 is the last release under the old rules, and the new ones have already started. The [first Node 27 alpha shipped on October 29](https://nodejs.org/en/blog/release/v27.0.0-alpha.0.0.0), and the schedule puts 27 in Current in April 2027, LTS in October 2027 and end of life on April 30, 2030.

## When Each Version Stops Getting Fixes

The announcement gives months, but the exact dates live in the Release Working Group's `schedule.json` file in the nodejs/Release repository. That file is the authoritative record (the announcement says the plan may still be amended), and it's the one your tools should read.

| Line | Status | End of life |
|---|---|---|
| Node 20 | **Already end of life** | April 30, 2026 |
| Node 22 | Maintenance (fixes only) since October 2025 | **April 30, 2027** |
| Node 24 | Maintenance since October 20, 2026 | **April 30, 2028** |
| Node 26 | Active LTS since October 28, 2026 | April 30, 2029 |
| Node 27 | Alpha | April 30, 2030 |

Node 22 has about five months left, so if it's in your stack, the upgrade belongs on this quarter's plan, not next year's. End-of-life dates now land on April 30 every single year, which makes every spring a deadline for somebody.

## Upgrades Now Have a Date

Node didn't make upgrades free, but it made them predictable. Every new version arrives in April, becomes LTS in October and runs out of fixes on April 30 three years after it arrived, so the deadline for every version you run is already published. An upgrade with a date on it can be planned and finished before a security release turns it into an emergency.

The same goes for every runtime, base image and managed service in your stack - all of them stop getting fixes eventually, and most don't publish their dates as clearly as Node now does. Is your app ready for the next security release?