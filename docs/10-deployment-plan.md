# Pawradise – Docker Compose Deployment Plan

**Course:** SENG 321 – Software Engineering  
**Sprint:** Sprint 2  
**Responsible Role:** DevOps Engineer  
**Status:** Sprint 2 baseline – approved and merged

## 1. Technology Stack

| Component | Technology | Purpose |
|---|---|---|
| Frontend | React + Nginx | Web interface and reverse proxy |
| Backend | FastAPI (Python) | REST API and business logic |
| Database | MySQL | Persistent relational data |
| Orchestration | Docker Compose | Multi-container deployment |

## 2. Deployment Architecture

The application will consist of three Docker Compose services:

- `frontend`: Serves the React production build using Nginx.
- `backend`: Runs the FastAPI application.
- `db`: Runs MySQL with persistent storage.

Request flow:

Browser → Nginx (Port 80) → `/api/` → FastAPI (Port 8000) → MySQL (Port 3306)

## 3. Network and Port Configuration

- Only port `80` will be publicly exposed through Nginx.
- FastAPI will communicate through the internal Docker network.
- MySQL port `3306` will not be publicly exposed.
- During local development, FastAPI port `8000` may optionally be bound to `127.0.0.1` for Swagger UI access.
- Nginx will forward `/api/` requests to FastAPI.
- The frontend and API will share the same origin, avoiding the need for cross-origin requests.

## 4. Environment Variables and Security

Sensitive configuration will be stored in a local `.env` file and excluded from Git.

A `.env.example` file will document required configuration variables using placeholder values.

Required variables include:

- `MYSQL_DATABASE`
- `MYSQL_USER`
- `MYSQL_PASSWORD`
- `MYSQL_ROOT_PASSWORD`
- `DATABASE_URL`
- `JWT_SECRET`
- `JWT_ALGORITHM=HS256`
- `ACCESS_TOKEN_EXPIRE_MINUTES=30`

JWT access tokens will expire after 30 minutes. Refresh tokens are not planned for the initial version. Expired or invalid tokens will result in `401 Unauthorized`, requiring the user to log in again.

No real credentials or secret values will be committed to the repository.

## 5. Data Persistence

MySQL data will be stored in a Docker named volume called `mysql_data`.

The volume will preserve database data when containers are stopped, removed, or recreated, unless the volume is explicitly deleted.

## 6. Startup and Initialization

- **Single command:** The whole system starts with `docker compose up --build` (NFR-10).
- **Start order:** `depends_on` with health checks starts `db` first, then `backend`, and `frontend` (Nginx) last.
- **Admin accounts:** Admin users are created by a seed step during deployment, not through public registration (A-03).

## 7. Deployment Responsibilities

The DevOps Engineer is responsible for documenting the deployment architecture, Docker Compose service structure, network exposure, environment configuration, and persistent storage strategy.

Actual Dockerfiles, Compose configuration, and Nginx configuration will be implemented in a subsequent implementation task.

## 8. Architecture Alignment

This plan follows the team's approved React + FastAPI + MySQL architecture and supports the deployment and security requirements documented in `docs/06-architecture.md` and `docs/08-nfr.md`.
