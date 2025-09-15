# Copilot Instructions for AI Coding Agents

## Project Overview

This workspace organizes sprint and weekly status reports for the Payment Systems team, focusing on the "EPayment New Arch" initiative. Reports are generated in Markdown, summarizing PRs, Jira tickets, and technical highlights.

### Directory Structure

- `sprint-reports/`: Sprint-level summaries and PR logs.
- `weekly-reports/`: Weekly status reports.
- `.github/prompts/`: Contains separate prompt templates for each report type:
  - `sprint-status-report.prompt.md`: Sprint report generation prompt and template
  - `weekly-status-report.prompt.md`: Weekly report generation prompt and template

## Key Workflows

**Weekly Status Report Generation**  
Use `.github/prompts/weekly-status-report.prompt.md` to generate weekly reports.

- Inputs: Sprint/weekly period, developer name, PR JSON files, Jira ticket data.
- Output: Markdown report saved as `weekly_status_report_<START_MMDD><END_MMDD><YYYY>.md` in `weekly-reports/`.
- PR metrics: Only count PRs merged within the period. Compute average and fastest merge times.
- Jira queries: Filter by label `EPayment-New-Arch`, assignee `"Muhammad Kashif"`, and status.

**Sprint Reports**  
Use `.github/prompts/sprint-status-report.prompt.md` to generate sprint reports summarizing major deliverables, merged PRs, and associated Jira stories.

## Patterns & Conventions

- **Report Naming**  
  Weekly reports use the pattern `weekly_status_report_<START_MMDD><END_MMDD><YYYY>.md`.
- **PR Data Source**  
  PRs are listed in JSON files (e.g., `prs_03170925.json`) with fields: `createdAt`, `mergedAt`, `title`, `url`.
- **Jira Integration**  
  All ticket references use the SERV project and filter by label/assignee as above.
- **Markdown Structure**  
  Reports follow strict templates for consistency. See the relevant prompt file in `.github/prompts/` for required sections and formatting.

## Technical Highlights

- Recent work includes hybrid payment processing, error handling improvements, and refactoring for maintainability.
- Authentication and wallet requests require the `X-Country-Code` header.
- Tests focus on refund handling and payment service reliability.

## External Integrations

- Jira: SERV project, label `EPayment-New-Arch`, assignee `"Muhammad Kashif"`.
- GitHub: PRs referenced by direct links.

## Example Files

- `.github/prompts/sprint-status-report.prompt.md`: Sprint report prompt and template
- `.github/prompts/weekly-status-report.prompt.md`: Weekly report prompt and template

---

Please review and let me know if any sections need clarification or if there are additional project-specific patterns to document.
