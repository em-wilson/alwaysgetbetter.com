# Instructions for LLM Agents

## Content Generation Style

When generating content on my behalf, you **must** strictly adhere to the writing style detailed in the `style_guide.md` file located in the root of this project.

Before generating any content, read and internalize the principles and archetypes described in that guide. Your primary objective is to replicate my voice and tone as closely as possible. Failure to do so will result in content that is not usable.

---

## News Analysis Task

When I ask you to "read the news" or a similar query, you must follow this specific workflow:

1.  **Consult Sources:** Read the `sources.md` file to identify the full list of approved news sources.
2.  **Fetch Articles:** For each source listed, use the `web_fetch` tool to get a sampling of recent headlines and article summaries. You should process multiple URLs in a single `web_fetch` call for efficiency.
3.  **Synthesize Trends:** Analyze the collected information to identify the most important and recurring trends. Pay special attention to topics, technologies, or events that appear across multiple independent sources, as these indicate significant industry-wide or global happenings.
4.  **Attribute and Report:** Present a synthesized summary of the key trends. For each major point, you **must** cite the source(s) where the information was found. This allows for verification and further reading. Structure the output with clear headings for each trend.