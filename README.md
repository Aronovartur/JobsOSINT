# JobsOSINT

**JobsOSINT** is an open-source market intelligence tool designed to automate the tracking, scrapping, and analysis of company career pages. It serves as a personal "Head of Recruiting" bot, helping job seekers and researchers maintain a live database of opportunities across their target companies without manual checking.

Unlike generic job boards, JobsOSINT goes directly to the source—company career pages—to find roles that may not be aggregated elsewhere. It utilizes specialized scrapers for major ATS platforms (Greenhouse, Lever, Workday, SmartRecruiters, etc.) and a generic Selenium fallback for custom sites.

##  Core Features

### 1. Target Company Tracking
- Maintain a curated list of companies you are interested in.
- **Automated Enrichment**: Built-in script to automatically enrich company data metadata (Industry, Size, Ranking, etc.).
- Monitor "Pivot Potential" and presence in specific regions (e.g., Canada).

### 2. Intelligent Job Scraping
- **Nightly Automation**: Automated nightly workflows that scour your target companies and generate a daily digest of **newly created jobs**.
- **Automatic Status Tracking**: Automatically detects when jobs are removed from the ATS and marks them as "Filled" in your database, keeping your pipeline clean.
- **Platform-Aware Scrapers**: Specialized, high-reliability scrapers for:
  - Greenhouse
  - Lever
  - Workday
  - SmartRecruiters
  - Rippling
  - HubSpot
- **Generic Selenium Scraper**: A fallback engine for custom career pages that aren't on standard ATS platforms.
- **XML Sitemap Support**: Ability to parse career site XML sitemaps for rapid discovery.

### 3. Data Enrichment & Analysis
- **Automated Compatibility Scoring**: Nightly vector builds automatically analyze new jobs against your profile to generate compatibility scores, saving you from reading irrelevant JDs.
- **Full Description Fetching**: Downloads the complete HTML/Text of job descriptions for deeper analysis.
- **Vector Embeddings**: Uses `pgvector` for semantic search and similarity matching.

### 4. Application Management
- **Application Tracking**: Log your applications, track their status, and store notes.
- **Resume Management**: Upload and link specific resumes to applications for future interview preparation.

### 5. Search & Organization
- **Unified Dashboard**: View all jobs from all companies in a single, searchable interface.
- **Status Tracking**: Track which companies have been scraped successfully and which encountered errors.
- **Filtering**: Filter by "Active", "Filled", or specific keywords in the job title/description.

## 🛠️ Tech Stack

- **Backend**: Django (Python 3.9+)
- **Database**: PostgreSQL with `pgvector` extension
- **Scraping**: Selenium WebDriver (Chrome), BeautifulSoup4
- **Containerization**: Docker & Docker Compose

##  Limitations

This is a **demo/prototype** repository and comes with inherent limitations:

1.  **Bot Detection**: While scraping is done via a real browser (Selenium), aggressive WAFs (Cloudflare, Akamai) or bot detection systems on some career sites may block requests.
2.  **Fragile Selectors**: Career pages change frequently. CSS selectors for the "Generic" scraper or even specific ATS parsers may break and require maintenance.
3.  **Performance**: Selenium is resource-intensive. Bulk scraping hundreds of companies can take significant time and system resources.
4.  **Workday Complexity**: Workday is notoriously difficult to scrape due to dynamic loading and session management; the Workday scraper is experimental.
5.  **Local Execution**: Currently designed to run locally or on a VPS; not optimized for serverless or distributed cloud deployment.

##  Roadmap

We are actively working on improving JobsOSINT. Here is what's coming:

- [ ] **ReactiveResume Integration**: Tighter integration with the ReactiveResume project.
- [ ] **LLM Integration**: Use local LLMs (Ollama/Llama 3) to summarize job descriptions and extract tech stacks automatically.
- [ ] **Headless Improvements**: Integrate lighter-weight scraping tools (e.g., Playwright) for faster execution.
- [ ] **Notification System**: Slack/Email alerts when new jobs matching specific criteria are found.
- [ ] **Auto-Apply (Experimental)**: Basic form-filling capabilities for simplified applications.
- [ ] **Proxy Rotation**: Built-in support for proxy services to improve success rates against WAFs.
- [ ] **Analytics Dashboard**: Visualizations for hiring trends (e.g., "Which companies are freezing hiring?").

## Contributing

This is a public demo project. Pull requests, issue reports, and feature suggestions are welcome!

## License

MIT
