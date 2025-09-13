# Herramientas por Lenguaje (Estándares NO NEGOCIABLES)

## ¿Qué es?

Las **herramientas por lenguaje** son conjuntos específicos y **obligatorios**
de utilidades, librerías, frameworks y extensiones establecidas como estándar
para cada lenguaje de programación del stack tecnológico. Esta documentación
define los **estándares únicos** y configuraciones específicas que deben
seguirse sin excepciones para maximizar la productividad y mantener consistencia
en todos los proyectos.

Este documento establece las **especialidades técnicas NO NEGOCIABLES** de cada
lenguaje, incluyendo gestores de dependencias únicos, herramientas de desarrollo
obligatorias, debugging, profiling y ecosistemas específicos que son los únicos
permitidos en el desarrollo de cada tecnología.

## ¿Por qué es importante?

### 🎯 **Especialización Técnica**

- Cada lenguaje tiene herramientas específicas que optimizan el desarrollo
- Aprovecha las fortalezas y características únicas de cada tecnología
- Mejora la eficiencia mediante herramientas nativas del ecosistema

### 🔧 **Configuración Optimizada**

- Configuraciones específicas para cada entorno de desarrollo
- Integración nativa con IDEs y editores
- Aprovechamiento de características específicas del lenguaje

### 📚 **Ecosistema Completo**

- Acceso a librerías y frameworks especializados
- Comunidad y documentación específica del lenguaje
- Patrones y mejores prácticas establecidas

### 🚀 **Productividad Maximizada**

- Herramientas que entienden la sintaxis y semántica del lenguaje
- Autocomplete, refactoring y debugging especializados
- Integración con sistemas de tipos y análisis estático

## ¿Qué debe incluir?

### Herramientas de Desarrollo

- IDEs y editores especializados
- Extensiones y plugins obligatorios
- Configuraciones de desarrollo optimizadas
- Herramientas de debugging específicas

### Gestión de Dependencias

- Gestores de paquetes nativos
- Configuración de repositorios
- Estrategias de versionado
- Herramientas de auditoría de seguridad

### Testing y Calidad

- Frameworks de testing específicos
- Herramientas de coverage
- Linters y formateadores nativos
- Analizadores estáticos especializados

### Build y Deployment

- Herramientas de compilación/transpilación
- Optimizadores específicos
- Empaquetadores y bundlers
- Herramientas de profiling

## ¿Qué debo hacer?

### 1. **Analizar el Stack Tecnológico**

```bash
# Revisar tecnologías del proyecto
echo "📊 Stack Tecnológico:"
echo "- Frontend: React + TypeScript"
echo "- Backend: PHP + Laravel"
echo "- Base de datos: MySQL"
echo "- Móvil: React Native + TypeScript"
echo "- DevOps: Docker + GitHub Actions"
```

### 2. **Documentar Herramientas por Tecnología**

**Template por lenguaje:**

```markdown
## [LENGUAJE] - Herramientas Esenciales

### Desarrollo

- **IDE Principal**: [Herramienta]
- **Editor Alternativo**: [Herramienta]
- **Extensiones**: [Lista]

### Gestión de Dependencias

- **Gestor Principal**: [Herramienta]
- **Registry**: [URL/Configuración]
- **Auditoría**: [Herramientas de seguridad]

### Testing

- **Framework Principal**: [Herramienta]
- **Mocking**: [Herramienta]
- **Coverage**: [Herramienta]

### Calidad de Código

- **Linter**: [Herramienta]
- **Formatter**: [Herramienta]
- **Type Checker**: [Herramienta]
```

### 3. **Implementar Configuraciones Base**

Crear archivos de configuración estándar para cada herramienta.

## Herramientas por Tecnología

## TypeScript/JavaScript (Frontend & Móvil)

### 🎯 **Desarrollo**

- **IDE Principal**: Visual Studio Code
- **Editor Alternativo**: WebStorm
- **Extensiones VS Code**:
  ```json
  {
     "recommendations": [
        "bradlc.vscode-tailwindcss",
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint",
        "ms-vscode.vscode-typescript-next",
        "formulahendry.auto-rename-tag",
        "christian-kohler.path-intellisense",
        "ms-vscode.vscode-json"
     ]
  }
  ```

### 📦 **Gestión de Dependencias**

- **Gestor Principal**: npm (único permitido)
- **Registry**: npmjs.com
- **Auditoría**: `npm audit` (único comando estándar)
- **Configuración**:
  ```json
  {
     "engines": {
        "node": ">=18.0.0",
        "npm": ">=8.0.0"
     },
     "packageManager": "npm@9.0.0"
  }
  ```

### 🧪 **Testing (Estándar NO NEGOCIABLE)**

- **Framework Principal**: Vitest (único permitido con Vite)
- **Testing Library**: React Testing Library (obligatorio)
- **E2E**: Playwright (único framework permitido)
- **Coverage**: Vitest Coverage (integrado)
- **Configuración Vitest** (en vite.config.ts):
  ```typescript
  // vite.config.ts
  import { defineConfig } from "vite";
  import react from "@vitejs/plugin-react";
  import { resolve } from "path";

  export default defineConfig({
     plugins: [react()],
     test: {
        globals: true,
        environment: "jsdom",
        setupFiles: ["./src/test/setup.ts"],
        coverage: {
           provider: "v8",
           reporter: ["text", "json", "html"],
           exclude: ["node_modules/", "src/test/"],
           thresholds: {
              global: {
                 branches: 80,
                 functions: 80,
                 lines: 80,
                 statements: 80,
              },
           },
        },
     },
  });
  ```

### 📏 **Calidad de Código (Estándares Únicos)**

- **Linter**: ESLint con TypeScript (único permitido)
- **Formatter**: Prettier (obligatorio)
- **Type Checker**: TypeScript Compiler (tsc)
- **Configuración ESLint** (estándar NO NEGOCIABLE):
  ```json
  {
     "extends": [
        "@typescript-eslint/recommended",
        "react-app",
        "react-app/jest",
        "prettier"
     ],
     "rules": {
        "@typescript-eslint/no-unused-vars": "error",
        "@typescript-eslint/explicit-function-return-type": "warn",
        "react-hooks/exhaustive-deps": "warn"
     }
  }
  ```

### ⚡ **Build y Bundling**

- **Bundler**: Vite (Frontend), Metro (React Native)
- **Transpiler**: TypeScript + SWC
- **Optimización**: Tree-shaking automático
- **Configuración Vite**:
  ```typescript
  import { defineConfig } from "vite";
  import react from "@vitejs/plugin-react-swc";

  export default defineConfig({
     plugins: [react()],
     build: {
        target: "es2020",
        sourcemap: true,
        rollupOptions: {
           output: {
              manualChunks: {
                 vendor: ["react", "react-dom"],
                 router: ["react-router-dom"],
              },
           },
        },
     },
  });
  ```

## PHP (Backend)

### 🎯 **Desarrollo**

- **IDE Principal**: PhpStorm
- **Editor Alternativo**: Visual Studio Code
- **Extensiones VS Code**:
  ```json
  {
     "recommendations": [
        "bmewburn.vscode-intelephense-client",
        "ryannaddy.laravel-artisan",
        "onecentlin.laravel-blade",
        "neilbrayfield.php-docblocker",
        "xdebug.php-debug"
     ]
  }
  ```

### 📦 **Gestión de Dependencias**

- **Gestor Principal**: Composer
- **Registry**: Packagist.org
- **Auditoría**: `composer audit`
- **Configuración**:
  ```json
  {
     "require": {
        "php": "^8.2"
     },
     "autoload": {
        "psr-4": {
           "App\\": "app/",
           "Database\\Factories\\": "database/factories/",
           "Database\\Seeders\\": "database/seeders/"
        }
     },
     "config": {
        "optimize-autoloader": true,
        "preferred-install": "dist",
        "sort-packages": true
     }
  }
  ```

### 🧪 **Testing**

- **Framework Principal**: PHPUnit
- **Mocking**: Mockery
- **Coverage**: PHPUnit Coverage
- **Configuración PHPUnit**:
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
           bootstrap="vendor/autoload.php"
           colors="true">
      <testsuites>
          <testsuite name="Unit">
              <directory suffix="Test.php">./tests/Unit</directory>
          </testsuite>
          <testsuite name="Feature">
              <directory suffix="Test.php">./tests/Feature</directory>
          </testsuite>
      </testsuites>
      <coverage>
          <include>
              <directory suffix=".php">./app</directory>
          </include>
      </coverage>
  </phpunit>
  ```

### 📏 **Calidad de Código**

- **Linter**: PHP_CodeSniffer (phpcs)
- **Formatter**: PHP CS Fixer
- **Static Analysis**: PHPStan / Psalm
- **Configuración PHP CS Fixer**:
  ```php
  <?php
  return (new PhpCsFixer\Config())
      ->setRules([
          '@PSR12' => true,
          '@PHP82Migration' => true,
          'array_syntax' => ['syntax' => 'short'],
          'ordered_imports' => ['sort_algorithm' => 'alpha'],
          'no_unused_imports' => true,
          'strict_comparison' => true,
          'strict_param' => true,
      ])
      ->setFinder(
          PhpCsFixer\Finder::create()
              ->exclude('bootstrap/cache')
              ->exclude('storage')
              ->in(__DIR__)
      );
  ```

### 🔧 **Herramientas Laravel Específicas**

- **Artisan Commands**: Comandos personalizados
- **Telescope**: Debugging y profiling
- **Debugbar**: Debug en desarrollo
- **IDE Helper**: Autocompletado mejorado
- **Configuración**:
  ```bash
  # Instalación herramientas de desarrollo
  composer require --dev barryvdh/laravel-ide-helper
  composer require --dev barryvdh/laravel-debugbar
  composer require --dev laravel/telescope

  # Configurar IDE Helper
  php artisan ide-helper:generate
  php artisan ide-helper:models
  php artisan ide-helper:meta
  ```

## MySQL (Base de Datos)

### 🎯 **Desarrollo**

- **Cliente Principal**: MySQL Workbench
- **Cliente Alternativo**: DBeaver, phpMyAdmin
- **CLI**: MySQL Client, Sequel Pro (macOS)

### 📦 **Gestión y Versionado**

- **Migraciones**: Laravel Migrations
- **Seeders**: Laravel Seeders
- **Backup**: mysqldump, Laravel Backup
- **Configuración**:
  ```yaml
  # docker-compose.yml
  mysql:
     image: mysql:8.0
     environment:
        MYSQL_ROOT_PASSWORD: secret
        MYSQL_DATABASE: laravel
     volumes:
        - mysql_data:/var/lib/mysql
        - ./docker/mysql/my.cnf:/etc/mysql/conf.d/my.cnf
     ports:
        - "3306:3306"
  ```

### 🧪 **Testing**

- **Base de datos de testing**: SQLite/MySQL
- **Factory**: Laravel Model Factories
- **Seeders de testing**: Database Seeders
- **Configuración**:
  ```php
  // config/database.php
  'testing' => [
      'driver' => 'sqlite',
      'database' => ':memory:',
      'prefix' => '',
  ],
  ```

### 📏 **Optimización y Monitoreo**

- **Profiling**: MySQL Performance Schema
- **Monitoring**: Laravel Telescope, Clockwork
- **Query Analysis**: EXPLAIN, Laravel Debugbar
- **Optimización**:
  ```sql
  -- Análisis de consultas lentas
  SET GLOBAL slow_query_log = 'ON';
  SET GLOBAL long_query_time = 2;

  -- Índices obligatorios
  CREATE INDEX idx_users_email ON users(email);
  CREATE INDEX idx_posts_created_at ON posts(created_at);
  ```

## Docker & DevOps

### 🎯 **Desarrollo**

- **Orquestador**: Docker Compose
- **Registry**: Docker Hub, GitHub Container Registry
- **CLI**: Docker CLI, Docker Desktop

### 📦 **Configuración Base**

```dockerfile
# Dockerfile (Backend)
FROM php:8.2-fpm-alpine

RUN apk add --no-cache \
    git \
    curl \
    libpng-dev \
    oniguruma-dev \
    libxml2-dev \
    zip \
    unzip

RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www
COPY . .

RUN composer install --no-dev --optimize-autoloader

EXPOSE 9000
CMD ["php-fpm"]
```

```dockerfile
# Dockerfile (Frontend)
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Scripts de Automatización

### 📜 **Setup del Proyecto**

```bash
#!/bin/bash
# scripts/setup-tools.sh

echo "🔧 Configurando herramientas por lenguaje..."

# TypeScript/Node.js
echo "📦 Instalando dependencias Node.js..."
npm install -g typescript @typescript-eslint/parser prettier

# PHP
echo "🐘 Configurando herramientas PHP..."
composer global require laravel/installer
composer global require friendsofphp/php-cs-fixer
composer global require phpstan/phpstan

# Base de datos
echo "🗄️ Configurando MySQL..."
docker pull mysql:8.0

# Docker
echo "🐳 Verificando Docker..."
docker --version
docker-compose --version

echo "✅ Configuración de herramientas completada"
```

### 🔍 **Verificación de Calidad**

```bash
#!/bin/bash
# scripts/quality-check.sh

echo "🔍 Ejecutando verificaciones de calidad..."

# Frontend
echo "⚛️ Verificando Frontend..."
cd frontend
npm run lint
npm run type-check
npm run test
cd ..

# Backend
echo "🐘 Verificando Backend..."
cd backend
./vendor/bin/phpcs
./vendor/bin/phpstan analyse
./vendor/bin/phpunit
cd ..

echo "✅ Verificaciones completadas"
```

## Tips

### 💡 **Mejores Prácticas**

1. **Configuración Centralizada**
   - Mantén archivos de configuración en el repositorio
   - Usa archivos de configuración compartidos entre equipos
   - Documenta configuraciones específicas del proyecto

2. **Automatización**
   - Scripts de instalación automática de herramientas
   - Pre-commit hooks para verificaciones automáticas
   - CI/CD integrado con herramientas específicas

3. **Consistencia**
   - Mismas versiones de herramientas en todo el equipo
   - Configuraciones estandarizadas
   - Documentación actualizada de cambios

4. **Performance**
   - Perfiles de configuración por entorno
   - Optimizaciones específicas para producción
   - Monitoreo de performance por tecnología

### ⚠️ **Consideraciones**

- Mantén las herramientas actualizadas pero de forma controlada
- Prueba nuevas versiones en entornos de desarrollo primero
- Documenta incompatibilidades conocidas
- Establece políticas de actualización por tipo de herramienta

## Ejemplos

### Configuración de Workspace VS Code

```json
{
   "settings": {
      "typescript.preferences.quoteStyle": "single",
      "editor.formatOnSave": true,
      "editor.defaultFormatter": "esbenp.prettier-vscode",
      "eslint.format.enable": true,
      "php.validate.executablePath": "/usr/bin/php",
      "php.suggest.basic": false,
      "intelephense.stubs": [
         "bcmath",
         "Core",
         "date",
         "dom",
         "json",
         "mbstring",
         "mysql",
         "mysqli",
         "PDO",
         "pdo_mysql",
         "Reflection"
      ]
   },
   "extensions": {
      "recommendations": [
         "bradlc.vscode-tailwindcss",
         "esbenp.prettier-vscode",
         "dbaeumer.vscode-eslint",
         "bmewburn.vscode-intelephense-client",
         "ms-vscode.vscode-typescript-next"
      ]
   }
}
```

### Package.json para Frontend

```json
{
   "name": "frontend-app",
   "scripts": {
      "dev": "vite",
      "build": "vite build",
      "preview": "vite preview",
      "lint": "eslint src --ext .ts,.tsx",
      "lint:fix": "eslint src --ext .ts,.tsx --fix",
      "type-check": "tsc --noEmit",
      "test": "vitest",
      "test:watch": "vitest --watch",
      "test:coverage": "vitest run --coverage"
   },
   "devDependencies": {
      "@typescript-eslint/eslint-plugin": "^6.0.0",
      "@typescript-eslint/parser": "^6.0.0",
      "eslint": "^8.0.0",
      "eslint-config-prettier": "^9.0.0",
      "vitest": "^1.0.0",
      "prettier": "^3.0.0",
      "typescript": "^5.0.0",
      "vite": "^4.0.0"
   }
}
```

---

## ⚠️ Herramientas PROHIBIDAS por Lenguaje

### Frontend (React + TypeScript)

- ❌ **Jest** → Usar Vitest (único testing framework)
- ❌ **Webpack/CRA** → Usar Vite (único bundler)
- ❌ **Yarn/pnpm** → Usar npm (único gestor de paquetes)
- ❌ **Cypress** → Usar Playwright (único E2E)
- ❌ **Standard/JSHint** → Usar ESLint (único linter)

### Backend (PHP/Laravel)

- ❌ **Pest** → Usar PHPUnit (único testing framework)
- ❌ **PHPCS** → Usar PHP-CS-Fixer (único formatter)
- ❌ **Psalm** → Usar PHPStan (único análisis estático)
- ❌ **Composer v1** → Usar Composer v2+ (obligatorio)

### Base de Datos

- ❌ **PostgreSQL/SQLite** → Usar MySQL 8+ (único DBMS)
- ❌ **Migrations manuales** → Usar Laravel Migrations (obligatorio)

### Justificación

- **Consistencia**: Un solo camino para cada tecnología
- **Mantenimiento**: Configuraciones estándar únicas
- **Soporte**: Documentación y troubleshooting unificados
- **Productividad**: Menos decisiones, más desarrollo

---

## Navegación

[⬅️ Elección de Herramientas](./eleccion-herramientas.md) |
[🏠 README Principal](../../README.md) |
[Checklist Mínima por Proyecto ➡️](./checklist-minima-proyecto.md)
