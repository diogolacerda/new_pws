# ProtoWorks System (PWS) - Infraestrutura

**Versao:** 1.0
**Data:** 17 de Dezembro de 2025

---

## 1. Visao Geral

### Ambiente
- **Homeserver:** Ubuntu com Docker, Portainer e Watchtower
- **CI/CD:** GitHub Actions + Docker Hub
- **Proposito:** Desenvolvimento e testes locais
- **Futuro:** Cloud quando precisar compartilhar com usuarios externos

### Fluxo de Deploy

```
+-------------+       +--------------+       +-------------+
|   GitHub    |       | Docker Hub   |       | Homeserver  |
|   (codigo)  |       | (imagens)    |       | (Watchtower)|
+------+------+       +------+-------+       +------+------+
       |                     |                      |
       | push                |                      |
       v                     |                      |
+------+------+              |                      |
| GitHub      | build & push |                      |
| Actions     +------------->|                      |
+-------------+              |  poll new images     |
                             |<---------------------+
                             |                      |
                             |  pull & restart      |
                             +--------------------->|
                                                    v
                                           +--------+--------+
                                           | Containers      |
                                           | atualizados!    |
                                           +-----------------+
```

---

## 2. Docker Hub

### Imagens

```
docker.io/SEU_USUARIO/pws-backend:latest
docker.io/SEU_USUARIO/pws-frontend:latest
```

### Tags

| Tag | Quando | Branch |
|-----|--------|--------|
| `latest` | Push em master | master |
| `stage` | Push em stage | stage |
| `sha-abc1234` | Todo push | qualquer |

---

## 3. Dockerfiles

### backend/Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential libpq-dev curl && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### frontend/Dockerfile

```dockerfile
FROM node:20-alpine as builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

ARG VITE_API_URL
ENV VITE_API_URL=${VITE_API_URL}

RUN npm run build

FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### frontend/nginx.conf

```nginx
events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    server {
        listen 80;
        root /usr/share/nginx/html;
        index index.html;

        gzip on;
        gzip_types text/plain text/css application/json application/javascript;

        location / {
            try_files $uri $uri/ /index.html;
        }
    }
}
```

---

## 4. GitHub Actions

### .github/workflows/ci-cd.yml

```yaml
name: CI/CD

on:
  push:
    branches: [stage, master]
  pull_request:
    branches: [stage]

env:
  DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
  BACKEND_IMAGE: ${{ secrets.DOCKERHUB_USERNAME }}/pws-backend
  FRONTEND_IMAGE: ${{ secrets.DOCKERHUB_USERNAME }}/pws-frontend

jobs:
  # =============================================
  # TESTES
  # =============================================
  test-backend:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16-alpine
        env:
          POSTGRES_DB: pws_test
          POSTGRES_USER: pws_test
          POSTGRES_PASSWORD: test123
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
          cache: 'pip'
          cache-dependency-path: backend/requirements.txt

      - name: Install and Test
        working-directory: ./backend
        env:
          DATABASE_URL: postgresql+asyncpg://pws_test:test123@localhost:5432/pws_test
          SECRET_KEY: test-secret
        run: |
          pip install -r requirements.txt
          pip install pytest pytest-cov pytest-asyncio ruff
          ruff check .
          pytest --cov=app

  test-frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: frontend/package-lock.json

      - name: Install and Test
        working-directory: ./frontend
        run: |
          npm ci
          npm run lint
          npm run test

  # =============================================
  # BUILD E PUSH
  # =============================================
  build-and-push:
    needs: [test-backend, test-frontend]
    runs-on: ubuntu-latest
    if: github.event_name == 'push'

    steps:
      - uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Set tags
        id: tags
        run: |
          BRANCH=${GITHUB_REF#refs/heads/}
          SHA=${GITHUB_SHA::7}

          if [ "$BRANCH" = "master" ]; then
            TAG="latest"
          else
            TAG="$BRANCH"
          fi

          echo "TAG=$TAG" >> $GITHUB_OUTPUT
          echo "SHA=$SHA" >> $GITHUB_OUTPUT

      - name: Build and push Backend
        uses: docker/build-push-action@v5
        with:
          context: ./backend
          push: true
          tags: |
            ${{ env.BACKEND_IMAGE }}:${{ steps.tags.outputs.TAG }}
            ${{ env.BACKEND_IMAGE }}:${{ steps.tags.outputs.SHA }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

      - name: Build and push Frontend
        uses: docker/build-push-action@v5
        with:
          context: ./frontend
          push: true
          tags: |
            ${{ env.FRONTEND_IMAGE }}:${{ steps.tags.outputs.TAG }}
            ${{ env.FRONTEND_IMAGE }}:${{ steps.tags.outputs.SHA }}
          build-args: |
            VITE_API_URL=${{ vars.VITE_API_URL }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

### Configurar Secrets no GitHub

**Settings > Secrets and variables > Actions > Secrets:**

| Secret | Valor |
|--------|-------|
| `DOCKERHUB_USERNAME` | Seu usuario do Docker Hub |
| `DOCKERHUB_TOKEN` | Token do Docker Hub (criar em hub.docker.com > Account Settings > Security) |

**Settings > Secrets and variables > Actions > Variables:**

| Variable | Valor |
|----------|-------|
| `VITE_API_URL` | `http://IP_DO_HOMESERVER:8000` |

---

## 5. Docker Compose no Homeserver

### docker-compose.yml

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:16-alpine
    container_name: pws-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: pws
      POSTGRES_USER: pws
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-pws_dev_123}
    volumes:
      - pws_postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  minio:
    image: minio/minio:latest
    container_name: pws-minio
    restart: unless-stopped
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: ${MINIO_ROOT_USER:-minioadmin}
      MINIO_ROOT_PASSWORD: ${MINIO_ROOT_PASSWORD:-minioadmin123}
    volumes:
      - pws_minio_data:/data
    ports:
      - "9000:9000"
      - "9001:9001"

  backend:
    image: SEU_USUARIO/pws-backend:stage
    container_name: pws-backend
    restart: unless-stopped
    environment:
      DATABASE_URL: postgresql+asyncpg://pws:${POSTGRES_PASSWORD:-pws_dev_123}@postgres:5432/pws
      MINIO_ENDPOINT: minio:9000
      MINIO_ACCESS_KEY: ${MINIO_ROOT_USER:-minioadmin}
      MINIO_SECRET_KEY: ${MINIO_ROOT_PASSWORD:-minioadmin123}
      SECRET_KEY: ${SECRET_KEY:-dev-secret-key}
      ENVIRONMENT: development
    ports:
      - "8000:8000"
    depends_on:
      - postgres
      - minio
    labels:
      - "com.centurylinklabs.watchtower.enable=true"

  frontend:
    image: SEU_USUARIO/pws-frontend:stage
    container_name: pws-frontend
    restart: unless-stopped
    ports:
      - "5173:80"
    depends_on:
      - backend
    labels:
      - "com.centurylinklabs.watchtower.enable=true"

volumes:
  pws_postgres_data:
  pws_minio_data:
```

**Substituir `SEU_USUARIO` pelo seu usuario do Docker Hub.**

### Labels do Watchtower

Os containers `backend` e `frontend` tem a label:
```yaml
labels:
  - "com.centurylinklabs.watchtower.enable=true"
```

Isso faz o Watchtower monitorar essas imagens e atualizar automaticamente.

---

## 6. Resumo do Fluxo

1. Push no GitHub (branch `stage` ou `master`)
2. GitHub Actions roda testes
3. GitHub Actions faz build das imagens
4. GitHub Actions publica no Docker Hub
5. Watchtower detecta nova imagem
6. Watchtower faz pull e reinicia containers

**Tempo:** ~5-10 min do push ao deploy

---

## 7. Comandos Uteis

```bash
# Forcar Watchtower a verificar agora
docker exec watchtower /watchtower --run-once

# Ver logs
docker logs pws-backend -f

# Rodar migrations
docker exec pws-backend alembic upgrade head

# Pull manual de imagem
docker pull SEU_USUARIO/pws-backend:stage
```

---

## 8. Migracao para Cloud (Futuro)

Quando precisar:

| Homeserver | Cloud |
|------------|-------|
| PostgreSQL container | RDS / Cloud SQL |
| MinIO container | S3 / Cloud Storage |
| Docker + Watchtower | ECS / Cloud Run |

A aplicacao ja esta containerizada - migracao sera simples.

---

**Fim do Documento**
