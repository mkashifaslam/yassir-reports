# Yassir Reports — Project Instructions

## Project Overview

This workspace manages sprint and weekly status reports for the **Payment Systems / Financial Services** team at Yassir, focused on the **EPayment New Arch** initiative. Reports are generated in Markdown from GitHub PR data and Jira ticket data.

## Developer Identity

- **Name:** Muhammad Kashif
- **GitHub username:** `kashif-yassir`
- **Jira email:** `muhammad.kashif@yassir.com`
- **Working days:** Sunday – Thursday (6-day week, Thu–Thu reporting cycle)

## External Integrations

### GitHub

- **Repository:** `YAtechnologies/fs-epayment`
- **Base URL:** `https://github.com/YAtechnologies/fs-epayment`
- **Auth:** Use `gh auth status` to verify; switch with `gh auth switch` → select `kashif-yassir`
- **Always use `GH_PAGER=cat`** before `gh pr list` to avoid paging issues

### Jira

- **Project:** SERV (Financial Services)
- **Board ID:** `797`
- **Base URL:** `https://yassir.atlassian.net/browse/SERV-`
- **Cloud ID:** `78a96af4-a80c-47e6-ab86-975e9a422169`
- **Auth env vars:** `ATLASSIAN_EMAIL`, `ATLASSIAN_API_TOKEN`
- **Default filter:** label `EPayment-New-Arch`, assignee `Muhammad Kashif`
- **Ticket format in PR titles:** `(SERV-XXXX)` at end of title

## Directory Structure

```
reports/
├── sprint-reports/          # Sprint-level summaries + PR JSON files
├── weekly-reports/          # Weekly status reports + PR JSON files
├── annual-reports/          # Annual performance reviews + Jira CSV exports
├── .github/
│   └── prompts/             # Source prompt templates (do not modify)
└── .claude/
    ├── CLAUDE.md            # This file
    ├── commands/            # Atomic reusable commands
    └── skills/              # Composed multi-step workflows
```

## Naming Conventions

| Report type | File name pattern | Save location |
|---|---|---|
| Weekly report | `weekly-status-report-YYYY-MM-DD-to-YYYY-MM-DD.md` | `weekly-reports/` |
| Sprint report | `sprint-status-report-YYYY-MM-DD-to-YYYY-MM-DD.md` | `sprint-reports/` |
| Annual review | `annual-performance-review-YYYY.md` | `annual-reports/` |
| PR JSON (sprint) | `prs_<START_DD><END_DD><MM><YY>.json` | `sprint-reports/` |
| PR JSON (weekly) | `prs_<START_DD><END_DD><MM><YY>.json` | `weekly-reports/` |

**PR JSON filename example:** Sprint Sep 3–17 2025 → `prs_03170925.json`

## PR Data Source Strategy

1. **Primary:** GitHub CLI (`gh pr list`) — always try first
2. **Fallback:** Local JSON file matching the period under `sprint-reports/` or `weekly-reports/`
   - Select file whose encoded date range fully covers the target period
   - Filter to only include PRs where `mergedAt` falls within the period

## Jira Status Mapping (for report sections)

| Section | Jira statuses |
|---|---|
| In Progress | `In Progress`, `Review` |
| Blocked | `Blocked`, `Need More Work` |
| Done | `Done`, `Cancelled` |
| Testing | `Ready For Testing`, `In Test` |
| Next Action Items | `To Do`, `Open`, `Blocked` (unassigned or mine) + label `EPayment-New-Arch` |

## Technical Context

- Authentication and wallet requests require the `X-Country-Code` header
- Payment work spans hybrid processing, error handling, and refactoring
- Tests focus on refund handling and payment service reliability
- PR titles contain `SERV-XXXX` ticket references in parentheses at the end
