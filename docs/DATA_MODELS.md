# Data Models Specification

## Entity Relationship Diagram (ERD)

```mermaid
erDiagram
    ATS ||--o{ COMPANY : "hosts jobs for"
    ATS ||--o{ SCRAPER_CONFIG : "scraped by"
    COMPANY ||--o{ JOB_POSTING : "has"
    COMPANY ||--o{ SCRAPER_CONFIG : "scraped by"
    JOB_POSTING ||--o{ JOB_APPLICATION : "targeted by"
    USER ||--o{ JOB_APPLICATION : "applies to"
    USER ||--o{ RESUME : "owns"
    RESUME ||--o{ JOB_APPLICATION : "used in"

    ATS {
        string name
        string base_url
    }

    COMPANY {
        string name
        string website
        fk ats_id
        string career_website_link

    }

    JOB_POSTING {
        string title
        text description
        string url
        string external_id
        datetime posted_date
        boolean is_active
        fk company_id
    }

    SCRAPER_CONFIG {
        string name
        string target_type "ATS or COMPANY"
        fk target_id
        int frequency_hours
    }

    JOB_APPLICATION {
        fk user_id
        fk job_id
        fk resume_id
        string status
        int fit_score
        text fit_analysis
        datetime applied_at
    }

    RESUME {
        string name
        string file_path
        json content_json
        string source_url
        fk user_id
    }
```

## Django Model Definitions

### 1. `core.ATS`
Represents an Applicant Tracking System platform (e.g., 'Greenhouse', 'Levro').
- `name`: CharField(max_length=100, unique=True)
- `url_pattern`: CharField(max_length=255, blank=True) - *Regex or pattern to identify job URLs for this ATS.*

### 2. `core.Company`
- `name`: CharField(max_length=255)
- `ats`: ForeignKey('ATS', on_delete=SET_NULL, null=True) - *The primary ATS this company uses.*
- `career_page_url`: URLField()
- `career_website_link`: URLField(blank=True)
- `logo`: ImageField(upload_to='company_logos/', blank=True)

### 3. `jobs.JobPosting`
The raw job data scraped or added.
- `company`: ForeignKey('core.Company', on_delete=CASCADE)
- `title`: CharField(max_length=255)
- `description`: TextField() - *Full HTML or Markdown content.*
- `external_id`: CharField(max_length=255) - *Unique ID on the ATS.*
- `url`: URLField(unique=True)
- `posted_at`: DateTimeField(null=True)
- `is_active`: BooleanField(default=True)
- `discovered_at`: DateTimeField(auto_now_add=True)

### 4. `scraper.ScraperConfig`
Configuration for the scraping engine.
- `target_ats`: ForeignKey('core.ATS', null=True, blank=True)
- `target_company`: ForeignKey('core.Company', null=True, blank=True)
- `schedule_crontab`: CharField(max_length=100)
- `last_run`: DateTimeField(null=True)
- `is_custom_logic`: BooleanField(default=False)
- `custom_code`: TextField(blank=True) - *Python code to override default logic.*

### 5. `scraper.ScrapeLog`
Log of scraper execution results and errors.
- `scraper_config`: ForeignKey('ScraperConfig', on_delete=CASCADE)
- `started_at`: DateTimeField(auto_now_add=True)
- `finished_at`: DateTimeField(null=True)
- `status`: CharField(choices=['SUCCESS', 'partial', 'FAILURE'])
- `jobs_found`: IntegerField(default=0)
- `error_message`: TextField(null=True, blank=True) - *Stores traceback or error details.*

### 6. `resumes.Resume`
- `user`: ForeignKey(User, on_delete=CASCADE)
- `name`: CharField(max_length=255) - *e.g., "Full Stack Resume 2024"*
- `file`: FileField(upload_to='resumes/', null=True, blank=True)
- `content_json`: JSONField(null=True, blank=True) - *Structured data if added via URL/Text*
- `source_url`: URLField(null=True, blank=True)
- `created_at`: DateTimeField(auto_now_add=True)

### 7. `applications.JobApplication`
The core user interaction object.
- `user`: ForeignKey(User, on_delete=CASCADE)
- `job`: ForeignKey('jobs.JobPosting', on_delete=CASCADE)
- `resume`: ForeignKey('resumes.Resume', on_delete=SET_NULL, null=True) - *The specific resume version used.*
- `status`: CharField(choices=STATUS_CHOICES, default='TRACKED')
    - Choices: `['TRACKED', 'APPLIED', 'INTERVIEW', 'REJECTED', 'OFFER']`
- `fit_score`: IntegerField(null=True) - *0-100 score from LLM.*
- `fit_analysis`: TextField(null=True) - *LLM explanation of the fit.*

### 8. `applications.FitAnalysis` (Optional extension)
If we want to track multiple analyses or versions (e.g. using different prompts).
- `application`: ForeignKey('JobApplication', on_delete=CASCADE)
- `llm_model`: CharField(max_length=100) - *e.g. 'ollama/llama3', 'gpt-4'*
- `raw_response`: JSONField()
