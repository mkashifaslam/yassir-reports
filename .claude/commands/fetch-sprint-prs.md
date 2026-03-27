# fetch-sprint-prs

Fetch merged and open (WIP) pull requests for a sprint period from the `fs-epayment` repo.

## Usage

```
/fetch-sprint-prs start=2025-09-03T00:00:00Z end=2025-09-17T23:59:59Z
```

## Instructions

### Step 1 — Check GitHub auth

```bash
gh auth status
# If kashif-yassir is not the active user:
gh auth switch   # select kashif-yassir
```

### Step 2 — Fetch merged PRs

```bash
GH_PAGER=cat gh pr list \
  --repo YAtechnologies/fs-epayment \
  --state merged \
  --author kashif-yassir \
  --json title,url,mergedAt,createdAt \
  --jq '[.[] | select(.mergedAt >= "[START_TIMESTAMP]" and .mergedAt <= "[END_TIMESTAMP]")]'
```

### Step 3 — Fetch open (WIP) PRs

```bash
GH_PAGER=cat gh pr list \
  --repo YAtechnologies/fs-epayment \
  --state open \
  --author kashif-yassir \
  --json title,url,createdAt \
  --jq '[.[] | select(.createdAt >= "[START_TIMESTAMP]" and .createdAt <= "[END_TIMESTAMP]")]'
```

### Step 4 — Save merged PR JSON

Save output to `sprint-reports/prs_<START_DD><END_DD><MM><YY>.json`.

Example for Sep 3–17 2025: `sprint-reports/prs_03170925.json`

## Fallback

If the CLI fails or is rate-limited, use the existing JSON file:

```bash
# Construct file name from sprint dates
# Filter to only PRs where mergedAt is within the sprint period
cat ./sprint-reports/prs_[SPRINT_PRS_FILE_NAME].json | \
  jq '[.[] | select(.mergedAt >= "[START_TIMESTAMP]" and .mergedAt <= "[END_TIMESTAMP]")]'
```

## Timestamp Format

`YYYY-MM-DDTHH:MM:SSZ` — e.g., `2025-09-03T00:00:00Z`
