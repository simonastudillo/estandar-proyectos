# CI/CD Pipelines con GitHub Actions

## ¿Qué es?

Los pipelines de CI/CD (Continuous Integration/Continuous Deployment) con GitHub
Actions son flujos de trabajo automatizados que se ejecutan en respuesta a
eventos del repositorio (como push, pull requests, etc.). Permiten automatizar
tareas como testing, building, y deployment, garantizando que cada cambio pase
por un proceso consistente antes de llegar a producción.

## ¿Por qué es importante?

- **Automatización**: Elimina procesos manuales propensos a errores
- **Velocidad**: Acelera el ciclo de desarrollo y deployment
- **Calidad**: Garantiza que todo código pase por tests antes del deploy
- **Consistencia**: Mismo proceso para todos los desarrolladores y entornos
- **Rollback**: Facilita la reversión de cambios problemáticos
- **Trazabilidad**: Historial completo de todos los deployments

## ¿Qué debe incluir?

### Workflow de Integración Continua (CI)

- Testing automatizado (backend y frontend)
- Code linting y quality checks
- Security scanning de dependencias
- Build verification
- Notificaciones de status

### Workflow de Deployment Continuo (CD)

- Deploy automático por entornos (staging, production)
- Database migrations automáticas
- Asset compilation y optimization
- Cache invalidation
- Health checks post-deployment

### Gestión de Secretos

- Variables de entorno por ambiente
- Credenciales de servidor seguras
- API keys y tokens
- Database connection strings

### Estrategias de Deployment

- Blue-Green deployment
- Rolling updates
- Canary releases
- Rollback automático en caso de fallos

## ¿Qué debo hacer?

### 1. Configurar Estructura de Workflows

```bash
# Crear directorio de workflows
mkdir -p .github/workflows

# Estructura recomendada:
# .github/workflows/
# ├── ci.yml                 # Continuous Integration
# ├── deploy-staging.yml     # Deploy a staging
# ├── deploy-production.yml  # Deploy a producción
# ├── security.yml          # Security checks
# └── dependency-update.yml  # Update automático de dependencias
```

### 2. Configurar Secrets del Repositorio

En GitHub: Settings → Secrets and variables → Actions

```bash
# Secrets necesarios:
PRODUCTION_HOST=your-server-ip
PRODUCTION_USER=deployment-user
SSH_PRIVATE_KEY=your-ssh-private-key
DB_PASSWORD=your-db-password
APP_KEY=your-laravel-app-key

# Variables por ambiente:
STAGING_DB_HOST=staging-db-host
PRODUCTION_DB_HOST=production-db-host
```

### 3. Workflow de Continuous Integration

```yaml
# .github/workflows/ci.yml
name: Continuous Integration

on:
   push:
      branches: [main, develop]
   pull_request:
      branches: [main, develop]

jobs:
   backend-tests:
      runs-on: ubuntu-latest

      services:
         mysql:
            image: mysql:8.0
            env:
               MYSQL_ROOT_PASSWORD: password
               MYSQL_DATABASE: testing
            options: >-
               --health-cmd="mysqladmin ping"
               --health-interval=10s
               --health-timeout=5s
               --health-retries=3

      steps:
         - uses: actions/checkout@v4

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"
              extensions: mysql, zip, gd

         - name: Install Backend Dependencies
           working-directory: ./backend
           run: composer install --prefer-dist --no-progress

         - name: Copy Environment File
           working-directory: ./backend
           run: cp .env.example .env.testing

         - name: Generate Application Key
           working-directory: ./backend
           run: php artisan key:generate --env=testing

         - name: Run Database Migrations
           working-directory: ./backend
           run: php artisan migrate --env=testing

         - name: Run Backend Tests
           working-directory: ./backend
           run: php artisan test

   frontend-tests:
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v4

         - name: Setup Node.js
           uses: actions/setup-node@v4
           with:
              node-version: "18"
              cache: "npm"
              cache-dependency-path: "./frontend/package-lock.json"

         - name: Install Frontend Dependencies
           working-directory: ./frontend
           run: npm ci

         - name: Run Frontend Linting
           working-directory: ./frontend
           run: npm run lint

         - name: Run Frontend Tests
           working-directory: ./frontend
           run: npm run test

         - name: Build Frontend
           working-directory: ./frontend
           run: npm run build

   security-checks:
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v4

         - name: Run Security Audit (Backend)
           working-directory: ./backend
           run: composer audit

         - name: Run Security Audit (Frontend)
           working-directory: ./frontend
           run: npm audit
```

### 4. Workflow de Deploy a Staging

```yaml
# .github/workflows/deploy-staging.yml
name: Deploy to Staging

on:
   push:
      branches: [develop]

jobs:
   deploy:
      runs-on: ubuntu-latest
      environment: staging

      steps:
         - uses: actions/checkout@v4

         - name: Setup Node.js
           uses: actions/setup-node@v4
           with:
              node-version: "18"

         - name: Build Frontend
           working-directory: ./frontend
           run: |
              npm ci
              npm run build

         - name: Deploy to Staging Server
           uses: appleboy/ssh-action@v1.0.0
           with:
              host: ${{ secrets.STAGING_HOST }}
              username: ${{ secrets.STAGING_USER }}
              key: ${{ secrets.SSH_PRIVATE_KEY }}
              script: |
                 cd /var/www/staging

                 # Pull latest changes
                 git pull origin develop

                 # Backend deployment
                 cd backend
                 composer install --optimize-autoloader --no-dev
                 php artisan migrate --force
                 php artisan config:cache
                 php artisan route:cache
                 php artisan view:cache

                 # Frontend deployment
                 cd ../frontend
                 npm ci
                 npm run build

                 # Restart services
                 sudo systemctl restart php8.2-fpm
                 sudo systemctl reload nginx

         - name: Health Check
           run: |
              sleep 30
              curl -f ${{ secrets.STAGING_URL }}/health || exit 1

         - name: Notify Slack
           if: always()
           uses: 8398a7/action-slack@v3
           with:
              status: ${{ job.status }}
              channel: "#deployments"
           env:
              SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

### 5. Workflow de Deploy a Producción

```yaml
# .github/workflows/deploy-production.yml
name: Deploy to Production

on:
   push:
      tags:
         - "v*"

jobs:
   deploy:
      runs-on: ubuntu-latest
      environment: production

      steps:
         - uses: actions/checkout@v4

         - name: Verify Tag
           run: |
              if [[ ! $GITHUB_REF =~ ^refs/tags/v[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
                echo "Invalid tag format. Use semantic versioning (v1.0.0)"
                exit 1
              fi

         - name: Create Deployment
           id: deployment
           uses: actions/github-script@v6
           with:
              script: |
                 const deployment = await github.rest.repos.createDeployment({
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   ref: context.ref,
                   environment: 'production',
                   required_contexts: []
                 });
                 return deployment.data.id;

         - name: Build Application
           run: |
              # Build frontend
              cd frontend
              npm ci
              npm run build
              tar -czf ../frontend-build.tar.gz dist/

              # Prepare backend
              cd ../backend
              composer install --optimize-autoloader --no-dev
              tar -czf ../backend-build.tar.gz . --exclude=node_modules --exclude=.git

         - name: Deploy to Production
           uses: appleboy/ssh-action@v1.0.0
           with:
              host: ${{ secrets.PRODUCTION_HOST }}
              username: ${{ secrets.PRODUCTION_USER }}
              key: ${{ secrets.SSH_PRIVATE_KEY }}
              script: |
                 set -e

                 # Create backup
                 sudo -u app cp -r /var/www/html /var/www/backup-$(date +%Y%m%d-%H%M%S)

                 # Deploy new version
                 cd /tmp

                 # Upload and extract builds
                 scp frontend-build.tar.gz app@${{ secrets.PRODUCTION_HOST }}:/tmp/
                 scp backend-build.tar.gz app@${{ secrets.PRODUCTION_HOST }}:/tmp/

                 # Extract frontend
                 sudo -u app tar -xzf frontend-build.tar.gz -C /var/www/html/

                 # Extract backend
                 sudo -u app tar -xzf backend-build.tar.gz -C /var/www/html/backend/

                 # Run migrations
                 cd /var/www/html/backend
                 sudo -u app php artisan migrate --force

                 # Clear and cache
                 sudo -u app php artisan config:cache
                 sudo -u app php artisan route:cache
                 sudo -u app php artisan view:cache

                 # Restart services
                 sudo systemctl restart php8.2-fpm
                 sudo systemctl reload nginx

                 # Cleanup
                 rm -f /tmp/frontend-build.tar.gz /tmp/backend-build.tar.gz

         - name: Health Check
           run: |
              sleep 60
              for i in {1..5}; do
                if curl -f ${{ secrets.PRODUCTION_URL }}/health; then
                  echo "Health check passed"
                  exit 0
                fi
                echo "Attempt $i failed, retrying..."
                sleep 30
              done
              echo "Health check failed"
              exit 1

         - name: Update Deployment Status
           if: always()
           uses: actions/github-script@v6
           with:
              script: |
                 const state = '${{ job.status }}' === 'success' ? 'success' : 'failure';
                 await github.rest.repos.createDeploymentStatus({
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   deployment_id: ${{ steps.deployment.outputs.result }},
                   state: state,
                   environment_url: '${{ secrets.PRODUCTION_URL }}'
                 });
```

## Tips

### Optimización de Workflows

- **Cache Dependencies**: Usar cache para node_modules y vendor
- **Parallel Jobs**: Ejecutar tests frontend y backend en paralelo
- **Conditional Steps**: Solo ejecutar pasos cuando sea necesario
- **Matrix Strategy**: Probar en múltiples versiones de PHP/Node

### Seguridad en CI/CD

- **Secrets Management**: Nunca hardcodear credenciales
- **Environment Protection**: Usar GitHub environments con approvals
- **Least Privilege**: Usuarios de deploy con permisos mínimos
- **Audit Logs**: Revisar regularmente los logs de deployment

### Estrategias de Rollback

- **Backup Automático**: Antes de cada deployment
- **Tagged Releases**: Para facilitar rollbacks a versiones específicas
- **Database Rollbacks**: Scripts para revertir migraciones
- **Quick Rollback**: Workflow dedicado para rollbacks rápidos

### Monitoreo de Deployments

- **Health Checks**: Verificar que la aplicación funciona post-deploy
- **Performance Metrics**: Monitorear latencia después del deploy
- **Error Tracking**: Alertas automáticas si aumentan los errores
- **Notification**: Slack/Teams para notificar status de deployments

## Ejemplos

### Workflow con Matrix Strategy

```yaml
strategy:
   matrix:
      php-version: [8.1, 8.2, 8.3]
      node-version: [16, 18, 20]

steps:
   - name: Setup PHP ${{ matrix.php-version }}
     uses: shivammathur/setup-php@v2
     with:
        php-version: ${{ matrix.php-version }}
```

### Deployment con Blue-Green Strategy

```yaml
- name: Blue-Green Deployment
  run: |
     # Deploy to inactive environment
     if [ "$ACTIVE_ENV" = "blue" ]; then
       DEPLOY_ENV="green"
     else
       DEPLOY_ENV="blue"
     fi

     # Deploy to inactive environment
     deploy_to_environment $DEPLOY_ENV

     # Run health checks
     health_check $DEPLOY_ENV

     # Switch traffic
     switch_load_balancer $DEPLOY_ENV

     # Update active environment variable
     echo "ACTIVE_ENV=$DEPLOY_ENV" >> production.env
```

### Rollback Workflow

```yaml
name: Rollback Production

on:
   workflow_dispatch:
      inputs:
         version:
            description: "Version to rollback to (tag)"
            required: true
            type: string

jobs:
   rollback:
      runs-on: ubuntu-latest
      environment: production

      steps:
         - name: Rollback to Version
           uses: appleboy/ssh-action@v1.0.0
           with:
              host: ${{ secrets.PRODUCTION_HOST }}
              username: ${{ secrets.PRODUCTION_USER }}
              key: ${{ secrets.SSH_PRIVATE_KEY }}
              script: |
                 cd /var/www/html
                 git fetch --tags
                 git checkout ${{ github.event.inputs.version }}

                 # Rollback database if needed
                 php artisan migrate:rollback --step=1

                 # Clear caches
                 php artisan cache:clear
                 php artisan config:clear

                 # Restart services
                 sudo systemctl restart php8.2-fpm
```

### Conditional Deployment

```yaml
- name: Deploy to Production
  if: github.ref == 'refs/heads/main' && contains(github.event.head_commit.message, '[deploy]')
  run: echo "Deploying to production..."

- name: Deploy to Staging
  if: github.ref == 'refs/heads/develop'
  run: echo "Deploying to staging..."
```

## Navegación

**Progreso en Despliegue y DevOps:**

- ✅ [Despliegue y DevOps](./despliegue-devops.md)
- ✅
  [Configuración de Servidores de Producción](./configuracion-servidores-produccion.md)
- ✅ **CI/CD Pipelines con GitHub Actions** ← Estás aquí
- ⏭️ [Docker y Contenedores](./docker-contenedores.md)
- ⏭️ [Monitoreo y Logging](./monitoreo-logging.md)
- ⏭️ [Optimización base de datos](./optimizacion-base-datos.md)
- ⏭️ [Backup y Recovery](./backup-recovery-strategies.md)
- ⏭️ [SSL y Configuraciones de Seguridad](./ssl-configuraciones-seguridad.md)

---

### Siguiente Paso

Continúa con [**Docker y Contenedores**](./docker-contenedores.md)

[⬅️ Configuración de Servidores de Producción](./configuracion-servidores-produccion.md)
| [🏠 README Principal](../../README.md) |
[➡️ Docker y Contenedores](./docker-contenedores.md)
