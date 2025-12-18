# ===========================================
# BUILD STAGE
# ===========================================
FROM node:20-alpine AS builder

WORKDIR /app

# Dependencias
COPY package*.json ./
RUN npm ci

# Codigo
COPY . .

# Build argument para URL da API
ARG VITE_API_URL
ENV VITE_API_URL=${VITE_API_URL}

# Build de producao
RUN npm run build

# ===========================================
# PRODUCTION STAGE
# ===========================================
FROM nginx:alpine

# Copiar build
COPY --from=builder /app/dist /usr/share/nginx/html

# Copiar configuracao do nginx
COPY nginx.conf /etc/nginx/nginx.conf

# Porta
EXPOSE 80

# Health check
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD curl -f http://localhost/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
