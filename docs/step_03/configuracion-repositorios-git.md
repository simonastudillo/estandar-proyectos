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

### � **Conventional Commits y Mensajes Estándar**

#### **Formato de Commits**

Los mensajes de commit deben seguir el estándar **Conventional Commits** para
mantener un historial limpio y permitir automatización:

```bash
<tipo>[ámbito opcional]: <descripción>

[cuerpo opcional]

[footer opcional]
```

#### **Tipos de Commit Permitidos**

- **feat**: Nueva funcionalidad para el usuario
- **fix**: Corrección de bug
- **docs**: Cambios en documentación
- **style**: Cambios de formato (espacios, comas, etc.)
- **refactor**: Refactoring de código sin cambios funcionales
- **test**: Agregar o modificar tests
- **chore**: Cambios en build, dependencias, etc.
- **perf**: Mejoras de performance
- **ci**: Cambios en configuración de CI/CD
- **build**: Cambios en sistema de build

#### **Ejemplos de Commits Válidos**

```bash
# Funcionalidades
feat(auth): agregar autenticación con Laravel Sanctum
feat(user): implementar CRUD de usuarios en domain layer
feat(api): agregar endpoint GET /api/v1/users

# Correcciones
fix(auth): corregir validación de tokens expirados
fix(database): resolver problema de conexión en producción

# Documentación
docs(readme): actualizar instrucciones de instalación
docs(api): agregar documentación de endpoints v2

# Refactoring
refactor(user): extraer validaciones a value objects
refactor(api): simplificar estructura de responses

# Tests
test(auth): agregar tests unitarios para AuthService
test(integration): agregar tests de endpoints de usuarios
```

### 🌳 **Branching Strategy - Git Flow Adaptado**

#### **Estructura de Ramas**

```bash
main/                    # Código en producción (solo releases)
├── develop/            # Rama principal de desarrollo
│   ├── feature/user-management     # Nueva funcionalidad
│   ├── feature/auth-system        # Nueva funcionalidad
│   └── feature/api-v2            # Nueva funcionalidad
├── release/v1.2.0     # Preparación de release
├── hotfix/urgent-bug  # Correcciones urgentes en producción
└── docs/update-readme # Actualizaciones de documentación
```

#### **Convenciones de Nombres de Ramas**

- **main**: Código estable en producción
- **develop**: Integración de features completadas
- **feature/[nombre-descriptivo]**: Nuevas funcionalidades
- **release/v[x.y.z]**: Preparación de versiones
- **hotfix/[descripción-corta]**: Correcciones urgentes
- **docs/[tema]**: Actualizaciones de documentación
- **chore/[tarea]**: Tareas de mantenimiento

#### **Flujo de Trabajo por Tipo de Rama**

```bash
# 1. Feature Development
git checkout develop
git pull origin develop
git checkout -b feature/user-authentication
# ... desarrollo ...
git commit -m "feat(auth): implementar login con Sanctum"
git push origin feature/user-authentication
# Crear Pull Request hacia develop

# 2. Release Preparation
git checkout develop
git checkout -b release/v1.2.0
# ... preparar release (tests, docs, etc.) ...
git commit -m "chore(release): preparar versión v1.2.0"
# Merge a main y develop, crear tag

# 3. Hotfix
git checkout main
git checkout -b hotfix/critical-security-fix
# ... corrección urgente ...
git commit -m "fix(security): corregir vulnerabilidad en auth"
# Merge a main y develop
```

### �📁 **Configuración Inicial del Repositorio**

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

### 🪝 **Git Hooks para Automatización**

#### **Pre-commit Hook - Validaciones Automáticas**

```bash
#!/bin/sh
# .git/hooks/pre-commit

echo "🔍 Ejecutando validaciones pre-commit..."

# 1. Validar formato de commit message
if [ -z "$(head -1 .git/COMMIT_EDITMSG 2>/dev/null | grep -E '^(feat|fix|docs|style|refactor|test|chore|perf|ci|build)(\(.+\))?: .{1,50}')" ]; then
    echo "❌ Error: Mensaje de commit no sigue Conventional Commits"
    echo "   Formato: tipo(ámbito): descripción"
    echo "   Ejemplo: feat(auth): agregar autenticación con JWT"
    exit 1
fi

# 2. Ejecutar linting en archivos modificados
echo "🎨 Verificando formato de código..."

# Linting para JavaScript/TypeScript
if git diff --cached --name-only | grep -E '\.(js|jsx|ts|tsx)$'; then
    npm run lint:check
    if [ $? -ne 0 ]; then
        echo "❌ Error: Problemas de linting en archivos JS/TS"
        echo "   Ejecuta: npm run lint:fix"
        exit 1
    fi
fi

# Linting para PHP
if git diff --cached --name-only | grep '\.php$'; then
    ./vendor/bin/php-cs-fixer fix --dry-run --diff
    if [ $? -ne 0 ]; then
        echo "❌ Error: Problemas de formato en archivos PHP"
        echo "   Ejecuta: composer run format"
        exit 1
    fi
fi

# 3. Ejecutar tests unitarios rápidos
echo "🧪 Ejecutando tests unitarios..."
npm run test:unit --silent
if [ $? -ne 0 ]; then
    echo "❌ Error: Tests unitarios fallando"
    echo "   Revisa los tests antes de hacer commit"
    exit 1
fi

echo "✅ Todas las validaciones pasaron correctamente"
```

#### **Commit-msg Hook - Validación de Mensajes**

```bash
#!/bin/sh
# .git/hooks/commit-msg

commit_regex='^(feat|fix|docs|style|refactor|test|chore|perf|ci|build)(\(.+\))?: .{1,50}'

if ! grep -qE "$commit_regex" "$1"; then
    echo "❌ Error: Mensaje de commit inválido"
    echo ""
    echo "El formato debe ser: tipo(ámbito): descripción"
    echo ""
    echo "Tipos válidos:"
    echo "  feat     - nueva funcionalidad"
    echo "  fix      - corrección de bug"
    echo "  docs     - cambios en documentación"
    echo "  style    - cambios de formato"
    echo "  refactor - refactoring sin cambios funcionales"
    echo "  test     - agregar o modificar tests"
    echo "  chore    - tareas de mantenimiento"
    echo "  perf     - mejoras de performance"
    echo "  ci       - cambios en CI/CD"
    echo "  build    - cambios en build system"
    echo ""
    echo "Ejemplos válidos:"
    echo "  feat(auth): agregar login con OAuth"
    echo "  fix(database): corregir conexión en staging"
    echo "  docs(readme): actualizar instrucciones"
    exit 1
fi
```

#### **Pre-push Hook - Validaciones Avanzadas**

```bash
#!/bin/sh
# .git/hooks/pre-push

echo "🚀 Ejecutando validaciones pre-push..."

# 1. Verificar que no se está pusheando a main directamente
branch=$(git rev-parse --abbrev-ref HEAD)
if [ "$branch" = "main" ]; then
    echo "❌ Error: No se permite push directo a main"
    echo "   Usa Pull Requests para mergear a main"
    exit 1
fi

# 2. Ejecutar test suite completo
echo "🧪 Ejecutando test suite completo..."
npm run test:all
if [ $? -ne 0 ]; then
    echo "❌ Error: Test suite completo fallando"
    exit 1
fi

# 3. Verificar que la rama está actualizada con develop
echo "🔄 Verificando sincronización con develop..."
git fetch origin develop
if ! git merge-base --is-ancestor origin/develop HEAD; then
    echo "⚠️  Advertencia: La rama no está actualizada con develop"
    echo "   Ejecuta: git rebase origin/develop"
    echo "   ¿Continuar anyway? (y/N)"
    read -r response
    if [ "$response" != "y" ] && [ "$response" != "Y" ]; then
        exit 1
    fi
fi

echo "✅ Validaciones pre-push completadas"
```

#### **Script de Instalación de Hooks**

```bash
#!/bin/bash
# scripts/install-git-hooks.sh

echo "📦 Instalando Git Hooks..."

# Crear directorio de hooks si no existe
mkdir -p .git/hooks

# Copiar hooks
cp scripts/hooks/pre-commit .git/hooks/
cp scripts/hooks/commit-msg .git/hooks/
cp scripts/hooks/pre-push .git/hooks/

# Hacer ejecutables
chmod +x .git/hooks/pre-commit
chmod +x .git/hooks/commit-msg
chmod +x .git/hooks/pre-push

echo "✅ Git Hooks instalados correctamente"
echo ""
echo "Hooks instalados:"
echo "  - pre-commit: valida formato y ejecuta tests"
echo "  - commit-msg: valida formato de mensajes"
echo "  - pre-push: ejecuta test suite completo"
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

## 🛠️ Resolución de Conflictos Git

### ¿Qué son los Conflictos?

Los **conflictos de Git** ocurren cuando dos o más desarrolladores modifican las
mismas líneas de código en diferentes ramas, y Git no puede resolver
automáticamente qué cambios mantener durante un merge o rebase.

### Tipos de Conflictos Comunes

#### **1. Conflictos de Merge**

```bash
# Situación típica
git checkout develop
git pull origin develop
git checkout feature/nueva-funcionalidad
git merge develop

# Git muestra:
Auto-merging src/utils/helpers.js
CONFLICT (content): Merge conflict in src/utils/helpers.js
Automatic merge failed; fix conflicts and then commit the result.
```

#### **2. Conflictos de Rebase**

```bash
# Durante un rebase interactivo
git rebase -i develop

# Git muestra:
CONFLICT (content): Merge conflict in backend/app/Services/UserService.php
error: could not apply abc1234... feat(user): add validation
```

#### **3. Conflictos en Pull Requests**

GitHub/GitLab detecta conflictos antes del merge y requiere resolución manual.

### 🔧 Proceso de Resolución

#### **Paso 1: Identificar Archivos en Conflicto**

```bash
# Ver estado actual
git status

# Output típico:
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
    both modified:   src/components/UserForm.tsx
    both modified:   backend/app/Models/User.php
```

#### **Paso 2: Examinar el Conflicto**

```typescript
// Ejemplo en UserForm.tsx
<<<<<<< HEAD
const validateUser = (user: User): boolean => {
  return user.email && user.name && user.age >= 18;
};
=======
const validateUser = (user: UserData): boolean => {
  return user.email && user.name && user.phone;
};
>>>>>>> feature/user-validation
```

**Explicación de marcadores**:

- `<<<<<<< HEAD`: Cambios en la rama actual
- `=======`: Separador entre versiones
- `>>>>>>> branch-name`: Cambios de la rama que se está mergeando

#### **Paso 3: Resolver Manualmente**

```typescript
// Resolución combinando ambas versiones
const validateUser = (user: UserData): boolean => {
   return user.email && user.name && user.phone && user.age >= 18;
};
```

#### **Paso 4: Marcar como Resuelto**

```bash
# Agregar archivo resuelto
git add src/components/UserForm.tsx

# Verificar estado
git status
# Output: All conflicts fixed but you are still merging.
```

#### **Paso 5: Completar el Merge**

```bash
# Commit de resolución
git commit -m "resolve: merge conflict in UserForm validation"

# O si es durante rebase
git rebase --continue
```

### 🛠️ Herramientas para Resolución

#### **1. VS Code (Recomendado)**

VS Code detecta automáticamente conflictos y ofrece opciones:

- **Accept Current Change**: Mantener versión actual
- **Accept Incoming Change**: Usar versión entrante
- **Accept Both Changes**: Combinar ambas
- **Compare Changes**: Ver diff lado a lado

#### **2. Git Mergetool**

```bash
# Configurar herramienta por defecto
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Usar mergetool
git mergetool
```

#### **3. GitKraken / SourceTree**

Herramientas gráficas que simplifican la resolución visual de conflictos.

### 🚨 Prevención de Conflictos

#### **1. Comunicación en Equipo**

```bash
# Antes de empezar feature
git checkout develop
git pull origin develop
git checkout -b feature/nueva-funcionalidad

# Comunicar en Slack/Teams:
# "Trabajando en UserService.php - evitar cambios por 2 horas"
```

#### **2. Merge Frecuente**

```bash
# Diariamente, mergear develop a feature
git checkout feature/mi-rama
git merge develop

# O rebase para mantener historia limpia
git rebase develop
```

#### **3. Archivos Pequeños y Focused**

- Evitar archivos gigantes que tocan múltiples desarrolladores
- Usar dependency injection para reducir acoplamiento
- Separar concerns en archivos distintos

### 📋 Comandos de Emergencia

#### **Abortar Merge/Rebase**

```bash
# Si el conflicto es muy complejo, abortar
git merge --abort
git rebase --abort

# Vuelta al estado anterior
```

#### **Reset a Estado Anterior**

```bash
# Ver historial
git reflog

# Resetear a commit específico
git reset --hard HEAD~1
```

#### **Stash Temporal**

```bash
# Guardar trabajo en progreso
git stash

# Aplicar después de resolver
git stash pop
```

---

## 📖 Glosario de Comandos Git Comunes

### 🚀 Comandos Básicos

| Comando                   | Descripción                             | Ejemplo                                      |
| ------------------------- | --------------------------------------- | -------------------------------------------- |
| `git init`                | Inicializar repositorio local           | `git init mi-proyecto`                       |
| `git clone <url>`         | Clonar repositorio remoto               | `git clone https://github.com/user/repo.git` |
| `git status`              | Ver estado actual del working directory | `git status`                                 |
| `git add <archivos>`      | Agregar archivos al staging area        | `git add src/ package.json`                  |
| `git commit -m "mensaje"` | Crear commit con mensaje                | `git commit -m "feat: add user validation"`  |
| `git log`                 | Ver historial de commits                | `git log --oneline --graph`                  |
| `git diff`                | Ver diferencias no commiteadas          | `git diff HEAD~1..HEAD`                      |

### 🌿 Manejo de Ramas

| Comando                           | Descripción                   | Ejemplo                                |
| --------------------------------- | ----------------------------- | -------------------------------------- |
| `git branch`                      | Listar ramas locales          | `git branch -a` (incluye remotas)      |
| `git checkout <rama>`             | Cambiar a rama existente      | `git checkout develop`                 |
| `git checkout -b <rama>`          | Crear y cambiar a nueva rama  | `git checkout -b feature/user-auth`    |
| `git merge <rama>`                | Mergear rama a la actual      | `git merge feature/user-auth`          |
| `git rebase <rama>`               | Rebase rama actual sobre otra | `git rebase develop`                   |
| `git branch -d <rama>`            | Eliminar rama local           | `git branch -d feature/completed`      |
| `git push origin --delete <rama>` | Eliminar rama remota          | `git push origin --delete feature/old` |

### 🌐 Comandos de Remoto

| Comando                         | Descripción              | Ejemplo                                                        |
| ------------------------------- | ------------------------ | -------------------------------------------------------------- |
| `git remote -v`                 | Ver remotos configurados | `git remote -v`                                                |
| `git remote add <nombre> <url>` | Agregar remoto           | `git remote add upstream https://github.com/original/repo.git` |
| `git fetch`                     | Traer cambios sin merge  | `git fetch origin`                                             |
| `git pull`                      | Traer y mergear cambios  | `git pull origin develop`                                      |
| `git push`                      | Subir cambios al remoto  | `git push origin feature/my-branch`                            |
| `git push -u origin <rama>`     | Subir y trackear rama    | `git push -u origin feature/new-feature`                       |

### 🔧 Comandos de Configuración

| Comando                          | Descripción              | Ejemplo                                           |
| -------------------------------- | ------------------------ | ------------------------------------------------- |
| `git config --global user.name`  | Configurar nombre        | `git config --global user.name "Juan Pérez"`      |
| `git config --global user.email` | Configurar email         | `git config --global user.email "juan@email.com"` |
| `git config --list`              | Ver configuración actual | `git config --list --global`                      |
| `git config core.editor`         | Configurar editor        | `git config --global core.editor "code --wait"`   |

### 📚 Comandos de Historial y Navegación

| Comando               | Descripción                   | Ejemplo                            |
| --------------------- | ----------------------------- | ---------------------------------- |
| `git log --oneline`   | Historial compacto            | `git log --oneline -10`            |
| `git log --graph`     | Historial con gráfico         | `git log --graph --all --decorate` |
| `git show <commit>`   | Ver detalles de commit        | `git show abc1234`                 |
| `git reflog`          | Ver historial de referencias  | `git reflog`                       |
| `git blame <archivo>` | Ver quién modificó cada línea | `git blame src/utils/helpers.js`   |

### 🔄 Comandos de Modificación de Historial

| Comando                   | Descripción                               | Ejemplo                                 |
| ------------------------- | ----------------------------------------- | --------------------------------------- |
| `git reset --soft HEAD~1` | Deshacer último commit (mantener cambios) | `git reset --soft HEAD~1`               |
| `git reset --hard HEAD~1` | Deshacer último commit (eliminar cambios) | `git reset --hard HEAD~1`               |
| `git revert <commit>`     | Crear commit que revierte cambios         | `git revert abc1234`                    |
| `git commit --amend`      | Modificar último commit                   | `git commit --amend -m "nuevo mensaje"` |
| `git rebase -i HEAD~3`    | Rebase interactivo últimos 3 commits      | `git rebase -i HEAD~3`                  |

### 💾 Comandos de Stash

| Comando                     | Descripción                     | Ejemplo                                      |
| --------------------------- | ------------------------------- | -------------------------------------------- |
| `git stash`                 | Guardar trabajo temporal        | `git stash`                                  |
| `git stash save "mensaje"`  | Guardar con mensaje             | `git stash save "WIP: working on user form"` |
| `git stash list`            | Ver lista de stashes            | `git stash list`                             |
| `git stash pop`             | Aplicar y eliminar último stash | `git stash pop`                              |
| `git stash apply stash@{1}` | Aplicar stash específico        | `git stash apply stash@{1}`                  |
| `git stash drop stash@{1}`  | Eliminar stash específico       | `git stash drop stash@{1}`                   |

### 🏷️ Comandos de Tags

| Comando                                 | Descripción          | Ejemplo                                 |
| --------------------------------------- | -------------------- | --------------------------------------- |
| `git tag`                               | Listar tags          | `git tag -l`                            |
| `git tag v1.0.0`                        | Crear tag ligero     | `git tag v1.0.0`                        |
| `git tag -a v1.0.0 -m "Release v1.0.0"` | Crear tag anotado    | `git tag -a v1.0.0 -m "Release v1.0.0"` |
| `git push origin v1.0.0`                | Subir tag específico | `git push origin v1.0.0`                |
| `git push origin --tags`                | Subir todos los tags | `git push origin --tags`                |

### 🧹 Comandos de Limpieza

| Comando         | Descripción                       | Ejemplo               |
| --------------- | --------------------------------- | --------------------- |
| `git clean -n`  | Ver qué archivos se eliminarían   | `git clean -n`        |
| `git clean -f`  | Eliminar archivos no trackeados   | `git clean -f`        |
| `git clean -fd` | Eliminar archivos y directorios   | `git clean -fd`       |
| `git gc`        | Garbage collection                | `git gc --aggressive` |
| `git prune`     | Eliminar objetos no referenciados | `git prune`           |

### 🔍 Comandos de Búsqueda

| Comando                     | Descripción                                   | Ejemplo                              |
| --------------------------- | --------------------------------------------- | ------------------------------------ |
| `git grep "texto"`          | Buscar texto en repositorio                   | `git grep "validateUser"`            |
| `git log --grep="texto"`    | Buscar en mensajes de commit                  | `git log --grep="fix"`               |
| `git log -S "texto"`        | Buscar cambios que agregaron/eliminaron texto | `git log -S "function validateUser"` |
| `git log --author="nombre"` | Filtrar commits por autor                     | `git log --author="Juan Pérez"`      |

### ⚡ Aliases Útiles

```bash
# Configurar aliases útiles
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
git config --global alias.lg 'log --oneline --graph --all --decorate'
```

### 🆘 Comandos de Emergencia

| Situación                      | Comando                                                      | Descripción                       |
| ------------------------------ | ------------------------------------------------------------ | --------------------------------- |
| **Commit mal mensaje**         | `git commit --amend -m "nuevo mensaje"`                      | Cambiar mensaje del último commit |
| **Agregué archivo equivocado** | `git reset HEAD archivo.txt`                                 | Quitar archivo del staging        |
| **Quiero deshacer cambios**    | `git checkout -- archivo.txt`                                | Descartar cambios no commiteados  |
| **Merge fue un error**         | `git reset --hard HEAD~1`                                    | Deshacer último merge             |
| **Perdí commits**              | `git reflog` → `git reset --hard abc1234`                    | Recuperar commits "perdidos"      |
| **Branch incorrecto**          | `git stash` → `git checkout rama-correcta` → `git stash pop` | Mover trabajo a otra rama         |

---

## Navegación

[⬅️ Instalación de dependencias iniciales](./instalacion-dependencias-iniciales.md)
| [🏠 README Principal](../../README.md) |
[Setup de entornos de desarrollo local con Docker ➡️](./setup-entorno-local-docker.md)
