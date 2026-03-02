# NetworkOps on Komodo

This guide covers deploying NetworkOps with Docker Compose on Komodo.

## Files Added

- `docker-compose.komodo-demo.yml` - Demo deployment (API + frontend, `DEMO_MODE=true`)
- `docker-compose.komodo.yml` - Production-ready baseline (frontend, API, Redis, PostgreSQL)
- `.env.komodo.example` - Minimal environment template for Komodo secrets

## Recommended Rollout Path

1. Deploy demo stack first using `docker-compose.komodo-demo.yml`
2. Validate login, topology rendering, and health endpoints
3. Deploy full stack using `docker-compose.komodo.yml`
4. Add worker profile if you need async jobs
5. Add MCP profile only when you need an always-on MCP container

## Komodo Demo Stack

Use compose file:

`docker-compose.komodo-demo.yml`

Required env/secrets:

- `JWT_SECRET` (strong random value)

Optional:

- `DASHBOARD_ORIGIN`

Ports:

- `3000` -> frontend
- `5001` -> API

## Komodo Full Stack

Use compose file:

`docker-compose.komodo.yml`

Default services:

- `frontend`
- `api`
- `redis`
- `postgres`

Optional profiles:

- `worker` -> starts `celery-worker` + `celery-beat`
- `mcp` -> starts `mcp-server`

Required env/secrets:

- `JWT_SECRET`
- `POSTGRES_PASSWORD`
- `REDIS_PASSWORD`
- `DEVICE_USERNAME` and `DEVICE_PASSWORD` (unless `DEMO_MODE=true`)

## Health and Readiness Checks

After deployment:

- API liveness: `GET /healthz`
- API readiness: `GET /readyz`
- Dashboard reachable on frontend port

## Notes for Komodo

- Keep secrets in Komodo environment variables, not in committed `.env` files.
- Start with only default services in full stack; add optional profiles later.
- If Komodo provides ingress/reverse proxy, expose only frontend externally and keep API internal when possible.
