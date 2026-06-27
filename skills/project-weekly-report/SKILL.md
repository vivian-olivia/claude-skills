Generate my weekly project report for vivianolivia-fs from GitHub org GDP-ADMIN.

## Instructions

Pull data from these four projects under the `GDP-ADMIN` GitHub organization:
- Project 402: GLAIR Leads & Project Tracking
- Project 398: Product - Meemo
- Project 441: [ACTION ITEM] Speech & NLP
- Project 223: GL SDK Project Board

### Step 1: Fetch project items

**IMPORTANT: Always paginate through ALL pages for every project. Do not stop at page 1.**

For each project, use the GitHub GraphQL API (`gh api graphql`) to fetch all items with:
- Field values: Assignees, Status, Title, Priority, Due Date, Sprint (iteration field)
- Issue content: title, body, state, url, assignees, updatedAt
- Comments (last 10 per issue): author login and body

**Project 402**: Check ALL items with comments this week — not just ones assigned to vivianolivia-fs. Always look for: Lokadata, UH Investment Assistant, BCA Finance, Polytron — these are commonly missed on later pages.

**Project 398**: Always fetch the Sprint iteration field. Identify the current sprint and next sprint numbers. Paginate ALL pages to find sprint items — they appear across many pages.

**Project 441**: Filter by vivianolivia-fs as assignee.

Repo mapping:
- Project 402 issues → `dummy-gl-sdk` repo (for leads tracking)
- Project 398 issues → `meemo` repo
- Project 441 issues → `dummy-gl-sdk` repo (for speech/NLP action items)
- Project 223 issues → `dummy-gl-sdk` repo (for GL SDK board)

### Step 2: Fetch individual issue details
For each active issue, fetch:
- Full issue body: `gh api repos/GDP-ADMIN/<repo>/issues/<number>`
- All recent comments: `gh api repos/GDP-ADMIN/<repo>/issues/<number>/comments`
- Always re-fetch issue titles directly — they can change

Use the **most recent comment** on each issue as the primary source of truth for Issues, Accomplishments, and Next Actions.

### Step 3: Synthesize the report

Produce the report grouped by client/product. Skip any section that has nothing to report — do not write "None".

---

**Issues**

1. **[Client/Project Name]**
   1. [Issue or blocker]

**Accomplishments**

1. **[Client/Project Name]**
   1. [What was done]

**Next Actions**

1. **[Client/Project Name]**
   1. [What needs to happen next]

---

### Key rules — General
- **Skip empty sections entirely** — never write "None"
- Keep it **PM-level**: what has been fixed/released to where, what needs review
- Always include **issue numbers** for all items
- Sort issue numbers in **ascending order** within each group
- Only include clients/projects with activity this week or actively In Progress / In Review
- Do not include Dropped (L9.2) or old Completed projects unless they had activity this week
- Today's date is used to determine "this week" (last 7 days)

### Key rules — Project 402
- Paginate ALL pages (400+ items total)
- Check ALL items that had comments this week, regardless of assignee
- Source Issues, Accomplishments, and Next Actions from the most recent weekly update comment (written by vivianolivia-fs, structured as Issues / Accomplishments / Next Actions)
- Group by client: BCA Meemo, DPRD Jatim Meemo, Telkomsel, Lokadata, UH Investment Assistant, BCA Finance, etc.

### Key rules — Project 398 (Meemo)
- Always fetch the Sprint iteration field to identify current sprint (e.g. Sprint 39) and next sprint (e.g. Sprint 40)
- Paginate ALL pages to collect every sprint item
- **Accomplishments** = ALL items in the current sprint with status: Done, In Review Staging, or In Review Production (work is already done — in-review items count as accomplishments)
- **Next Actions** = ALL items in the next sprint (paginate all pages) + current sprint In Review items that still need to be reviewed/released
- For In Review items in Next Actions, label clearly: "review & release to production" or "review & release to staging"
- Group items within Accomplishments and Next Actions by:
  1. **Meeting Assistant Agent** — features/work specific to the meeting agent
  2. **Improvements** — new features, infra improvements, non-bug work
  3. **Bug Fixes** — actual bugs fixed or being fixed
  4. **Others** — anything that doesn't fit above (e.g. DNS, infra discussions)

### Key rules — Project 441 (Speech & NLP)
- Filter by vivianolivia-fs as assignee
- In Review items count as Accomplishments (same rule as Meemo)
- Include a "Speech" section in the report
