# extract-jira-tickets

Extract unique, sorted Jira ticket IDs from PR title data.

## Usage

```
/extract-jira-tickets file=sprint-reports/prs_03170925.json
```

## Instructions

### From a local JSON file

```bash
cat ./sprint-reports/prs_[FILE_NAME].json \
  | jq -r '.[] | .title' \
  | grep -o 'SERV-[0-9]*' \
  | sort -u
```

### From piped PR titles

```bash
echo "[PR_TITLES_HERE]" | grep -o 'SERV-[0-9]*' | sort -u
```

### Print as formatted list

```bash
cat ./sprint-reports/prs_[FILE_NAME].json \
  | jq -r '.[] | .title' \
  | grep -o 'SERV-[0-9]*' \
  | sort -u \
  | awk '{print "• " $0}'
```

## Output

A unique sorted list of `SERV-XXXX` ticket IDs.

## Notes

- Ticket format in PR titles: `(SERV-XXXX)` at the end of the title
- Regex pattern: `SERV-[0-9]*`
- Each ticket ID can be turned into a clickable link: `https://yassir.atlassian.net/browse/SERV-XXXX`
