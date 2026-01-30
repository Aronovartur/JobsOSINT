# Project Requirements

## 1. Project Overview
**Apps Name:** JobOSI (Job Seeker's Interface / OS using Intelligence)
**Purpose:** A job tracking application for job seekers to monitor new job postings from selected companies, track applications, and analyze fit using LLM tools (local Ollama or external APIs).

## 2. Target Audience
- **Primary:** Active Job Seekers.
- **Secondary:** Recruiters or headhunters managing multiple candidates (potential future scope).

## 3. Core Features
### Job Tracking & Scraping
- [ ] **Company & ATS Management**: specific companies and their associated ATS (Applicant Tracking System).
- [ ] **Scraping Engine**: Automated scraping configuration for `ATS` -> `Company` -> `Job` hierarchy.
- [ ] **Job Listing**: View and filter tracked jobs.

### Application Management
- [ ] **Application Tracking**: User can "track" a selected job.
- [ ] **Resume Management**: Upload specific resume copies used for applications.
- [ ] **Status Workflow**: Applied, Interviewing, Rejected, Offer, etc.

### Intelligence / Analysis
- [ ] **Fit Analysis**: Calculate "Overall Fit" score between Job Description and User Resume.
- [ ] **LLM Integration**: Support for local (Ollama) and remote (OpenAI, Anthropic, etc.) backends.

## 4. Data Entities (High Level)
- **ATS**: The platform hosting jobs (e.g., Greenhouse, Lever).
- **Company**: The hiring entity; linked to an ATS.
- **Job Description**: Specific role details.
- **Scraper**: Configurations to fetch jobs.
- **Job Application**: The intersection of a User, a Job, and their Resume/Analysis.
