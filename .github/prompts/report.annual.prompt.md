---
agent: agent
description: Generate a comprehensive annual performance review report based on contributions in a GitHub repository.
---

# Annual Performance Review Prompt

## Input Parameters

When using this prompt, provide the following input:

- **YEAR**: The target year for the annual review (e.g., "2025", "2026")

The prompt will automatically derive:

- `START_DATE` → `[YEAR]-01-01T00:00:00Z`
- `END_DATE` → `[YEAR]-12-31T23:59:59Z`

**Example Usage:**

```
Generate annual report for YEAR=2025
Generate annual report for YEAR=2026
```

**Important Note on Data Collection:**

GitHub CLI by default limits results to 30 items. All data collection commands use `--limit 1000` to ensure complete PR history is fetched. If you have more than 1000 PRs in a year, increase this limit accordingly.

---

## Role & Goal

You are an **AI Engineering Performance Analyst**.

Your task is to generate my **Annual Performance Review for the year [YEAR]** based **strictly on evidence from this GitHub repository and linked repositories I have contributed to**.

---

## Primary Data Sources (Mandatory)

Analyze and extract insights from:

- Pull Requests created by me in [YEAR]
- Pull Requests reviewed or approved by me in [YEAR]
- Commits authored or co-authored by me in [YEAR]
- Merge history involving my contributions
- Issue discussions and design / architecture conversations (if present)

**Ignore all activity outside the year [YEAR].**

---

## Identity Matching Rules

Identify my contributions using:

- GitHub username(s) visible in this repository
- Commit author email(s)
- PR author metadata

If multiple identities exist, **consolidate them into a single contributor profile**.

---

## Analysis Requirements

### Quantitative Summary

Provide a clear numerical summary including:

- Number of PRs opened
- Number of PRs merged
- Number of commits
- Number of PR reviews / approvals
- Identification of high-impact or large PRs (based on scope, size, or architectural importance)

---

### Qualitative Achievements

Identify and summarize achievements supported by repository evidence:

- Major features delivered
- Critical bugs fixed
- Refactoring or architectural improvements
- Performance, scalability, or security improvements
- Developer experience improvements (tests, tooling, CI/CD, automation)

---

### Technical Leadership & Ownership

Highlight evidence of:

- Design or architectural ownership
- Cross-team collaboration
- Mentorship through PR reviews, discussions, or guidance
- Contributions that improved long-term maintainability

---

### Engineering Excellence

Assess evidence of:

- Code quality improvements
- Test coverage or reliability gains
- Reduction in technical debt
- Adoption or enforcement of best practices, standards, or patterns

---

## Evidence Rules (Strict)

- **Every achievement must reference specific evidence**, such as:
  - PR numbers
  - Commit hashes
  - File names or directories
- **Do NOT invent or infer achievements**
- If evidence is insufficient for any category, explicitly state:

> **“Insufficient repository evidence found for this category”**

---

## Output Format (Professional & Manager-Ready)

Generate the report in **Markdown** using the following structure:

```md
# Annual Performance Review – [YEAR]

## Executive Summary

(High-level impact in 5–7 concise bullet points)

## Key Metrics

- PRs opened:
- PRs merged:
- Commits:
- Reviews completed:

## Major Achievements

### Feature Development

### Bug Fixes & Stability

### Architecture & Refactoring

### Performance & Security

## Technical Leadership & Collaboration

## Engineering Quality & Best Practices

## Representative Work (Evidence)

- PR #123 – Short impact summary
- Commit abc123 – Why it mattered

## Overall Impact Assessment

(Concise evaluation suitable for promotion or performance calibration)
```

---

## Tone & Style

- Professional
- Impact-focused
- Clear and concise
- Suitable for senior engineering performance evaluation

---

## Data Collection Steps

### Step 1: Set Up Authentication

Ensure you have the necessary credentials configured:

- **GitHub CLI Authentication:**

  ```bash
  # Check current GitHub user
  gh auth status

  # Switch to correct user if needed
  gh auth switch  # Select kashif-yassir if not active
  ```

---

### Step 2: Fetch All PRs Created in [YEAR]

Collect all PRs authored by you in [YEAR] across relevant repositories:

**IMPORTANT:** Use `--limit` parameter to fetch all PRs (GitHub CLI defaults to 30 results). Set limit to a high value (e.g., 1000) to ensure complete data collection.

```bash
# Fetch all merged PRs in [YEAR] (limit set to 1000 to capture all PRs)
GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment \
  --state merged \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,mergedAt,createdAt,number,additions,deletions \
  --jq '[.[] | select(.mergedAt >= "[START_DATE]" and .mergedAt <= "[END_DATE]")]'

# Fetch all open PRs created in [YEAR]
GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment \
  --state open \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,createdAt,number,additions,deletions \
  --jq '[.[] | select(.createdAt >= "[START_DATE]" and .createdAt <= "[END_DATE]")]'

# Fetch all closed (but not merged) PRs in [YEAR]
GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment \
  --state closed \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,closedAt,createdAt,number \
  --jq '[.[] | select(.createdAt >= "[START_DATE]" and .createdAt <= "[END_DATE]")]'
```

---

### Step 3: Fetch All PRs Reviewed in [YEAR]

Collect PRs where you provided reviews or approvals:

```bash
# Fetch all PRs from [YEAR] to check for your reviews (limit to 1000)
GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment \
  --state all \
  --limit 1000 \
  --json number,title,url,createdAt,mergedAt,author \
  --jq '[.[] | select(.createdAt >= "[START_DATE]" and .createdAt <= "[END_DATE]")]' > ./annual-reports/all_prs_for_review_[YEAR].json

# Then iterate through each PR to find your reviews
# Note: This is a manual process as GitHub CLI doesn't have direct review filtering by user
cat ./annual-reports/all_prs_for_review_[YEAR].json | jq -r '.[].number' | \
  while read pr_number; do
    echo "Checking PR #$pr_number..."
    gh pr view $pr_number --repo YAtechnologies/fs-epayment --json reviews \
      --jq ".reviews[] | select(.author.login == \"kashif-yassir\") | {pr: $pr_number, state: .state, submittedAt: .submittedAt}"
  done > ./annual-reports/prs_reviewed_[YEAR].jsonl

# Alternative: Get review activity for specific PR
gh pr view <PR_NUMBER> --repo YAtechnologies/fs-epayment --json reviews
```

---

### Step 4: Fetch Commit History in [YEAR]

Collect all commits authored or co-authored by you:

```bash
# Fetch commits from main branch in [YEAR]
gh api repos/YAtechnologies/fs-epayment/commits \
  --field author=kashif-yassir \
  --field since=[START_DATE] \
  --field until=[END_DATE] \
  --jq '.[] | {sha: .sha, message: .commit.message, date: .commit.author.date, url: .html_url}'

# Alternative: Clone and use git log
git log --author="kashif-yassir" --since="[YEAR]-01-01" --until="[YEAR]-12-31" \
  --pretty=format:"%H|%an|%ae|%ad|%s" --date=iso
```

---

### Step 5: Extract Jira Tickets from CSV

Use the pre-exported Jira CSV file instead of querying the Atlassian API:

**File Location:** `annual-reports/all-jira-tickets-[YEAR].csv`

**CSV Structure:**

- Issue key: SERV-XXXX format
- Summary: Ticket description
- Assignee: Muhammad Kashif (filter for your tickets)
- Status: Done, In Progress, etc.
- Created: Ticket creation date
- Updated: Last update date
- Labels: e.g., EPayment-New-Arch, New_Arch_BE

**Commands to analyze:**

```bash
# Count total tickets assigned to you in [YEAR]
cat ./annual-reports/"all-jira-tickets-[YEAR].csv" | \
  grep "Muhammad Kashif" | wc -l

# Extract unique ticket keys
cat ./annual-reports/"all-jira-tickets-[YEAR].csv" | \
  grep "Muhammad Kashif" | \
  cut -d',' -f1 | tail -n +2 | sort -u

# Group tickets by status
cat ./annual-reports/"all-jira-tickets-[YEAR].csv" | \
  grep "Muhammad Kashif" | \
  awk -F',' '{print $6}' | sort | uniq -c

# Filter by label (EPayment-New-Arch)
cat ./annual-reports/"all-jira-tickets-[YEAR].csv" | \
  grep "Muhammad Kashif" | \
  grep "EPayment-New-Arch"

# Extract ticket summaries for Done tickets
cat ./annual-reports/"all-jira-tickets-[YEAR].csv" | \
  grep "Muhammad Kashif" | \
  grep "Done" | \
  awk -F',' '{print $1 ": " $3}'
```

**Cross-reference with PRs:**

```bash
# Extract Jira tickets from PR titles
cat <merged_prs.json> | jq -r '.[] | .title' | grep -o 'SERV-[0-9]*' | sort -u

# Compare with Jira CSV to ensure completeness
```

---

### Step 6: Calculate PR Metrics

Compute key metrics from collected data:

```bash
# Total PR count
cat <merged_prs.json> | jq '. | length'

# Average merge time (hours)
cat <merged_prs.json> | jq -r '[.[] |
  (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 3600)] |
  add / length'

# Fastest merge time
cat <merged_prs.json> | jq -r '[.[] |
  (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 3600)] |
  min'

# Total lines changed (additions + deletions)
cat <merged_prs.json> | jq '[.[] | .additions + .deletions] | add'
```

---

### Step 7: Identify High-Impact PRs

Filter PRs by size, scope, or importance:

```bash
# Large PRs (>500 lines changed)
cat <merged_prs.json> | jq '[.[] | select((.additions + .deletions) > 500)] |
  .[] | {title, url, lines: (.additions + .deletions)}'

# PRs with specific keywords (architectural, refactor, security, performance)
cat <merged_prs.json> | jq -r '.[] | select(.title |
  test("architect|refactor|security|performance|migration"; "i")) |
  {title, url}'
```

---

### Step 8: Fetch Work in Progress

Identify ongoing work at year-end:

```bash
# Open PRs at the end of [YEAR] (created during the year but still open)
GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment \
  --state open \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,createdAt,isDraft \
  --jq '[.[] | select(.createdAt >= "[START_DATE]" and .createdAt <= "[END_DATE]")]'
```

---

### Step 9: Cross-Repository Analysis (If Applicable)

If you contributed to multiple repositories, repeat Steps 2-4 for each:

```bash
# Example for another repository (with --limit to fetch all)
GH_PAGER=cat gh pr list --repo YAtechnologies/<other-repo> \
  --state merged \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,mergedAt,createdAt \
  --jq '[.[] | select(.mergedAt >= "[START_DATE]" and .mergedAt <= "[END_DATE]")]'
```

---

### Step 10: Consolidate All Data

Combine all collected data into structured files:

- `prs_merged_[YEAR].json` – All merged PRs
- `prs_reviewed_[YEAR].json` – All reviewed PRs
- `commits_[YEAR].json` – All commits
- `jira_tickets_[YEAR].txt` – Unique Jira tickets from Jira CSV
- `metrics_[YEAR].json` – Computed metrics

Use these consolidated files as input for generating the annual review report.

---

## Final Report Generation

### Output Requirements:

1. **File Name:** `annual-performance-review-[YEAR].md`
2. **Location:** Save in `annual-reports/` folder
3. **Format:** Markdown as specified in the "Output Format" section above

### Generation Steps:

1. Analyze all collected data from Steps 1-10
2. Generate quantitative metrics summary
3. Identify and categorize achievements with evidence
4. Write professional, manager-ready content
5. Save the complete report to: `annual-reports/annual-performance-review-[YEAR].md`

---

## Final Instruction

Begin analysis now using:

- Repository context
- Data collected through Steps 1-10 above
- Jira tickets from `annual-reports/all-jira-tickets-[YEAR].csv`

Generate the **complete, evidence-backed annual performance review** and save it as `annual-reports/annual-performance-review-[YEAR].md`.
