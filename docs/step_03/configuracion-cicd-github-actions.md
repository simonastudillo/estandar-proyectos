# Configuración de Herramientas de CI/CD con GitHub Actions

## Conocimientos involucrados
- Automatización de procesos
- Integración continua (CI)
- Despliegue continuo (CD)
- Uso de GitHub Actions
- Testing automatizado

## Responsable de la etapa
- Equipo de DevOps

## ¿Qué es?

CI/CD (Continuous Integration/Continuous Deployment) es una metodología de
desarrollo que automatiza la integración, testing y despliegue de código
mediante pipelines. GitHub Actions es la plataforma nativa de GitHub que permite
crear workflows automatizados para implementar CI/CD de manera eficiente y
escalable.

## ¿Por qué es importante?

- **Automatización completa**: Elimina procesos manuales propensos a errores
- **Calidad de código**: Ejecuta tests, linting y análisis automáticamente
- **Despliegues seguros**: Garantiza que solo código probado llegue a producción
- **Feedback rápido**: Detecta problemas inmediatamente después del commit
- **Rollbacks automáticos**: Facilita la reversión en caso de fallos
- **Escalabilidad**: Maneja múltiples ambientes y equipos de desarrollo
- **Trazabilidad**: Historial completo de todos los despliegues

## ¿Qué debe incluir?

### Pipeline Frontend (React + TypeScript + Vite)

- **Build y Testing**: Compilación, tests unitarios, cobertura
- **Calidad de código**: ESLint, Prettier, TypeScript check
- **Análisis de seguridad**: Dependencias vulnerables, SAST
- **Optimización**: Bundle analysis, performance metrics
- **Despliegue**: Build para múltiples ambientes

### Pipeline Backend (Laravel + Clean Architecture)

- **Testing completo**: Unitarios, integración, feature tests
- **Calidad de código**: PHPStan, PHP CS Fixer, arquitectura
- **Base de datos**: Migraciones, seeders, schema validation
- **Seguridad**: Dependencias, análisis estático, secrets
- **Documentación**: API docs, cobertura de tests

### Pipeline Mobile (React Native)

- **Testing multiplataforma**: iOS y Android
- **Build nativo**: Compilación para cada plataforma
- **Distribución**: TestFlight, Google Play Internal Testing
- **Performance**: Bundle size, memoria, CPU

## ¿Qué debo hacer?

### 1. Estructura de Workflows

```
.github/
├── workflows/
│   ├── frontend-ci.yml           # CI para React app
│   ├── backend-ci.yml            # CI para Laravel API
│   ├── mobile-ci.yml             # CI para React Native
│   ├── deploy-staging.yml        # Deploy a staging
│   ├── deploy-production.yml     # Deploy a producción
│   └── security-scan.yml         # Análisis de seguridad
├── actions/                      # Custom actions reutilizables
│   ├── setup-node/
│   ├── setup-php/
│   └── setup-database/
└── CODEOWNERS                    # Code review automático
```

### 2. Workflow Frontend (React + TypeScript + Vite)

```yaml
name: Frontend CI/CD

on:
   push:
      branches: [main, develop]
      paths: ["frontend/**"]
   pull_request:
      branches: [main, develop]
      paths: ["frontend/**"]

env:
   NODE_VERSION: "20"
   FRONTEND_DIR: "./frontend"

jobs:
   test:
      name: Test & Lint
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v4

         - name: Setup Node.js
           uses: actions/setup-node@v4
           with:
              node-version: ${{ env.NODE_VERSION }}
              cache: "npm"
              cache-dependency-path: ${{ env.FRONTEND_DIR }}/package-lock.json

         - name: Install dependencies
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm ci

         - name: Type checking
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm run type-check

         - name: Lint code
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm run lint

         - name: Format check
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm run format:check

         - name: Run unit tests
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm run test:coverage

         - name: Upload coverage to Codecov
           uses: codecov/codecov-action@v3
           with:
              file: ${{ env.FRONTEND_DIR }}/coverage/lcov.info
              flags: frontend

   build:
      name: Build Application
      runs-on: ubuntu-latest
      needs: test

      strategy:
         matrix:
            environment: [staging, production]

      steps:
         - uses: actions/checkout@v4

         - name: Setup Node.js
           uses: actions/setup-node@v4
           with:
              node-version: ${{ env.NODE_VERSION }}
              cache: "npm"
              cache-dependency-path: ${{ env.FRONTEND_DIR }}/package-lock.json

         - name: Install dependencies
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm ci

         - name: Build for ${{ matrix.environment }}
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm run build:${{ matrix.environment }}
           env:
              VITE_API_URL: ${{ secrets[format('VITE_API_URL_{0}', upper(matrix.environment))] }}
              VITE_APP_ENV: ${{ matrix.environment }}

         - name: Bundle analysis
           working-directory: ${{ env.FRONTEND_DIR }}
           run: npm run analyze

         - name: Upload build artifacts
           uses: actions/upload-artifact@v4
           with:
              name: frontend-build-${{ matrix.environment }}
              path: ${{ env.FRONTEND_DIR }}/dist
              retention-days: 7

   security:
      name: Security Scan
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v4

         - name: Run Trivy vulnerability scanner
           uses: aquasecurity/trivy-action@master
           with:
              scan-type: "fs"
              scan-ref: ${{ env.FRONTEND_DIR }}
              format: "sarif"
              output: "trivy-results.sarif"

         - name: Upload Trivy scan results
           uses: github/codeql-action/upload-sarif@v2
           with:
              sarif_file: "trivy-results.sarif"
```

### 3. Workflow Backend (Laravel + Clean Architecture)

```yaml
name: Backend CI/CD

on:
   push:
      branches: [main, develop]
      paths: ["backend/**"]
   pull_request:
      branches: [main, develop]
      paths: ["backend/**"]

env:
   PHP_VERSION: "8.2"
   BACKEND_DIR: "./backend"

jobs:
   test:
      name: Test & Quality
      runs-on: ubuntu-latest

      services:
         mysql:
            image: mysql:8.0
            env:
               MYSQL_ROOT_PASSWORD: password
               MYSQL_DATABASE: testing
            ports:
               - 3306:3306
            options: >-
               --health-cmd="mysqladmin ping"
               --health-interval=10s
               --health-timeout=5s
               --health-retries=3

         redis:
            image: redis:7
            ports:
               - 6379:6379
            options: >-
               --health-cmd="redis-cli ping"
               --health-interval=10s
               --health-timeout=5s
               --health-retries=3

      steps:
         - uses: actions/checkout@v4

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: ${{ env.PHP_VERSION }}
              extensions: dom, curl, libxml, mbstring, zip, pcntl, pdo, sqlite, pdo_sqlite, bcmath, soap, intl, gd, exif, iconv
              coverage: xdebug

         - name: Copy environment file
           working-directory: ${{ env.BACKEND_DIR }}
           run: cp .env.testing .env

         - name: Install Composer dependencies
           working-directory: ${{ env.BACKEND_DIR }}
           run: composer install --no-dev --optimize-autoloader --no-interaction

         - name: Generate application key
           working-directory: ${{ env.BACKEND_DIR }}
           run: php artisan key:generate

         - name: Run database migrations
           working-directory: ${{ env.BACKEND_DIR }}
           run: php artisan migrate --force
           env:
              DB_CONNECTION: mysql
              DB_HOST: 127.0.0.1
              DB_PORT: 3306
              DB_DATABASE: testing
              DB_USERNAME: root
              DB_PASSWORD: password

         - name: Run database seeders
           working-directory: ${{ env.BACKEND_DIR }}
           run: php artisan db:seed --force

         - name: PHPStan Static Analysis
           working-directory: ${{ env.BACKEND_DIR }}
           run: ./vendor/bin/phpstan analyse --memory-limit=2G

         - name: PHP CS Fixer
           working-directory: ${{ env.BACKEND_DIR }}
           run: ./vendor/bin/php-cs-fixer fix --dry-run --diff

         - name: Run Feature Tests
           working-directory: ${{ env.BACKEND_DIR }}
           run: php artisan test --coverage --coverage-clover=coverage.xml
           env:
              DB_CONNECTION: mysql
              DB_HOST: 127.0.0.1
              DB_PORT: 3306
              DB_DATABASE: testing
              DB_USERNAME: root
              DB_PASSWORD: password

         - name: Upload coverage to Codecov
           uses: codecov/codecov-action@v3
           with:
              file: ${{ env.BACKEND_DIR }}/coverage.xml
              flags: backend

   architecture-test:
      name: Architecture Validation
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v4

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: ${{ env.PHP_VERSION }}

         - name: Install dependencies
           working-directory: ${{ env.BACKEND_DIR }}
           run: composer install --no-interaction

         - name: Validate Clean Architecture
           working-directory: ${{ env.BACKEND_DIR }}
           run: php artisan test --filter=ArchitectureTest

   api-docs:
      name: Generate API Documentation
      runs-on: ubuntu-latest
      needs: test

      steps:
         - uses: actions/checkout@v4

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: ${{ env.PHP_VERSION }}

         - name: Install dependencies
           working-directory: ${{ env.BACKEND_DIR }}
           run: composer install --no-interaction

         - name: Generate OpenAPI documentation
           working-directory: ${{ env.BACKEND_DIR }}
           run: php artisan l5-swagger:generate

         - name: Upload API docs
           uses: actions/upload-artifact@v4
           with:
              name: api-documentation
              path: ${{ env.BACKEND_DIR }}/storage/api-docs/
```

### 4. Workflow Despliegue a Staging

```yaml
name: Deploy to Staging

on:
   push:
      branches: [develop]
   workflow_dispatch:

env:
   STAGING_SERVER: ${{ secrets.STAGING_SERVER }}
   STAGING_USER: ${{ secrets.STAGING_USER }}

jobs:
   deploy-frontend:
      name: Deploy Frontend to Staging
      runs-on: ubuntu-latest
      environment: staging

      steps:
         - uses: actions/checkout@v4

         - name: Setup Node.js
           uses: actions/setup-node@v4
           with:
              node-version: "20"
              cache: "npm"
              cache-dependency-path: "./frontend/package-lock.json"

         - name: Install and build
           working-directory: ./frontend
           run: |
              npm ci
              npm run build:staging
           env:
              VITE_API_URL: ${{ secrets.VITE_API_URL_STAGING }}
              VITE_APP_ENV: staging

         - name: Deploy to staging server
           uses: easingthemes/ssh-deploy@main
           with:
              SSH_PRIVATE_KEY: ${{ secrets.STAGING_SSH_KEY }}
              ARGS: "-rlgoDzvc -i --delete"
              SOURCE: "frontend/dist/"
              REMOTE_HOST: ${{ env.STAGING_SERVER }}
              REMOTE_USER: ${{ env.STAGING_USER }}
              TARGET: "/var/www/staging/frontend"

   deploy-backend:
      name: Deploy Backend to Staging
      runs-on: ubuntu-latest
      environment: staging

      steps:
         - uses: actions/checkout@v4

         - name: Deploy backend
           uses: appleboy/ssh-action@v1.0.0
           with:
              host: ${{ env.STAGING_SERVER }}
              username: ${{ env.STAGING_USER }}
              key: ${{ secrets.STAGING_SSH_KEY }}
              script: |
                 cd /var/www/staging/backend
                 git pull origin develop
                 composer install --no-dev --optimize-autoloader
                 php artisan migrate --force
                 php artisan config:cache
                 php artisan route:cache
                 php artisan view:cache
                 php artisan queue:restart
                 sudo systemctl reload php8.2-fpm
```

### 5. Configuración de Secrets

En GitHub Settings > Secrets and variables > Actions:

```bash
# Staging
STAGING_SERVER=staging.tudominio.com
STAGING_USER=deploy
STAGING_SSH_KEY=<private_key>
VITE_API_URL_STAGING=https://api-staging.tudominio.com
DB_PASSWORD_STAGING=<password>

# Production  
PRODUCTION_SERVER=app.tudominio.com
PRODUCTION_USER=deploy
PRODUCTION_SSH_KEY=<private_key>
VITE_API_URL_PRODUCTION=https://api.tudominio.com
DB_PASSWORD_PRODUCTION=<password>

# General
CODECOV_TOKEN=<token>
SLACK_WEBHOOK=<webhook_url>
```

### 6. Custom Actions Reutilizables

```yaml
# .github/actions/setup-php/action.yml
name: "Setup PHP Environment"
description: "Configure PHP with extensions and dependencies"

inputs:
   php-version:
      description: "PHP version to setup"
      required: true
      default: "8.2"

   extensions:
      description: "PHP extensions to install"
      required: false
      default: "dom, curl, libxml, mbstring, zip, pcntl, pdo, sqlite, pdo_sqlite, bcmath, soap, intl, gd, exif, iconv"

runs:
   using: "composite"
   steps:
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
           php-version: ${{ inputs.php-version }}
           extensions: ${{ inputs.extensions }}
           coverage: xdebug

      - name: Get Composer cache directory
        shell: bash
        run: echo "composer-cache=$(composer config cache-files-dir)" >> $GITHUB_OUTPUT
        id: composer-cache

      - name: Cache Composer dependencies
        uses: actions/cache@v3
        with:
           path: ${{ steps.composer-cache.outputs.composer-cache }}
           key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
           restore-keys: ${{ runner.os }}-composer-
```

### 7. Notificaciones y Monitoreo

```yaml
# Al final de cada workflow
notify:
   name: Notify Results
   runs-on: ubuntu-latest
   needs: [test, build, deploy]
   if: always()

   steps:
      - name: Slack Notification
        uses: 8398a7/action-slack@v3
        with:
           status: ${{ job.status }}
           channel: "#deployments"
           webhook_url: ${{ secrets.SLACK_WEBHOOK }}
        env:
           SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

## Tips

### Optimización de Performance

- **Cache inteligente**: Usa cache para node_modules, vendor, y builds
- **Paralelización**: Ejecuta jobs independientes en paralelo
- **Artifacts selectivos**: Solo guarda lo necesario entre jobs
- **Matrix builds**: Prueba múltiples versiones/ambientes simultáneamente

### Seguridad

- **Secrets management**: Nunca hardcodees credenciales
- **OIDC tokens**: Usa OpenID Connect para autenticación sin secrets
- **Least privilege**: Permisos mínimos necesarios para cada job
- **Branch protection**: Requiere checks exitosos antes de merge

### Debugging

- **Debug mode**: Usa `ACTIONS_STEP_DEBUG: true` para logs detallados
- **SSH debugging**: `tmate` action para acceso SSH temporal
- **Local testing**: `act` para ejecutar workflows localmente
- **Conditional steps**: Usa `if:` para ejecutar pasos condicionalmente

### Monitoreo

- **Health checks**: Verifica servicios después del deploy
- **Rollback automático**: En caso de fallos críticos
- **Métricas**: Tiempo de build, cobertura, performance
- **Alertas**: Notificaciones proactivas de problemas

## Ejemplos

### Pipeline Completo para Feature Branch

```yaml
name: Feature Branch CI

on:
   pull_request:
      branches: [develop]

jobs:
   changes:
      name: Detect Changes
      runs-on: ubuntu-latest
      outputs:
         frontend: ${{ steps.changes.outputs.frontend }}
         backend: ${{ steps.changes.outputs.backend }}
      steps:
         - uses: actions/checkout@v4
         - uses: dorny/paths-filter@v2
           id: changes
           with:
              filters: |
                 frontend:
                   - 'frontend/**'
                 backend:
                   - 'backend/**'

   frontend-tests:
      name: Frontend Tests
      runs-on: ubuntu-latest
      needs: changes
      if: needs.changes.outputs.frontend == 'true'

      steps:
         - uses: actions/checkout@v4

         - name: Setup Node.js
           uses: actions/setup-node@v4
           with:
              node-version: "20"
              cache: "npm"
              cache-dependency-path: "./frontend/package-lock.json"

         - name: Install dependencies
           working-directory: ./frontend
           run: npm ci

         - name: Run tests with coverage
           working-directory: ./frontend
           run: npm run test:coverage

         - name: Comment PR with coverage
           uses: romeovs/lcov-reporter-action@v0.2.16
           with:
              github-token: ${{ secrets.GITHUB_TOKEN }}
              lcov-file: ./frontend/coverage/lcov.info

   backend-tests:
      name: Backend Tests
      runs-on: ubuntu-latest
      needs: changes
      if: needs.changes.outputs.backend == 'true'

      strategy:
         matrix:
            php-version: [8.1, 8.2]

      steps:
         - uses: actions/checkout@v4

         - name: Setup PHP ${{ matrix.php-version }}
           uses: shivammathur/setup-php@v2
           with:
              php-version: ${{ matrix.php-version }}
              coverage: xdebug

         - name: Run tests
           working-directory: ./backend
           run: php artisan test --coverage

   security-scan:
      name: Security Analysis
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v4

         - name: CodeQL Analysis
           uses: github/codeql-action/init@v2
           with:
              languages: javascript, php

         - name: Autobuild
           uses: github/codeql-action/autobuild@v2

         - name: Perform CodeQL Analysis
           uses: github/codeql-action/analyze@v2
```

### Workflow de Release Automático

```yaml
name: Release

on:
   push:
      tags:
         - "v*"

jobs:
   create-release:
      name: Create Release
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v4
           with:
              fetch-depth: 0

         - name: Generate changelog
           id: changelog
           run: |
              CHANGELOG=$(git log --pretty=format:"- %s" $(git describe --tags --abbrev=0 HEAD^)..HEAD)
              echo "changelog<<EOF" >> $GITHUB_OUTPUT
              echo "$CHANGELOG" >> $GITHUB_OUTPUT
              echo "EOF" >> $GITHUB_OUTPUT

         - name: Create Release
           uses: actions/create-release@v1
           env:
              GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
           with:
              tag_name: ${{ github.ref }}
              release_name: Release ${{ github.ref }}
              body: |
                 ## Changes
                 ${{ steps.changelog.outputs.changelog }}
              draft: false
              prerelease: false

   deploy-production:
      name: Deploy to Production
      runs-on: ubuntu-latest
      needs: create-release
      environment: production

      steps:
         - name: Deploy
           run: echo "Deploying to production..."
```

### Configuración para Monorepo

```yaml
name: Monorepo CI

on:
   push:
      branches: [main, develop]

jobs:
   changes:
      runs-on: ubuntu-latest
      outputs:
         packages: ${{ steps.changes.outputs.changes }}
      steps:
         - uses: actions/checkout@v4
         - uses: dorny/paths-filter@v2
           id: changes
           with:
              filters: |
                 frontend:
                   - 'packages/frontend/**'
                 backend:
                   - 'packages/backend/**'
                 mobile:
                   - 'packages/mobile/**'
                 shared:
                   - 'packages/shared/**'

   test:
      runs-on: ubuntu-latest
      needs: changes
      strategy:
         matrix:
            package: ${{ fromJSON(needs.changes.outputs.packages) }}

      steps:
         - uses: actions/checkout@v4
         - name: Test ${{ matrix.package }}
           run: |
              cd packages/${{ matrix.package }}
              npm ci
              npm test
```

### Deployment con Blue-Green

```yaml
name: Blue-Green Deployment

on:
   workflow_dispatch:
      inputs:
         environment:
            description: "Environment to deploy"
            required: true
            default: "staging"
            type: choice
            options:
               - staging
               - production

jobs:
   deploy:
      runs-on: ubuntu-latest
      environment: ${{ github.event.inputs.environment }}

      steps:
         - uses: actions/checkout@v4

         - name: Deploy to Blue Environment
           run: |
              # Deploy to blue environment
              echo "Deploying to blue..."

         - name: Health Check
           run: |
              # Verify deployment health
              curl -f https://blue.example.com/health

         - name: Switch Traffic
           run: |
              # Switch load balancer to blue
              echo "Switching traffic..."

         - name: Cleanup Green
           run: |
              # Stop old green environment
              echo "Cleaning up green..."
```

---

## Navegación

[⬅️ Setup de entornos de desarrollo local con Docker](./setup-entorno-local-docker.md)
| [🏠 README Principal](../../README.md) |
[Setup de ambientes (desarrollo, testing, staging, producción) ➡️](./setup-ambientes-multiples.md)
