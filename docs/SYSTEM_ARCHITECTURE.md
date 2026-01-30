# System Architecture

## Overview
The application will be deployed as a set of Docker containers orchestrated via `docker-compose`. The primary data store is PostgreSQL.

## Technology Stack
- **Backend Framework**: Django 5.x (Python 3.12+)
- **Database**: PostgreSQL 16
- **Task Queue**: Celery (for Scraping & Fit Analysis)
- **Message Broker**: Redis
- **LLM Provider**:
    - Local: Ollama (running in a container or host network)
    - Remote: OpenAI/Anthropic APIs

## Container Architecture

### Services
1.  **`web`**:
    - Django Application (Gunicorn / Uvicorn).
    - Exposes port `8000`.
    - Mounts source code volume for development.

2.  **`db`**:
    - Image: `postgres:16-alpine`.
    - Volume: `postgres_data:/var/lib/postgresql/data`.
    - Environment: `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD` configured via `.env`.

3.  **`redis`**:
    - Image: `redis:7-alpine`.
    - Used as the broker for Celery and caching.

4.  **`worker`**:
    - Django Celery worker instance.
    - Handles background scraping jobs and long-running LLM analysis tasks.
    - Scalable (can run multiple replicas).

5.  **`beat`** (Optional but recommended):
    - Celery Beat scheduler.
    - Triggers scheduled scrapes based on `ScraperConfig.schedule_crontab`.

6.  **`ollama`** (Optional):
    - Image: `ollama/ollama`.
    - Required only if running local inference inside the Docker network.
    - Volume: `ollama_models:/root/.ollama` to persist downloaded models.

## Volumes & Persistence
- `postgres_data`: Persists database records (Companies, Jobs, Applications).
- `media_volume`: Persists uploaded Resumes and Company Logos.
- `static_volume`: Shared volume for Nginx (production) to serve static assets.

## Networking
- All services communicate on a private bridge network `jobosi_net`.
- Only `web` (8000) is exposed to the host by default.
