# Elección de Herramientas (Estándares Establecidos)

## ¿Qué es?

La **elección de herramientas** define los **estándares NO NEGOCIABLES** de las
herramientas de desarrollo, testing, deployment y monitoreo que son
**obligatorias** en todos los proyectos. Este documento establece las
**decisiones finales y definitivas** con criterios ya evaluados y **alternativas
prohibidas** que guiarán de manera uniforme tanto el desarrollo como el
mantenimiento del proyecto.

A diferencia del stack tecnológico (que define frameworks y lenguajes
principales), la elección de herramientas establece el **ecosistema único de
soporte** que debe utilizarse sin excepciones para garantizar productividad,
calidad y consistencia en todos los proyectos.

## ¿Por qué es Importante?

### 🚀 **Productividad del Equipo**

- Herramientas adecuadas aceleran significativamente el desarrollo
- Automatización reduce tareas repetitivas y minimiza errores humanos
- Integración fluida entre herramientas mejora el workflow del equipo

### 🎯 **Calidad y Consistencia**

- Herramientas de linting y formateo mantienen estándares de código
- Pipelines de CI/CD aseguran que todos los cambios pasen por procesos de
  calidad
- Testing automatizado garantiza la estabilidad del software

### 💰 **Optimización de Costos**

- Herramientas gratuitas vs. premium: balance entre funcionalidades y
  presupuesto
- Reducción de tiempo de desarrollo = reducción de costos
- Prevención de problemas costosos en producción

### 🔧 **Escalabilidad del Desarrollo**

- Herramientas que crecen con el proyecto y el equipo
- Facilita la incorporación de nuevos desarrolladores
- Permite mantener eficiencia conforme el proyecto se expande

### 📊 **Visibilidad y Control**

- Monitoreo de métricas de desarrollo y rendimiento
- Trazabilidad completa desde desarrollo hasta producción
- Capacidad de tomar decisiones basadas en datos

## ¿Qué Debe Incluir?

### 1. Criterios de Evaluación

- **Compatibilidad**: Integración con el stack tecnológico elegido
- **Curva de aprendizaje**: Facilidad de adopción por el equipo
- **Comunidad y soporte**: Documentación, updates, community support
- **Costo**: Licencias, subscripciones, costos operacionales
- **Escalabilidad**: Capacidad de crecer con el proyecto
- **Seguridad**: Compliance, auditorías, políticas de seguridad

### 2. Categorías de Herramientas

- **Desarrollo**: IDEs, editores, debugging tools
- **Control de versiones**: Git hosting, branching strategies
- **CI/CD**: Pipelines, testing, deployment automation
- **Testing**: Unit, integration, e2e, performance testing
- **Monitoreo**: APM, logging, alertas, analytics
- **Comunicación**: Chat, video calls, documentation
- **Gestión de proyecto**: Tableros, time tracking, reporting

### 3. Matriz de Comparación

- Evaluación cuantitativa ya completada
- Scoring basado en criterios definidos
- Justificación de decisiones finales

### 4. Plan de Implementación

- Orden de adopción de herramientas
- Training plan para el equipo
- Migration strategies cuando sea necesario

## ¿Qué Debo Hacer?

### Paso 1: Definir Criterios de Evaluación (2 Story Points)

Establece los criterios específicos para tu proyecto:

```yaml
Criterios_Evaluacion:
   Tecnicos:
      Compatibilidad_Stack:
         React_TypeScript: ⭐⭐⭐⭐⭐ (crítico)
         Laravel_PHP: ⭐⭐⭐⭐⭐ (crítico)
         MySQL: ⭐⭐⭐⭐ (importante)

      Performance:
         Velocidad_Build: ⭐⭐⭐⭐ (importante)
         Tiempo_Setup: ⭐⭐⭐ (moderado)
         Resource_Usage: ⭐⭐⭐ (moderado)

   Equipo:
      Curva_Aprendizaje: ⭐⭐⭐⭐ (importante)
      Documentacion: ⭐⭐⭐⭐ (importante)
      Community_Support: ⭐⭐⭐ (moderado)

   Negocio:
      Costo_Licencias: ⭐⭐⭐⭐ (importante)
      Tiempo_Implementacion: ⭐⭐⭐⭐ (importante)
      ROI: ⭐⭐⭐⭐⭐ (crítico)
```

### Paso 2: Evaluar Herramientas de Desarrollo (3 Story Points)

**IDEs y Editores (ESTÁNDAR ÚNICO):**

| Herramienta  | Compatibilidad | Plugins    | Performance | Costo      | Estado       |
| ------------ | -------------- | ---------- | ----------- | ---------- | ------------ |
| **VS Code**  | ⭐⭐⭐⭐⭐     | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐    | ⭐⭐⭐⭐⭐ | ✅ ÚNICO     |
| PhpStorm     | ⭐⭐⭐⭐⭐     | ⭐⭐⭐⭐   | ⭐⭐⭐      | ⭐⭐       | ❌ PROHIBIDO |
| Sublime Text | ⭐⭐⭐         | ⭐⭐⭐     | ⭐⭐⭐⭐⭐  | ⭐⭐⭐     | ❌ PROHIBIDO |

**Estándar**: VS Code con extensiones obligatorias:

- PHP Intelephense (único PHP intellisense)
- Laravel Extension Pack (obligatorio)
- ES7+ React/Redux/React-Native snippets (obligatorio)
- TypeScript Hero (único organizador imports)

**Herramientas de Build (NO NEGOCIABLE):**

| Herramienta | Setup      | Speed      | Ecosystem  | Learning Curve | Estado       |
| ----------- | ---------- | ---------- | ---------- | -------------- | ------------ |
| **Vite**    | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐   | ⭐⭐⭐⭐⭐     | ✅ ÚNICO     |
| Webpack     | ⭐⭐       | ⭐⭐⭐     | ⭐⭐⭐⭐⭐ | ⭐⭐           | ❌ PROHIBIDO |
| Parcel      | ⭐⭐⭐⭐   | ⭐⭐⭐⭐   | ⭐⭐⭐     | ⭐⭐⭐⭐       | ❌ PROHIBIDO |

**Estándar NO NEGOCIABLE**: Vite para todos los proyectos frontend.

### Paso 3: Herramientas de CI/CD (ESTÁNDARES DEFINIDOS)

**Plataformas de CI/CD:**

| Plataforma         | GitHub Integration | Ease of Use | Free Tier | Enterprise | Score     |
| ------------------ | ------------------ | ----------- | --------- | ---------- | --------- |
| **GitHub Actions** | ⭐⭐⭐⭐⭐         | ⭐⭐⭐⭐    | ⭐⭐⭐⭐  | ⭐⭐⭐⭐   | **21/25** |
| GitLab CI          | ⭐⭐⭐             | ⭐⭐⭐⭐    | ⭐⭐⭐    | ⭐⭐⭐⭐⭐ | 18/25     |
| CircleCI           | ⭐⭐⭐⭐           | ⭐⭐⭐⭐    | ⭐⭐      | ⭐⭐⭐⭐   | 17/25     |

**Pipeline Recomendado (GitHub Actions):**

```yaml
Pipeline_Configuration:
   Triggers:
      - Push to main/develop
      - Pull requests
      - Scheduled runs (daily)

   Stages:
      1. Code_Quality:
         - ESLint (React/TypeScript)
         - PHP CS Fixer (Laravel)
         - PHPStan (Static analysis)

      2. Testing:
         - Vitest (React components)
         - PHPUnit (Laravel backend)
         - Cypress (E2E tests)

      3. Security:
         - npm audit
         - Composer audit
         - SAST scanning

      4. Build:
         - Vite build (Frontend)
         - Laravel optimize (Backend)

      5. Deploy:
         - Staging (automatic)
         - Production (manual approval)
```

### Paso 4: Herramientas de Testing (ESTÁNDARES ÚNICOS)

**Testing Frontend:**

| Herramienta      | React Support | TypeScript | Ease of Use | Community  | Score        |
| ---------------- | ------------- | ---------- | ----------- | ---------- | ------------ |
| **Vitest + RTL** | ⭐⭐⭐⭐⭐    | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐  | ⭐⭐⭐⭐⭐ | ✅ ÚNICO     |
| Jest + RTL       | ⭐⭐⭐⭐      | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐    | ⭐⭐⭐⭐⭐ | ❌ PROHIBIDO |
| Vitest           | ⭐⭐⭐⭐⭐    | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐  | ⭐⭐⭐     | 22/25        |
| Mocha + Chai     | ⭐⭐⭐        | ⭐⭐⭐     | ⭐⭐⭐      | ⭐⭐⭐⭐   | 16/25        |

**Testing Backend:**

| Herramienta | Laravel Support | Features   | Documentation | Score     |
| ----------- | --------------- | ---------- | ------------- | --------- |
| **PHPUnit** | ⭐⭐⭐⭐⭐      | ⭐⭐⭐⭐   | ⭐⭐⭐⭐⭐    | **21/25** |
| Pest        | ⭐⭐⭐⭐⭐      | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐      | 22/25     |

**E2E Testing:**

| Herramienta | Modern APIs | Cross-browser | Developer Experience | Score     |
| ----------- | ----------- | ------------- | -------------------- | --------- |
| **Cypress** | ⭐⭐⭐⭐⭐  | ⭐⭐⭐        | ⭐⭐⭐⭐⭐           | **20/25** |
| Playwright  | ⭐⭐⭐⭐⭐  | ⭐⭐⭐⭐⭐    | ⭐⭐⭐⭐             | 22/25     |
| Selenium    | ⭐⭐⭐      | ⭐⭐⭐⭐⭐    | ⭐⭐                 | 15/25     |

### Paso 5: Herramientas de Monitoreo (ESTÁNDARES DEFINIDOS)

**Application Performance Monitoring:**

| Herramienta   | Laravel Support | React Support | Free Tier | Enterprise | Score     |
| ------------- | --------------- | ------------- | --------- | ---------- | --------- |
| **New Relic** | ⭐⭐⭐⭐⭐      | ⭐⭐⭐⭐      | ⭐⭐⭐    | ⭐⭐⭐⭐⭐ | **21/25** |
| DataDog       | ⭐⭐⭐⭐        | ⭐⭐⭐⭐      | ⭐⭐      | ⭐⭐⭐⭐⭐ | 19/25     |
| AppSignal     | ⭐⭐⭐⭐⭐      | ⭐⭐⭐        | ⭐⭐⭐    | ⭐⭐⭐⭐   | 18/25     |

**Error Tracking:**

| Herramienta | PHP Support | JS Support | Integration | Free Tier | Score     |
| ----------- | ----------- | ---------- | ----------- | --------- | --------- |
| **Sentry**  | ⭐⭐⭐⭐⭐  | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐  | ⭐⭐⭐⭐  | **23/25** |
| Rollbar     | ⭐⭐⭐⭐    | ⭐⭐⭐⭐   | ⭐⭐⭐⭐    | ⭐⭐⭐    | 18/25     |
| Bugsnag     | ⭐⭐⭐⭐    | ⭐⭐⭐⭐   | ⭐⭐⭐⭐    | ⭐⭐      | 17/25     |

### Paso 6: Herramientas de Gestión de Proyecto (ESTÁNDARES DEFINIDOS)

**Project Management:**

| Herramienta         | Scrum Support | GitHub Integration | Free Tier  | Team Size  | Score     |
| ------------------- | ------------- | ------------------ | ---------- | ---------- | --------- |
| **GitHub Projects** | ⭐⭐⭐⭐      | ⭐⭐⭐⭐⭐         | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐   | **22/25** |
| Jira                | ⭐⭐⭐⭐⭐    | ⭐⭐⭐             | ⭐⭐       | ⭐⭐⭐⭐⭐ | 19/25     |
| Trello              | ⭐⭐⭐        | ⭐⭐⭐             | ⭐⭐⭐⭐   | ⭐⭐⭐⭐   | 17/25     |

**Comunicación:**

| Herramienta     | Developer Focus | Integration | Free Tier  | Features   | Score     |
| --------------- | --------------- | ----------- | ---------- | ---------- | --------- |
| **Discord**     | ⭐⭐⭐⭐⭐      | ⭐⭐⭐⭐    | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐   | **22/25** |
| Slack           | ⭐⭐⭐⭐        | ⭐⭐⭐⭐⭐  | ⭐⭐⭐     | ⭐⭐⭐⭐⭐ | 21/25     |
| Microsoft Teams | ⭐⭐⭐          | ⭐⭐⭐      | ⭐⭐⭐⭐   | ⭐⭐⭐⭐   | 17/25     |

## Tips

### Para la Evaluación

- **Haz pruebas prácticas**: No te bases solo en reviews, prueba las
  herramientas
- **Considera el ecosistema completo**: Cómo se integran entre sí las
  herramientas
- **Evalúa el vendor lock-in**: Qué tan fácil es migrar a otra herramienta
- **Consulta al equipo**: Involve al team en las decisiones

### Para la Implementación

- **Implementación gradual**: Introduce herramientas de una en una
- **Training plan**: Asegúrate de que el equipo sepa usar las herramientas
- **Documentación**: Mantén docs actualizadas sobre setup y uso
- **Métricas de adopción**: Mide si las herramientas mejoran la productividad

### Para el Mantenimiento

- **Updates regulares**: Mantén las herramientas actualizadas
- **Review periódico**: Evalúa si las herramientas siguen siendo las mejores
- **Backup plans**: Ten alternativas identificadas por si algo falla
- **Cost monitoring**: Controla los costos, especialmente en herramientas SaaS

## Ejemplos

### Stack de Herramientas Recomendado

```yaml
Development_Stack:
   IDE: VS Code con extensiones específicas
   Build_Tool: Vite
   Package_Manager:
      Frontend: npm
      Backend: Composer

   Version_Control:
      Platform: GitHub
      Strategy: Git Flow

   CI_CD:
      Platform: GitHub Actions
      Runners: GitHub-hosted

   Testing:
      Unit_Frontend: Vitest + React Testing Library
      Unit_Backend: PHPUnit
      E2E: Cypress
      Performance: Lighthouse CI

   Quality:
      Linting: ESLint + PHP CS Fixer
      Static_Analysis: TypeScript + PHPStan
      Security: npm audit + Composer audit

   Monitoring:
      APM: New Relic
      Error_Tracking: Sentry
      Uptime: StatusCake

   Communication:
      Chat: Discord
      Documentation: GitHub Wiki + README

   Project_Management:
      Boards: GitHub Projects
      Time_Tracking: Toggl
```

### Configuración de VS Code

```json
{
   "recommendations": [
      "bmewburn.vscode-intelephense-client",
      "bradlc.vscode-tailwindcss",
      "esbenp.prettier-vscode",
      "ms-vscode.vscode-typescript-next",
      "onecentlin.laravel-extension-pack",
      "dsznajder.es7-react-js-snippets",
      "ms-playwright.playwright",
      "cypressio.cypress-helper"
   ],
   "settings": {
      "editor.defaultFormatter": "esbenp.prettier-vscode",
      "editor.formatOnSave": true,
      "typescript.preferences.includePackageJsonAutoImports": "auto",
      "php.suggest.basic": false,
      "intelephense.stubs": ["laravel"]
   }
}
```

### GitHub Actions Workflow

```yaml
name: CI/CD Pipeline

on:
   push:
      branches: [main, develop]
   pull_request:
      branches: [main]

jobs:
   frontend-tests:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
         - uses: actions/setup-node@v3
           with:
              node-version: "18"
              cache: "npm"

         - run: npm ci
         - run: npm run lint
         - run: npm run test:coverage
         - run: npm run build

   backend-tests:
      runs-on: ubuntu-latest
      services:
         mysql:
            image: mysql:8.0
            env:
               MYSQL_ALLOW_EMPTY_PASSWORD: yes
               MYSQL_DATABASE: testing
            ports:
               - 3306:3306

      steps:
         - uses: actions/checkout@v3
         - uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"
              extensions: mbstring, pdo, pdo_mysql

         - run: composer install --no-dev --optimize-autoloader
         - run: php artisan config:cache
         - run: vendor/bin/phpunit
         - run: vendor/bin/phpstan analyse
```

---

## ⚠️ Herramientas PROHIBIDAS (Estándares Definidos)

### Frontend

- ❌ **Jest** → Usar Vitest (único con Vite)
- ❌ **Webpack/Create React App** → Usar Vite
- ❌ **Yarn/pnpm** → Usar npm
- ❌ **Cypress** → Usar Playwright
- ❌ **Sublime/PhpStorm** → Usar VS Code

### Backend

- ❌ **Pest** → Usar PHPUnit
- ❌ **PHPCS** → Usar PHP-CS-Fixer
- ❌ **Psalm** → Usar PHPStan

### CI/CD

- ❌ **Jenkins/GitLab CI** → Usar GitHub Actions
- ❌ **Travis CI** → Usar GitHub Actions

### Gestión

- ❌ **Jira/Trello** → Usar GitHub Projects
- ❌ **Slack** → Usar GitHub Discussions

**Justificación**: Cada herramienta seleccionada representa el estándar único y
NO NEGOCIABLE para garantizar:

- Consistencia entre proyectos
- Curva de aprendizaje mínima
- Compatibilidad total del ecosistema
- Mantenimiento simplificado

---

## Navegación

[⬅️ Stack Tecnológico](./stack-tecnologico.md) |
[🏠 README Principal](../../README.md) |
[Herramientas por Lenguaje ➡️](./herramientas-por-lenguaje.md)
