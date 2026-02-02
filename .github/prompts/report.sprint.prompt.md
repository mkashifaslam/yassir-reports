---
agent: agent
description: A prompt template for generating sprint status reports for developers.
---

## Sprint Status Report Template

# Sprint Review Status - [START_DATE] To [END_DATE]

## Key Highlights

- PRs merged: [N]; Avg merge: [HHh MMm]; Fastest: [HHh MMm]
- Associated Jira tickets: [N] unique tickets
- Deliverables completed: [N] items
- Work in progress: [N] PRs under review
- Sprint velocity: [N] points (if tracking story points)
- Notable achievements: [1-2 key accomplishments]

## Sprint Review - Development Summary Report

**Sprint Period:** [START_DATE] - [END_DATE], [YEAR]  
**Developer:** [YOUR_NAME]  
**Status:** [STATUS] (Work completed through [COMPLETION_DATE])

### Sprint Summary:

[Provide a high-level summary of what was accomplished during the sprint. Focus on key achievements, resolved issues, and delivered value. Mention the main components/modules worked on and overall impact.]

### Sprint Deliverables:

• [List each major task/feature/fix completed]
• [Include technical details and business impact]
• [Mention any architectural improvements or optimizations]
• [Add performance improvements or bug fixes]
• [Include any refactoring or code quality improvements]

### Associated Jira Stories:

• [JIRA-TICKET-1]
• [JIRA-TICKET-2]
• [JIRA-TICKET-3]

### Pull Requests Merged:

• [PR Title 1 (JIRA-TICKET)](PR_URL_1)
• [PR Title 2 (JIRA-TICKET)](PR_URL_2)
• [PR Title 3 (JIRA-TICKET)](PR_URL_3)

### Work in Progress:

• [PR Title 1 (JIRA-TICKET) - Status: Under Review](PR_URL_1)
• [PR Title 2 (JIRA-TICKET) - Status: In Development](PR_URL_2)
• [PR Title 3 (JIRA-TICKET) - Status: Pending Approval](PR_URL_3)

---

## How to Use This Template:

### Step 1: Replace Placeholders

- `[START_DATE]` → Sprint start date (e.g., "May 28")
- `[END_DATE]` → Sprint end date (e.g., "June 11")
- `[YEAR]` → Current year (e.g., "2025")
- `[YOUR_NAME]` → Your name (e.g "Muhammad Kashif")
- `[STATUS]` → "Completed" or "In Progress"
- `[COMPLETION_DATE]` → Date when work was completed

### Step 2: Fill Content Sections

1. **Key Highlights**: Generate this first after data collection
   - Count total merged PRs and calculate average/fastest merge times
   - Count unique Jira tickets from PR titles
   - Count deliverables from the Sprint Deliverables section
   - Count open PRs from Work in Progress section
   - Highlight 1-2 most impactful achievements
2. **Sprint Summary**: Write 2-3 sentences about overall sprint work
3. **Sprint Deliverables**: List major tasks completed (bullet points)
4. **Jira Stories**: Extract JIRA tickets from PR titles
5. **Pull Requests**: Copy PR titles and URLs for merged PRs
6. **Work in Progress**: List PRs created during sprint but not yet merged

### Step 3: Data Collection

0. **Get Sprint Dates & Info from Jira Board (SERV / Board 797)**
   - Prefer fetching sprint metadata (name, startDate, endDate, goal) from Jira Agile API instead of manual input.
   - Requirements: Set environment variables `ATLASSIAN_EMAIL` and `ATLASSIAN_API_TOKEN` (API token from Atlassian account). The board id is `797`.
   - Commands:

     ```bash
     # Active sprint on Board 797
     curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
       "https://yassir.atlassian.net/rest/agile/1.0/board/797/sprint?state=active" \
       | jq -r '.values[0] | {name, startDate, endDate, goal}'

     # If no active sprint, fallback to the most recent sprint (e.g., last closed or the next planned)
     curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
       "https://yassir.atlassian.net/rest/agile/1.0/board/797/sprint?state=closed" \
       | jq -r '.values | sort_by(.endDate) | last | {name, startDate, endDate, goal}'

     # Optionally fetch sprint by name (e.g., "Payments 26.01")
     # Note: filter client-side using jq
     curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
       "https://yassir.atlassian.net/rest/agile/1.0/board/797/sprint?state=active,closed,future" \
       | jq -r '.values[] | select(.name | contains("Payments 26.01")) | {name, startDate, endDate, goal}'
     ```

   - Use `startDate` and `endDate` from the response to derive `[START_DATE]`, `[END_DATE]`, and timestamps (`YYYY-MM-DDTHH:MM:SSZ`) for PR queries below. Use the sprint `name` to populate the report header (e.g., "Payments 26.01").

1. **Gather Sprint PR Data:**
   - Use the existing sprint PR JSON file with naming format: `prs_[START_DAY][END_DAY][MONTH][YEAR].json`
   - If file does not exist, fetch using GitHub CLI:
   - **Check current GitHub user:** `gh auth status`
     - **Switch to correct user if needed:** `gh auth switch` (select `kashif-yassir` if not active)
   - **Fetch merged PRs for the sprint period:**
     ```bash
     GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment --state merged --author kashif-yassir --json title,url,mergedAt,createdAt --jq '[.[] | select(.mergedAt >= "[START_TIMESTAMP]" and .mergedAt <= "[END_TIMESTAMP]")]'
     ```
   - `START_TIMESTAMP` and `END_TIMESTAMP` format: `YYYY-MM-DDTHH:MM:SSZ`

2. **Extract Jira Tickets:**
   - Parse PR titles to extract Jira ticket numbers (format: `SERV-XXXX`)
   - Create unique sorted list of Jira tickets
   - Command:
     ```bash
     cat ./sprint-reports/prs_[SPRINT_PRS_FILE_NAME].json | jq -r '.[] | .title' | grep -o 'SERV-[0-9]*' | sort -u
     ```

3. **Compute PR Metrics:**
   - **Total PR count:** Number of merged PRs in the sprint period
   - **Average merge time:** Calculate average duration from `createdAt` to `mergedAt` for all merged PRs
   - **Fastest merge time:** Find the shortest duration from `createdAt` to `mergedAt`
   - Format times as: [HHh MMm] (e.g., "2h 30m")

4. **Fetch Work in Progress PRs:**
   - Open PRs created during the sprint period:
     ```bash
     GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment --state open --author kashif-yassir --json title,url,createdAt --jq '[.[] | select(.createdAt >= "[START_TIMESTAMP]" and .createdAt <= "[END_TIMESTAMP]")]'
     ```

### Step 4: Quick Data Extraction

## How to Read Sprint PRs File

- The Sprint PRs JSON file name format is:

  ```
  prs_[START_DAY][END_DAY][MONTH_NUMBER][YEAR_LAST_TWO_DIGITS].json
  ```

  - `[START_DAY]`: Sprint start day (2 digits, e.g., "03")
  - `[END_DAY]`: Sprint end day (2 digits, e.g., "17")
  - `[MONTH_NUMBER]`: Month number (2 digits, e.g., "09" for September)
  - `[YEAR_LAST_TWO_DIGITS]`: Last two digits of the year (e.g., "25" for 2025)
  - `[SPRINT_PRS_FILE_NAME]`: The complete file name (e.g., "prs_03170925.json")

- Example file name for a sprint from September 3 to September 17, 2025:

  ```
  prs_03170925.json
  ```

- To read this file in your scripts or commands, use the constructed file name:

```
./sprint-reports/prs_[SPRINT_PRS_FILE_NAME].json
```

- Use this file for extracting PR titles and Jira tickets as shown in the template.

From your GitHub CLI JSON output:

```bash
# Extract PR titles and Jira tickets from merged PRs
cat ./sprint-reports/prs_[SPRINT_PRS_FILE_NAME].json | jq -r '.[] | "• \(.title)"'

# Extract unique Jira tickets
cat ./sprint-reports/prs_[SPRINT_PRS_FILE_NAME].json | jq -r '.[] | .title' | grep -o 'SERV-[0-9]*' | sort -u

# Get work in progress PRs (created during sprint, not yet merged)
GH_PAGER=cat gh pr list --repo YAtechnologies/fs-epayment --state open --author kashif-yassir --json title,url,createdAt --jq '[.[] | select(.createdAt >= "[START_TIMESTAMP]" and .createdAt <= "[END_TIMESTAMP]")]'
```

### Step 4: Professional Tips

- Keep summary concise but impactful
- Group similar tasks in deliverables
- Highlight business value and technical improvements
- Use consistent formatting for all PRs
- Include metrics when possible (e.g., "Fixed 3 critical bugs", "Improved performance by X%")
- Key Highlights should provide a quick executive summary of sprint performance

### Step 5: Key Highlights Generation Guidelines

The Key Highlights section provides an at-a-glance summary. Generate it using:

- **PRs merged metric:** `[Total Count]; Avg merge: [HHh MMm]; Fastest: [HHh MMm]`
- **Associated Jira tickets:** Count unique SERV-XXXX tickets from all PR titles
- **Deliverables completed:** Count bullet points in Sprint Deliverables section
- **Work in progress:** Count open PRs from Work in Progress section
- **Sprint velocity:** Include if tracking story points (optional)
- **Notable achievements:** Select 1-2 most impactful items from Sprint Deliverables

Example:

```
- PRs merged: 11; Avg merge: 1h 15m; Fastest: 45m
- Associated Jira tickets: 6 unique tickets
- Deliverables completed: 10 items
- Work in progress: 2 PRs under review
- Notable achievements: Hybrid payment integration improvements, Security vulnerabilities resolved
```

### Step 6: Save & Reuse

- Save this template in your notes/documents
- Copy and customize for each sprint
- Keep the same structure for consistency across sprints

### Step 7: Save the completed report as a markdown file for easy sharing and archiving.

- saved as `sprint-status-report-[START_DATE]-to-[END_DATE].md`
- saved at path: `./sprint-reports`
- Example: `./sprint-reports/sprint-status-report-2023-09-03-to-2023-09-17.md`
