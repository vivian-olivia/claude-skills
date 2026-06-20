---
name: weekly-articles-search
version: 1.0.0
description: >
  Search the web for the top 5 articles from this week (or a specified week) covering
  AI agents, LLMs, speech technology, meeting agent tech, and product/project management
  or productivity tips. Outputs a numbered list with title, 1-sentence summary, and link.
  Trigger when user types "/weekly-articles-search", "cariin artikel minggu ini",
  "find articles this week", "top AI articles", or asks to search for weekly tech/PM
  articles for the weekly report. Do NOT use for pulling saved links from the Google Doc
  — use /articles-weekly-report instead.
---

# Weekly Articles Search

Web-searches for the top 5 relevant articles from the report week and formats them for
the weekly report's "Technology, Business, Communication, Leadership, Management & Marketing" section.

## Topics to Cover

Search across these topic areas (in priority order):

1. **Speech technology** — ASR, TTS, STT, voice agents, meeting transcription, diarization
2. **Meeting agent technology** — AI meeting assistants, note-takers, action item extraction
3. **LLMs and AI agents** — model releases, agentic AI, multi-agent systems, AI infrastructure
4. **Product / project management tips** — PM tools, prioritization frameworks, stakeholder communication, roadmapping
5. **Productivity** — async workflows, team efficiency, AI-augmented work

Aim for a mix: at least 1 speech/meeting article, at least 1 LLM/agent article, and at least 1 PM/productivity article.

---

## Step 1: Determine Week Range

- Default = current week (Sunday–Saturday WIB)
- If user says "minggu lalu" → shift one week back
- Confirm range if ambiguous

---

## Step 2: Search

Run **3–5 web searches** targeting different topic areas. Example queries:
- `speech technology AI [month year]`
- `AI meeting agent transcription [month year]`
- `LLM AI agent news this week [month year]`
- `product manager tips productivity AI [month year]`
- `agentic AI voice enterprise [month year]`

Prioritize results published **within the report week**. If fewer than 5 are from that exact week, expand to the past 2 weeks.

Prefer:
- Original reporting (company blogs, research orgs, reputable tech media)
- Practical, actionable content over listicles
- Content relevant to an AI product manager at an AI company

Avoid:
- Conference event announcements with no substance
- Pure SEO roundup articles
- Paywalled articles (prefer open access)

---

## Step 3: Output

Format each article as:

```
[number]. **[Title]** — [1-sentence summary in your own words]. *([Source])*
[URL]
```

Example:
```
1. **Azure Speech at Build 2026: Voice Agents Go Enterprise-Ready** — Microsoft announced Voice Live for Foundry Prompt Agents going GA, letting developers build real-time voice agents that can listen, reason, and respond naturally across use cases like healthcare intake and multilingual support. *(Microsoft Tech Community)*
https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/...
```

Rules:
- Exactly 5 articles
- 1 URL per article (full link, no markdown formatting on the URL)
- Summaries in your own words — no direct quotes from the source
- Flag which topic area each article covers if the mix is unclear

---

## Error Handling

- Fewer than 5 strong results from the report week → expand to past 2 weeks and note it
- All results from same topic area → re-search for underrepresented areas
- Web search not available → say "Web search tidak tersedia. Coba lagi atau jalankan manual."
