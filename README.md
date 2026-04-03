# n8n + PostgreSQL Docker Starter

Small Docker-based starter project for running `n8n` with PostgreSQL as the main database.

This repository is set up for developers who want:

- a clean local `n8n` environment backed by PostgreSQL
- simple environment-based configuration
- persistent data through Docker volumes
- an easy place to install custom/community nodes

## Stack

- `n8n`
- `PostgreSQL`
- `Docker Compose`

## Project Structure

```text
.
|-- custom/
|   `-- nodes/
|       `-- package.json
|-- docker-compose.yml
|-- .env.example
`-- README.md
```

## Quick Start

1. Copy the environment template:

```powershell
Copy-Item .env.example .env
```

2. Update `.env` with your own credentials.

3. Start the stack:

```powershell
docker compose up -d
```

4. Open n8n:

```text
http://localhost:5678
```

5. Log in with the credentials from `.env`.

## PostgreSQL Integration

`n8n` is configured to use PostgreSQL instead of SQLite through these environment variables:

```yaml
DB_TYPE: postgresdb
DB_POSTGRESDB_HOST: postgres
DB_POSTGRESDB_PORT: 5432
DB_POSTGRESDB_DATABASE: ${POSTGRES_DB}
DB_POSTGRESDB_USER: ${POSTGRES_USER}
DB_POSTGRESDB_PASSWORD: ${POSTGRES_PASSWORD}
```

This means:

- the `n8n` container connects to the `postgres` service over the internal Docker network
- credentials are managed in `.env` instead of being hardcoded in the compose file
- PostgreSQL starts first and is health-checked before `n8n` boots

## Environment Variables

The main values you will usually change are:

| Variable | Purpose |
|---|---|
| `POSTGRES_USER` | PostgreSQL username |
| `POSTGRES_PASSWORD` | PostgreSQL password |
| `POSTGRES_DB` | PostgreSQL database name |
| `POSTGRES_PORT` | Host port mapped to PostgreSQL |
| `N8N_PORT` | Host port mapped to n8n |
| `N8N_HOST` | External hostname for n8n |
| `N8N_PROTOCOL` | `http` or `https` |
| `WEBHOOK_URL` | Public base URL used by webhooks |
| `GENERIC_TIMEZONE` | n8n timezone |
| `N8N_BASIC_AUTH_*` | Basic auth for local/dev access |

## Common Commands

Start:

```powershell
docker compose up -d
```

Stop:

```powershell
docker compose down
```

View logs:

```powershell
docker compose logs -f
```

Rebuild after editing local custom node dependencies:

```powershell
docker compose down
docker compose up -d
```

## Custom and Community Nodes

The repository includes a tracked custom nodes workspace in [`custom/nodes/package.json`](./custom/nodes/package.json).

The `custom` directory is mounted into the container here:

```text
/home/node/.n8n/custom
```

If you want to add more community or custom nodes:

1. Edit `custom/nodes/package.json`
2. Install dependencies in that folder if needed
3. Restart the stack

Example:

```powershell
cd custom/nodes
npm install n8n-nodes-some-package
cd ../..
docker compose up -d
```

## Persistence

Docker named volumes are used for:

- `postgres_data` for PostgreSQL data
- `n8n_data` for n8n user data and runtime state

That keeps data outside the repository while preserving it across container restarts.

## Why This Layout Helps Developers

- sensitive values are moved out of `docker-compose.yml`
- runtime state is excluded from git
- PostgreSQL-backed n8n is explicit and easy to modify
- custom extension support is already wired in
- health checks reduce startup race conditions

## Notes

- This setup is suitable for local development and team onboarding.
- For production, add stronger secrets management, HTTPS, backups, and a reverse proxy.
