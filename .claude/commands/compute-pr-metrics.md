# compute-pr-metrics

Compute PR merge time metrics (total count, average merge time, fastest merge time) from a PR JSON file.

## Usage

```
/compute-pr-metrics file=sprint-reports/prs_03170925.json
```

## Instructions

All calculations use the `createdAt` → `mergedAt` duration for merged PRs.

### Total PR count

```bash
cat <prs.json> | jq '. | length'
```

### Average merge time (in hours, decimal)

```bash
cat <prs.json> | jq -r '[.[] |
  (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 3600)] |
  add / length'
```

### Fastest merge time (in hours, decimal)

```bash
cat <prs.json> | jq -r '[.[] |
  (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 3600)] |
  min'
```

### All-in-one metrics summary

```bash
cat <prs.json> | jq -r '
  [.[] | (((.mergedAt | fromdateiso8601) - (.createdAt | fromdateiso8601)) / 3600)] as $times |
  "Total: \(. | length)",
  "Average: \($times | add / length | . * 100 | round / 100)h",
  "Fastest: \($times | min | . * 100 | round / 100)h"
'
```

## Output Format

Format times as `HHh MMm` — e.g., `2h 30m`, `45m`.

**Key Highlights line format:**
```
PRs merged: [N]; Avg merge: [HHh MMm]; Fastest: [HHh MMm]
```

## Notes

- Only compute metrics for PRs where `mergedAt` falls within the reporting period
- Sort PRs by `mergedAt` ascending for consistent output
