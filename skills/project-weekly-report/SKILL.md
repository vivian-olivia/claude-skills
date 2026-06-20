---
name: project-weekly-report
version: 1.0.0
description: >
  Generate a weekly project status report for vivianolivia-fs by pulling live data from
  four GitHub Projects under the GDP-ADMIN org (projects 402, 398, 441, 223). Fetches
  issue bodies and comments — reads the most recent weekly update comment per issue as
  the source of truth for Issues, Accomplishments, and Next Actions. Outputs a report
  grouped by client/product in the standard Issues / Accomplishments / Next Actions format.
  Trigger when user types "/project-weekly-report", "bikin weekly report", "generate weekly
  report", or asks to pull project status from GitHub this week.
  Do NOT use for articles or calendar — use /weekly-articles-search or /weekly-calendar-report instead.
---

# Project Weekly Report

Pulls live data from four GDP-ADMIN GitHub Projects and generates a weekly status report
for `vivianolivia-fs`, grouped by client/product with Issues, Accomplishments, and Next Actions.

## Projects

| # | Name | Repo |
|---|---|---|
| 402 | GLAIR Leads & Project Tracking | `GDP-ADMIN/dummy-gl-sdk` |
| 398 | Product - Meemo | `GDP-ADMIN/meemo` |
| 441 | [ACTION ITEM] Speech & NLP | `GDP-ADMIN/dummy-gl-sdk` |
| 223 | GL SDK Project Board | `GDP-ADMIN/dummy-gl-sdk` |

---

## Step 1: Fetch All Four Projects in Parallel

Run this GraphQL query for each project number simultaneously using `gh api graphql`:

```graphql
{
  organization(login: "GDP-ADMIN") {
    projectV2(number: <PROJECT_NUMBER>) {
      title
      items(first: 100) {
        nodes {
          fieldValues(first: 20) {
            nodes {
              ... on ProjectV2ItemFieldTextValue { text field { ... on ProjectV2Field { name } } }
              ... on ProjectV2ItemFieldSingleSelectValue { name field { ... on ProjectV2SingleSelectField { name } } }
              ... on ProjectV2ItemFieldDateValue { date field { ... on ProjectV2Field { name } } }
              ... on ProjectV2ItemFieldUserValue { users(first: 3) { nodes { login } } field { ... on ProjectV2Field { name } } }
            }
          }
          content {
            ... on Issue {
              number
              title
              body
              state
              url
              assignees(first: 3) { nodes { login } }
              updatedAt
              comments(last: 10) {
                nodes { author { login } body createdAt }
              }
            }
          }
        }
      }
    }
  }
}
```

Filter results to only items where `vivianolivia-fs` appears in:
- The `Assignees` field value nodes, OR
- The issue's `assignees` list

---

## Step 2: Fetch Full Issue Detail for Active Items

For each issue assigned to `vivianolivia-fs` that was updated in the **last 14 days** OR has status **In Progress / In Review**, fetch the full comment history:

```bash
gh api repos/GDP-ADMIN/<REPO>/issues/<NUMBER>/comments
```

Use the **most recent comment by `vivianolivia-fs`** as the primary source of truth.
These weekly update comments follow this structure:

```
## Issues
- ...

## Accomplishments
- ...

## Next Actions
- ...
```

Extract each section verbatim and use it to populate the report. If no such comment exists, infer from the issue body and current project status.

---

## Step 3: Synthesize the Report

Output in **exactly** this format:

---

**Issues**

1. **[Client / Project Name]**
   1. [Blocker or issue]
   2. [Additional issue; or write "None" if the comment says None]

**Accomplishments**

1. **[Client / Project Name]**
   1. [What was done this week]

**Next Actions**

1. **[Client / Project Name]**
   1. [What needs to happen next]

---

## Grouping Rules

- One numbered entry per client/product — not one per GitHub project board
- **Meemo clients** (BCA, BPJS Kesehatan, DPRD Jatim) → each gets its own entry by client name
- **Platform/product Meemo work** (bugs, product tasks from Project 398) → group under **"Meemo"**
- **Speech & NLP action items** (Project 441) → group under **"Speech"**
- **Cross-team / GL SDK items** (Project 223) → group under **"Others"** if relevant
- Always list all three sections (Issues / Accomplishments / Next Actions) per client, even if "None"

## Inclusion Rules

- Include: status is **In Progress**, **In Review**, **P2 Project Ongoing**, **P3 Warranty/Maintenance**, or updated in the last 7 days
- Skip: `L9.2 Dropped`, `P4 Project Completed`, `P4.1 Project Completed 2026`, `P5 PoC Completed` — unless updated this week
- If a client has no activity this week, still include them if they are an active contract (P2/P3) — write "None" explicitly rather than omitting

---

## Error Handling

- Project not found → `gh api graphql` returns NOT_FOUND → check org name `GDP-ADMIN` and active account with `gh auth status`; switch to correct account if needed
- Output too large → pipe through Python to filter by assignee before printing, rather than reading the raw JSON
- No recent comment by `vivianolivia-fs` → infer Issues/Accomplishments/Next Actions from issue body and status field; note the inference
- `gh` not authenticated → prompt user to run `gh auth login`
