---
agent: agent
description: A prompt template for generating sprint status reports for developers.
---

## Sprint Status Report Template

# Sprint Review Status - [START_DATE] To [END_DATE]

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

1. **Sprint Summary**: Write 2-3 sentences about overall sprint work
2. **Sprint Deliverables**: List major tasks completed (bullet points)
3. **Jira Stories**: Extract JIRA tickets from PR titles
4. **Pull Requests**: Copy PR titles and URLs for merged PRs
5. **Work in Progress**: List PRs created during sprint but not yet merged

### Step 3: Quick Data Extraction

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

### Step 5: Save & Reuse

- Save this template in your notes/documents
- Copy and customize for each sprint
- Keep the same structure for consistency across sprints

### Step 6: Save the completed report as a markdown file for easy sharing and archiving.

- saved as `sprint-status-report-[START_DATE]-to-[END_DATE].md`
- saved at path: `./sprint-reports`
- Example: `./sprint-reports/sprint-status-report-2023-09-03-to-2023-09-17.md`
