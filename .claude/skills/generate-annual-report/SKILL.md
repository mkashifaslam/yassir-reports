# generate-annual-report

Generate a comprehensive annual performance review based on GitHub PR history and Jira ticket data for a given year.

## Trigger phrases

- "generate annual report for [YEAR]"
- "annual performance review [YEAR]"
- "create annual review"

## Input

- **YEAR** (required): The target year, e.g., `2025`
- Derives automatically: `START_DATE = [YEAR]-01-01T00:00:00Z`, `END_DATE = [YEAR]-12-31T23:59:59Z`

## Step-by-step workflow

### Step 1 — Auth setup

```bash
gh auth status
# Switch if needed:
gh auth switch   # select kashif-yassir
```

### Step 2 — Fetch all merged PRs for the year

```bash
GH_PAGER=cat gh pr list \
  --repo YAtechnologies/fs-epayment \
  --state merged \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,mergedAt,createdAt,number,additions,deletions \
  --jq '[.[] | select(.mergedAt >= "[START_DATE]" and .mergedAt <= "[END_DATE]")]' \
  > ./annual-reports/prs_merged_[YEAR].json
```

### Step 3 — Fetch open and closed PRs

```bash
# Open PRs created in the year
GH_PAGER=cat gh pr list \
  --repo YAtechnologies/fs-epayment \
  --state open \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,createdAt,number,additions,deletions,isDraft \
  --jq '[.[] | select(.createdAt >= "[START_DATE]" and .createdAt <= "[END_DATE]")]'

# Closed (not merged) PRs
GH_PAGER=cat gh pr list \
  --repo YAtechnologies/fs-epayment \
  --state closed \
  --author kashif-yassir \
  --limit 1000 \
  --json title,url,closedAt,createdAt,number \
  --jq '[.[] | select(.createdAt >= "[START_DATE]" and .createdAt <= "[END_DATE]")]'
```

### Step 4 — Compute PR metrics

```bash
# Total count
cat ./annual-reports/prs_merged_[YEAR].json | jq '. | length'

# Average merge time (hours)
cat ./annual-reports/prs_merged_[YEAR].json | jq -r \
  '[.[] | (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 3600)] | add / length'

# Total lines changed
cat ./annual-reports/prs_merged_[YEAR].json | jq '[.[] | .additions + .deletions] | add'

# High-impact PRs (>500 lines)
cat ./annual-reports/prs_merged_[YEAR].json | jq \
  '[.[] | select((.additions + .deletions) > 500)] | .[] | {title, url, lines: (.additions + .deletions)}'

# Architectural/refactor/security PRs
cat ./annual-reports/prs_merged_[YEAR].json | jq -r \
  '.[] | select(.title | test("architect|refactor|security|performance|migration"; "i")) | {title, url}'
```

### Step 5 — Extract Jira tickets from Jira CSV

**File location:** `annual-reports/all-jira-tickets-[YEAR].csv`

```bash
# Count tickets assigned to me
grep "Muhammad Kashif" ./annual-reports/all-jira-tickets-[YEAR].csv | wc -l

# Tickets by status
grep "Muhammad Kashif" ./annual-reports/all-jira-tickets-[YEAR].csv | \
  awk -F',' '{print $6}' | sort | uniq -c

# Done tickets with summaries
grep "Muhammad Kashif" ./annual-reports/all-jira-tickets-[YEAR].csv | \
  grep "Done" | awk -F',' '{print $1 ": " $3}'
```

Cross-reference Jira tickets with PRs:
```bash
cat ./annual-reports/prs_merged_[YEAR].json | \
  jq -r '.[] | .title' | grep -o 'SERV-[0-9]*' | sort -u
```

### Step 6 — Generate the report

Analyze all collected data and write the report in the format below. Every achievement must reference specific evidence (PR numbers, commit hashes, file names). Do NOT invent achievements.

---

## Report Template

```markdown
# Annual Performance Review – [YEAR]

## Executive Summary

(5–7 concise bullet points on high-level impact)

## Key Metrics

- PRs opened:
- PRs merged:
- Commits:
- Reviews completed:
- Total lines changed:

## Major Achievements

### Feature Development

### Bug Fixes & Stability

### Architecture & Refactoring

### Performance & Security

## Technical Leadership & Collaboration

## Engineering Quality & Best Practices

## Representative Work (Evidence)

- PR #XXX – [Short impact summary]
- Commit abc123 – [Why it mattered]

## Overall Impact Assessment

(Concise evaluation suitable for promotion or performance calibration)
```

## Save output

- **File name:** `annual-performance-review-[YEAR].md`
- **Location:** `annual-reports/`

## Tone & style

- Professional, impact-focused, evidence-backed
- Suitable for senior engineering performance evaluation
- If evidence is insufficient for any section, state: "Insufficient repository evidence found for this category"
