# fetch-jira-issues

Query Jira for issues assigned to Muhammad Kashif in the SERV project, filtered by date and status.

## Usage

```
/fetch-jira-issues since=2025-10-02
/fetch-jira-issues since=2025-10-02 status=Done
```

## Instructions

Requires: `ATLASSIAN_EMAIL`, `ATLASSIAN_API_TOKEN` environment variables.

### All updated issues since a date (for weekly report)

```bash
# JQL: issues assigned to me, updated since START_DATE
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  "https://yassir.atlassian.net/rest/api/3/search" \
  --get \
  --data-urlencode 'jql=project = SERV AND assignee = currentUser() AND updated >= "[YYYY-MM-DD]"' \
  --data-urlencode 'fields=summary,status,labels,assignee' \
  | jq -r '.issues[] | "\(.key): \(.fields.summary) [\(.fields.status.name)]"'
```

### Next action items (unstarted EPayment work)

```bash
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  "https://yassir.atlassian.net/rest/api/3/search" \
  --get \
  --data-urlencode 'jql=project = SERV AND (status = "To Do" OR status = "Open" OR status = "Blocked") AND (assignee = currentUser() OR assignee is empty) AND (labels = "EPayment-New-Arch" OR summary ~ "[EPayment New Arch]") AND updated >= "[YYYY-MM-DD]"' \
  --data-urlencode 'fields=summary,status,labels,assignee' \
  | jq -r '.issues[] | "• \(.key): \(.fields.summary)"'
```

## Section Mapping

| Report section | Jira statuses to include |
|---|---|
| In Progress | `In Progress`, `Review` |
| Blocked | `Blocked`, `Need More Work` (assigned to me) |
| Done | `Done`, `Cancelled` |
| Testing | `Ready For Testing`, `In Test` |
| Next Action Items | `To Do`, `Open`, `Blocked` (mine or unassigned) + label `EPayment-New-Arch` or title `[EPayment New Arch]` |

## Output Format

Each item as a clickable Jira link:
```
- [Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)
```
