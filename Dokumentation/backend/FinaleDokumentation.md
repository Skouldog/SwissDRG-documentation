# SwissDRG Data Search — Deployment & Maintenance Guide

> Version: 3.0  
> Last updated: 2026-05-11

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Architecture](#2-architecture)
3. [Prerequisites](#3-prerequisites)
4. [Initial Deployment](#4-initial-deployment)
5. [Configuration Reference](#5-configuration-reference)
6. [API Reference](#6-api-reference)
7. [Database](#7-database)
8. [Operations & Maintenance](#8-operations--maintenance)
9. [Troubleshooting](#9-troubleshooting)

---

## 1. System Overview

SwissDRG Data Search is a web application that allows users to search and filter patient data (EGIN datasets) using DRG logic expressions. Users upload EGIN datasets and workspace definitions, then write filter expressions to retrieve matching patients and export the results as CSV files.

**Key capabilities:**
- Upload and manage EGIN patient datasets (CSV format)
- Upload and manage workspace definitions (ZIP format)
- Filter patients using DRG logic expressions evaluated by the DLS Server
- Paginate, sort, and display results in a data table
- Export filtered results as CSV downloads
- Cache query results for fast repeat queries
- Admin panel for cache and thread configuration

---

## 2. Architecture

The application consists of five Docker services that work together:

```
┌─────────────────────────────────────────────────────────┐
│                     Docker Network                       │
│                                                          │
│  ┌──────────┐    ┌──────────┐    ┌──────────────────┐  │
│  │ Frontend │───▶│ Backend  │───▶│   PostgreSQL 15  │  │
│  │ (Vite /  │    │ (Rails 8 │    │   (Primary DB +  │  │
│  │  React)  │    │  API)    │    │   Job Queue +    │  │
│  │ Port 3002│    │ Port 3001│    │   Cache DB)      │  │
│  └──────────┘    └────┬─────┘    └──────────────────┘  │
│                       │                                  │
│              ┌────────┴──────────┐                      │
│              │                   │                       │
│  ┌───────────▼──┐    ┌──────────▼──────────┐           │
│  │  DLS Server  │    │  Background Worker   │           │
│  │ (Expression  │    │  (Good Job /         │           │
│  │  Evaluation) │    │   CSV Generation)    │           │
│  │  Port 8787   │    └─────────────────────┘           │
│  └──────────────┘                                        │
└─────────────────────────────────────────────────────────┘
```

| Service | Technology | Purpose |
|---|---|---|
| **frontend** | React 19 + TypeScript + Vite | User interface |
| **backend** | Ruby on Rails 8.1 (API mode) | REST API, business logic |
| **worker** | Good Job (Rails background jobs) | Asynchronous CSV generation |
| **db** | PostgreSQL 15 | Data persistence, caching, job queue |
| **dls-server** | SwissDRG DRG Language Server | DRG expression parsing and evaluation |

**Data flow for a patient search:**

```
User enters expression
  → Frontend sends POST /api/filters
  → Backend checks filter cache (PostgreSQL)
  → Cache miss: Backend calls DLS Server → receives matching patient IDs
  → Backend queries Parquet file (DuckDB) with patient IDs → paginates/sorts
  → Result returned as JSON; cache entry saved for future queries
```

---

## 3. Prerequisites

### Required Software

| Software | Version | Notes |
|---|---|---|
| Docker | 24.0 or later | [Install Docker](https://docs.docker.com/engine/install/) |
| Docker Compose | v2 plugin | Included with Docker Desktop |

No other software needs to be installed on the host machine — Ruby, Node.js, and all other dependencies run inside containers.

### Required Files

Before deploying, ensure you have the following files available:

| File | Description |
|---|---|
| `drg-language-server-1-1-1.tar` | DLS Server Docker image (provided by SwissDRG) |
| EGIN CSV file(s) | Patient dataset files to upload |
| Workspace ZIP file(s) | Workspace definition files to upload |

### Hardware Recommendations

| Resource | Minimum | Recommended |
|---|---|---|
| CPU | 2 cores | 4 cores |
| RAM | 4 GB | 8 GB |
| Disk | 20 GB | 50 GB+ (depends on dataset size) |

---

## 4. Initial Deployment

### Step 1 — Load the DLS Server Docker Image

The DLS Server image is distributed as a `.tar` file and must be loaded into Docker before the first start.

```bash
docker load -i drg-language-server-1-1-1.tar
```

Verify the image was loaded:

```bash
docker images | grep drg-language-server
```

### Step 2 — Create the Storage Directory Structure

The application stores uploaded files on the host filesystem. Create the required directories:

```bash
mkdir -p storage/egins storage/workspaces
```

### Step 3 — Start All Services

```bash
docker compose up --build -d
```

On the first run, Docker will download and build all images. This may take several minutes.

Check that all services are running:

```bash
docker compose ps
```

All five services (`db`, `backend`, `worker`, `frontend`, `dls-server`) should have status `running`.

### Step 4 — Initialize the Database

Run database migrations and seed initial settings:

```bash
docker compose exec backend rails db:migrate
docker compose exec backend rails db:seed
```

### Step 5 — Verify the Deployment

| Check | Command / URL |
|---|---|
| Frontend loads | Open http://localhost:3002 in a browser |
| Backend API responds | `curl http://localhost:3001/up` → should return `200 OK` |
| EGIN list endpoint | `curl http://localhost:3001/api/egins` → should return `[]` |
| DLS Server responds | `curl -I http://localhost:8787/batch_evaluate` → should return `200` |

### Step 6 — Upload Initial Data

Once the application is running, upload EGIN and workspace files through the admin panel in the frontend, or via the API:

**Upload an EGIN dataset:**
```bash
curl -X POST http://localhost:3001/api/admin/egins \
  -F "egin[title]=My Dataset" \
  -F "egin[file]=@/path/to/egin.csv"
```

**Upload a workspace:**
```bash
curl -X POST http://localhost:3001/api/admin/workspaces \
  -F "workspace[title]=My Workspace" \
  -F "workspace[file]=@/path/to/workspace.zip"
```

---

## 5. Configuration Reference

### Environment Variables

The following environment variables can be set in `docker-compose.yml` under the `backend` service:

| Variable | Default | Description |
|---|---|---|
| `RAILS_ENV` | `development` | Rails environment (`development` or `production`) |
| `DATABASE_URL` | `postgres://...` | PostgreSQL connection string |
| `DEBUG_PERFORMANCE_TIME` | `true` | Logs query timing to backend console |
| `READER_MODE` | `parquet` | Data reading mode: `parquet` (recommended), `duckdb`, or `ruby_csv` |
| `SOLID_QUEUE_IN_PUMA` | (unset) | Run background job worker inside the web process (not recommended in production) |
| `WEB_CONCURRENCY` | `1` | Number of Puma worker processes |
| `JOB_CONCURRENCY` | `1` | Number of background job threads |
| `RAILS_MASTER_KEY` | (required in production) | Decrypts Rails credentials; keep secret |

### Cache Size Limits

Cache limits can be changed at runtime via the admin API without restarting:

| Setting | Default | Description |
|---|---|---|
| `filter_cache_limit` | `100` | Maximum number of cached filter results |
| `download_cache_limit` | `10` | Maximum number of pre-generated CSV files |

When a limit is reached, the oldest cache entries are evicted automatically.

**View current limits:**
```bash
curl http://localhost:3001/api/admin/cache-size
```

**Update limits:**
```bash
curl -X PUT http://localhost:3001/api/admin/cache-size \
  -H "Content-Type: application/json" \
  -d '{"filter_cache_limit": 200, "download_cache_limit": 20}'
```

### Thread Count

The backend web server thread count can be changed via the admin API. **A server restart is required for this setting to take effect.**

**View current thread count:**
```bash
curl http://localhost:3001/api/admin/max-threads
```

**Update thread count (then restart backend):**
```bash
curl -X PUT http://localhost:3001/api/admin/max-threads \
  -H "Content-Type: application/json" \
  -d '{"max_threads": 5}'

docker compose restart backend
```

### Port Mapping

| Service | Internal Port | External Port (host) |
|---|---|---|
| frontend | 5173 | **3002** |
| backend | 3000 | **3001** |
| dls-server | 8787 | **8787** |
| db (PostgreSQL) | 5432 | *(not exposed externally)* |

To change the external ports, edit the `ports` section of the relevant service in `docker-compose.yml`.

### File Storage

Uploaded files (EGIN CSVs, Parquet files, workspace ZIPs, generated CSV exports) are stored under the `storage/` directory on the host machine, which is mounted as a Docker volume.

```
storage/
├── egins/          # (reserved — not used by Active Storage)
├── workspaces/     # (reserved — not used by Active Storage)
└── <hash>/         # Active Storage blobs (managed automatically)
```

**Important:** Do not delete or move files inside `storage/` manually. The database contains references to these files. If files are deleted, the application will report errors when accessing the affected EGIN or workspace.

---

## 6. API Reference

All endpoints are under the base URL `http://localhost:3001`.

### Health Check

| Method | Path | Description |
|---|---|---|
| `GET` | `/up` | Returns `200 OK` when the server is healthy |

---

### EGIN Management

#### List EGIN Datasets

```
GET /api/egins
```

**Response `200`:**
```json
[
  { "id": 1, "title": "Dataset 2024" },
  { "id": 2, "title": "Dataset 2023" }
]
```

---

#### Upload EGIN Dataset (Admin)

```
POST /api/admin/egins
Content-Type: multipart/form-data
```

| Field | Type | Required | Description |
|---|---|---|---|
| `egin[title]` | string | yes | Display name for the dataset |
| `egin[file]` | file (CSV) | yes | The EGIN CSV file |

The file is automatically converted to Parquet format after upload. This conversion is synchronous and may take time for large files.

**Response `201`:**
```json
{ "id": 3, "title": "New Dataset" }
```

**Response `422`:** Validation error (e.g., missing title or file).

---

#### Delete EGIN Dataset (Admin)

```
DELETE /api/admin/egins/:id
```

**Response `204`:** Deleted successfully.  
**Response `404`:** EGIN not found.

---

#### Export EGIN CSV (Admin)

```
GET /api/admin/egins/:id/egin
```

Returns the original CSV file as a download attachment.

**Response `200`:** CSV file.  
**Response `404`:** EGIN not found.

---

### Workspace Management

#### List Workspaces

```
GET /api/workspaces
```

**Response `200`:**
```json
[
  { "id": 1, "title": "Workspace 2024" }
]
```

---

#### Upload Workspace (Admin)

```
POST /api/admin/workspaces
Content-Type: multipart/form-data
```

| Field | Type | Required | Description |
|---|---|---|---|
| `workspace[title]` | string | yes | Display name for the workspace |
| `workspace[file]` | file (ZIP) | yes | The workspace ZIP file |

**Response `201`:**
```json
{ "id": 2, "title": "New Workspace" }
```

**Response `422`:** Validation error.

---

#### Delete Workspace (Admin)

```
DELETE /api/admin/workspaces/:id
```

**Response `204`:** Deleted successfully.  
**Response `404`:** Workspace not found.

---

#### Export Workspace ZIP

```
GET /api/workspaces/:id/export
```

Returns the workspace ZIP file as a download attachment.

**Response `200`:** ZIP file.  
**Response `404`:** Workspace not found.

---

### Patient Filtering

#### Filter Patients

```
POST /api/filters
Content-Type: application/json
```

**Request body:**
```json
{
  "filter": {
    "egin_id": 1,
    "workspace_id": 1,
    "logic_term": "age > 65 AND diagnosis = 'A10'",
    "page": 1,
    "per_page": 50,
    "sorting": {
      "key": "patient_id",
      "direction": "ASC"
    }
  }
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `egin_id` | integer | yes | ID of the EGIN dataset to query |
| `workspace_id` | integer | yes | ID of the workspace for expression evaluation |
| `logic_term` | string | yes | DRG logic expression |
| `page` | integer | no | Page number (default: 1) |
| `per_page` | integer | no | Results per page (default: 50) |
| `sorting.key` | string | no | Column to sort by |
| `sorting.direction` | string | no | `ASC` or `DESC` |

**Response `200`:**
```json
{
  "data": [ { "patient_id": "123", "age": 70, ... } ],
  "total_count": 842,
  "page": 1,
  "per_page": 50,
  "query_hash": "d41d8cd98f00b204...",
  "source": "dls"
}
```

| Field | Description |
|---|---|
| `data` | Array of patient rows for the current page |
| `total_count` | Total number of matching patients |
| `query_hash` | Use this hash to download results as CSV |
| `source` | `dls` = fresh result from DLS Server; `cache` = returned from cache |

**Response `404`:** EGIN or workspace not found.  
**Response `500`:** DLS Server error or expression evaluation failure.

---

#### Download Filtered Results as CSV

CSV generation runs in the background. After filtering, the CSV is prepared asynchronously. The download endpoint waits up to 15 seconds for it to be ready.

```
GET /api/filters/download?query_hash=<hash>
```

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query_hash` | string | yes | Hash returned by `POST /api/filters` |

**Response `200`:** CSV file as download attachment.  
**Response `404`:** No filter cache found for the given hash.  
**Response `202`:** CSV is still generating (caller should retry).

---

### Expression Parsing

#### Parse DRG Expression

```
GET /api/parse_expression?expression=<expr>
```

Forwards the expression to the DLS Server for syntax validation and parsing. Use this to check expressions before running a full filter query.

**Response `200`:** Parsed AST (JSON from DLS Server).  
**Response `5xx`:** DLS Server unreachable or expression invalid.

---

### Admin — Cache & Threads

#### Get Cache Limits

```
GET /api/admin/cache-size
```

**Response `200`:**
```json
{
  "filter_cache_limit": 100,
  "download_cache_limit": 10
}
```

---

#### Update Cache Limits

```
PUT /api/admin/cache-size
Content-Type: application/json
```

```json
{
  "filter_cache_limit": 200,
  "download_cache_limit": 20
}
```

Both fields are optional; omit one to leave it unchanged.

**Response `200`:** Updated settings.

---

#### Get Thread Count

```
GET /api/admin/max-threads
```

**Response `200`:**
```json
{ "max_threads": 3 }
```

---

#### Update Thread Count

```
PUT /api/admin/max-threads
Content-Type: application/json
```

```json
{ "max_threads": 5 }
```

`max_threads` must be an integer ≥ 1.

**Response `200`:** Updated value.  
**Response `422`:** Invalid value.

> **Note:** A backend restart is required for the new thread count to take effect:
> ```bash
> docker compose restart backend
> ```

---

## 7. Database

### Schema Overview

| Table | Purpose |
|---|---|
| `egins` | EGIN dataset metadata (title, file attachments) |
| `workspaces` | Workspace metadata (title, file attachment) |
| `filter_caches` | Cached filter results (patient IDs, query hash, CSV status) |
| `system_settings` | Key-value configuration (cache limits) |
| `active_storage_*` | Rails file attachment metadata (managed automatically) |
| `good_job_*` | Background job queue tables (managed automatically) |

### Connection Details (Development)

The PostgreSQL database runs inside Docker and is not exposed on a host port by default.

Access the database console:
```bash
docker compose exec db psql -U postgres -d app_development
```

### Running Migrations

After updating the application, always run migrations before starting the new version:

```bash
docker compose exec backend rails db:migrate
```

To check migration status:
```bash
docker compose exec backend rails db:migrate:status
```

### Backup

To create a database backup:

```bash
docker compose exec db pg_dump -U postgres app_development > backup_$(date +%Y%m%d).sql
```

To restore from a backup:

```bash
cat backup_20260101.sql | docker compose exec -T db psql -U postgres -d app_development
```

**Also back up the `storage/` directory**, which contains all uploaded files:

```bash
tar -czf storage_backup_$(date +%Y%m%d).tar.gz storage/
```

---

## 8. Operations & Maintenance

### Starting and Stopping

```bash
# Start all services (detached)
docker compose up -d

# Stop all services (containers preserved)
docker compose stop

# Stop and remove containers (data volumes preserved)
docker compose down

# Stop and remove containers AND data volumes (DESTRUCTIVE — all data lost)
docker compose down -v
```

### Viewing Logs

```bash
# All services
docker compose logs -f

# Backend only
docker compose logs -f backend

# Background worker only
docker compose logs -f worker

# DLS Server only
docker compose logs -f dls-server
```

### Restarting Individual Services

```bash
docker compose restart backend
docker compose restart worker
docker compose restart frontend
```

### Updating the Application

When a new version of the application is released:

1. Pull the latest code:
   ```bash
   git pull
   ```

2. Rebuild containers:
   ```bash
   docker compose build
   ```

3. Start updated containers:
   ```bash
   docker compose up -d
   ```

4. Run any new database migrations:
   ```bash
   docker compose exec backend rails db:migrate
   ```

### Clearing All Caches

Filter result caches can be cleared from the database without affecting EGIN or workspace data:

```bash
docker compose exec backend rails runner "FilterCache.destroy_all"
```

### Checking Background Jobs

Background jobs (CSV generation) are tracked in the `good_jobs` table. To check the status:

```bash
docker compose exec backend rails runner "puts GoodJob::Job.all.count; puts GoodJob::Job.where(finished_at: nil).count"
```

### Disk Usage

Monitor disk usage for the storage directory:

```bash
du -sh storage/
```

If disk space is low, old pre-generated CSV files can be purged (filter caches will regenerate them on next download request):

```bash
docker compose exec backend rails runner "FilterCache.where.not(generating_csv: true).find_each { |fc| fc.pregenerated_csv.purge if fc.pregenerated_csv.attached? }"
```

---

## 9. Troubleshooting

### Backend fails to start

**Symptom:** `docker compose ps` shows `backend` as `exited`.

**Check logs:**
```bash
docker compose logs backend
```

**Common causes:**
- Database not ready: wait a few seconds and retry `docker compose up -d`
- Missing `RAILS_MASTER_KEY` in production environment
- Port 3001 already in use on the host machine

---

### DLS Server not reachable

**Symptom:** Filter queries return 500 errors; backend logs show connection refused to `dls-server:8787`.

**Check:**
```bash
docker compose ps dls-server
curl -I http://localhost:8787/batch_evaluate
```

**Common causes:**
- DLS Server image was not loaded: run `docker load -i drg-language-server-1-1-1.tar`
- DLS Server crashed: `docker compose restart dls-server`

---

### Filter queries are slow

**Possible causes:**
- Large EGIN dataset with many matching patients
- DLS Server is under load
- Low thread count — increase via admin API and restart backend

**Check performance logs** (if `DEBUG_PERFORMANCE_TIME=true`):
```bash
docker compose logs -f backend | grep "ms"
```

---

### CSV download never completes

**Symptom:** Download button spins indefinitely.

**Check the worker:**
```bash
docker compose logs worker
docker compose ps worker
```

If the worker is not running:
```bash
docker compose up -d worker
```

If the worker keeps crashing, check for errors in logs. A stuck job can be cleared:
```bash
docker compose exec backend rails runner "GoodJob::Job.where(finished_at: nil).destroy_all; FilterCache.update_all(generating_csv: false)"
```

---

### EGIN upload fails

**Symptom:** Uploading a CSV returns a 422 error or the Parquet conversion fails.

**Check:**
- The file is valid CSV (comma-separated, UTF-8 encoded)
- The `storage/` directory is writable by Docker
- Disk space is available

```bash
docker compose logs backend | tail -50
```

---

### Database connection errors

**Symptom:** Backend logs show `PG::ConnectionBad` or similar.

**Check that PostgreSQL is running:**
```bash
docker compose ps db
docker compose logs db
```

**Restart the database:**
```bash
docker compose restart db
# Wait ~5 seconds, then restart backend
docker compose restart backend
```

---

### Complete Reset (Development Only)

> **Warning:** This destroys all data including uploaded EGINs, workspaces, and filter caches.

```bash
docker compose down -v
rm -rf storage/*
docker compose up -d
docker compose exec backend rails db:schema:load db:seed
```

---

## Appendix — File Formats

### EGIN CSV Format

EGIN files must be UTF-8 encoded CSV files with a header row. The application treats all columns as data columns — no specific column names are required, but `patient_id` is used as the default sort key if present.

### Workspace ZIP Format

Workspace ZIP files contain workspace definition files as expected by the DLS Server. The format is defined by SwissDRG. Contact SwissDRG for the workspace specification.

---

*For questions or issues, contact the development team.*
