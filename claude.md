# DevOps Scaffolding Guide

## Purpose
This repo provides a minimal local stack (MongoDB, Redis, Postgres, Keycloak) via Docker Compose. Extend it safely and keep consistency across services.

---

## Core Principles
- Prefer **official images** and pinned versions
- Use **`restart: unless-stopped`**
- Always include **healthchecks**
- Persist data via **named volumes**
- Never hardcode secrets → use `.env` where possible
- Services must be **self-contained and reproducible**

---

## Running the Stack
```bash
docker compose up -d
```

## Check status:
```bash
docker compose ps
docker compose logs -f <service>
```

## Stop:
```bash
docker compose down
```

## Reset (deletes data):
```bash
docker compose down -v
```

## Service Access

MongoDB: `mongodb://admin:admin_secret@localhost:27017`
Redis: `redis://:admin_secret@localhost:6379`
Postgres: `postgres://admin:admin_secret@localhost:5432/db-main`
Keycloak: `http://localhost:8080` (admin/admin_secret)

## Testing Services

MongoDB:
```bash
mongosh mongodb://admin:admin_secret@localhost:27017
```
Redis:
```bash
redis-cli -a admin_secret ping
```
Postgres:
```bash
psql -h localhost -U admin -d db-main
```
Keycloak:
```bash
curl http://localhost:8080/health/ready
```

## Adding a New Service

When extending docker-compose.yml:

1. Basic Template
```
myservice:
  image: <image:tag>
  restart: unless-stopped
  ports:
    - "<host>:<container>"
  environment:
    - KEY=value
  volumes:
    - myservice_data:/path
  depends_on:
    <dependency>:
      condition: service_healthy
  healthcheck:
    test: ["CMD", "..."]
    interval: 10s
    timeout: 5s
    retries: 5
```

2. Rules
* Add to existing networks implicitly (default)
* Use service names as hostnames
* Add volume if stateful
* Add depends_on + healthcheck if required
* Avoid port conflicts

## Common Patterns

* Internal connection: http://service-name:port
* Wait for dependencies via depends_on + healthcheck
* Use .env for secrets:
```bash
docker compose --env-file .env up -d
```

## Validation Checklist

Before committing:

- [ ] Service starts cleanly
- [ ] Healthcheck passes
- [ ] No port conflicts
- [ ] Data persists after restart
- [ ] Logs show no critical errors

---