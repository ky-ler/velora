# Velora

Velora is a full-stack project management app with Kanban-style boards, live collaboration, and role-based access controls.

## Highlights

- Multi-board workspace with customizable columns
- Drag-and-drop task flow with real-time board updates
- Tasks with assignees, labels, due dates, comments, and activity history
- Checklist items and board/task activity feeds
- Invite links and collaborator role management
- Auth0 integration for authentication and authorization

## Tech Stack

| Layer  | Technologies                                                                                                       |
| ------ | ------------------------------------------------------------------------------------------------------------------ |
| API    | Java 25, Spring Boot, Spring Security OAuth2 Resource Server, Spring Data JPA, Flyway, PostgreSQL, WebSocket/STOMP |
| Client | React 19, TypeScript, Vite, TanStack Router/Query, Tailwind CSS, shadcn/ui, Auth0 React                            |
| Infra  | Docker Compose, Caddy, GitHub Actions, GHCR                                                                        |

## Repository Layout

```text
api/                    Spring Boot API
client/                 React + Vite frontend
docker-compose.yml      Local Postgres service
docker-compose.prod.yml Production stack (Postgres + API + Caddy)
Caddyfile               Production reverse proxy config
run-api.sh              Local helper (loads .env, starts DB + API)
```

## Quickstart (Local)

### Prerequisites

- Java 25
- Node.js 24+ and npm
- Docker
- Auth0 tenant (SPA app + API)

### 1) Configure root environment

```bash
cp .env.dev.example .env
```

Edit `.env` for your environment (especially Auth0 and CORS values).

### 2) Start PostgreSQL

```bash
docker compose up -d
```

### 3) Configure client environment

```bash
cp client/.env.example client/.env
```

Edit `client/.env` with your Auth0 values and any client URL overrides.

### 4) Run the API (dev profile)

Option A (manual):

```bash
cd api
DB_USERNAME=your_db_username DB_PASSWORD=your_db_password ./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
```

Option B (helper script from repo root):

```bash
./run-api.sh
```

This script expects a root `.env` file (used for DB and API env values).

### 5) Run the client

```bash
cd client
npm install
npm run dev
```

Client: `http://localhost:5173`  
API: `http://localhost:8080`

## Useful Commands

| Task                           | Command                                   |
| ------------------------------ | ----------------------------------------- |
| API tests (unit + integration) | `cd api && ./mvnw verify -B`              |
| API package (skip tests)       | `cd api && ./mvnw package -DskipTests -B` |
| Client lint                    | `cd client && npm run lint`               |
| Client build                   | `cd client && npm run build`              |
| Regenerate API client          | `cd client && npm run generate:api`       |

## API Docs

When the API is running with docs enabled:

- Swagger UI: `http://localhost:8080/swagger-ui.html`
- OpenAPI JSON: `http://localhost:8080/api-docs`
- OpenAPI YAML: `http://localhost:8080/api-docs.yaml`

## Deployment Overview

- `docker-compose.prod.yml` runs PostgreSQL, the API, and Caddy.
- `Caddyfile` proxies `${DOMAIN}` traffic to `api:8080`.
- Use `.env.production.example` as the deployment environment template.
- GitHub Actions:
  - `ci.yml`: API verify + client lint/build on push/PR to `main`
  - `deploy.yml`: on successful CI, build/push API image and deploy on the configured droplet

## License

Licensed under the GNU Affero General Public License v3.0. See [LICENSE](LICENSE).
