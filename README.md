# DevOps Scaffolding

A minimal local development stack using Docker Compose with:

- MongoDB
- Redis
- PostgreSQL
- Keycloak (Auth)

Designed for quick bootstrapping and easy extension.

---

## Getting Started

### Prerequisites
- Docker
- Docker Compose (v2+)

### Start the stack
```bash
docker compose up -d
```

## Check status
```bash
docker compose ps
```

## View logs
```bash 
docker compose logs -f
```

## Stop the stack
```bash
docker compose down
```

## Reset (deletes all data)
```bash
docker compose down -v
```

## Services

Service	URL / Connection
MongoDB	`mongodb://admin:admin_secret@localhost:27017`
Redis	`redis://:admin_secret@localhost:6379`
Postgres	`postgres://admin:admin_secret@localhost:5432/db-main`
Keycloak	`http://localhost:8080` (admin / admin_secret)

## Health Checks

All services include healthchecks. Example:
```bash
docker compose ps
```
Or manually:

MongoDB: 
```bash
mongosh --eval "db.adminCommand('ping')"
```
Redis: 
```bash
redis-cli -a admin_secret ping
```
Postgres: 
```bash
pg_isready -U admin
```
Keycloak:
```bash
curl http://localhost:8080/health/ready
```

## Extending the Stack

To add a new service:

* Define it in docker-compose.yml
* Follow existing conventions:
* Use official images
* Add restart: unless-stopped
* Include a healthcheck
* Add volumes if stateful
* Use service names for internal networking

Example:
```
myservice:
  image: my-image:latest
  restart: unless-stopped
  ports:
    - "3000:3000"
  environment:
    - KEY=value
  depends_on:
    postgres:
      condition: service_healthy
```

## Environment Variables

* For better security, move secrets to a .env file:
```bash
docker compose --env-file .env up -d
```

## Volumes

Persistent data is stored in:

* mongodb_data
* redis_data
* postgres_data

## Notes
* This setup is intended for local development only
* Credentials are insecure by default
* Not production-ready (no TLS, no scaling, minimal hardening)

---