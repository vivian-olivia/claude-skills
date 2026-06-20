# claude-skills

Personal Claude skill library. Each skill is a `SKILL.md` file that Claude reads before executing a task — it encodes the exact steps, output format, filters, and error handling for a specific workflow.

---

## Skills

### `/weekly-articles-search`

Searches the web for the top 5 articles from a given week, formatted for the weekly report's tech & PM section.

**Triggers:** `/weekly-articles-search`, `cariin artikel minggu ini`, `find articles this week`, `top AI articles`

**Output:** Numbered list of 5 articles — title, 1-sentence summary, source, URL.

**Topics covered (in priority order):**
1. Speech technology (ASR, TTS, STT, voice agents, diarization)
2. Meeting agent technology
3. LLMs and AI agents
4. Product / project management tips
5. Productivity and async workflows

**Week definition:** Sunday–Saturday WIB. Say `minggu lalu` to shift one week back.

**Does NOT:** Pull saved links from the articles Google Doc — use `/articles-weekly-report` for that.

---

### `/weekly-calendar-report`

Fetches accepted Google Calendar events for the report week and outputs a grouped meeting list.

**Triggers:** `/weekly-calendar-report`, `cek meeting minggu ini`, `pull meetings this week`, `meetingku minggu ini`

**Output:** Meetings grouped by category (Projects, Speech/Meemo, GLChat, Brownbags, Others) + OOO check.

**Filters:** Accepted events only, Mon–Fri, excludes Focus Time / Declined / Reminders / Placeholders / duplicates.

**Week definition:** Sunday–Saturday WIB. Say `minggu lalu` to shift one week back.

**Does NOT:** Generate the full weekly report — use `/weekly-report-generator` for that.

---

## How Skills Work

Claude reads the relevant `SKILL.md` at the start of each task. The file defines:
- Trigger phrases that activate the skill
- Step-by-step execution logic
- Output format and rules
- Error handling

Claude selects a skill based on the `description` field in the frontmatter — this is what gets matched against the user's request, so it should be precise and include trigger phrases.

---

## Structure

```
claude-skills/
├── README.md
└── skills/
    ├── weekly-articles-search/
    │   └── SKILL.md
    └── weekly-calendar-report/
        └── SKILL.md
```

Each skill lives in its own folder named after the skill. The folder name = the slash command.

---

## Adding a New Skill

1. Create a new folder under `skills/` named after the command (e.g. `skills/my-new-skill/`)
2. Add a `SKILL.md` with this frontmatter at the top:

```yaml
---
name: my-new-skill
version: 1.0.0
description: >
  One paragraph describing what this skill does, when to trigger it,
  and what it does NOT do. Include trigger phrases here.
---
```

3. Write the skill body: steps, output format, filters, error handling.
4. Register the skill in Claude's skill library (upload via the Claude.ai interface).
5. Update this README.

---

## Versioning

Frontmatter includes a `version` field. Bump it on any logic or output format change:
- Patch (`1.0.x`): wording fixes, minor filter adjustments
- Minor (`1.x.0`): new output section, new trigger phrase, new filter rule
- Major (`x.0.0`): breaking change to output format or step logic

---

## Commit Convention

```
feat(skill-name): short description
fix(skill-name): short description
docs: update README
```

Example: `fix(weekly-calendar-report): exclude tentative events from output`
