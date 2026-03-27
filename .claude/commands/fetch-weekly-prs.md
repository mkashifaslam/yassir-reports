# fetch-weekly-prs

Fetch merged and open pull requests for a weekly reporting period (last Thursday → today).

## Usage

```
/fetch-weekly-prs
/fetch-weekly-prs start=2025-10-02T00:00:00Z end=2025-10-09T23:59:59Z
```

## Instructions

### Step 0 — Calculate date range

- **Start Date:** Last Thursday at 00:00:00 UTC
- **End Date:** Current date at 23:59:59 UTC
- Format: `YYYY-MM-DDTHH:MM:SSZ`

### Step 1 — Check GitHub auth

```bash
gh auth status
# If kashif-yassir is not active:
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

### Step 3 — If no merged PRs, fetch open PRs

```bash
GH_PAGER=cat gh pr list \
  --repo YAtechnologies/fs-epayment \
  --state open \
  --author kashif-yassir \
  --json title,url,createdAt \
  --jq '[.[] | select(.createdAt >= "[START_TIMESTAMP]" and .createdAt <= "[END_TIMESTAMP]")]'
```

### Step 4 — Save output

Save merged PR JSON to `weekly-reports/prs_<START_DD><END_DD><MM><YY>.json`.

`SHORT_DATE_FORMAT` = `<START_DD><END_DD><MM><YY>` — e.g., `25021025` for Oct 25–02, 2025.

## Fallback

If CLI fails, find the local JSON file whose encoded range covers the period:

```bash
# Filter to only PRs where mergedAt is within the period
cat ./weekly-reports/prs_[SHORT_DATE_FORMAT].json | \
  jq '[.[] | select(.mergedAt >= "[START_TIMESTAMP]" and .mergedAt <= "[END_TIMESTAMP]")]'
```
