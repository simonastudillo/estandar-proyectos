# Configuración de Repositorios Git y GitHub

## ¿Qué es la Configuración de Repositorios Git y GitHub?

La **Configuración de Repositorios Git y GitHub** es el proceso de establecer y
configurar el **control de versiones** y la **plataforma de colaboración** que
utilizará el proyecto durante todo su ciclo de desarrollo. Incluye la
configuración inicial del repositorio Git, la configuración de GitHub (o
GitLab/Bitbucket), el establecimiento de **branch strategy**, **workflows de
colaboración**, **protecciones de ramas** y la integración con herramientas de
**CI/CD**.

Esta configuración es fundamental para garantizar un **desarrollo colaborativo
eficiente**, **trazabilidad completa** del código, **control de calidad
automatizado** y **procesos de deployment seguros** siguiendo las mejores
prácticas de la industria.

La configuración debe soportar la arquitectura **Clean Architecture + DDD**
definida en las etapas anteriores, facilitando el trabajo en **múltiples capas**
(Domain, Application, Infrastructure) y **múltiples proyectos** (backend
Laravel, frontend React, móvil React Native).

## ¿Por qué es Importante?

### 🔄 **Control de Versiones Eficiente**

- Permite rastrear todos los cambios en el código de manera granular
- Facilita la colaboración simultánea de múltiples desarrolladores
- Proporciona historial completo de evolución del proyecto
- Permite rollbacks seguros a versiones anteriores cuando sea necesario

### 🛡️ **Calidad y Seguridad del Código**

- Implementa code reviews obligatorios antes de merge a ramas principales
- Establece protecciones que evitan pushes directos a producción
- Configura verificaciones automáticas de calidad (linting, testing)
- Integra security scanning y dependency vulnerability checks

### 🚀 **Automatización de Workflows**

- Configura CI/CD pipelines que se ejecutan automáticamente
- Automatiza testing en múltiples ambientes
- Facilita deployments automáticos basados en branch strategy
- Implementa notificaciones y reportes automáticos

### 👥 **Colaboración y Gestión de Equipo**

- Establece workflows claros para feature development
- Facilita code reviews estructurados con templates
- Integra con herramientas de project management (Issues, Projects)
- Proporciona documentación centralizada y accesible

## ¿Qué debe incluir?

### 📁 **Configuración Inicial del Repositorio**

#### **1. Inicialización y Estructura Básica**

```bash
# Inicialización del repositorio principal
git init
git remote add origin https://github.com/usuario/proyecto.git

# Configuración inicial
git config user.name "Nombre Desarrollador"
git config user.email "email@dominio.com"
git config init.defaultBranch main
```

#### **2. Archivo .gitignore Completo**

```gitignore
# Dependencies
node_modules/
vendor/

# Production builds
/build
/dist
/public/hot
/public/storage
/storage/*.key

# Environment files
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Laravel specific
/bootstrap/cache/*.php
/storage/app/*
/storage/framework/cache/*
/storage/framework/sessions/*
/storage/framework/views/*
/storage/logs/*

# React/Node specific
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Testing
/coverage
/.nyc_output

# Docker
docker-compose.override.yml
```

#### **3. README.md Estructurado**

- Descripción del proyecto y objetivos
- Stack tecnológico utilizado
- Instrucciones de instalación y setup
- Guías de desarrollo y contribución
- Enlaces a documentación adicional

### 🌿 **Branch Strategy (Git Flow)**

#### **Estructura de Ramas**

```
main/master           # Producción - Solo código estable
├── develop          # Desarrollo - Integración continua
├── feature/*        # Nuevas funcionalidades
├── release/*        # Preparación de releases
├── hotfix/*         # Fixes urgentes para producción
└── bugfix/*         # Corrección de bugs
```

#### **Nomenclatura de Ramas**

```bash
# Features
feature/auth-sistema-login
feature/crud-usuarios
feature/dashboard-admin

# Releases
release/v1.0.0
release/v1.1.0

# Hotfixes
hotfix/security-fix-auth
hotfix/critical-bug-payments

# Bugfixes
bugfix/correccion-validacion-email
bugfix/problema-responsive-mobile
```

### 🛡️ **Protecciones de Ramas en GitHub**

#### **Configuración para rama `main`**

- ✅ Require pull request reviews before merging (mínimo 2 reviewers)
- ✅ Require status checks to pass before merging
- ✅ Require branches to be up to date before merging
- ✅ Require conversation resolution before merging
- ✅ Restrict pushes that create files larger than 100MB
- ✅ Do not allow bypassing the above settings

#### **Configuración para rama `develop`**

- ✅ Require pull request reviews before merging (mínimo 1 reviewer)
- ✅ Require status checks to pass before merging
- ✅ Require branches to be up to date before merging

### 🔧 **Templates de GitHub**

#### **Pull Request Template**

```markdown
## Descripción

Descripción clara y concisa de los cambios realizados.

## Tipo de cambio

- [ ] Bug fix (cambio que corrige un problema)
- [ ] Nueva funcionalidad (cambio que añade funcionalidad)
- [ ] Breaking change (cambio que puede afectar funcionalidad existente)
- [ ] Actualización de documentación

## ¿Cómo se ha probado?

- [ ] Tests unitarios
- [ ] Tests de integración
- [ ] Tests manuales
- [ ] Tests en diferentes browsers/dispositivos

## Checklist

- [ ] Mi código sigue las convenciones de estilo del proyecto
- [ ] He realizado self-review de mi código
- [ ] He comentado mi código en áreas complejas
- [ ] He realizado cambios correspondientes en la documentación
- [ ] Mis cambios no generan nuevas advertencias
- [ ] He añadido tests que prueban mi fix o funcionalidad
- [ ] Tests nuevos y existentes pasan localmente

## Screenshots (si aplica)

[Agregar screenshots para cambios visuales]

## Issues relacionados

Fixes #[número]
```

#### **Issue Templates**

**Bug Report Template:**

```markdown
---
name: Bug Report
about: Reportar un problema o error
title: "[BUG] "
labels: bug
assignees: ""
---

## Descripción del Bug

Descripción clara y concisa de cuál es el problema.

## Pasos para Reproducir

1. Ir a '...'
2. Hacer click en '....'
3. Scroll down to '....'
4. Ver error

## Comportamiento Esperado

Descripción clara de lo que esperabas que sucediera.

## Comportamiento Actual

Descripción clara de lo que sucede actualmente.

## Screenshots

Si aplica, agregar screenshots para explicar el problema.

## Entorno

- OS: [ej. iOS, Windows, Linux]
- Browser: [ej. chrome, safari]
- Versión: [ej. 22]

## Información Adicional

Cualquier otra información sobre el problema.
```

**Feature Request Template:**

```markdown
---
name: Feature Request
about: Sugerir una nueva funcionalidad
title: "[FEATURE] "
labels: enhancement
assignees: ""
---

## ¿Tu solicitud está relacionada con un problema?

Descripción clara y concisa de cuál es el problema.

## Describe la solución que te gustaría

Descripción clara y concisa de lo que quieres que suceda.

## Describe alternativas que has considerado

Descripción clara y concisa de soluciones alternativas.

## Información adicional

Cualquier otra información o screenshots sobre la solicitud.
```

### ⚙️ **Configuración de GitHub Actions**

#### **Archivo base: `.github/workflows/ci.yml`**

```yaml
name: CI/CD Pipeline

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
            ports:
               - 3306:3306
            options: --health-cmd="mysqladmin ping" --health-interval=10s --health-timeout=5s --health-retries=3

      steps:
         - uses: actions/checkout@v3

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"
              extensions: mbstring, dom, fileinfo, mysql

         - name: Install Backend Dependencies
           run: |
              cd backend
              composer install --prefer-dist --no-progress

         - name: Copy Environment File
           run: |
              cd backend
              cp .env.example .env
              php artisan key:generate

         - name: Run Backend Tests
           run: |
              cd backend
              php artisan test

   frontend-tests:
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"
              cache: "npm"
              cache-dependency-path: frontend/package-lock.json

         - name: Install Frontend Dependencies
           run: |
              cd frontend
              npm ci

         - name: Run Linting
           run: |
              cd frontend
              npm run lint

         - name: Run Tests
           run: |
              cd frontend
              npm run test:ci

         - name: Build
           run: |
              cd frontend
              npm run build

   code-quality:
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v3

         - name: Run PHP CS Fixer
           run: |
              cd backend
              composer install
              vendor/bin/php-cs-fixer fix --dry-run --diff

         - name: Run ESLint
           run: |
              cd frontend
              npm ci
              npm run lint

   security-scan:
      runs-on: ubuntu-latest

      steps:
         - uses: actions/checkout@v3

         - name: Run Security Scanner
           uses: securecodewarrior/github-action-add-sarif@v1
           with:
              sarif-file: "security-scan-results.sarif"
```

### 🏷️ **Configuración de Releases y Tags**

#### **Versionado Semántico**

```bash
# Versiones principales
v1.0.0  # Release mayor
v1.1.0  # Release menor (nuevas features)
v1.1.1  # Patch (bug fixes)

# Pre-releases
v1.0.0-alpha.1
v1.0.0-beta.1
v1.0.0-rc.1
```

#### **Automated Release Notes**

```yaml
# .github/release.yml
changelog:
   exclude:
      labels:
         - ignore-for-release
   categories:
      - title: Breaking Changes 🛠
        labels:
           - Semver-Major
           - breaking-change
      - title: Exciting New Features 🎉
        labels:
           - Semver-Minor
           - enhancement
      - title: Bug Fixes 🐛
        labels:
           - Semver-Patch
           - bug
      - title: Other Changes
        labels:
           - "*"
```

## ¿Qué debo hacer?

### 📋 **Paso 1: Configuración Inicial del Repositorio**

#### **1.1 Crear y Configurar Repositorio Local**

```bash
# Navegar al directorio del proyecto
cd /ruta/al/proyecto

# Inicializar repositorio Git
git init

# Configurar información del usuario
git config user.name "Tu Nombre"
git config user.email "tu.email@dominio.com"

# Configurar rama principal
git config init.defaultBranch main

# Crear archivos básicos
touch README.md .gitignore
```

#### **1.2 Crear Repositorio en GitHub**

1. Ir a GitHub y crear nuevo repositorio
2. **NO** inicializar con README, .gitignore o LICENSE
3. Configurar como repositorio privado inicialmente
4. Agregar descripción clara del proyecto

#### **1.3 Conectar Local con Remoto**

```bash
# Agregar remote origin
git remote add origin https://github.com/usuario/proyecto.git

# Verificar remote
git remote -v

# Primer commit
git add .
git commit -m "Initial commit: project structure"

# Push inicial
git push -u origin main
```

### 🌿 **Paso 2: Implementar Branch Strategy**

#### **2.1 Crear Estructura de Ramas**

```bash
# Crear rama develop
git checkout -b develop
git push -u origin develop

# Crear primera feature
git checkout -b feature/setup-project-structure
git push -u origin feature/setup-project-structure
```

#### **2.2 Configurar Git Flow (Opcional)**

```bash
# Instalar git-flow
# En Ubuntu/Debian: sudo apt-get install git-flow
# En macOS: brew install git-flow

# Inicializar git-flow
git flow init

# Crear nueva feature
git flow feature start nueva-funcionalidad

# Finalizar feature
git flow feature finish nueva-funcionalidad
```

### 🛡️ **Paso 3: Configurar Protecciones en GitHub**

#### **3.1 Proteger Rama Main**

1. Ir a **Settings** > **Branches**
2. Click en **Add rule**
3. Configurar **Branch name pattern**: `main`
4. Activar:
   - ✅ Require pull request reviews before merging
   - ✅ Require status checks to pass before merging
   - ✅ Require branches to be up to date before merging
   - ✅ Require conversation resolution before merging
   - ✅ Include administrators

#### **3.2 Proteger Rama Develop**

1. Repetir proceso para rama `develop`
2. Configurar con menos restricciones que `main`
3. Permitir merge commits y squash merging

### 📝 **Paso 4: Configurar Templates**

#### **4.1 Crear Template de Pull Request**

```bash
# Crear directorio
mkdir -p .github

# Crear template de PR
touch .github/pull_request_template.md
```

#### **4.2 Crear Templates de Issues**

```bash
# Crear directorio para templates
mkdir -p .github/ISSUE_TEMPLATE

# Crear templates específicos
touch .github/ISSUE_TEMPLATE/bug_report.md
touch .github/ISSUE_TEMPLATE/feature_request.md
```

### ⚙️ **Paso 5: Configurar GitHub Actions**

#### **5.1 Crear Directorio de Workflows**

```bash
mkdir -p .github/workflows
```

#### **5.2 Crear Workflow Básico de CI**

```bash
touch .github/workflows/ci.yml
```

#### **5.3 Configurar Secrets**

1. Ir a **Settings** > **Secrets and variables** > **Actions**
2. Agregar secrets necesarios:
   - `DB_PASSWORD`
   - `JWT_SECRET`
   - `API_KEYS`

### 🔧 **Paso 6: Configurar Herramientas Adicionales**

#### **6.1 Configurar Dependabot**

```yaml
# .github/dependabot.yml
version: 2
updates:
   - package-ecosystem: "composer"
     directory: "/backend"
     schedule:
        interval: "weekly"

   - package-ecosystem: "npm"
     directory: "/frontend"
     schedule:
        interval: "weekly"
```

#### **6.2 Configurar CodeQL (Security)**

```yaml
# .github/workflows/codeql-analysis.yml
name: "CodeQL"

on:
   push:
      branches: [main, develop]
   pull_request:
      branches: [main]

jobs:
   analyze:
      name: Analyze
      runs-on: ubuntu-latest

      strategy:
         fail-fast: false
         matrix:
            language: ["javascript", "php"]

      steps:
         - name: Checkout repository
           uses: actions/checkout@v3

         - name: Initialize CodeQL
           uses: github/codeql-action/init@v2
           with:
              languages: ${{ matrix.language }}

         - name: Perform CodeQL Analysis
           uses: github/codeql-action/analyze@v2
```

## Tips

### 💡 **Mejores Prácticas de Commits**

#### **Convenciones de Commit Messages**

```bash
# Formato: tipo(scope): descripción
feat(auth): implementar login con JWT
fix(api): corregir validación de email
docs(readme): actualizar instrucciones de instalación
style(frontend): aplicar formato con prettier
refactor(backend): reorganizar estructura de controladores
test(auth): agregar tests unitarios para login
chore(deps): actualizar dependencias de seguridad
```

#### **Tipos de Commits Estándar**

- **feat**: Nueva funcionalidad
- **fix**: Corrección de bugs
- **docs**: Cambios en documentación
- **style**: Cambios de formato (no afectan la lógica)
- **refactor**: Refactoring de código
- **test**: Agregar o modificar tests
- **chore**: Tareas de mantenimiento

### 🚀 **Optimización de Workflows**

#### **Paralelización de Jobs**

```yaml
jobs:
   tests:
      strategy:
         matrix:
            php-version: [8.1, 8.2]
            node-version: [16, 18]
      runs-on: ubuntu-latest
```

#### **Cache para Dependencies**

```yaml
- name: Cache Composer dependencies
  uses: actions/cache@v3
  with:
     path: vendor
     key: composer-${{ hashFiles('composer.lock') }}

- name: Cache NPM dependencies
  uses: actions/cache@v3
  with:
     path: ~/.npm
     key: npm-${{ hashFiles('package-lock.json') }}
```

### 🔍 **Monitoreo y Métricas**

#### **GitHub Insights**

- Revisar regularmente **Pulse** para actividad del repo
- Monitorear **Contributors** y distribución de commits
- Analizar **Traffic** para ver clones y visits
- Usar **Dependency graph** para security alerts

#### **Configurar Webhooks**

```bash
# Para integración con herramientas externas
# Slack, Discord, Jira, etc.
curl -X POST https://api.github.com/repos/usuario/repo/hooks \
  -H "Authorization: token YOUR_TOKEN" \
  -d '{
    "name": "web",
    "active": true,
    "events": ["push", "pull_request"],
    "config": {
      "url": "https://example.com/webhook",
      "content_type": "json"
    }
  }'
```

## Ejemplos

### 📁 **Ejemplo 1: Configuración Completa de Nuevo Proyecto**

#### **Estructura inicial del proyecto**

```
proyecto-ecommerce/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── codeql-analysis.yml
│   │   └── release.yml
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   ├── pull_request_template.md
│   └── dependabot.yml
├── backend/
│   ├── app/
│   ├── database/
│   ├── tests/
│   └── composer.json
├── frontend/
│   ├── src/
│   ├── tests/
│   └── package.json
├── docs/
├── README.md
├── .gitignore
└── LICENSE
```

#### **Script de inicialización completa**

```bash
#!/bin/bash

# configurar-repo.sh
# Script para configurar un nuevo repositorio con todas las mejores prácticas

set -e

PROJECT_NAME=$1
GITHUB_USER=$2

if [ -z "$PROJECT_NAME" ] || [ -z "$GITHUB_USER" ]; then
    echo "Uso: ./configurar-repo.sh nombre-proyecto usuario-github"
    exit 1
fi

echo "🚀 Configurando repositorio: $PROJECT_NAME"

# 1. Crear directorio y navegar
mkdir $PROJECT_NAME && cd $PROJECT_NAME

# 2. Inicializar Git
git init
git config init.defaultBranch main

# 3. Crear estructura básica
mkdir -p .github/{workflows,ISSUE_TEMPLATE}
mkdir -p backend frontend docs

# 4. Crear archivos básicos
cat > README.md << EOF
# $PROJECT_NAME

## Descripción
[Descripción del proyecto]

## Stack Tecnológico
- Backend: Laravel 10 + PHP 8.2
- Frontend: React 18 + TypeScript
- Base de datos: MySQL 8.0
- Deployment: Docker + GitHub Actions

## Instalación
[Instrucciones de instalación]

## Contribución
[Guías de contribución]
EOF

cat > .gitignore << EOF
# Dependencies
node_modules/
vendor/

# Environment files
.env
.env.local

# Build outputs
/build
/dist

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Logs
*.log
EOF

# 5. Crear templates de GitHub
cat > .github/pull_request_template.md << EOF
## Descripción
[Descripción de los cambios]

## Tipo de cambio
- [ ] Bug fix
- [ ] Nueva funcionalidad
- [ ] Breaking change
- [ ] Documentación

## Checklist
- [ ] Código revisado
- [ ] Tests añadidos/actualizados
- [ ] Documentación actualizada
EOF

# 6. Crear repositorio en GitHub (requiere GitHub CLI)
gh repo create $GITHUB_USER/$PROJECT_NAME --private --description "Proyecto $PROJECT_NAME"

# 7. Conectar con remoto
git remote add origin https://github.com/$GITHUB_USER/$PROJECT_NAME.git

# 8. Primer commit
git add .
git commit -m "feat: initial project setup with GitHub configuration"

# 9. Crear ramas
git push -u origin main
git checkout -b develop
git push -u origin develop

echo "✅ Repositorio configurado exitosamente!"
echo "🌐 URL: https://github.com/$GITHUB_USER/$PROJECT_NAME"
```

### 🔄 **Ejemplo 2: Workflow de Desarrollo Típico**

#### **Desarrollo de nueva funcionalidad**

```bash
# 1. Actualizar develop
git checkout develop
git pull origin develop

# 2. Crear nueva feature
git checkout -b feature/sistema-autenticacion

# 3. Desarrollar funcionalidad
# ... desarrollo ...
git add .
git commit -m "feat(auth): implementar registro de usuarios"

# 4. Push de feature
git push -u origin feature/sistema-autenticacion

# 5. Crear Pull Request via GitHub CLI
gh pr create \
  --title "feat: Sistema de autenticación completo" \
  --body "Implementa registro, login y logout de usuarios con JWT" \
  --base develop \
  --head feature/sistema-autenticacion

# 6. Después de review y merge, limpiar
git checkout develop
git pull origin develop
git branch -d feature/sistema-autenticacion
```

#### **Hotfix urgente**

```bash
# 1. Crear hotfix desde main
git checkout main
git pull origin main
git checkout -b hotfix/security-fix-v1.0.1

# 2. Aplicar fix
# ... fix ...
git add .
git commit -m "fix(security): corregir vulnerabilidad en validación"

# 3. Push y PR a main
git push -u origin hotfix/security-fix-v1.0.1
gh pr create --title "hotfix: Security fix v1.0.1" --base main

# 4. También merge a develop
gh pr create --title "hotfix: Security fix v1.0.1" --base develop
```

### 📊 **Ejemplo 3: Configuración de Métricas y Monitoreo**

#### **GitHub Actions con reportes**

```yaml
name: CI with Reports

on: [push, pull_request]

jobs:
   test-coverage:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Run Tests with Coverage
           run: |
              npm test -- --coverage --watchAll=false

         - name: Upload Coverage to Codecov
           uses: codecov/codecov-action@v3
           with:
              file: ./coverage/lcov.info

         - name: Comment PR with Coverage
           uses: marocchino/sticky-pull-request-comment@v2
           if: github.event_name == 'pull_request'
           with:
              recreate: true
              path: coverage/lcov-report/index.html

   performance-audit:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Lighthouse CI
           run: |
              npm install -g @lhci/cli@0.9.x
              lhci autorun
           env:
              LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}
```

---

## Navegación

**Progreso en Configuración del Entorno de Desarrollo:**

- ✅
  [Configuración del Entorno - Introducción](./configuracion-entorno-desarrollo.md)
- ✅ [Requisitos mínimos del sistema](./requisitos-sistema.md)
- ✅
  [Instalación de dependencias iniciales](./instalacion-dependencias-iniciales.md)
- ⏭️ **Configuración de repositorios Git y GitHub** ← Estás aquí
- ⏭️
  [Setup de entornos de desarrollo local con Docker](./setup-entorno-local-docker.md)
- ⏭️
  [Configuración de herramientas de CI/CD con GitHub Actions](./configuracion-cicd-github-actions.md)
- ⏭️
  [Setup de ambientes (desarrollo, testing, staging, producción)](./setup-ambientes-multiples.md)
- ⏭️
  [Configuración de herramientas de calidad de código](./configuracion-herramientas-calidad.md)
- ⏭️
  [Setup de Laravel con Clean Architecture](./setup-laravel-clean-architecture.md)
- ⏭️
  [Configuración de React + TypeScript + Vite](./configuracion-react-typescript-vite.md)

---

### Siguiente Paso

Continúa con
[**Setup de entornos de desarrollo local con Docker**](./setup-entorno-local-docker.md).

[⬅️ Instalación de dependencias iniciales](./instalacion-dependencias-iniciales.md)
| [🏠 README Principal](../../README.md) |
[➡️ Setup de entornos de desarrollo local con Docker](./setup-entorno-local-docker.md)
