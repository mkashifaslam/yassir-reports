---
mode: agent
description: A prompt template for generating weekly status reports for developers.
---

# Weekly Status Report Generation Prompt

## Context and Background

I am Muhammad Kashif, a Senior Full Stack Engineer at Yassir working on the Financial Services team. I submit weekly status reports to my team and manager containing work completed and upcoming action items.

## Personal Details

- **GitHub Username:** kashif-yassir
- **Jira Email:** muhammad.kashif@yassir.com
- **Working Days:** Thursday, Sunday, Monday, Tuesday, Wednesday, Thursday (6-day work week)
- **Report Period:** From last Thursday 12:00 AM to current date/time

## Project Information

### Jira Project Details:

- **Name:** Financial Services
- **Key:** SERV
- **Base URL:** https://yassir.atlassian.net/browse/SERV-
- **Cloud ID:** 78a96af4-a80c-47e6-ab86-975e9a422169

### GitHub Repository Details:

- **Name:** fs-epayment
- **Owner:** YAtechnologies
- **Base URL:** https://github.com/YAtechnologies/fs-epayment

## Report Generation Instructions

### Step 1: Data Collection

1. **Activate Jira tools** to access project data
2. **Search for my Jira issues** assigned to me and updated since last Thursday:
   - JQL: `project = SERV AND assignee = currentUser() AND updated >= "YYYY-MM-DD"`
3. **Fetch my merged GitHub PRs** since last Thursday:
   - Search query: `repo:YAtechnologies/fs-epayment author:kashif-yassir is:merged merged:>=YYYY-MM-DD`
4. **Search for next action items** based on expanded criteria:
   - JQL: `project = SERV AND (status = "To Do" OR status = "Open" OR status = "Blocked") AND (assignee = currentUser() OR assignee is empty) AND (labels = "EPayment-New-Arch" OR summary ~ "[EPayment New Arch]") AND updated >= "YYYY-MM-DD"`

### Step 2: Report Structure and Categorization

#### Section Mapping by Jira Status:

- **In Progress:** Issues with status "In Progress" or "Review"
- **Blocked:** Issues with status "Blocked" or "Need More Work" (assigned to me)
- **Done:** Issues with status "Done" or "Cancelled"
- **Testing:** Issues with status "Ready For Testing" or "In Test"
- **Next Action Items:** Issues with status "Todo", "Open", or "Blocked" that are either assigned to me or unassigned, AND have label "EPayment-New-Arch" OR title contains "[EPayment New Arch]"

#### Report Format:

```markdown
# Weekly Status Report - [StartDate] - [EndDate], [Current Year]

## In Progress:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Blocked:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Done:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Testing:

- [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

## Other Items:

- [PR Title](https://github.com/YAtechnologies/fs-epayment/pull/XXX)

## Next Action Items:

1. [Jira Issue Title] - [SERV-XXXX](https://yassir.atlassian.net/browse/SERV-XXXX)

---

**Report Period:** [StartDate] (Thursday) - [EndDate] ([Day])  
**Generated on:** [Current Date]  
**Repository:** [fs-epayment](https://github.com/YAtechnologies/fs-epayment)  
**Jira Project:** [SERV - Financial Services](https://yassir.atlassian.net/browse/SERV)
```

### Step 3: File Generation and Storage

1. **Create markdown file** with filename format: `weekly-status-report-YYYY-MM-DD-to-YYYY-MM-DD.md`
2. **Save to directory:** `G:\Kashif MacBookPro Data\MacbookPro\yassir\yassir\Weekly Status\`
3. **Ensure proper markdown formatting** with clickable links

### Step 4: Date Calculations

- **Start Date:** Calculate last Thursday from current date
- **End Date:** Current date
- **Format:** YYYY-MM-DD for file names, readable format for report content

## Important Notes

- Only include items updated/created within the reporting period
- Use exact Jira issue titles and GitHub PR titles
- Ensure all links are properly formatted and clickable
- Include both Jira tickets and GitHub PRs in appropriate sections
- Focus on Financial Services (SERV) project items
- Next Action Items should prioritize items with EPayment New Arch label or title prefix
- Blocked items assigned to me go in "Blocked" section, blocked items unassigned/assigned to others go in "Next Action Items"
- Next Action Items must have either label "EPayment-New-Arch" OR title containing "[EPayment New Arch]"

## Tools Required

- Jira/Atlassian integration tools
- GitHub integration tools
- File creation and directory management tools
- Date calculation capabilities

## Example Usage

"Please generate my weekly status report for the period from last Thursday to today using the comprehensive prompt instructions."
