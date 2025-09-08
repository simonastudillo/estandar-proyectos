# Setup de Ambientes (Desarrollo, Testing, Staging, Producción)

## ¿Qué es?

El setup de ambientes múltiples es la configuración y gestión de diferentes
entornos de trabajo que permiten el desarrollo, testing, integración y
despliegue controlado de aplicaciones. Cada ambiente tiene configuraciones
específicas, variables de entorno y niveles de acceso diferenciados.

## ¿Por qué es importante?

- **Separación de responsabilidades**: Cada ambiente tiene un propósito
  específico en el ciclo de desarrollo
- **Prevención de errores**: Evita que cambios experimentales afecten producción
- **Testing controlado**: Permite probar funcionalidades en entornos similares a
  producción
- **Despliegue gradual**: Facilita la implementación progresiva de nuevas
  funcionalidades
- **Debugging eficiente**: Aísla problemas en entornos específicos
- **Seguridad**: Protege datos sensibles y configuraciones críticas

## ¿Qué debe incluir?

### Ambientes estándar

- **Development**: Entorno local de desarrollo
- **Testing**: Ambiente para pruebas automatizadas
- **Staging**: Réplica de producción para testing final
- **Production**: Ambiente de producción real

### Configuraciones por ambiente

- Variables de entorno específicas
- Configuraciones de base de datos
- URLs y endpoints de APIs
- Configuraciones de logs
- Configuraciones de seguridad
- Configuraciones de cache

## ¿Qué debo hacer?

### 1. Definir estructura de archivos de configuración

```
config/
├── environments/
│   ├── .env.development
│   ├── .env.testing
│   ├── .env.staging
│   └── .env.production
├── docker/
│   ├── docker-compose.dev.yml
│   ├── docker-compose.test.yml
│   ├── docker-compose.staging.yml
│   └── docker-compose.prod.yml
└── deploy/
    ├── development.yml
    ├── staging.yml
    └── production.yml
```

### 2. Variables de entorno por ambiente

#### Development (.env.development)

```bash
# App
APP_NAME="Mi App - Development"
APP_ENV=development
APP_DEBUG=true
APP_URL=http://localhost:3000
API_URL=http://localhost:8000/api

# Database
DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=miapp_dev
DB_USERNAME=dev_user
DB_PASSWORD=dev_password

# Cache & Sessions
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_CONNECTION=sync

# Mail
MAIL_MAILER=log
MAIL_FROM_ADDRESS=dev@miapp.local

# Logging
LOG_LEVEL=debug
LOG_CHANNEL=stack
```

#### Testing (.env.testing)

```bash
# App
APP_NAME="Mi App - Testing"
APP_ENV=testing
APP_DEBUG=true
APP_URL=http://localhost:3000
API_URL=http://localhost:8000/api

# Database (Usar SQLite en memoria para tests rápidos)
DB_CONNECTION=sqlite
DB_DATABASE=:memory:

# Cache & Sessions
CACHE_DRIVER=array
SESSION_DRIVER=array
QUEUE_CONNECTION=sync

# Mail
MAIL_MAILER=array

# Logging
LOG_LEVEL=debug
LOG_CHANNEL=stderr
```

#### Staging (.env.staging)

```bash
# App
APP_NAME="Mi App - Staging"
APP_ENV=staging
APP_DEBUG=false
APP_URL=https://staging.miapp.com
API_URL=https://api-staging.miapp.com

# Database
DB_CONNECTION=mysql
DB_HOST=staging-db.miapp.com
DB_PORT=3306
DB_DATABASE=miapp_staging
DB_USERNAME=${STAGING_DB_USER}
DB_PASSWORD=${STAGING_DB_PASSWORD}

# Cache & Sessions
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis

# Redis
REDIS_HOST=staging-redis.miapp.com
REDIS_PASSWORD=${STAGING_REDIS_PASSWORD}

# Mail
MAIL_MAILER=smtp
MAIL_HOST=smtp.staging.miapp.com
MAIL_FROM_ADDRESS=noreply@staging.miapp.com

# Logging
LOG_LEVEL=info
LOG_CHANNEL=daily
```

#### Production (.env.production)

```bash
# App
APP_NAME="Mi App"
APP_ENV=production
APP_DEBUG=false
APP_URL=https://miapp.com
API_URL=https://api.miapp.com

# Database
DB_CONNECTION=mysql
DB_HOST=${PROD_DB_HOST}
DB_PORT=3306
DB_DATABASE=${PROD_DB_NAME}
DB_USERNAME=${PROD_DB_USER}
DB_PASSWORD=${PROD_DB_PASSWORD}

# Cache & Sessions
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis

# Redis
REDIS_HOST=${PROD_REDIS_HOST}
REDIS_PASSWORD=${PROD_REDIS_PASSWORD}

# Mail
MAIL_MAILER=smtp
MAIL_HOST=${PROD_MAIL_HOST}
MAIL_FROM_ADDRESS=noreply@miapp.com

# Logging
LOG_LEVEL=error
LOG_CHANNEL=daily

# Security
FORCE_HTTPS=true
SESSION_SECURE_COOKIE=true
```

### 3. Docker Compose por ambiente

#### Development (docker-compose.dev.yml)

```yaml
version: "3.8"

services:
   frontend:
      build:
         context: ./frontend
         dockerfile: Dockerfile.dev
      ports:
         - "3000:3000"
      volumes:
         - ./frontend:/app
         - /app/node_modules
      environment:
         - NODE_ENV=development
         - CHOKIDAR_USEPOLLING=true

   backend:
      build:
         context: ./backend
         dockerfile: Dockerfile.dev
      ports:
         - "8000:8000"
      volumes:
         - ./backend:/var/www/html
      environment:
         - APP_ENV=development
      depends_on:
         - database
         - redis

   database:
      image: mysql:8.0
      ports:
         - "3306:3306"
      environment:
         MYSQL_ROOT_PASSWORD: root
         MYSQL_DATABASE: miapp_dev
         MYSQL_USER: dev_user
         MYSQL_PASSWORD: dev_password
      volumes:
         - mysql_dev_data:/var/lib/mysql

   redis:
      image: redis:7-alpine
      ports:
         - "6379:6379"

volumes:
   mysql_dev_data:
```

#### Staging (docker-compose.staging.yml)

```yaml
version: "3.8"

services:
   frontend:
      build:
         context: ./frontend
         dockerfile: Dockerfile.staging
      ports:
         - "80:80"
      environment:
         - NODE_ENV=staging

   backend:
      build:
         context: ./backend
         dockerfile: Dockerfile.staging
      ports:
         - "8000:8000"
      environment:
         - APP_ENV=staging
      depends_on:
         - database
         - redis

   database:
      image: mysql:8.0
      environment:
         MYSQL_ROOT_PASSWORD: ${STAGING_DB_ROOT_PASSWORD}
         MYSQL_DATABASE: ${STAGING_DB_NAME}
         MYSQL_USER: ${STAGING_DB_USER}
         MYSQL_PASSWORD: ${STAGING_DB_PASSWORD}
      volumes:
         - mysql_staging_data:/var/lib/mysql

   redis:
      image: redis:7-alpine
      command: redis-server --requirepass ${STAGING_REDIS_PASSWORD}

volumes:
   mysql_staging_data:
```

### 4. Scripts de gestión de ambientes

#### package.json (Frontend)

```json
{
   "scripts": {
      "dev": "vite --mode development",
      "build:dev": "vite build --mode development",
      "build:staging": "vite build --mode staging",
      "build:prod": "vite build --mode production",
      "test": "vitest --mode testing",
      "test:staging": "vitest run --mode staging",
      "preview:staging": "vite preview --port 4173",
      "deploy:staging": "npm run build:staging && ./scripts/deploy-staging.sh",
      "deploy:prod": "npm run build:prod && ./scripts/deploy-prod.sh"
   }
}
```

#### Scripts de deploy

**scripts/deploy-staging.sh**

```bash
#!/bin/bash

echo "🚀 Deploying to STAGING..."

# Build del proyecto
npm run build:staging

# Subir archivos al servidor staging
rsync -avz --delete dist/ user@staging-server:/var/www/staging/

# Ejecutar comandos en el servidor staging
ssh user@staging-server << 'EOF'
  cd /var/www/staging
  php artisan migrate --force
  php artisan config:cache
  php artisan route:cache
  php artisan view:cache
  supervisorctl restart laravel-worker:*
EOF

echo "✅ Staging deployment completed!"
```

**scripts/deploy-prod.sh**

```bash
#!/bin/bash

echo "🚀 Deploying to PRODUCTION..."

# Verificar que estamos en la rama main
BRANCH=$(git rev-parse --abbrev-ref HEAD)
if [ "$BRANCH" != "main" ]; then
  echo "❌ Error: Must be on main branch to deploy to production"
  exit 1
fi

# Verificar que no hay cambios sin commit
if [ -n "$(git status --porcelain)" ]; then
  echo "❌ Error: There are uncommitted changes"
  exit 1
fi

# Build del proyecto
npm run build:prod

# Backup del release anterior
ssh user@prod-server 'cp -r /var/www/production /var/www/backup-$(date +%Y%m%d-%H%M%S)'

# Subir archivos al servidor producción
rsync -avz --delete dist/ user@prod-server:/var/www/production/

# Ejecutar comandos en el servidor producción
ssh user@prod-server << 'EOF'
  cd /var/www/production
  php artisan down --retry=60
  php artisan migrate --force
  php artisan config:cache
  php artisan route:cache
  php artisan view:cache
  supervisorctl restart laravel-worker:*
  php artisan up
EOF

echo "✅ Production deployment completed!"
```

### 5. Configuración de Vite por ambiente

#### vite.config.ts

```typescript
import { defineConfig, loadEnv } from "vite";
import react from "@vitejs/plugin-react";
import { resolve } from "path";

export default defineConfig(({ command, mode }) => {
   // Cargar variables de entorno
   const env = loadEnv(mode, process.cwd(), "");

   return {
      plugins: [react()],

      // Configuración base
      base: mode === "production" ? "/app/" : "/",

      // Alias para imports
      resolve: {
         alias: {
            "@": resolve(__dirname, "src"),
            "@components": resolve(__dirname, "src/components"),
            "@pages": resolve(__dirname, "src/pages"),
            "@services": resolve(__dirname, "src/services"),
            "@store": resolve(__dirname, "src/store"),
            "@types": resolve(__dirname, "src/types"),
            "@utils": resolve(__dirname, "src/utils"),
         },
      },

      // Variables de entorno
      define: {
         __APP_ENV__: JSON.stringify(mode),
         __API_URL__: JSON.stringify(env.VITE_API_URL),
      },

      // Configuración del servidor de desarrollo
      server: {
         port: 3000,
         host: true,
         cors: true,
      },

      // Configuración de build
      build: {
         outDir: "dist",
         sourcemap: mode !== "production",
         minify: mode === "production" ? "esbuild" : false,

         rollupOptions: {
            output: {
               manualChunks: {
                  vendor: ["react", "react-dom"],
                  redux: ["@reduxjs/toolkit", "react-redux"],
                  router: ["react-router-dom"],
               },
            },
         },
      },

      // Configuración específica por ambiente
      ...(mode === "development" && {
         server: {
            open: true,
            hmr: true,
         },
      }),

      ...(mode === "testing" && {
         test: {
            globals: true,
            environment: "jsdom",
            setupFiles: ["./src/setupTests.ts"],
         },
      }),
   };
});
```

### 6. Configuración de Laravel por ambiente

#### config/app.php (Laravel)

```php
<?php

return [
    'name' => env('APP_NAME', 'Laravel'),
    'env' => env('APP_ENV', 'production'),
    'debug' => env('APP_DEBUG', false),
    'url' => env('APP_URL', 'http://localhost'),
    
    // Configuraciones específicas por ambiente
    'environments' => [
        'development' => [
            'log_level' => 'debug',
            'cache_ttl' => 60, // 1 minuto
            'enable_query_log' => true,
        ],
        'testing' => [
            'log_level' => 'debug',
            'cache_ttl' => 0, // Sin cache
            'enable_query_log' => false,
        ],
        'staging' => [
            'log_level' => 'info',
            'cache_ttl' => 3600, // 1 hora
            'enable_query_log' => false,
        ],
        'production' => [
            'log_level' => 'error',
            'cache_ttl' => 86400, // 24 horas
            'enable_query_log' => false,
        ],
    ],
];
```

### 7. Gestión de secretos

#### GitHub Secrets (para CI/CD)

```yaml
# Staging
STAGING_DB_HOST
STAGING_DB_USER
STAGING_DB_PASSWORD
STAGING_REDIS_PASSWORD
STAGING_SSH_PRIVATE_KEY

# Production
PROD_DB_HOST
PROD_DB_USER
PROD_DB_PASSWORD
PROD_REDIS_PASSWORD
PROD_SSH_PRIVATE_KEY
```

#### Docker Secrets (para producción)

```yaml
# docker-compose.prod.yml
services:
   backend:
      secrets:
         - db_password
         - redis_password

secrets:
   db_password:
      file: ./secrets/db_password.txt
   redis_password:
      file: ./secrets/redis_password.txt
```

## Tips

### Seguridad

- **Nunca commitear** archivos `.env` reales en el repositorio
- Usar **GitHub Secrets** o **Docker Secrets** para datos sensibles
- Rotar credenciales regularmente
- Usar conexiones SSL/TLS en staging y producción

### Gestión de configuraciones

- Mantener archivos `.env.example` actualizados
- Documentar todas las variables de entorno requeridas
- Usar validación de configuración al inicio de la aplicación
- Implementar health checks para cada ambiente

### Monitoreo

- Configurar logs diferenciados por ambiente
- Implementar métricas de rendimiento
- Configurar alertas para errores en producción
- Usar herramientas de APM (Application Performance Monitoring)

### Automatización

- Automatizar deployments con GitHub Actions
- Implementar rollback automático en caso de errores
- Configurar testing automático antes de deploy
- Usar blue-green deployment para producción

## Ejemplos

### Ejemplo de configuración de ambiente en React

```typescript
// src/config/environment.ts
interface EnvironmentConfig {
   apiUrl: string;
   appName: string;
   environment: "development" | "testing" | "staging" | "production";
   debug: boolean;
   enableAnalytics: boolean;
}

const getEnvironmentConfig = (): EnvironmentConfig => {
   const env = import.meta.env.MODE;

   const configs: Record<string, EnvironmentConfig> = {
      development: {
         apiUrl: "http://localhost:8000/api",
         appName: "Mi App - Dev",
         environment: "development",
         debug: true,
         enableAnalytics: false,
      },
      testing: {
         apiUrl: "http://localhost:8000/api",
         appName: "Mi App - Test",
         environment: "testing",
         debug: true,
         enableAnalytics: false,
      },
      staging: {
         apiUrl: "https://api-staging.miapp.com",
         appName: "Mi App - Staging",
         environment: "staging",
         debug: false,
         enableAnalytics: true,
      },
      production: {
         apiUrl: "https://api.miapp.com",
         appName: "Mi App",
         environment: "production",
         debug: false,
         enableAnalytics: true,
      },
   };

   return configs[env] || configs.production;
};

export const config = getEnvironmentConfig();
```

### Ejemplo de configuración condicional

```typescript
// src/services/api.ts
import { config } from "@/config/environment";

// Configuración de Axios por ambiente
const apiClient = axios.create({
   baseURL: config.apiUrl,
   timeout: config.environment === "production" ? 10000 : 30000,
   headers: {
      "Content-Type": "application/json",
      ...(config.environment !== "production" && {
         "X-Debug-Mode": "true",
      }),
   },
});

// Interceptor para logging en desarrollo
if (config.debug) {
   apiClient.interceptors.request.use((request) => {
      console.log("API Request:", request);
      return request;
   });

   apiClient.interceptors.response.use(
      (response) => {
         console.log("API Response:", response);
         return response;
      },
      (error) => {
         console.error("API Error:", error);
         return Promise.reject(error);
      },
   );
}
```

---

## Navegación

**Progreso en Configuración del Entorno de Desarrollo:**

- ✅
  [Configuración del Entorno - Introducción](./configuracion-entorno-desarrollo.md)
- ✅
  [Configuración de repositorios Git y GitHub](./configuracion-repositorios-git.md)
- ✅
  [Setup de entornos de desarrollo local con Docker](./setup-entorno-local-docker.md)
- ✅
  [Configuración de herramientas de CI/CD con GitHub Actions](./configuracion-cicd-github-actions.md)
- ⏭️ **Setup de ambientes (desarrollo, testing, staging, producción)** ← Estás
  aquí
- ⏭️
  [Configuración de herramientas de calidad de código](./configuracion-herramientas-calidad.md)
- ⏭️
  [Setup de Laravel con Clean Architecture](./setup-laravel-clean-architecture.md)
- ⏭️
  [Configuración de React + TypeScript + Vite](./configuracion-react-typescript-vite.md)

---

### Siguiente Paso

Continúa con
[**Configuración de herramientas de calidad de código**](./configuracion-herramientas-calidad.md)

[⬅️ Configuración de herramientas de CI/CD con GitHub Actions](./configuracion-cicd-github-actions.md)
| [🏠 README Principal](../../README.md) |
[➡️ Configuración de herramientas de calidad de código](./configuracion-herramientas-calidad.md)
