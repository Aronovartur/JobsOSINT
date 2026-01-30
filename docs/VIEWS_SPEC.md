# Views & URL Specification

This document outlines the standard Django Views (UI) and URL patterns for the application.

## 1. Core / Dashboard
- **Dashboard**: `/`
    - Summary of tracked jobs, recent applications, and scrape status.

## 2. Companies
- **List View**: `/companies/`
    - **Data**: List of all companies.
    - **Filters**: By ATS (Greenhouse, Lever, etc.).
    - **Actions**: Add New Company.
- **Detail View**: `/companies/<int:pk>/`
    - **Data**: Company details, list of associated Scrapers, list of Jobs.

## 3. Jobs
- **List View**: `/jobs/`
    - **Data**: All Job Postings.
    - **Filters**: Active/Closed, Company, Title Search.
- **New Jobs View**: `/jobs/new/`
    - **Data**: Jobs discovered in the last 7 days.
    - **Default Sort**: `discovered_at` descending.
- **Detail View**: `/jobs/<int:pk>/`
    - **Data**: Full Job Description (Rendered Markdown/HTML).
    - **Actions**: "Track this Job", "Create Application", "Analyze Fit".

## 4. Scrapers
- **List View**: `/scrapers/`
    - **Data**: All Scraper Configurations.
    - **Columns**: Target Name, Last Run Status, Frequency.
- **Detail / Edit View**: `/scrapers/<int:pk>/`
    - **Data**: Configuration limits, logs of recent runs.
    - **Actions**: "Run Now", "Edit Config".
    - **Editor**: If "Custom Logic" is enabled, show **CodeMirror** (or Monaco) editor to modify the Python script.
- **Error Log View**: `/scrapers/errors/` (or `/scrapers/logs/`)
    - **Data**: List of failed `ScrapeLog` entries.
    - **Grouping**: Grouped by **Company Name**.
    - **Features**: Accordion expand to show stack trace/error details for each company.

## 5. Resumes
- **List View**: `/resumes/`
    - **Data**: User's uploaded resumes.
- **Detail View**: `/resumes/<int:pk>/`
    - **Data**: File preview, parsed JSON content (if applicable).

## 6. Job Applications
- **List View**: `/applications/`
    - **Data**: User's applications pipeline.
    - **Format**: Kanban Board or Table.
    - **Columns**: Job Title, Company, Status (Applied, Interviewing...), Fit Score.
- **Detail View**: `/applications/<int:pk>/`
    - **Data**: Timeline of updates, specific resume used, fit analysis notes.
    - **Actions**: Update Status, View Fit Analysis.
