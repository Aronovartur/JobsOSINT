# Scraper Engine Specification

## Overview
The Scraper Engine is responsible for populating the `JobPosting` table by fetching data from Company career pages. The scraping strategy is determined by the Company's associated `ATS` (Applicant Tracking System).

## Core Logic
The scraper operates on a **Strategy Pattern**:
1.  **Identify Target**: The scraper reads the `Company.ats` field.
2.  **Select Strategy**: Based on the ATS, a specific `BaseScraper` implementation is chosen (e.g., `GreenhouseScraper`, `LeverScraper`, or `GenericHTMLScraper`).
3.  **Fetch**: The scraper makes an HTTP GET request to `Company.career_website_link`.
4.  **Parse**:
    - **JSON-based ATS (e.g., Greenhouse API)**: Parses the JSON response directly.
    - **HTML-based ATS**: Uses `BeautifulSoup` to extract job list elements.
5.  **Normalize**: Converts raw data into `JobPosting` model fields (`title`, `url`, `external_id`, `description`, etc.).
6.  **Persist**: Uses `update_or_create` on `JobPosting` to avoid duplicates (keyed by `external_id` or `url`).

## Supported ATS Strategies

### 1. Greenhouse (JSON Mode)
Many companies use a Greenhouse board URL that returns JSON data.
- **Trigger**: `Company.ats.name == 'Greenhouse'`
- **Target URL**: `Company.career_website_link` (e.g., `https://boards-api.greenhouse.io/v1/boards/{board_token}/jobs`)
- **Parsing Logic**:
    - Iterates over the `jobs` list in the JSON response.
    - Maps `id` -> `JobPosting.external_id`.
    - Maps `absolute_url` -> `JobPosting.url`.
    - Maps `title` -> `JobPosting.title`.
    - **Note**: Description might need a separate fetch if not included in the list view.

### 2. Lever (JSON Mode)
Similar to Greenhouse, Lever often provides an API endpoint.
- **Trigger**: `Company.ats.name == 'Lever'`
- **Parsing Logic**: Maps Lever's JSON schema to our model.

### 3. Generic / Custom (HTML Mode)
For companies with custom career sites or unsupported ATS.
- **Trigger**: `Company.ats` is Null OR `Generic`.
- **Logic**: A more complex HTML parser.

### 4. Custom Python Logic
User-defined overrides for specific edge cases.
- **Trigger**: `ScraperConfig.is_custom_logic` is True.
- **Execution**: The system executes the python code stored in `ScraperConfig.custom_code`.
- **Interface**: The script must define a `def scrape(company_url):` function that returns a list of `JobPosting` dictionaries.
- **Security Check**: *Sandbox execution required to prevent restricted access (e.g., using `exec` in a restricted scope or Dockerized sandbox).*

## Execution Flow & Error Handling
1.  **Scheduler** (Celery Beat) picks up a `ScraperConfig`.
2.  **Worker** executes `run_scraper(config_id)`.
3.  **Try/Catch Block**:
    - If `is_custom_logic`: Execute user script.
    - Else: Load `ScraperStrategy`.
4.  **Result Logging**:
    - **Success**: Create `ScrapeLog` with status `SUCCESS` and count of jobs.
    - **Exception**: Catch any error, create `ScrapeLog` with status `FAILURE` and save the traceback to `error_message`.
5.  **Persistence**: `JobPosting` records are updated/created.

