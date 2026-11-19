# Instructions for LLM Agents

## Content Generation Style

When generating content on my behalf, you **must** strictly adhere to the writing style detailed in the `style_guide.md` file located in the root of this project.

Before generating any content, read and internalize the principles and archetypes described in that guide. Your primary objective is to replicate my voice and tone as closely as possible. Failure to do so will result in content that is not usable.

---

## News Analysis Task

When I ask you to "read the news" or a similar query, you must follow this specific workflow:

1.  **Consult Sources:** Read the `sources.md` file to identify the full list of approved news sources.
2.  **Fetch Articles:** Cover every Tier 1 source and sample Tier 2, following the tiers in `sources.md`. Use the web fetch tool to get recent headlines and article summaries, processing multiple URLs together for efficiency. If a homepage can't be fetched, try its RSS feed, a site-restricted web search, or the fallback aggregators listed in `sources.md`. Check Tier 3 general news only for major events that affect tech.
3.  **Trace to Primary Sources:** Digests, aggregators and news write-ups are for discovery only. Before citing a claim, follow it back to the original report, advisory, release note or vendor post, and cite that. If you can't find the original, say so instead of citing the secondary source.
4.  **Synthesize Trends:** Analyze the collected information to identify the most important and recurring trends. Pay special attention to topics, technologies, or events that appear across multiple independent sources, as these indicate significant industry-wide or global happenings. Prioritize trends that connect to `interested_trends.md` and to recent posts in `_posts/`.
5.  **Attribute and Report:** Present a synthesized summary of the key trends. For each major point, you **must** cite the source(s) where the information was found. This allows for verification and further reading. Structure the output with clear headings for each trend.
6.  **Report Gaps:** List any sources that couldn't be reached so the source list can be fixed.
