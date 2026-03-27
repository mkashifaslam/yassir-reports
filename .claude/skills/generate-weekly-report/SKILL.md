# generate-weekly-report

Generate a weekly status report covering last Thursday through today, pulling data from Jira and GitHub.

## Trigger phrases

- "generate weekly report"
- "create weekly status report"
- "weekly report for this week"

## Composed commands

This skill orchestrates the following commands in order:

1. `/fetch-weekly-prs` — fetch merged PRs (last Thu → today) from GitHub
2. `/fetch-jira-issues` — query Jira for updated issues in the period
3. `/extract-jira-tickets` — parse SERV-XXXX tickets from PR titles
4. `/compute-pr-metrics` — calculate merge time metrics

## Step-by-step workflow

### Step 1 — Calculate date range

- **Start Date:** Last Thursday at 00:00:00 UTC
- **End Date:** Current date at 23:59:59 UTC
- Format timestamps as `YYYY-MM-DDTHH:MM:SSZ`

### Step 2 — Fetch PR data

Run `/fetch-weekly-prs start=[START_TIMESTAMP] end=[END_TIMESTAMP]`.

- If merged PRs found: save to `weekly-reports/prs_<SHORT_DATE>.json`
- If no merged PRs: fetch open PRs and add to "In Progress" section

### Step 3 — Fetch Jira issues

Run `/fetch-jira-issues since=[YYYY-MM-DD]`.

Map each issue to its report section by status:

| Status | Section |
|---|---|
| `In Progress`, `Review` | In Progress |
| `Blocked`, `Need More Work` (mine) | Blocked |
| `Done`, `Cancelled` | Done |
| `Ready For Testing`, `In Test` | Testing |
| `To Do`, `Open`, `Blocked` (mine/unassigned) + EPayment label | Next Action Items |

### Step 4 — Extract Jira tickets

Run `/extract-jira-tickets` on the PR JSON file.

### Step 5 — Compute PR metrics

Run `/compute-pr-metrics` on the merged PR JSON file.

### Step 6 — Generate the report

Fill in the report template below using all collected data.

---

## Report Template

```markdown
# Weekly Status Report - [StartDate] - [EndDate], [Year]

## Key Highlights

- PRs merged: [N]; Avg merge: [HHh MMm]; Fastest: [HHh MMm]
- Status counts — In Progress: [N], Done: [N], Testing: [N], Blocked: [N]
- Notable deliverables: [Title 1 - SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)
- Notable PRs: [PR Title 1](https://github.com/YAtechnologies/fs-epayment/pull/XXX)
- Blockers: [SERV-ZZZZ](https://yassir.atlassian.net/browse/SERV-ZZZZ) _(if any)_
- Next actions: [N]

## In Progress:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Blocked:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Done:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Testing:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Other Items:

- [PR Title](https://github.com/YAtechnologies/fs-epayment/pull/XXX)

## Next Action Items:

1. [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

---

**Report Period:** [StartDate] (Thursday) - [EndDate] ([Day])
**Generated on:** [Current Date]
**Repository:** [fs-epayment](https://github.com/YAtechnologies/fs-epayment)
**Jira Project:** [SERV - Financial Services](https://yassir.atlassian.net/browse/SERV)
```

## Save output

- **File name:** `weekly-status-report-YYYY-MM-DD-to-YYYY-MM-DD.md`
- **Location:** `weekly-reports/`

## Open PR handling (when no merged PRs)

When no merged PRs exist in the period:
- Fetch open PRs created within the period
- Add to "In Progress" section with format:
  `PR Title - SERV-XXXX (Jira Link) - PR Link (GitHub URL)`
- If PR title has no Jira ticket: `PR Title - PR Link (GitHub URL)`

## Important rules

- Only include items updated/created within the reporting period
- Use exact Jira issue titles and GitHub PR titles
- Next Action Items must have label `EPayment-New-Arch` OR title containing `[EPayment New Arch]`
- Blocked items assigned to me → "Blocked" section
- Blocked unassigned/others → "Next Action Items"
- Key Highlights must reflect computed metrics from sections below
