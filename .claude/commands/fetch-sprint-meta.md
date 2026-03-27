# fetch-sprint-meta

Fetch sprint metadata (name, start date, end date, goal) from Jira Board 797.

## Usage

```
/fetch-sprint-meta
/fetch-sprint-meta state=closed
/fetch-sprint-meta name="Payments 26.01"
```

## Instructions

Requires environment variables: `ATLASSIAN_EMAIL`, `ATLASSIAN_API_TOKEN`.

### Active sprint (default)

```bash
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  "https://yassir.atlassian.net/rest/agile/1.0/board/797/sprint?state=active" \
  | jq -r '.values[0] | {name, startDate, endDate, goal}'
```

### Most recent closed sprint (fallback)

```bash
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  "https://yassir.atlassian.net/rest/agile/1.0/board/797/sprint?state=closed" \
  | jq -r '.values | sort_by(.endDate) | last | {name, startDate, endDate, goal}'
```

### Sprint by name

```bash
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  "https://yassir.atlassian.net/rest/agile/1.0/board/797/sprint?state=active,closed,future" \
  | jq -r '.values[] | select(.name | contains("[SPRINT_NAME]")) | {name, startDate, endDate, goal}'
```

## Output

Use `startDate` and `endDate` from the response to derive:
- `START_DATE` / `END_DATE` for report headers (human-readable)
- `START_TIMESTAMP` / `END_TIMESTAMP` in `YYYY-MM-DDTHH:MM:SSZ` format for GitHub CLI PR queries
- Sprint `name` for the report title (e.g., "Payments 26.01")
