---
name: weekly-calendar-report
version: 1.0.0
description: >
  Pull and format the user's meeting list from Google Calendar for a given week.
  Outputs grouped meeting names only — no timestamps, no links. Trigger when user
  types "/weekly-calendar-report", "cek meeting minggu ini", "pull meetings this week",
  "meetingku minggu ini", or asks to list meetings from their calendar for weekly report
  purposes. Week runs Sunday–Saturday WIB. Do NOT use for the full weekly report —
  use /weekly-report-generator instead.
---

# Weekly Calendar Report

Fetches accepted calendar events for the report week and outputs a grouped meeting list.

## Week Definition

- Week runs **Sunday to Saturday** (WIB, GMT+7)
- Default = current week (most recent Sunday through today)
- If user specifies "minggu lalu" or "last week", shift one week back

---

## Step 1: Determine Date Range

Calculate in **GMT+7**:
- `week_start` = most recent Sunday 00:00 WIB
- `week_end` = coming Saturday 23:59 WIB (or today if mid-week)

If user says "minggu lalu" → shift both dates back 7 days.

Confirm range with user before fetching: e.g., "Oke, mau pull meetings 15–21 Jun 2026 ya?"

---

## Step 2: Fetch Calendar Events

Use Google Calendar MCP `list_events`:
- Fetch **all events** in the Monday–Friday window (work week only)
- Enable pagination — fetch all pages if needed
- Use the primary calendar unless another is specified

---

## Step 3: Filter Events

**Include ONLY** events where the user's response status is `accepted`.

**Exclude** events that match any of the following:
- Status: Declined, Maybe, Tentative, Not Responded, Optional, Transparent
- Event type: Focus Time
- Title contains: `Reminder`, `Async`, `Placeholder`, `Focus`
- Cancelled events
- Events outside the Monday–Friday window
- Duplicate titles (keep one instance)

---

## Step 4: Group Events

Group remaining events in this order. Skip a group entirely if it's empty.

1. **Projects** — client or external meetings. Keywords: AVA, TSEL, Telkomsel, BPJS, DPR, Jumpstart, DE, Latte, BUA, Government, VA Web, Lokadata, MSI, UH, MSCDX, Complaint. Also includes commercial Meemo client meetings.
2. **Speech and NLP / Meemo Product** — internal product or technical meetings about Meemo, Speech, NLP, Diarization, ASR, TTS, STT.
3. **GLChat** — any meeting with "GLChat" in the title.
4. **Brownbags** — titles containing: Brownbag, Talk, Training, Workshop, Socialization, Demo Day.
5. **Others** — standups, syncs, 1-on-1s, all-hands, anything else not covered above.

---

## Step 5: Output

Format:

```
**Projects**
- Lokadata Daily Update
- UH Sync with GDPV

**Speech and NLP / Meemo Product**
- Meemo Daily
- Speech Agent Review

**GLChat**
- GLChat Weekly Sync

**Brownbags**
- AI Talk: RAG Best Practices

**Others**
- GLAIR Daily Meeting
- 1:1 with Antonia
```

Rules:
- Event names only — no date, no time, no attendees, no links
- One bullet per event
- One blank line between groups
- Group headers in bold

---

## Out of Office

After the meeting list, check for OOO events:
- Look for titles like: "OOO", "Out of Office", "Cuti", "Leave", or all-day blocks with those keywords
- If found, list them below the meetings
- If none found, output: `OOO: —`

---

## Error Handling

- No events found → output: `Tidak ada meeting yang ditemukan untuk minggu ini.`
- Calendar MCP not connected → say "Google Calendar MCP belum tersambung. Cek di tools menu."
- Ambiguous week → ask user to confirm date range before fetching
