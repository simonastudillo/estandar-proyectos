# Cobertura de Pruebas (Coverage)

## ¿Qué es?

La cobertura de pruebas es una métrica que indica qué porcentaje del código
fuente está siendo ejecutado durante las pruebas automatizadas. Incluye
diferentes tipos de cobertura (líneas, funciones, ramas, statements) y
proporciona información valiosa sobre la calidad y completitud de la suite de
testing, ayudando a identificar áreas del código que necesitan más pruebas.

## ¿Por qué es importante?

- **Calidad del testing**: Identifica código no probado que podría contener bugs
- **Confianza**: Aumenta la seguridad al realizar refactoring o cambios
- **Detección de dead code**: Encuentra código que nunca se ejecuta
- **Mejora continua**: Establece metas medibles para el equipo
- **Compliance**: Cumple con estándares de calidad de la industria
- **Risk management**: Identifica áreas de alto riesgo con poca cobertura
- **Code review**: Facilita las revisiones de código con métricas objetivas

## ¿Qué debe incluir?

### Tipos de Cobertura

#### Statement Coverage (Cobertura de Sentencias)

- Mide qué porcentaje de statements se ejecutan
- Es la métrica más básica pero fundamental
- Objetivo: 85-90% para código crítico

#### Branch Coverage (Cobertura de Ramas)

- Evalúa si todas las rutas de decisión se prueban
- Incluye if/else, switch, operadores ternarios
- Objetivo: 80-85% para lógica de negocio

#### Function Coverage (Cobertura de Funciones)

- Verifica qué funciones/métodos se ejecutan
- Útil para identificar funciones no utilizadas
- Objetivo: 90-95% para APIs públicas

#### Line Coverage (Cobertura de Líneas)

- Mide qué líneas de código se ejecutan
- Similar a statement coverage pero más granular
- Objetivo: 85-90% general

### Configuración Frontend (Jest + TypeScript)

#### Jest Coverage Configuration

```javascript
// jest.config.js
module.exports = {
   collectCoverage: true,
   coverageDirectory: "coverage",
   coverageReporters: [
      "text", // Terminal output
      "html", // HTML report
      "lcov", // For SonarQube/CI integration
      "json", // Machine-readable format
      "clover", // XML format
   ],

   collectCoverageFrom: [
      "src/**/*.{ts,tsx}",
      "!src/**/*.d.ts",
      "!src/**/*.stories.{ts,tsx}",
      "!src/**/*.test.{ts,tsx}",
      "!src/index.tsx",
      "!src/reportWebVitals.ts",
      "!src/setupTests.ts",
      "!src/__tests__/**/*",
      "!src/**/__mocks__/**/*",
   ],

   coverageThreshold: {
      global: {
         branches: 80,
         functions: 85,
         lines: 85,
         statements: 85,
      },
      "./src/components/": {
         branches: 90,
         functions: 95,
         lines: 95,
         statements: 95,
      },
      "./src/hooks/": {
         branches: 85,
         functions: 90,
         lines: 90,
         statements: 90,
      },
      "./src/services/": {
         branches: 75,
         functions: 80,
         lines: 80,
         statements: 80,
      },
      "./src/utils/": {
         branches: 95,
         functions: 95,
         lines: 95,
         statements: 95,
      },
   },

   coveragePathIgnorePatterns: [
      "/node_modules/",
      "/build/",
      "/dist/",
      "/coverage/",
      "\\.d\\.ts$",
   ],
};
```

#### Coverage Scripts (package.json)

```json
{
   "scripts": {
      "test": "jest",
      "test:coverage": "jest --coverage",
      "test:coverage:watch": "jest --coverage --watchAll",
      "test:coverage:ci": "jest --coverage --ci --watchAll=false --passWithNoTests",
      "coverage:open": "open coverage/lcov-report/index.html",
      "coverage:check": "jest --coverage --passWithNoTests && npm run coverage:validate",
      "coverage:validate": "node scripts/validate-coverage.js"
   }
}
```

#### Coverage Validation Script

```javascript
// scripts/validate-coverage.js
const fs = require("fs");
const path = require("path");

const coverageSummaryPath = path.join(
   __dirname,
   "../coverage/coverage-summary.json",
);

if (!fs.existsSync(coverageSummaryPath)) {
   console.error("❌ Coverage summary not found");
   process.exit(1);
}

const coverageSummary = JSON.parse(
   fs.readFileSync(coverageSummaryPath, "utf8"),
);
const { total } = coverageSummary;

const thresholds = {
   statements: 85,
   branches: 80,
   functions: 85,
   lines: 85,
};

const results = {};
let passed = true;

Object.keys(thresholds).forEach((metric) => {
   const actual = total[metric].pct;
   const required = thresholds[metric];
   const status = actual >= required;

   results[metric] = {
      actual,
      required,
      status: status ? "✅" : "❌",
   };

   if (!status) passed = false;
});

console.log("\n📊 Coverage Report:");
console.log("==================");
Object.keys(results).forEach((metric) => {
   const { actual, required, status } = results[metric];
   console.log(
      `${status} ${metric.padEnd(12)}: ${
         actual.toFixed(1)
      }% (required: ${required}%)`,
   );
});

if (passed) {
   console.log("\n✅ All coverage thresholds met!");
   process.exit(0);
} else {
   console.log("\n❌ Some coverage thresholds not met");
   process.exit(1);
}
```

### Configuración Backend (PHPUnit + Laravel)

#### PHPUnit Coverage Configuration

```xml
<!-- phpunit.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/10.0/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
         processIsolation="false"
         stopOnFailure="false"
         cacheDirectory=".phpunit.cache">
    
    <testsuites>
        <testsuite name="Unit">
            <directory>tests/Unit</directory>
        </testsuite>
        <testsuite name="Integration">
            <directory>tests/Integration</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory>tests/Feature</directory>
        </testsuite>
    </testsuites>
    
    <source>
        <include>
            <directory suffix=".php">app/Domain</directory>
            <directory suffix=".php">app/Application</directory>
            <directory suffix=".php">app/Infrastructure</directory>
        </include>
        <exclude>
            <directory>app/Infrastructure/Console</directory>
            <directory>app/Infrastructure/Providers</directory>
            <file>app/Infrastructure/Http/Kernel.php</file>
            <file>app/Infrastructure/Exceptions/Handler.php</file>
        </exclude>
    </source>
    
    <coverage>
        <report>
            <html outputDirectory="storage/coverage/html" lowUpperBound="50" highLowerBound="80"/>
            <clover outputFile="storage/coverage/clover.xml"/>
            <text outputFile="storage/coverage/coverage.txt" showUncoveredFiles="true"/>
            <xml outputDirectory="storage/coverage/xml"/>
        </report>
    </coverage>
    
    <logging>
        <junit outputFile="storage/coverage/junit.xml"/>
    </logging>
</phpunit>
```

#### Coverage Analysis Script (PHP)

```php
<?php
// scripts/analyze-coverage.php

require_once 'vendor/autoload.php';

class CoverageAnalyzer
{
    private array $thresholds = [
        'domain' => [
            'line' => 95,
            'function' => 95,
            'class' => 95
        ],
        'application' => [
            'line' => 90,
            'function' => 90,
            'class' => 90
        ],
        'infrastructure' => [
            'line' => 75,
            'function' => 75,
            'class' => 75
        ]
    ];

    public function analyze(string $cloverFile): bool
    {
        if (!file_exists($cloverFile)) {
            echo "❌ Coverage file not found: {$cloverFile}\n";
            return false;
        }

        $xml = simplexml_load_file($cloverFile);
        $coverage = $this->parseCoverage($xml);
        
        return $this->validateCoverage($coverage);
    }

    private function parseCoverage(\SimpleXMLElement $xml): array
    {
        $coverage = [
            'domain' => ['line' => 0, 'function' => 0, 'class' => 0],
            'application' => ['line' => 0, 'function' => 0, 'class' => 0],
            'infrastructure' => ['line' => 0, 'function' => 0, 'class' => 0]
        ];

        foreach ($xml->xpath('//package') as $package) {
            $name = (string) $package['name'];
            $layer = $this->determineLayer($name);
            
            if (!$layer) continue;

            $metrics = $package->metrics;
            $coverage[$layer]['line'] = $this->calculatePercentage(
                (int) $metrics['coveredstatements'],
                (int) $metrics['statements']
            );
            $coverage[$layer]['function'] = $this->calculatePercentage(
                (int) $metrics['coveredmethods'],
                (int) $metrics['methods']
            );
            $coverage[$layer]['class'] = $this->calculatePercentage(
                (int) $metrics['coveredclasses'],
                (int) $metrics['classes']
            );
        }

        return $coverage;
    }

    private function determineLayer(string $packageName): ?string
    {
        if (str_contains($packageName, 'Domain')) return 'domain';
        if (str_contains($packageName, 'Application')) return 'application';
        if (str_contains($packageName, 'Infrastructure')) return 'infrastructure';
        return null;
    }

    private function calculatePercentage(int $covered, int $total): float
    {
        return $total > 0 ? ($covered / $total) * 100 : 0;
    }

    private function validateCoverage(array $coverage): bool
    {
        $allPassed = true;
        
        echo "\n📊 Coverage Analysis by Layer:\n";
        echo "================================\n";

        foreach ($coverage as $layer => $metrics) {
            echo "\n🏗️  " . ucfirst($layer) . " Layer:\n";
            
            foreach ($metrics as $type => $actual) {
                $required = $this->thresholds[$layer][$type];
                $status = $actual >= $required ? '✅' : '❌';
                
                echo sprintf(
                    "%s %s: %.1f%% (required: %d%%)\n",
                    $status,
                    ucfirst($type),
                    $actual,
                    $required
                );
                
                if ($actual < $required) {
                    $allPassed = false;
                }
            }
        }

        echo "\n" . ($allPassed ? "✅ All thresholds met!" : "❌ Some thresholds not met") . "\n";
        return $allPassed;
    }
}

// Usage
$analyzer = new CoverageAnalyzer();
$success = $analyzer->analyze('storage/coverage/clover.xml');
exit($success ? 0 : 1);
```

### Configuración Mobile (React Native + Jest)

#### Jest Coverage for React Native

```javascript
// jest.config.js
module.exports = {
   preset: "react-native",
   collectCoverage: true,
   coverageDirectory: "coverage",
   coverageReporters: ["text", "html", "lcov"],

   collectCoverageFrom: [
      "src/**/*.{ts,tsx}",
      "!src/**/*.d.ts",
      "!src/**/*.test.{ts,tsx}",
      "!src/**/*.stories.{ts,tsx}",
      "!src/__tests__/**/*",
      "!src/**/__mocks__/**/*",
   ],

   coverageThreshold: {
      global: {
         branches: 75,
         functions: 80,
         lines: 80,
         statements: 80,
      },
      "./src/components/": {
         branches: 85,
         functions: 90,
         lines: 90,
         statements: 90,
      },
      "./src/screens/": {
         branches: 80,
         functions: 85,
         lines: 85,
         statements: 85,
      },
      "./src/navigation/": {
         branches: 70,
         functions: 75,
         lines: 75,
         statements: 75,
      },
   },

   transformIgnorePatterns: [
      "node_modules/(?!(react-native|@react-native|@react-navigation)/)",
   ],
};
```

## ¿Qué debo hacer?

### 1. Configurar herramientas de coverage

#### Frontend Setup

```bash
# Instalar dependencias si no están
npm install --save-dev @istanbuljs/nyc-config-typescript

# Ejecutar coverage
npm run test:coverage

# Ver reporte HTML
npm run coverage:open
```

#### Backend Setup

```bash
# Generar coverage con PHPUnit
./vendor/bin/phpunit --coverage-html storage/coverage/html

# Ejecutar análisis personalizado
php scripts/analyze-coverage.php

# Ver reporte en navegador
open storage/coverage/html/index.html
```

### 2. Establecer estándares de coverage por proyecto

```typescript
// src/coverage/standards.ts
export interface CoverageStandards {
   critical: {
      statements: 95;
      branches: 90;
      functions: 95;
      lines: 95;
   };
   important: {
      statements: 85;
      branches: 80;
      functions: 85;
      lines: 85;
   };
   normal: {
      statements: 75;
      branches: 70;
      functions: 75;
      lines: 75;
   };
}

export const PROJECT_COVERAGE_RULES = {
   "src/domain/": "critical",
   "src/components/": "important",
   "src/services/": "important",
   "src/utils/": "critical",
   "src/hooks/": "important",
   "src/pages/": "normal",
} as const;
```

### 3. Integrar coverage con CI/CD

#### GitHub Actions Workflow

```yaml
# .github/workflows/coverage.yml
name: Coverage Analysis

on:
   push:
      branches: [main, develop]
   pull_request:
      branches: [main]

jobs:
   frontend-coverage:
      runs-on: ubuntu-latest
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
           run: npm run test:coverage:ci

         - name: Upload coverage to Codecov
           uses: codecov/codecov-action@v3
           with:
              file: ./frontend/coverage/lcov.info
              flags: frontend
              name: frontend-coverage

         - name: Coverage Comment
           uses: romeovs/lcov-reporter-action@v0.3.1
           with:
              lcov-file: ./frontend/coverage/lcov.info
              github-token: ${{ secrets.GITHUB_TOKEN }}

   backend-coverage:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v4

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"
              coverage: xdebug

         - name: Install dependencies
           working-directory: ./backend
           run: composer install --prefer-dist --no-progress

         - name: Run tests with coverage
           working-directory: ./backend
           run: |
              php artisan test --coverage-clover storage/coverage/clover.xml
              php scripts/analyze-coverage.php

         - name: Upload coverage to Codecov
           uses: codecov/codecov-action@v3
           with:
              file: ./backend/storage/coverage/clover.xml
              flags: backend
              name: backend-coverage
```

### 4. Configurar reportes automáticos

#### Coverage Badge Generation

```bash
# scripts/generate-badges.sh
#!/bin/bash

# Frontend coverage badge
FRONTEND_COVERAGE=$(node -e "
const fs = require('fs');
const summary = JSON.parse(fs.readFileSync('frontend/coverage/coverage-summary.json'));
console.log(summary.total.lines.pct);
")

curl -o frontend/coverage/badge.svg \
  "https://img.shields.io/badge/coverage-${FRONTEND_COVERAGE}%25-brightgreen"

# Backend coverage badge
BACKEND_COVERAGE=$(php -r "
\$xml = simplexml_load_file('backend/storage/coverage/clover.xml');
\$metrics = \$xml->xpath('//metrics[@elements and @coveredelements]')[0];
\$coverage = (\$metrics['coveredelements'] / \$metrics['elements']) * 100;
echo round(\$coverage, 1);
")

curl -o backend/storage/coverage/badge.svg \
  "https://img.shields.io/badge/coverage-${BACKEND_COVERAGE}%25-brightgreen"
```

### 5. Monitoreo continuo de coverage

#### Coverage Trend Analysis

```typescript
// scripts/coverage-trend.ts
import fs from "fs";
import path from "path";

interface CoverageData {
   timestamp: string;
   statements: number;
   branches: number;
   functions: number;
   lines: number;
}

class CoverageTrendAnalyzer {
   private historyFile = "coverage-history.json";

   saveCoverageData(coverage: Omit<CoverageData, "timestamp">): void {
      const history = this.loadHistory();
      const newEntry: CoverageData = {
         ...coverage,
         timestamp: new Date().toISOString(),
      };

      history.push(newEntry);

      // Keep only last 100 entries
      if (history.length > 100) {
         history.splice(0, history.length - 100);
      }

      fs.writeFileSync(this.historyFile, JSON.stringify(history, null, 2));
   }

   analyzetrend(days: number = 30): void {
      const history = this.loadHistory();
      const cutoff = new Date();
      cutoff.setDate(cutoff.getDate() - days);

      const recentData = history.filter((entry) =>
         new Date(entry.timestamp) >= cutoff
      );

      if (recentData.length < 2) {
         console.log("⚠️  Insufficient data for trend analysis");
         return;
      }

      const first = recentData[0];
      const last = recentData[recentData.length - 1];

      console.log(`\n📈 Coverage Trend (${days} days):`);
      console.log("================================");

      Object.keys(first).forEach((key) => {
         if (key === "timestamp") return;

         const change = last[key] - first[key];
         const trend = change > 0 ? "📈" : change < 0 ? "📉" : "➡️";

         console.log(
            `${trend} ${key}: ${last[key].toFixed(1)}% (${
               change > 0 ? "+" : ""
            }${change.toFixed(1)})`,
         );
      });
   }

   private loadHistory(): CoverageData[] {
      if (!fs.existsSync(this.historyFile)) {
         return [];
      }

      return JSON.parse(fs.readFileSync(this.historyFile, "utf8"));
   }
}

// Usage
const analyzer = new CoverageTrendAnalyzer();

// Save current coverage (called from CI)
if (process.argv[2] === "save") {
   const coverageFile = process.argv[3];
   const coverage = JSON.parse(fs.readFileSync(coverageFile, "utf8"));
   analyzer.saveCoverageData(coverage.total);
}

// Analyze trend
if (process.argv[2] === "analyze") {
   const days = parseInt(process.argv[3]) || 30;
   analyzer.analyzetrend(days);
}
```

## Tips

- **No busques 100%**: 85-90% es un buen objetivo para la mayoría de proyectos
- **Prioriza calidad sobre cantidad**: Mejor tests significativos que coverage
  artificial
- **Excluye archivos apropiados**: Configuración, mocks, generated code
- **Monitorea tendencias**: El coverage debería mantenerse o mejorar con el
  tiempo
- **Coverage != calidad**: Un test que no valida comportamiento no aporta valor
- **Diferencia por tipo de código**: Código crítico necesita más coverage
- **Automatiza la validación**: Integra checks de coverage en CI/CD
- **Reporta regularmente**: Compartir métricas con el equipo motiva mejora

## Ejemplos de Reportes

### Reporte de Coverage Detallado

```markdown
# Coverage Report - Sprint 23

## 📊 Overall Coverage

- **Statements**: 87.3% ✅ (target: 85%)
- **Branches**: 82.1% ✅ (target: 80%)
- **Functions**: 89.7% ✅ (target: 85%)
- **Lines**: 86.8% ✅ (target: 85%)

## 🎯 Coverage by Module

### Frontend (React)

| Module     | Statements | Branches | Functions | Lines | Status |
| ---------- | ---------- | -------- | --------- | ----- | ------ |
| Components | 92.1%      | 87.3%    | 94.2%     | 91.8% | ✅     |
| Hooks      | 89.4%      | 84.2%    | 91.7%     | 88.9% | ✅     |
| Services   | 81.2%      | 76.8%    | 83.4%     | 80.9% | ✅     |
| Utils      | 96.7%      | 93.2%    | 97.1%     | 96.3% | ✅     |

### Backend (Laravel)

| Layer          | Statements | Branches | Functions | Lines | Status |
| -------------- | ---------- | -------- | --------- | ----- | ------ |
| Domain         | 94.8%      | 91.2%    | 96.1%     | 94.3% | ✅     |
| Application    | 88.7%      | 83.9%    | 90.2%     | 88.1% | ✅     |
| Infrastructure | 76.3%      | 71.8%    | 78.9%     | 75.7% | ✅     |

## 🔍 Areas Needing Attention

1. **UserService.validateEmail()** - 45% coverage
2. **PaymentController.processRefund()** - 62% coverage
3. **AuthMiddleware.handleExpired()** - 58% coverage

## 📈 Trend (Last 30 days)

- Statements: +2.1% 📈
- Branches: +1.7% 📈
- Functions: +3.2% 📈
- Lines: +2.4% 📈

## 🎯 Next Sprint Goals

- [ ] Increase PaymentController coverage to 80%
- [ ] Add integration tests for email service
- [ ] Improve error handling test coverage
```

### Coverage Quality Gates

```yaml
# quality-gates.yml
coverage_gates:
   pull_request:
      min_coverage_delta: 0 # No decrease allowed
      min_total_coverage: 80

   release:
      min_coverage: 85
      critical_modules:
         - domain/*: 95
         - security/*: 98
         - payment/*: 90

   hotfix:
      min_coverage: 75 # Relaxed for emergencies
      require_coverage_tests: true
```

## Navegación

[⬅️ Ejemplos de Testing por Lenguaje](./ejemplos-testing-por-lenguaje.md) |
[Inicio](../../README.md) |
[Testing funcional automatizado ➡️](./testing-funcional-automatizado.md)
