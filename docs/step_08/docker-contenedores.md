# Docker y Contenedores

## ¿Qué es?

Docker y la contenedorización es una tecnología que permite empaquetar
aplicaciones con todas sus dependencias en contenedores ligeros y portables.
Para nuestro stack (Laravel + React + MySQL), Docker facilita el desarrollo
local, testing, y deployment al garantizar que la aplicación funcione igual en
todos los entornos.

## ¿Por qué es importante?

- **Consistencia**: Mismo comportamiento en desarrollo, testing y producción
- **Portabilidad**: La aplicación funciona en cualquier sistema que soporte
  Docker
- **Aislamiento**: Cada servicio corre en su propio contenedor independiente
- **Escalabilidad**: Fácil escalado horizontal de servicios específicos
- **Eficiencia**: Menor overhead que máquinas virtuales completas
- **DevOps**: Simplifica pipelines de CI/CD y deployment

## ¿Qué debe incluir?

### Contenedores de Aplicación

- **Backend Container**: Laravel con PHP-FPM y dependencias
- **Frontend Container**: React build servido por Nginx
- **Database Container**: MySQL optimizado para producción
- **Cache Container**: Redis para sesiones y caché

### Orquestación

- **Docker Compose**: Para desarrollo local y staging
- **Environment Variables**: Configuración por ambiente
- **Volumes**: Persistencia de datos y logs
- **Networks**: Comunicación segura entre contenedores

### Optimización

- **Multi-stage builds**: Imágenes optimizadas para producción
- **Layer caching**: Builds más rápidos en CI/CD
- **Security scanning**: Análisis de vulnerabilidades
- **Health checks**: Verificación de estado de contenedores

### Deployment

- **Production ready**: Configuraciones optimizadas para producción
- **Load balancing**: Distribución de carga entre instancias
- **Auto-scaling**: Escalado automático basado en métricas
- **Monitoring**: Métricas de contenedores y aplicación

## ¿Qué debo hacer?

### 1. Crear Dockerfile para Backend (Laravel)

```dockerfile
# backend/Dockerfile
FROM php:8.2-fpm-alpine AS base

# Install system dependencies
RUN apk add --no-cache \
    bash \
    curl \
    freetype-dev \
    g++ \
    gcc \
    git \
    icu-dev \
    jpeg-dev \
    libpng-dev \
    libzip-dev \
    make \
    mysql-client \
    oniguruma-dev \
    zip

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) \
        bcmath \
        gd \
        intl \
        mbstring \
        mysqli \
        pdo_mysql \
        zip

# Install Redis extension
RUN pecl install redis && docker-php-ext-enable redis

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Development stage
FROM base AS development

# Install Xdebug for development
RUN pecl install xdebug && docker-php-ext-enable xdebug

WORKDIR /var/www

COPY composer.json composer.lock ./
RUN composer install --no-scripts --no-autoloader

COPY . .
RUN composer dump-autoload --optimize

# Set permissions
RUN chown -R www-data:www-data /var/www \
    && chmod -R 755 /var/www/storage

EXPOSE 9000

CMD ["php-fpm"]

# Production stage
FROM base AS production

WORKDIR /var/www

# Copy composer files
COPY composer.json composer.lock ./

# Install production dependencies
RUN composer install --no-dev --optimize-autoloader --no-scripts

# Copy application code
COPY . .

# Generate optimized autoloader
RUN composer dump-autoload --optimize

# Optimize for production
RUN php artisan config:cache \
    && php artisan route:cache \
    && php artisan view:cache

# Set permissions
RUN chown -R www-data:www-data /var/www \
    && chmod -R 755 /var/www/storage

EXPOSE 9000

CMD ["php-fpm"]
```

### 2. Crear Dockerfile para Frontend (React)

```dockerfile
# frontend/Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy source code
COPY . .

# Build for production
RUN npm run build

# Production stage with Nginx
FROM nginx:alpine AS production

# Install curl for health checks
RUN apk add --no-cache curl

# Copy custom Nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Copy built application
COPY --from=builder /app/dist /usr/share/nginx/html

# Add non-root user
RUN addgroup -g 1001 -S nodejs \
    && adduser -S nextjs -u 1001

# Set permissions
RUN chown -R nextjs:nodejs /usr/share/nginx/html \
    && chown -R nextjs:nodejs /var/cache/nginx

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost/ || exit 1

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### 3. Configurar Nginx para Frontend

```nginx
# frontend/nginx.conf
events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    # Logging
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;
    error_log /var/log/nginx/error.log warn;

    # Performance
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 10240;
    gzip_proxied expired no-cache no-store private must-revalidate no_last_modified no_etag auth;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/javascript
        application/json
        application/xml+rss;

    server {
        listen 80;
        server_name localhost;
        root /usr/share/nginx/html;
        index index.html;

        # Security headers
        add_header X-Frame-Options "SAMEORIGIN" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header Referrer-Policy "no-referrer-when-downgrade" always;

        # Static assets caching
        location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
            expires 1y;
            add_header Cache-Control "public, immutable";
        }

        # React router support
        location / {
            try_files $uri $uri/ /index.html;
        }

        # Health check endpoint
        location /health {
            access_log off;
            return 200 "healthy\n";
            add_header Content-Type text/plain;
        }
    }
}
```

### 4. Docker Compose para Desarrollo

```yaml
# docker-compose.yml
version: "3.8"

services:
   # Database
   database:
      image: mysql:8.0
      container_name: app_database
      restart: unless-stopped
      environment:
         MYSQL_ROOT_PASSWORD: rootpassword
         MYSQL_DATABASE: app_development
         MYSQL_USER: app_user
         MYSQL_PASSWORD: app_password
      volumes:
         - db_data:/var/lib/mysql
         - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql
      ports:
         - "3306:3306"
      networks:
         - app_network
      healthcheck:
         test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
         timeout: 20s
         retries: 10

   # Redis Cache
   redis:
      image: redis:7-alpine
      container_name: app_redis
      restart: unless-stopped
      ports:
         - "6379:6379"
      volumes:
         - redis_data:/data
      networks:
         - app_network
      healthcheck:
         test: ["CMD", "redis-cli", "ping"]
         timeout: 20s
         retries: 10

   # Backend (Laravel)
   backend:
      build:
         context: ./backend
         target: development
         dockerfile: Dockerfile
      container_name: app_backend
      restart: unless-stopped
      environment:
         - APP_ENV=local
         - APP_DEBUG=true
         - DB_CONNECTION=mysql
         - DB_HOST=database
         - DB_PORT=3306
         - DB_DATABASE=app_development
         - DB_USERNAME=app_user
         - DB_PASSWORD=app_password
         - REDIS_HOST=redis
         - REDIS_PORT=6379
      volumes:
         - ./backend:/var/www:cached
         - backend_vendor:/var/www/vendor
      ports:
         - "9000:9000"
      depends_on:
         database:
            condition: service_healthy
         redis:
            condition: service_healthy
      networks:
         - app_network

   # Web Server (Nginx)
   webserver:
      image: nginx:alpine
      container_name: app_webserver
      restart: unless-stopped
      ports:
         - "8080:80"
      volumes:
         - ./backend:/var/www:cached
         - ./docker/nginx/:/etc/nginx/conf.d/
      depends_on:
         - backend
      networks:
         - app_network

   # Frontend (React)
   frontend:
      build:
         context: ./frontend
         dockerfile: Dockerfile
         target: production
      container_name: app_frontend
      restart: unless-stopped
      ports:
         - "3000:80"
      depends_on:
         - backend
      networks:
         - app_network

# Volumes
volumes:
   db_data:
      driver: local
   redis_data:
      driver: local
   backend_vendor:
      driver: local

# Networks
networks:
   app_network:
      driver: bridge
```

### 5. Docker Compose para Producción

```yaml
# docker-compose.prod.yml
version: "3.8"

services:
   database:
      image: mysql:8.0
      restart: always
      environment:
         MYSQL_ROOT_PASSWORD_FILE: /run/secrets/mysql_root_password
         MYSQL_DATABASE: app_production
         MYSQL_USER: app_user
         MYSQL_PASSWORD_FILE: /run/secrets/mysql_password
      volumes:
         - db_data:/var/lib/mysql
      secrets:
         - mysql_root_password
         - mysql_password
      networks:
         - app_network
      deploy:
         resources:
            limits:
               memory: 2G
            reservations:
               memory: 1G

   redis:
      image: redis:7-alpine
      restart: always
      volumes:
         - redis_data:/data
      networks:
         - app_network
      deploy:
         resources:
            limits:
               memory: 512M

   backend:
      build:
         context: ./backend
         target: production
      restart: always
      environment:
         - APP_ENV=production
         - APP_DEBUG=false
         - DB_HOST=database
         - REDIS_HOST=redis
      secrets:
         - app_key
         - db_password
      depends_on:
         - database
         - redis
      networks:
         - app_network
      deploy:
         replicas: 2
         resources:
            limits:
               memory: 1G
            reservations:
               memory: 512M

   frontend:
      build:
         context: ./frontend
         target: production
      restart: always
      ports:
         - "80:80"
         - "443:443"
      volumes:
         - ./ssl:/etc/ssl/certs:ro
      depends_on:
         - backend
      networks:
         - app_network
      deploy:
         replicas: 2

secrets:
   mysql_root_password:
      file: ./secrets/mysql_root_password.txt
   mysql_password:
      file: ./secrets/mysql_password.txt
   app_key:
      file: ./secrets/app_key.txt
   db_password:
      file: ./secrets/db_password.txt

volumes:
   db_data:
   redis_data:

networks:
   app_network:
      driver: overlay
```

### 6. Scripts de Gestión

```bash
#!/bin/bash
# scripts/docker-dev.sh

set -e

case "$1" in
    start)
        echo "🚀 Starting development environment..."
        docker-compose up -d
        echo "✅ Environment ready!"
        echo "Frontend: http://localhost:3000"
        echo "Backend: http://localhost:8080"
        ;;
    stop)
        echo "🛑 Stopping development environment..."
        docker-compose down
        ;;
    rebuild)
        echo "🔄 Rebuilding containers..."
        docker-compose down
        docker-compose build --no-cache
        docker-compose up -d
        ;;
    logs)
        docker-compose logs -f "${2:-}"
        ;;
    shell)
        if [ -z "$2" ]; then
            echo "Usage: $0 shell [backend|frontend|database]"
            exit 1
        fi
        docker-compose exec "$2" sh
        ;;
    migrate)
        echo "🗄️ Running database migrations..."
        docker-compose exec backend php artisan migrate
        ;;
    seed)
        echo "🌱 Seeding database..."
        docker-compose exec backend php artisan db:seed
        ;;
    fresh)
        echo "🗄️ Fresh database with seeds..."
        docker-compose exec backend php artisan migrate:fresh --seed
        ;;
    test)
        echo "🧪 Running tests..."
        docker-compose exec backend php artisan test
        docker-compose exec frontend npm test
        ;;
    *)
        echo "Usage: $0 {start|stop|rebuild|logs|shell|migrate|seed|fresh|test}"
        exit 1
        ;;
esac
```

## Tips

### Optimización de Imágenes

- **Multi-stage builds**: Separa dependencias de desarrollo y producción
- **Alpine Linux**: Usa distribuciones ligeras cuando sea posible
- **Layer caching**: Ordena comandos para maximizar cache hits
- **Security scanning**: Usa `docker scan` para detectar vulnerabilidades

### Development Workflow

- **Volume mounting**: Para hot-reload durante desarrollo
- **Environment variables**: Diferentes configs por ambiente
- **Database seeding**: Datos de prueba consistentes
- **Service dependencies**: Usa `depends_on` y health checks

### Production Best Practices

- **Resource limits**: Define límites de CPU y memoria
- **Health checks**: Para auto-recovery de contenedores
- **Secrets management**: Nunca hardcodear credenciales
- **Logging**: Centraliza logs con drivers específicos

### Monitoreo y Debugging

- **Container stats**: `docker stats` para métricas en tiempo real
- **Logs centralizados**: Usa drivers de logging para enviar a sistemas externos
- **Health endpoints**: Implementa endpoints para verificar estado
- **Debugging**: Usa `docker exec` para acceder a contenedores running

## Ejemplos

### Health Check personalizado

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost/health || exit 1
```

### Build con argumentos

```dockerfile
ARG APP_ENV=production
ARG NODE_ENV=production

RUN if [ "$APP_ENV" = "development" ] ; then \
        npm install ; \
    else \
        npm ci --only=production ; \
    fi
```

### Docker Compose Override

```yaml
# docker-compose.override.yml (automáticamente incluido en desarrollo)
version: "3.8"

services:
   backend:
      volumes:
         - ./backend:/var/www:cached
      environment:
         - APP_DEBUG=true
         - APP_ENV=local
```

### Deployment Script

```bash
#!/bin/bash
# deploy.sh

# Build images
docker build -t myapp/backend:latest ./backend
docker build -t myapp/frontend:latest ./frontend

# Push to registry
docker push myapp/backend:latest
docker push myapp/frontend:latest

# Deploy to swarm
docker stack deploy -c docker-compose.prod.yml myapp
```

## Navegación

[⬅️ CI/CD Pipelines con GitHub Actions](./ci-cd-pipelines-github-actions.md) |
[🏠 README Principal](../../README.md) |
[Monitoreo y Logging ➡️](./monitoreo-logging.md)
