# generate-sprint-report

Generate a sprint status report for a given sprint period, pulling data from Jira and GitHub.

## Trigger phrases

- "generate sprint report"
- "create sprint report for [dates]"
- "sprint status report"

## Composed commands

This skill orchestrates the following commands in order:

1. `/fetch-sprint-meta` — get sprint name, start/end dates from Jira Board 797
2. `/fetch-sprint-prs` — fetch merged + open PRs for the sprint period from GitHub
3. `/extract-jira-tickets` — parse unique SERV-XXXX tickets from PR titles
4. `/compute-pr-metrics` — calculate total, average merge time, fastest merge time

## Step-by-step workflow

### Step 1 — Get sprint metadata

Run `/fetch-sprint-meta` to obtain the sprint name, `startDate`, and `endDate`.

Derive:
- `START_DATE` / `END_DATE` for report headers (human-readable, e.g., "Sep 3" / "Sep 17")
- `START_TIMESTAMP` / `END_TIMESTAMP` in `YYYY-MM-DDTHH:MM:SSZ` for GitHub queries
- Sprint name for the report title

### Step 2 — Fetch PR data

Run `/fetch-sprint-prs start=[START_TIMESTAMP] end=[END_TIMESTAMP]`.

Save merged PR JSON to `sprint-reports/prs_<START_DD><END_DD><MM><YY>.json`.

### Step 3 — Extract Jira tickets

Run `/extract-jira-tickets file=sprint-reports/prs_[FILE_NAME].json`.

Build a unique sorted list of `SERV-XXXX` IDs found in merged PR titles.

### Step 4 — Compute PR metrics

Run `/compute-pr-metrics file=sprint-reports/prs_[FILE_NAME].json`.

Collect: total count, average merge time (HHh MMm), fastest merge time (HHh MMm).

### Step 5 — Fetch work in progress

Use the open PRs from Step 2 (created during sprint but not merged).

### Step 6 — Generate the report

Fill in the report template below using all collected data.

---

## Report Template

```markdown
# Sprint Review Status - [START_DATE] To [END_DATE]

## Key Highlights

- PRs merged: [N]; Avg merge: [HHh MMm]; Fastest: [HHh MMm]
- Associated Jira tickets: [N] unique tickets
- Deliverables completed: [N] items
- Work in progress: [N] PRs under review
- Notable achievements: [1-2 key accomplishments]

## Sprint Review - Development Summary Report

**Sprint Period:** [START_DATE] - [END_DATE], [YEAR]
**Developer:** Muhammad Kashif
**Status:** [Completed / In Progress]

### Sprint Summary:

[2-3 sentences on overall sprint work, key achievements, components worked on.]

### Sprint Deliverables:

• [Task/feature/fix with technical detail and business impact]
• [Architectural improvement or optimization]
• [Performance improvement or bug fix]
• [Refactoring or code quality improvement]

### Associated Jira Stories:

• [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

### Pull Requests Merged:

• [PR Title (SERV-XXXX)](PR_URL)

### Work in Progress:

• [PR Title (SERV-XXXX) - Status: Under Review](PR_URL)
```

## Save output

- **File name:** `sprint-status-report-[YYYY-MM-DD]-to-[YYYY-MM-DD].md`
- **Location:** `sprint-reports/`
- **Example:** `sprint-reports/sprint-status-report-2025-09-03-to-2025-09-17.md`

## Key Highlights generation rules

- **PRs merged metric:** `[Total Count]; Avg merge: [HHh MMm]; Fastest: [HHh MMm]`
- **Jira tickets:** Count unique SERV-XXXX from PR titles
- **Deliverables completed:** Count bullet points in Sprint Deliverables section
- **Work in progress:** Count open PRs
- **Notable achievements:** Pick 1-2 most impactful items from deliverables

## Tips

- Keep summary concise but impactful
- Group similar tasks in deliverables
- Highlight business value and technical improvements
- Include metrics when possible ("Fixed 3 critical bugs", "Improved performance by X%")
