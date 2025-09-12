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
  plugins: ["@typescript-eslint", "react", "react-hooks", "jsx-a11y", "import"],
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
        suggestion: "Considerar usar objeto de configuración o pattern Builder",
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
        suggestion: "Extraer funcionalidad común a función/hook reutilizable",
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
              `Line ${functionStart}: Function too long (${functionLength} lines)`
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
    methodName: string
  ): void {
    const content = fs.readFileSync(filePath, "utf8");
    const lines = content.split("\n");

    const extractedCode = lines.slice(startLine - 1, endLine).join("\n");
    const methodDeclaration = `\n  private ${methodName}(): void {\n${extractedCode}\n  }\n`;

    // Replace extracted code with method call
    lines.splice(
      startLine - 1,
      endLine - startLine + 1,
      `    this.${methodName}();`
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
    newName: string
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
    jsxCode: string
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
      `✅ Componente ${componentName} extraído a ${newComponentPath}`
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
      "coverage/coverage-summary.json"
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
      const result = execSync(`npx jscpd ${projectPath}/src --reporter json`, {
        encoding: "utf8",
      });
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

---

### Siguiente Paso

Continúa con [**Auditoría de Calidad de Código**](./auditoria-calidad-codigo.md)

[⬅️ Testing de Usabilidad](./testing-usabilidad.md) |
[🏠 README Principal](../../README.md) |
[➡️ Auditoría de Calidad de Código](./auditoria-calidad-codigo.md)
