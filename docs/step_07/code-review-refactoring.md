# Code Review y Refactoring

## ¿Qué es?

Code Review y Refactoring son procesos fundamentales para mantener la calidad
del código a lo largo del tiempo. El Code Review es la revisión sistemática del
código por parte de otros desarrolladores antes de integrarlo al repositorio
principal, mientras que el Refactoring es la mejora de la estructura interna del
código sin cambiar su comportamiento externo.

## ¿Por qué es importante?

- **Calidad del código**: Identifica problemas antes de que lleguen a producción
- **Transferencia de conocimiento**: Facilita el intercambio de conocimientos
  entre el equipo
- **Consistencia**: Mantiene estándares de codificación uniformes
- **Detección temprana de bugs**: Encuentra errores que pueden pasar
  desapercibidos
- **Mantenibilidad**: Mejora la legibilidad y estructura del código
- **Seguridad**: Identifica vulnerabilidades potenciales
- **Performance**: Optimiza el rendimiento y uso de recursos
- **Evolución del código**: Adapta el código a nuevos requerimientos sin
  degradar calidad

## ¿Qué debe incluir?

### 🔄 **Pull Request Workflow Completo**

#### **1. Creación de Pull Request**

##### **Template de Pull Request Estándar**

```markdown
<!-- .github/pull_request_template.md -->

# 🚀 Pull Request

## 📝 Descripción

<!-- Describe los cambios realizados de manera clara y concisa -->

## 🔧 Tipo de cambio

- [ ] 🐛 Bug fix (corrección que resuelve un problema)
- [ ] ✨ Nueva funcionalidad (cambio que añade funcionalidad)
- [ ] 💥 Breaking change (cambio que puede afectar funcionalidad existente)
- [ ] 📚 Documentación (solo cambios en documentación)
- [ ] 🎨 Refactoring (mejoras de código sin cambios funcionales)
- [ ] ⚡ Performance (mejoras de rendimiento)
- [ ] 🧪 Tests (agregar o modificar tests)

## 🏗️ Arquitectura afectada

- [ ] 🏛️ Domain Layer (entidades, value objects, repositorios)
- [ ] 🔧 Application Layer (casos de uso, DTOs, comandos)
- [ ] 🚀 Infrastructure Layer (controllers, models, servicios externos)
- [ ] 🎨 Frontend (componentes, hooks, estado)
- [ ] 📱 Mobile (pantallas, navegación, API)

## ✅ Checklist de completitud

### Desarrollo
- [ ] El código compila sin errores
- [ ] Se siguen los estándares de codificación del proyecto
- [ ] No hay código comentado innecesario
- [ ] Las funciones son pequeñas y tienen una sola responsabilidad
- [ ] Los nombres de variables y funciones son descriptivos

### Testing
- [ ] Se agregaron tests unitarios para el nuevo código
- [ ] Se actualizaron tests existentes si fue necesario
- [ ] Todos los tests pasan localmente
- [ ] La cobertura de código se mantiene o mejora

### Documentación
- [ ] Se actualizó la documentación relevante
- [ ] Se agregaron comentarios JSDoc/PHPDoc donde es necesario
- [ ] Se actualizó el README si hay cambios en la configuración
- [ ] Se documentaron los endpoints de API si aplica

### Seguridad y Performance
- [ ] Se validaron todas las entradas de usuario
- [ ] No se exponen datos sensibles
- [ ] Las consultas a BD son eficientes
- [ ] No hay memory leaks evidentes

## 🧪 Instrucciones de Testing

<!-- Describe cómo probar los cambios -->

### Setup requerido

```bash
# Comandos necesarios para configurar el entorno de testing
npm install
composer install
php artisan migrate --env=testing
```

### Casos de prueba

1. **Caso principal**: 
   - Ir a [URL/funcionalidad]
   - Hacer [acción]
   - Verificar que [resultado esperado]

2. **Casos edge**:
   - Probar con [datos límite]
   - Verificar [comportamiento esperado]

## 📸 Screenshots (si aplica)

<!-- Incluir capturas de pantalla para cambios de UI -->

## 🔗 Issues relacionados

Closes #[número_de_issue]
Related to #[número_de_issue]

## 📋 Notas adicionales

<!-- Cualquier información adicional que sea relevante para el review -->
```

##### **Template para Pull Request de Release**

```markdown
<!-- .github/pull_request_template/release.md -->

# 🎉 Release v[X.Y.Z]

## 📦 Contenido del Release

### ✨ Nuevas Funcionalidades
- [ ] **[Módulo]**: Descripción de la funcionalidad
- [ ] **[Módulo]**: Descripción de la funcionalidad

### 🐛 Bug Fixes
- [ ] **[Componente]**: Descripción del fix
- [ ] **[Componente]**: Descripción del fix

### 🎨 Mejoras
- [ ] **[Área]**: Descripción de la mejora
- [ ] **[Área]**: Descripción de la mejora

### 💥 Breaking Changes
- [ ] **[Componente]**: Descripción del cambio breaking

## ✅ Checklist de Release

### Pre-Release Testing
- [ ] Tests unitarios: 100% passing
- [ ] Tests de integración: 100% passing
- [ ] Tests E2E: 100% passing
- [ ] Tests de regresión: 100% passing
- [ ] Performance tests: dentro de límites aceptables
- [ ] Security scan: sin vulnerabilidades críticas
- [ ] Accessibility tests: WCAG 2.1 compliant

### Quality Gates
- [ ] Code coverage >= 80%
- [ ] No code smells críticos
- [ ] No vulnerabilidades de seguridad
- [ ] Bundle size dentro de límites
- [ ] Performance metrics aceptables

### Documentation
- [ ] CHANGELOG.md actualizado
- [ ] API documentation actualizada
- [ ] User documentation actualizada
- [ ] Migration guides creados (si aplica)

### Deployment Readiness
- [ ] Environment variables documentadas
- [ ] Database migrations tested
- [ ] Rollback plan documentado
- [ ] Monitoring alerts configuradas

## 🚀 Deployment Plan

### Staging
- [ ] Deploy to staging completed
- [ ] Smoke tests passed
- [ ] User acceptance testing completed

### Production
- [ ] Blue-green deployment ready
- [ ] Database backup completed
- [ ] Rollback procedure tested
- [ ] Monitoring dashboard ready

## 📊 Metrics & Monitoring

- **Build time**: [X] minutes
- **Bundle size**: [X] MB (±[X]% vs previous)
- **Test coverage**: [X]%
- **Performance score**: [X]/100

## 🎯 Post-Release Tasks

- [ ] Monitor error rates for 24h
- [ ] Verify key metrics
- [ ] Collect user feedback
- [ ] Update project documentation
```

#### **2. Flujo de Review Estructurado**

##### **Asignación Automática de Reviewers**

```yaml
# .github/CODEOWNERS

# Global reviewers
* @team-lead @senior-dev

# Backend específico
/backend/ @backend-team-lead @php-expert
/backend/app/Domain/ @domain-expert @architecture-lead
/backend/app/Infrastructure/ @infrastructure-expert

# Frontend específico
/frontend/ @frontend-team-lead @react-expert
/frontend/src/components/ @ui-expert
/frontend/src/store/ @state-management-expert

# Mobile específico
/mobile/ @mobile-team-lead @react-native-expert

# Infrastructure y DevOps
/.github/ @devops-lead
/docker/ @devops-lead
/scripts/ @devops-lead

# Documentation
/docs/ @tech-writer @team-lead
README.md @team-lead
```

##### **Configuración de Branch Protection**

```json
{
  "required_status_checks": {
    "strict": true,
    "contexts": [
      "ci/backend-tests",
      "ci/frontend-tests",
      "ci/mobile-tests",
      "ci/security-scan",
      "ci/performance-test"
    ]
  },
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "required_approving_review_count": 2,
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": true,
    "bypass_pull_request_allowances": {
      "users": [],
      "teams": ["emergency-response"]
    }
  },
  "restrictions": {
    "users": [],
    "teams": ["senior-developers"],
    "apps": ["github-actions"]
  }
}
```

#### **3. Proceso de Review por Etapas**

##### **Etapa 1: Review Automático**

```yaml
# .github/workflows/pr-review.yml
name: Automated PR Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  automated-review:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Run automated review
        uses: ./.github/actions/automated-review
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - name: Comment review results
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const review = JSON.parse(fs.readFileSync('review-results.json'));
            
            const comment = `
            ## 🤖 Automated Review Results
            
            ### ✅ Passed Checks
            ${review.passed.map(check => `- ${check}`).join('\n')}
            
            ### ❌ Failed Checks
            ${review.failed.map(check => `- ${check}`).join('\n')}
            
            ### ⚠️ Warnings
            ${review.warnings.map(warning => `- ${warning}`).join('\n')}
            
            **Overall Score**: ${review.score}/100
            `;
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
```

##### **Etapa 2: Review Humano**

```markdown
# Guía de Code Review

## 🔍 Checklist de Review

### 1. Arquitectura y Diseño (15 min)

#### Clean Architecture Compliance
- [ ] ¿Las dependencias apuntan hacia el centro?
- [ ] ¿Domain layer está libre de dependencias externas?
- [ ] ¿Application layer solo depende de Domain?
- [ ] ¿Infrastructure está correctamente separada?

#### DDD Principles
- [ ] ¿Las entidades modelan correctamente el dominio?
- [ ] ¿Los value objects son inmutables?
- [ ] ¿Los servicios de dominio encapsulan reglas complejas?
- [ ] ¿Los repositorios siguen las interfaces del dominio?

### 2. Calidad del Código (20 min)

#### SOLID Principles
- [ ] **Single Responsibility**: ¿Cada clase/función tiene una sola razón para cambiar?
- [ ] **Open/Closed**: ¿El código está abierto para extensión, cerrado para modificación?
- [ ] **Liskov Substitution**: ¿Las subclases pueden reemplazar a sus clases base?
- [ ] **Interface Segregation**: ¿Las interfaces son específicas y no fuerzan dependencias innecesarias?
- [ ] **Dependency Inversion**: ¿Se depende de abstracciones, no de concreciones?

#### Code Quality
- [ ] Nombres descriptivos y consistentes
- [ ] Funciones pequeñas (< 20 líneas idealmente)
- [ ] Complejidad ciclomática aceptable
- [ ] Sin código duplicado
- [ ] Error handling apropiado

### 3. Testing (10 min)

#### Test Coverage
- [ ] Tests unitarios para nueva funcionalidad
- [ ] Tests de integración si aplica
- [ ] Tests cubren casos edge
- [ ] Tests son claros y mantenibles

#### Test Quality
- [ ] Arrange-Act-Assert pattern
- [ ] Tests independientes
- [ ] Mocking apropiado
- [ ] Assertions específicas

### 4. Security & Performance (10 min)

#### Security
- [ ] Input validation
- [ ] Output encoding
- [ ] Authentication/Authorization
- [ ] No hardcoded secrets

#### Performance
- [ ] Consultas DB eficientes
- [ ] No N+1 queries
- [ ] Caching apropiado
- [ ] Memory usage optimizado

### 5. Documentation (5 min)

- [ ] Comentarios útiles (no obvios)
- [ ] JSDoc/PHPDoc para APIs públicas
- [ ] README actualizado si aplica
- [ ] CHANGELOG actualizado
```

#### **Etapa 3: Aprobación y Merge**

##### **Criterios de Aprobación**

```typescript
// scripts/review-approval-criteria.ts

interface ApprovalCriteria {
  minimumApprovers: number;
  requiredRoles: string[];
  qualityGates: QualityGate[];
  blockingConditions: BlockingCondition[];
}

interface QualityGate {
  name: string;
  threshold: number;
  current: number;
  status: 'passed' | 'failed' | 'warning';
}

interface BlockingCondition {
  condition: string;
  severity: 'critical' | 'high' | 'medium';
  blocker: boolean;
}

const approvalCriteria: ApprovalCriteria = {
  minimumApprovers: 2,
  requiredRoles: ['senior-developer', 'tech-lead'],
  qualityGates: [
    {
      name: 'Test Coverage',
      threshold: 80,
      current: 85,
      status: 'passed'
    },
    {
      name: 'Code Quality Score',
      threshold: 7.0,
      current: 8.2,
      status: 'passed'
    },
    {
      name: 'Security Vulnerabilities',
      threshold: 0,
      current: 0,
      status: 'passed'
    }
  ],
  blockingConditions: [
    {
      condition: 'Critical security vulnerabilities found',
      severity: 'critical',
      blocker: true
    },
    {
      condition: 'Test coverage below 80%',
      severity: 'high',
      blocker: true
    },
    {
      condition: 'More than 5 code smells',
      severity: 'medium',
      blocker: false
    }
  ]
};
```

### 🛠️ **Herramientas de Review Automatizado**

#### **SonarQube Integration**

```yaml
# .github/workflows/sonarqube.yml
name: SonarQube Analysis

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  sonarqube:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'

      - name: Install dependencies
        run: |
          npm ci
          composer install

      - name: Run tests with coverage
        run: |
          npm run test:coverage
          ./vendor/bin/phpunit --coverage-clover=coverage.xml

      - name: SonarQube Scan
        uses: sonarqube-scanner-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        with:
          args: >
            -Dsonar.projectKey=ecommerce-platform
            -Dsonar.sources=src,backend/app
            -Dsonar.tests=tests,backend/tests
            -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
            -Dsonar.php.coverage.reportPaths=coverage.xml
            -Dsonar.exclusions=**/*.test.js,**/*Test.php
```

#### **CodeClimate Integration**

```yaml
# .codeclimate.yml
version: "2"
checks:
  argument-count:
    config:
      threshold: 4
  complex-logic:
    config:
      threshold: 4
  file-lines:
    config:
      threshold: 250
  method-complexity:
    config:
      threshold: 5
  method-count:
    config:
      threshold: 20
  method-lines:
    config:
      threshold: 25

plugins:
  phpcodesniffer:
    enabled: true
    config:
      standard: "PSR2"
  eslint:
    enabled: true
    channel: "eslint-8"
  duplication:
    enabled: true
    config:
      languages:
        - javascript
        - php

exclude_patterns:
  - "tests/"
  - "spec/"
  - "**/*.test.js"
  - "vendor/"
  - "node_modules/"
```

#### **Automated Review Comments**

```javascript
// .github/actions/review-bot/index.js
const core = require('@actions/core');
const github = require('@actions/github');

async function analyzeAndComment() {
  const token = core.getInput('github-token');
  const octokit = github.getOctokit(token);
  
  const { context } = github;
  const pullRequest = context.payload.pull_request;
  
  // Analizar archivos modificados
  const files = await octokit.rest.pulls.listFiles({
    owner: context.repo.owner,
    repo: context.repo.repo,
    pull_number: pullRequest.number
  });
  
  const comments = [];
  
  // Análisis de archivos PHP
  const phpFiles = files.data.filter(file => file.filename.endsWith('.php'));
  for (const file of phpFiles) {
    const issues = await analyzePHPFile(file);
    comments.push(...issues);
  }
  
  // Análisis de archivos JS/TS
  const jsFiles = files.data.filter(file => 
    file.filename.match(/\.(js|jsx|ts|tsx)$/)
  );
  for (const file of jsFiles) {
    const issues = await analyzeJSFile(file);
    comments.push(...issues);
  }
  
  // Crear comentarios en el PR
  for (const comment of comments) {
    await octokit.rest.pulls.createReviewComment({
      owner: context.repo.owner,
      repo: context.repo.repo,
      pull_number: pullRequest.number,
      body: comment.body,
      path: comment.path,
      line: comment.line
    });
  }
}

async function analyzePHPFile(file) {
  const issues = [];
  
  // Verificar patrones problemáticos
  if (file.patch.includes('global $')) {
    issues.push({
      path: file.filename,
      line: getLineNumber(file.patch, 'global $'),
      body: '⚠️ **Evita variables globales**\n\nUsa inyección de dependencias en su lugar.'
    });
  }
  
  if (file.patch.includes('exit(') || file.patch.includes('die(')) {
    issues.push({
      path: file.filename,
      line: getLineNumber(file.patch, 'exit('),
      body: '❌ **Evita exit() y die()**\n\nUsa excepciones para manejar errores.'
    });
  }
  
  return issues;
}

async function analyzeJSFile(file) {
  const issues = [];
  
  // Verificar uso de console.log
  if (file.patch.includes('console.log')) {
    issues.push({
      path: file.filename,
      line: getLineNumber(file.patch, 'console.log'),
      body: '🧹 **Remove console.log**\n\nUsa un logger apropiado o remueve antes del merge.'
    });
  }
  
  // Verificar any types en TypeScript
  if (file.patch.includes(': any')) {
    issues.push({
      path: file.filename,
      line: getLineNumber(file.patch, ': any'),
      body: '🔒 **Evita tipo `any`**\n\nEspecifica tipos más precisos para mejor type safety.'
    });
  }
  
  return issues;
}

function getLineNumber(patch, searchString) {
  // Implementar lógica para encontrar número de línea
  const lines = patch.split('\n');
  for (let i = 0; i < lines.length; i++) {
    if (lines[i].includes(searchString)) {
      return i + 1;
    }
  }
  return 1;
}

analyzeAndComment().catch(error => {
  core.setFailed(error.message);
});
```

### 📊 **Métricas y Reporting**

#### **Dashboard de Code Review**

```typescript
// src/utils/review-metrics.ts

interface ReviewMetrics {
  averageReviewTime: number; // en horas
  approvalRate: number; // porcentaje
  defectDensity: number; // defectos por KLOC
  reviewParticipation: number; // porcentaje del equipo
  codeChurn: number; // líneas modificadas/total
  qualityScore: number; // score general
}

interface ReviewData {
  prNumber: number;
  author: string;
  reviewers: string[];
  createdAt: Date;
  mergedAt?: Date;
  linesAdded: number;
  linesDeleted: number;
  commentsCount: number;
  approvalsCount: number;
  changesRequestedCount: number;
  qualityGates: QualityGateResult[];
}

class ReviewMetricsCalculator {
  calculateAverageReviewTime(reviews: ReviewData[]): number {
    const completedReviews = reviews.filter(r => r.mergedAt);
    const totalTime = completedReviews.reduce((sum, review) => {
      return sum + (review.mergedAt!.getTime() - review.createdAt.getTime());
    }, 0);
    
    return totalTime / completedReviews.length / (1000 * 60 * 60); // en horas
  }
  
  calculateApprovalRate(reviews: ReviewData[]): number {
    const approvedReviews = reviews.filter(r => r.approvalsCount >= 2);
    return (approvedReviews.length / reviews.length) * 100;
  }
  
  calculateDefectDensity(reviews: ReviewData[]): number {
    const totalLOC = reviews.reduce((sum, r) => sum + r.linesAdded, 0);
    const totalDefects = reviews.reduce((sum, r) => sum + r.changesRequestedCount, 0);
    
    return (totalDefects / totalLOC) * 1000; // defectos por KLOC
  }
  
  generateReport(reviews: ReviewData[]): ReviewMetrics {
    return {
      averageReviewTime: this.calculateAverageReviewTime(reviews),
      approvalRate: this.calculateApprovalRate(reviews),
      defectDensity: this.calculateDefectDensity(reviews),
      reviewParticipation: this.calculateParticipation(reviews),
      codeChurn: this.calculateCodeChurn(reviews),
      qualityScore: this.calculateQualityScore(reviews)
    };
  }
  
  private calculateParticipation(reviews: ReviewData[]): number {
    const uniqueReviewers = new Set(
      reviews.flatMap(r => r.reviewers)
    );
    
    // Asumiendo un equipo de 8 personas
    return (uniqueReviewers.size / 8) * 100;
  }
  
  private calculateCodeChurn(reviews: ReviewData[]): number {
    const totalModified = reviews.reduce((sum, r) => 
      sum + r.linesAdded + r.linesDeleted, 0
    );
    const totalAdded = reviews.reduce((sum, r) => sum + r.linesAdded, 0);
    
    return totalModified / totalAdded;
  }
  
  private calculateQualityScore(reviews: ReviewData[]): number {
    // Algoritmo compuesto que considera múltiples factores
    const approvalWeight = 0.3;
    const timeWeight = 0.2;
    const participationWeight = 0.2;
    const defectWeight = 0.3;
    
    const approvalScore = this.calculateApprovalRate(reviews);
    const timeScore = Math.max(0, 100 - this.calculateAverageReviewTime(reviews) * 10);
    const participationScore = this.calculateParticipation(reviews);
    const defectScore = Math.max(0, 100 - this.calculateDefectDensity(reviews) * 10);
    
    return (
      approvalScore * approvalWeight +
      timeScore * timeWeight +
      participationScore * participationWeight +
      defectScore * defectWeight
    );
  }
}
```

### Proceso de Code Review

#### Revisión de Estructura

- Arquitectura y patrones de diseño
- Separación de responsabilidades
- Cumplimiento de SOLID principles
- Organización de archivos y carpetas

#### Revisión de Funcionalidad

- Lógica de negocio correcta
- Manejo de casos edge
- Validación de entradas
- Gestión de errores

#### Revisión de Calidad

- Legibilidad del código
- Nomenclatura consistente
- Documentación adecuada
- Tests incluidos

#### Revisión de Seguridad

- Validación de inputs
- Manejo seguro de datos sensibles
- Prevención de vulnerabilidades OWASP
- Configuraciones seguras

### Tipos de Refactoring

#### Refactoring de Métodos

- Extract Method
- Inline Method
- Rename Method
- Move Method

#### Refactoring de Clases

- Extract Class
- Inline Class
- Move Field
- Extract Interface

#### Refactoring de Datos

- Encapsulate Field
- Replace Data Value with Object
- Change Value to Reference
- Replace Array with Object

#### Refactoring de Arquitectura

- Extract Module
- Move Class
- Split Module
- Merge Module

## ¿Qué debo hacer?

### 1. Configurar herramientas de code review

#### GitHub Pull Request Templates

```markdown
<!-- .github/pull_request_template.md -->

# Pull Request

## Descripción

Breve descripción de los cambios realizados.

## Tipo de cambio

- [ ] Bug fix (cambio que corrige un problema)
- [ ] New feature (cambio que añade funcionalidad)
- [ ] Breaking change (cambio que causaría que la funcionalidad existente no
      funcione como se espera)
- [ ] Refactoring (cambio que mejora el código sin cambiar funcionalidad)
- [ ] Documentation update (actualización de documentación)

## ¿Cómo se ha probado?

- [ ] Tests unitarios
- [ ] Tests de integración
- [ ] Tests manuales
- [ ] Tests de performance

## Checklist de revisión

### Funcionalidad

- [ ] El código funciona correctamente
- [ ] Se manejan todos los casos edge
- [ ] La lógica de negocio es correcta
- [ ] Se incluyen validaciones apropiadas

### Calidad del código

- [ ] El código es legible y está bien documentado
- [ ] Sigue las convenciones de nomenclatura del proyecto
- [ ] No hay código duplicado
- [ ] Las funciones y clases tienen responsabilidades claras

### Tests

- [ ] Se incluyen tests para el nuevo código
- [ ] Todos los tests pasan
- [ ] La cobertura de tests es adecuada
- [ ] Los tests son comprensibles y mantenibles

### Seguridad

- [ ] No hay credenciales hardcodeadas
- [ ] Se validan correctamente las entradas del usuario
- [ ] Se manejan los errores de forma segura
- [ ] No se expone información sensible

### Performance

- [ ] No hay operaciones innecesarias costosas
- [ ] Se usan estructuras de datos apropiadas
- [ ] Las consultas a base de datos están optimizadas
- [ ] Se considera el impacto en memoria

## Capturas de pantalla (si aplica)

<!-- Incluir capturas de pantalla de cambios en UI -->

## Notas adicionales

<!-- Cualquier información adicional para los revisores -->

---

**Reviewers**: @team/backend @team/frontend **Assignees**: @author **Labels**:
enhancement, needs-review
```

#### Configuración de GitHub branch protection

```yaml
# .github/branch-protection.yml
protection_rules:
   main:
      required_status_checks:
         strict: true
         contexts:
            - "ci/tests"
            - "ci/lint"
            - "ci/security"
      enforce_admins: true
      required_pull_request_reviews:
         required_approving_review_count: 2
         dismiss_stale_reviews: true
         require_code_owner_reviews: true
         require_review_from_code_owners: true
      restrictions:
         users: []
         teams: ["developers"]
      allow_force_pushes: false
      allow_deletions: false
```

#### CODEOWNERS file

```bash
# .github/CODEOWNERS

# Global owners
* @team/leads

# Backend code
/app/ @team/backend
/database/ @team/backend
/config/ @team/backend

# Frontend code
/src/components/ @team/frontend
/src/pages/ @team/frontend
/src/hooks/ @team/frontend

# Infrastructure
/.github/ @team/devops
/docker/ @team/devops
/scripts/ @team/devops

# Documentation
/docs/ @team/leads @team/technical-writers
README.md @team/leads

# Security sensitive files
/app/Infrastructure/Security/ @team/security @team/leads
/src/utils/security.ts @team/security @team/frontend
```

### 2. Establecer checklist de code review

#### Checklist para Backend (Laravel)

```php
<?php
// Ejemplo de checklist en forma de comentario para PRs

/*
BACKEND CODE REVIEW CHECKLIST

□ ARQUITECTURA Y ESTRUCTURA
  □ Sigue Clean Architecture (Domain, Application, Infrastructure)
  □ Las entidades están en Domain sin dependencias externas
  □ Los casos de uso están en Application
  □ Las implementaciones técnicas están en Infrastructure
  □ Se respeta la inversión de dependencias

□ DOMAIN LAYER
  □ Las entidades son POPO (Plain Old PHP Objects)
  □ Los Value Objects son inmutables
  □ Las reglas de negocio están en el dominio
  □ No hay dependencias de framework en el dominio

□ APPLICATION LAYER
  □ Los casos de uso tienen una sola responsabilidad
  □ Se usan DTOs para comunicación entre capas
  □ Los casos de uso no conocen detalles de implementación
  □ Se validan las entradas correctamente

□ INFRASTRUCTURE LAYER
  □ Los repositorios implementan las interfaces del dominio
  □ Los controllers son delgados (solo orquestación)
  □ Se usan Form Requests para validación
  □ Los Resources formatean las respuestas API

□ TESTING
  □ Casos de uso tienen tests unitarios
  □ Repositorios tienen tests de integración
  □ APIs tienen tests feature
  □ Cobertura de al menos 80%

□ SEGURIDAD
  □ Inputs validados y sanitizados
  □ No hay SQL injection vulnerabilities
  □ Autenticación y autorización correctas
  □ No hay datos sensibles en logs

□ PERFORMANCE
  □ Consultas N+1 evitadas
  □ Índices de BD apropiados
  □ Paginación implementada
  □ Cache utilizado cuando corresponde
*/
```

#### Checklist para Frontend (React)

```typescript
/*
FRONTEND CODE REVIEW CHECKLIST

□ ARQUITECTURA Y ESTRUCTURA
  □ Componentes tienen responsabilidad única
  □ Custom hooks para lógica reutilizable
  □ Estado global solo cuando es necesario
  □ Separación clara entre presentación y lógica

□ COMPONENTES
  □ Props tipadas con TypeScript
  □ Componentes son funcionales (hooks)
  □ Memo usado apropiadamente para performance
  □ Naming consistente y descriptivo

□ ESTADO
  □ useState para estado local simple
  □ useReducer para estado complejo
  □ Redux Toolkit para estado global
  □ Immutabilidad respetada

□ EFECTOS Y CICLO DE VIDA
  □ useEffect con dependencias correctas
  □ Cleanup functions implementadas
  □ No memory leaks
  □ Loading states manejados

□ TIPOS Y INTERFACES
  □ Tipado fuerte en TypeScript
  □ Interfaces definidas para props
  □ Tipos para API responses
  □ No any types

□ ACCESIBILIDAD
  □ Semantic HTML usado
  □ ARIA labels apropiadas
  □ Keyboard navigation funcional
  □ Screen reader compatible

□ TESTING
  □ Tests unitarios para componentes
  □ Tests de integración para flujos
  □ User interactions tested
  □ Error boundaries tested

□ PERFORMANCE
  □ Lazy loading implementado
  □ Code splitting apropiado
  □ Images optimizadas
  □ Bundle size considerado
*/
```

### 3. Configurar herramientas automatizadas

#### ESLint para enforcing standards

```javascript
// .eslintrc.js
module.exports = {
   extends: [
      "eslint:recommended",
      "@typescript-eslint/recommended",
      "plugin:react/recommended",
      "plugin:react-hooks/recommended",
      "plugin:jsx-a11y/recommended",
   ],
   plugins: [
      "@typescript-eslint",
      "react",
      "react-hooks",
      "jsx-a11y",
      "import",
   ],
   rules: {
      // Code quality
      "no-console": "warn",
      "no-debugger": "error",
      "no-unused-vars": "off",
      "@typescript-eslint/no-unused-vars": "error",

      // React específico
      "react/prop-types": "off", // Usamos TypeScript
      "react/react-in-jsx-scope": "off", // React 17+
      "react-hooks/rules-of-hooks": "error",
      "react-hooks/exhaustive-deps": "warn",

      // Imports
      "import/order": [
         "error",
         {
            groups: [
               "builtin",
               "external",
               "internal",
               "parent",
               "sibling",
               "index",
            ],
            "newlines-between": "always",
         },
      ],

      // Accesibilidad
      "jsx-a11y/alt-text": "error",
      "jsx-a11y/anchor-has-content": "error",
      "jsx-a11y/click-events-have-key-events": "error",

      // TypeScript
      "@typescript-eslint/explicit-function-return-type": "warn",
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/prefer-const": "error",
   },
};
```

#### PHPStan para análisis estático

```php
<?php
// phpstan.neon
parameters:
    level: 8
    paths:
        - app
        - database
        - tests

    excludePaths:
        - vendor
        - bootstrap/cache
        - storage

    # Reglas específicas para Clean Architecture
    customRulesetUsed: true

    checkMissingIterableValueType: false
    checkGenericClassInNonGenericObjectType: false

    # Ignores para framework Laravel
    ignoreErrors:
        - '#Call to an undefined method Illuminate\\.*#'

    # Reglas adicionales
    rules:
        - App\Infrastructure\CodeQuality\Rules\DomainPurityRule
        - App\Infrastructure\CodeQuality\Rules\NoEloquentInDomainRule
        - App\Infrastructure\CodeQuality\Rules\UseCaseNamingRule
```

#### SonarQube Quality Gates

```yaml
# sonar-project.properties
sonar.projectKey=mi-proyecto
sonar.projectName=Mi Proyecto
sonar.projectVersion=1.0

# Source code
sonar.sources=src,app
sonar.tests=src/__tests__,tests
sonar.exclusions=**/node_modules/**,**/vendor/**,**/coverage/**

# Quality Gates
sonar.qualitygate.wait=true

# Coverage
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.php.coverage.reportPaths=coverage/clover.xml

# Duplications
sonar.cpd.exclusions=**/*test*/**,**/*spec*/**

# Thresholds
sonar.coverage.exclusions=**/*test*/**,**/*spec*/**,**/*mock*/**
```

### 4. Implementar proceso de refactoring

#### Identificación de code smells

```typescript
// src/utils/codeQuality.ts
export interface CodeSmell {
   type: string;
   severity: "low" | "medium" | "high";
   description: string;
   location: string;
   suggestion: string;
}

export class CodeQualityAnalyzer {
   static analyzeComponent(filePath: string, content: string): CodeSmell[] {
      const smells: CodeSmell[] = [];

      // 1. Long Parameter List
      const longParamMatches = content.match(/function\s+\w+\s*\([^)]{50,}\)/g);
      if (longParamMatches) {
         smells.push({
            type: "Long Parameter List",
            severity: "medium",
            description: "Función con demasiados parámetros",
            location: filePath,
            suggestion:
               "Considerar usar objeto de configuración o pattern Builder",
         });
      }

      // 2. Large Class/Component
      const lineCount = content.split("\n").length;
      if (lineCount > 500) {
         smells.push({
            type: "Large Component",
            severity: "high",
            description: `Componente muy grande (${lineCount} líneas)`,
            location: filePath,
            suggestion: "Dividir en componentes más pequeños",
         });
      }

      // 3. Duplicate Code
      const functionBodies = this.extractFunctionBodies(content);
      const duplicates = this.findDuplicates(functionBodies);
      if (duplicates.length > 0) {
         smells.push({
            type: "Duplicate Code",
            severity: "medium",
            description: "Código duplicado detectado",
            location: filePath,
            suggestion:
               "Extraer funcionalidad común a función/hook reutilizable",
         });
      }

      // 4. Long Method
      const longMethods = this.findLongMethods(content);
      if (longMethods.length > 0) {
         smells.push({
            type: "Long Method",
            severity: "medium",
            description: "Métodos muy largos detectados",
            location: filePath,
            suggestion: "Dividir métodos en funciones más pequeñas",
         });
      }

      // 5. Magic Numbers
      const magicNumbers = content.match(/\b(?<!const\s+\w+\s*=\s*)\d{2,}\b/g);
      if (magicNumbers && magicNumbers.length > 3) {
         smells.push({
            type: "Magic Numbers",
            severity: "low",
            description: "Números mágicos encontrados",
            location: filePath,
            suggestion: "Definir constantes con nombres descriptivos",
         });
      }

      return smells;
   }

   private static extractFunctionBodies(content: string): string[] {
      const functionRegex = /function\s+\w+[^{]*{([^{}]*(?:{[^{}]*}[^{}]*)*)}/g;
      const bodies: string[] = [];
      let match;

      while ((match = functionRegex.exec(content)) !== null) {
         bodies.push(match[1].trim());
      }

      return bodies;
   }

   private static findDuplicates(items: string[]): string[] {
      const seen = new Set<string>();
      const duplicates: string[] = [];

      for (const item of items) {
         if (item.length > 50 && seen.has(item)) {
            duplicates.push(item);
         }
         seen.add(item);
      }

      return duplicates;
   }

   private static findLongMethods(content: string): string[] {
      const methods: string[] = [];
      const lines = content.split("\n");

      let inFunction = false;
      let functionStart = 0;
      let braceCount = 0;

      for (let i = 0; i < lines.length; i++) {
         const line = lines[i];

         if (line.includes("function") || line.includes("=>")) {
            inFunction = true;
            functionStart = i;
            braceCount = 0;
         }

         if (inFunction) {
            braceCount += (line.match(/{/g) || []).length;
            braceCount -= (line.match(/}/g) || []).length;

            if (braceCount === 0 && inFunction) {
               const functionLength = i - functionStart;
               if (functionLength > 30) {
                  methods.push(
                     `Line ${functionStart}: Function too long (${functionLength} lines)`,
                  );
               }
               inFunction = false;
            }
         }
      }

      return methods;
   }
}
```

#### Automatización de refactoring

```typescript
// scripts/refactor-tools.ts
import * as fs from "fs";
import * as path from "path";

export class RefactoringTools {
   // Extract Method refactoring
   static extractMethod(
      filePath: string,
      startLine: number,
      endLine: number,
      methodName: string,
   ): void {
      const content = fs.readFileSync(filePath, "utf8");
      const lines = content.split("\n");

      const extractedCode = lines.slice(startLine - 1, endLine).join("\n");
      const methodDeclaration =
         `\n  private ${methodName}(): void {\n${extractedCode}\n  }\n`;

      // Replace extracted code with method call
      lines.splice(
         startLine - 1,
         endLine - startLine + 1,
         `    this.${methodName}();`,
      );

      // Add method declaration
      const classEndIndex = this.findClassEnd(lines);
      lines.splice(classEndIndex - 1, 0, methodDeclaration);

      fs.writeFileSync(filePath, lines.join("\n"));
      console.log(`✅ Método ${methodName} extraído exitosamente`);
   }

   // Rename Method refactoring
   static renameMethod(
      filePath: string,
      oldName: string,
      newName: string,
   ): void {
      const content = fs.readFileSync(filePath, "utf8");

      // Replace method declaration
      const methodRegex = new RegExp(`(\\b${oldName}\\s*\\()`, "g");
      const callRegex = new RegExp(`(\\b${oldName}\\s*\\()`, "g");

      const updatedContent = content
         .replace(methodRegex, `${newName}(`)
         .replace(callRegex, `${newName}(`);

      fs.writeFileSync(filePath, updatedContent);
      console.log(`✅ Método ${oldName} renombrado a ${newName}`);
   }

   // Extract Component refactoring
   static extractComponent(
      filePath: string,
      componentName: string,
      jsxCode: string,
   ): void {
      const dir = path.dirname(filePath);
      const newComponentPath = path.join(dir, `${componentName}.tsx`);

      const componentTemplate = `import React from 'react';

interface ${componentName}Props {
  // Define props here
}

export default function ${componentName}(props: ${componentName}Props): JSX.Element {
  return (
${jsxCode}
  );
}
`;

      fs.writeFileSync(newComponentPath, componentTemplate);
      console.log(
         `✅ Componente ${componentName} extraído a ${newComponentPath}`,
      );
   }

   // Remove Dead Code
   static removeDeadCode(filePath: string): void {
      const content = fs.readFileSync(filePath, "utf8");

      // Remove unused imports (simplified)
      const lines = content.split("\n");
      const filteredLines = lines.filter((line) => {
         if (line.trim().startsWith("import") && line.includes("from")) {
            const imported = line.match(/import\s+{([^}]+)}/)?.[1];
            if (imported) {
               const items = imported.split(",").map((item) => item.trim());
               return items.some((item) => content.includes(item));
            }
         }
         return true;
      });

      fs.writeFileSync(filePath, filteredLines.join("\n"));
      console.log(`✅ Código muerto removido de ${filePath}`);
   }

   private static findClassEnd(lines: string[]): number {
      let braceCount = 0;
      let inClass = false;

      for (let i = 0; i < lines.length; i++) {
         const line = lines[i];

         if (line.includes("class ") || line.includes("function ")) {
            inClass = true;
         }

         if (inClass) {
            braceCount += (line.match(/{/g) || []).length;
            braceCount -= (line.match(/}/g) || []).length;

            if (braceCount === 0) {
               return i + 1;
            }
         }
      }

      return lines.length;
   }
}
```

### 5. Configurar métricas de calidad

#### Configuración de métricas con SonarQube

```typescript
// scripts/quality-metrics.ts
export interface QualityMetrics {
   coverage: number;
   duplications: number;
   complexity: number;
   maintainabilityIndex: number;
   technicalDebt: number;
   codeSmells: number;
   bugs: number;
   vulnerabilities: number;
}

export class QualityMetricsCollector {
   static async collectMetrics(projectPath: string): Promise<QualityMetrics> {
      const metrics: QualityMetrics = {
         coverage: await this.getCoverage(projectPath),
         duplications: await this.getDuplications(projectPath),
         complexity: await this.getComplexity(projectPath),
         maintainabilityIndex: await this.getMaintainabilityIndex(projectPath),
         technicalDebt: await this.getTechnicalDebt(projectPath),
         codeSmells: await this.getCodeSmells(projectPath),
         bugs: await this.getBugs(projectPath),
         vulnerabilities: await this.getVulnerabilities(projectPath),
      };

      return metrics;
   }

   private static async getCoverage(projectPath: string): Promise<number> {
      // Leer reporte de coverage
      const coveragePath = path.join(
         projectPath,
         "coverage/coverage-summary.json",
      );
      if (fs.existsSync(coveragePath)) {
         const coverage = JSON.parse(fs.readFileSync(coveragePath, "utf8"));
         return coverage.total.lines.pct;
      }
      return 0;
   }

   private static async getDuplications(projectPath: string): Promise<number> {
      // Análisis de duplicaciones usando jscpd
      const { execSync } = require("child_process");
      try {
         const result = execSync(
            `npx jscpd ${projectPath}/src --reporter json`,
            {
               encoding: "utf8",
            },
         );
         const report = JSON.parse(result);
         return report.statistics.total.duplications;
      } catch {
         return 0;
      }
   }

   private static async getComplexity(projectPath: string): Promise<number> {
      // Calcular complejidad ciclomática
      const files = this.getAllTypeScriptFiles(projectPath);
      let totalComplexity = 0;
      let fileCount = 0;

      for (const file of files) {
         const content = fs.readFileSync(file, "utf8");
         const complexity = this.calculateCyclomaticComplexity(content);
         totalComplexity += complexity;
         fileCount++;
      }

      return fileCount > 0 ? totalComplexity / fileCount : 0;
   }

   private static calculateCyclomaticComplexity(code: string): number {
      // Simplified cyclomatic complexity calculation
      const patterns = [
         /if\s*\(/g,
         /else\s+if\s*\(/g,
         /while\s*\(/g,
         /for\s*\(/g,
         /switch\s*\(/g,
         /case\s+/g,
         /catch\s*\(/g,
         /&&/g,
         /\|\|/g,
         /\?/g, // ternary operator
      ];

      let complexity = 1; // Base complexity

      patterns.forEach((pattern) => {
         const matches = code.match(pattern);
         if (matches) {
            complexity += matches.length;
         }
      });

      return complexity;
   }

   private static getAllTypeScriptFiles(dir: string): string[] {
      const files: string[] = [];

      function traverse(currentDir: string) {
         const items = fs.readdirSync(currentDir);

         for (const item of items) {
            const fullPath = path.join(currentDir, item);
            const stat = fs.statSync(fullPath);

            if (stat.isDirectory() && !item.includes("node_modules")) {
               traverse(fullPath);
            } else if (item.endsWith(".ts") || item.endsWith(".tsx")) {
               files.push(fullPath);
            }
         }
      }

      traverse(dir);
      return files;
   }

   static generateQualityReport(metrics: QualityMetrics): string {
      const report = `
# Quality Metrics Report

## Resumen General
- **Cobertura de Tests**: ${metrics.coverage.toFixed(1)}%
- **Duplicaciones**: ${metrics.duplications}
- **Complejidad Promedio**: ${metrics.complexity.toFixed(1)}
- **Índice de Mantenibilidad**: ${metrics.maintainabilityIndex.toFixed(1)}

## Deuda Técnica
- **Total**: ${metrics.technicalDebt} horas
- **Code Smells**: ${metrics.codeSmells}
- **Bugs**: ${metrics.bugs}
- **Vulnerabilidades**: ${metrics.vulnerabilities}

## Estado de Calidad
${this.getQualityGate(metrics)}

## Recomendaciones
${this.getRecommendations(metrics)}
    `;

      return report.trim();
   }

   private static getQualityGate(metrics: QualityMetrics): string {
      const issues = [];

      if (metrics.coverage < 80) {
         issues.push("❌ Cobertura de tests insuficiente");
      }

      if (metrics.complexity > 10) {
         issues.push("❌ Complejidad demasiado alta");
      }

      if (metrics.duplications > 3) {
         issues.push("❌ Demasiadas duplicaciones");
      }

      if (metrics.vulnerabilities > 0) {
         issues.push("❌ Vulnerabilidades de seguridad");
      }

      return issues.length === 0
         ? "✅ PASSED"
         : `❌ FAILED\n\n${issues.join("\n")}`;
   }

   private static getRecommendations(metrics: QualityMetrics): string {
      const recommendations = [];

      if (metrics.coverage < 80) {
         recommendations.push("- Incrementar cobertura de tests");
      }

      if (metrics.complexity > 10) {
         recommendations.push("- Refactorizar métodos complejos");
      }

      if (metrics.duplications > 3) {
         recommendations.push("- Eliminar código duplicado");
      }

      return recommendations.length > 0
         ? recommendations.join("\n")
         : "- Mantener la calidad actual";
   }
}
```

### 6. Pipeline de CI/CD para code review

```yaml
# .github/workflows/code-review.yml
name: Code Review & Quality Gates

on:
   pull_request:
      branches: [main, develop]

jobs:
   code-analysis:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
           with:
              fetch-depth: 0

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"

         - name: Install dependencies
           run: |
              npm ci
              composer install

         - name: Run ESLint
           run: |
              npx eslint src/ --format=json --output-file=eslint-report.json
              npx eslint src/ --format=github

         - name: Run PHPStan
           run: |
              ./vendor/bin/phpstan analyse --error-format=github

         - name: Run tests with coverage
           run: |
              npm run test:coverage
              ./vendor/bin/phpunit --coverage-clover=coverage.xml

         - name: SonarQube analysis
           uses: sonarqube-quality-gate-action@master
           env:
              GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
              SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}

         - name: Quality Gate check
           run: |
              node scripts/quality-gate-check.js

         - name: Comment PR with results
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const report = fs.readFileSync('quality-report.md', 'utf8');

                 github.rest.issues.createComment({
                   issue_number: context.issue.number,
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   body: report
                 });

   security-review:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Run security audit
           run: |
              npm audit --audit-level=moderate
              composer audit

         - name: CodeQL Analysis
           uses: github/codeql-action/analyze@v2
           with:
              languages: javascript,php

   dependency-review:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Dependency Review
           uses: actions/dependency-review-action@v3
           with:
              fail-on-severity: moderate

         - name: Check outdated packages
           run: |
              npm outdated > outdated-packages.txt || true
              composer outdated > outdated-composer.txt || true
```

## Tips

- **Reviews pequeños y frecuentes**: PRs de máximo 400 líneas de código
- **Feedback constructivo**: Siempre explica el "por qué" de tus comentarios
- **Automatización**: Usa herramientas para validar estándares básicos
- **Contexto**: Proporciona contexto suficiente en la descripción del PR
- **Testing**: Incluye tests en todos los cambios de funcionalidad
- **Documentation**: Actualiza documentación cuando sea necesario
- **Security first**: Siempre considera implicaciones de seguridad
- **Performance**: Evalúa el impacto en rendimiento de los cambios

## Ejemplos

### Script de refactoring automatizado

```bash
#!/bin/bash
# automated-refactoring.sh

echo "🔧 Iniciando refactoring automatizado..."

# 1. Identificar code smells
echo "🔍 Identificando code smells..."
node scripts/identify-code-smells.js > code-smells-report.json

# 2. Remover imports no utilizados
echo "🧹 Removiendo imports no utilizados..."
npx ts-unused-exports tsconfig.json --excludeDeclarationFiles

# 3. Formatear código
echo "💅 Formateando código..."
npx prettier --write "src/**/*.{ts,tsx}"
npx php-cs-fixer fix app/ --rules=@PSR12

# 4. Ordenar imports
echo "📚 Ordenando imports..."
npx organize-imports-cli "src/**/*.ts" "src/**/*.tsx"

# 5. Análisis de duplicaciones
echo "🔄 Analizando duplicaciones..."
npx jscpd src/ --reporter json --output duplications-report.json

# 6. Generar reporte de refactoring
echo "📊 Generando reporte..."
node scripts/generate-refactoring-report.js

echo "✅ Refactoring automatizado completado"
echo "📋 Ver reporte en: refactoring-report.html"
```

### Template de comentario de code review

````markdown
<!-- Ejemplo de comentario constructivo -->

## 🔍 Code Review - Componente UserForm

### ✅ Aspectos Positivos

- Excelente uso de TypeScript con tipado fuerte
- Tests comprehensivos incluidos
- Manejo de errores bien implementado

### 🚨 Issues Críticos

1. **Línea 45**: Falta validación de input antes de envío

   ```typescript
   // Actual
   onSubmit(formData);

   // Sugerido
   if (validateFormData(formData)) {
      onSubmit(formData);
   }
   ```

2. **Línea 67**: Posible memory leak en useEffect
   ```typescript
   // Agregar cleanup function
   useEffect(() => {
      const subscription = api.subscribe(callback);
      return () => subscription.unsubscribe();
   }, []);
   ```

### 💡 Sugerencias de Mejora

- Considerar usar `useCallback` para `handleSubmit` (línea 32)
- Extraer validación a custom hook reutilizable
- Mover constantes mágicas a archivo de configuración

### 🎯 Next Steps

- [ ] Implementar validación de entrada
- [ ] Agregar cleanup en useEffect
- [ ] Extraer lógica de validación

**Estimación**: 2-3 horas de refactoring

---

**Reviewer**: @john-doe **Prioridad**: Alta **Area**: Frontend/Components
````

---

## Navegación

**Progreso en Testing y Quality Assurance:**

- ✅ [Testing y QA](./testing-qa.md)
- ✅ [Tipos de Pruebas](./tipos-pruebas.md)
- ✅ [Testing Funcional Automatizado](./testing-funcional-automatizado.md)
- ✅ [Testing de Regresión](./testing-regresion.md)
- ✅ [Checklists QA](./checklists-qa.md)
- ✅ [Pruebas de Aceptación del Usuario](./pruebas-aceptacion-usuario.md)
- ✅ [Gestión de Reportes de Errores](./gestion-reportes-errores.md)
- ✅ [Testing de Performance y Carga](./testing-performance-carga.md)
- ✅ [Testing de Seguridad OWASP](./testing-seguridad-owasp.md)
- ✅ [Testing de Usabilidad](./testing-usabilidad.md)
- ✅ **Code Review y Refactoring** ← Estás aquí
- ⏭️ [Auditoría de Calidad de Código](./auditoria-calidad-codigo.md)
- ⏭️ [Checklist Específico de Performance](./checklist-performance.md)
- ⏭️ [Revisión General del Código](./revision-general-codigo.md)
- ⏭️ [Optimización de Recursos](./optimizacion-recursos.md)

---

### Siguiente Paso

Continúa con [**Auditoría de Calidad de Código**](./auditoria-calidad-codigo.md)

[⬅️ Testing de Usabilidad](./testing-usabilidad.md) |
[🏠 README Principal](../../README.md) |
[➡️ Auditoría de Calidad de Código](./auditoria-calidad-codigo.md)
