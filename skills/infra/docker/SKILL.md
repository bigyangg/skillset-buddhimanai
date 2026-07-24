---
name: docker
description: Containerize any application with Docker. Use when a user says "dockerize this", "write a Dockerfile", "docker-compose", "containerize", "build a Docker image", "run in Docker", "multi-stage build", "Docker networking", "container orchestration", "shrink image size", or "production Docker setup".
---

# Docker

## What This Does

Author production-grade Dockerfiles, multi-stage builds, and Docker Compose configurations for any language or framework. Covers image optimization, layer caching, security hardening, and local-to-production parity.

## Non-Negotiables

- **Never run as root.** Always create and switch to a non-root user.
- **Pin base image tags.** Never use `latest`; always pin to a specific digest or semver tag.
- **Multi-stage for production.** Builder → runtime split keeps final images lean.
- **`.dockerignore` is mandatory.** Always generate one alongside a Dockerfile.
- **Secrets never in layers.** Use `--mount=type=secret` or runtime env vars for credentials.
- **`COPY` before `RUN`.** Only copy files needed for the next step to maximize cache hits.
- **`HEALTHCHECK` required** for any long-running service.

## Workflow

### Step 1 — Identify Runtime Requirements

Before writing any Dockerfile, determine:
- Language and version (e.g., Node 22, Python 3.12, Go 1.23)
- Build tool (npm, pnpm, yarn, pip, cargo, go build)
- Static or dynamic binary output?
- External service dependencies (database, cache, queue)
- Target environment: local dev / staging / production

### Step 2 — Choose a Base Image Strategy

| Use Case | Base Image |
|----------|-----------|
| Node.js app | `node:22-alpine` (runtime), `node:22-bookworm` (build) |
| Python service | `python:3.12-slim` |
| Go binary | `golang:1.23-bookworm` (build) → `gcr.io/distroless/static` (runtime) |
| Static site | `nginx:1.27-alpine` |
| Java service | `eclipse-temurin:21-jre-alpine` |

### Step 3 — Write the Dockerfile (Multi-Stage Pattern)

```dockerfile
# syntax=docker/dockerfile:1
# ─── Stage 1: Dependencies ───────────────────────────────────────────────────
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN corepack enable && pnpm install --frozen-lockfile --prod=false

# ─── Stage 2: Builder ────────────────────────────────────────────────────────
FROM deps AS builder
COPY . .
RUN pnpm build

# ─── Stage 3: Production Runtime ─────────────────────────────────────────────
FROM node:22-alpine AS runner
WORKDIR /app

# Security: non-root user
RUN addgroup --system --gid 1001 appgroup && \
    adduser  --system --uid  1001 appuser
USER appuser

# Only copy what's needed at runtime
COPY --from=builder --chown=appuser:appgroup /app/.next/standalone ./
COPY --from=builder --chown=appuser:appgroup /app/.next/static ./.next/static
COPY --from=builder --chown=appuser:appgroup /app/public ./public

ENV NODE_ENV=production \
    PORT=3000

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:3000/api/health || exit 1

CMD ["node", "server.js"]
```

### Step 4 — Generate `.dockerignore`

```dockerignore
# Dependencies
node_modules
.pnpm-store

# Build outputs (will be rebuilt inside Docker)
.next
dist
build
out

# Version control & Editor
.git
.gitignore
.vscode
.idea

# Environment & Secrets (NEVER include)
.env
.env.*
*.pem
*.key

# Dev & Test files
__tests__
*.test.ts
*.spec.ts
coverage
.jest.config.*

# OS artifacts
.DS_Store
Thumbs.db

# Docs
README.md
*.md
```

### Step 5 — Docker Compose for Local Dev

```yaml
# docker-compose.yml
services:
  app:
    build:
      context: .
      target: builder          # Use builder stage in dev for hot reload
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules      # Anonymous volume prevents host override
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://user:password@db:5432/appdb
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: appdb
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d appdb"]
      interval: 10s
      timeout: 5s
      retries: 5

  cache:
    image: redis:7-alpine
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes

volumes:
  postgres_data:
  redis_data:
```

## Image Optimization Checklist

- [ ] Multi-stage build separates build deps from runtime
- [ ] Base image pinned to specific tag (not `latest`)
- [ ] `.dockerignore` excludes `node_modules`, `.git`, `.env`
- [ ] Non-root user created and switched to before `CMD`
- [ ] `HEALTHCHECK` defined for all services
- [ ] No secrets in `ENV` instructions or `RUN` commands
- [ ] Layer order: system deps → app deps → source code → build
- [ ] `--frozen-lockfile` or `--ci` flag used for reproducible installs
- [ ] Final image size verified: `docker images | grep <name>`

## Common Commands

```bash
# Build
docker build -t myapp:latest .
docker build --target runner -t myapp:prod .

# Run
docker run -p 3000:3000 --env-file .env myapp:latest

# Compose
docker compose up -d --build
docker compose logs -f app
docker compose down -v       # Remove volumes too

# Debug
docker exec -it <container_id> sh
docker inspect <container_id>
docker stats

# Image analysis
docker image history myapp:latest
dive myapp:latest             # Install: https://github.com/wagoodman/dive
```

## Security Hardening

```dockerfile
# Drop all Linux capabilities, add only what's needed
# (in docker run or compose)
security_opt:
  - no-new-privileges:true
cap_drop:
  - ALL
cap_add:
  - NET_BIND_SERVICE   # only if binding to port < 1024

# Read-only filesystem where possible
read_only: true
tmpfs:
  - /tmp
  - /var/run
```

## Trigger Phrases

"dockerize", "write a Dockerfile", "docker-compose", "containerize my app", "build Docker image", "multi-stage build", "shrink image size", "Docker healthcheck", "non-root Docker user", "production Docker"
