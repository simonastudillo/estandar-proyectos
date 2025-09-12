# Testing de Regresión

## ¿Qué es?

El testing de regresión es un tipo de prueba que se ejecuta para verificar que los cambios recientes en el código (nuevas funcionalidades, correcciones de bugs, o modificaciones) no hayan afectado negativamente las funcionalidades existentes que anteriormente funcionaban correctamente. Es fundamental para mantener la estabilidad del sistema durante el desarrollo continuo.

## ¿Por qué es importante?

- **Estabilidad del sistema**: Garantiza que nuevos desarrollos no rompan funcionalidades existentes
- **Confianza en deployments**: Reduce el riesgo de introducir bugs en producción
- **Calidad continua**: Mantiene un nivel de calidad constante a lo largo del tiempo
- **Detección temprana**: Identifica problemas antes de que lleguen a usuarios finales
- **Reducción de costos**: Es más económico detectar regresiones en desarrollo que en producción
- **Automatización eficiente**: Permite implementar pipelines de CI/CD robustos

## ¿Qué debe incluir?

### Estrategias de Regresión

#### Regresión Completa

- Ejecuta toda la suite de pruebas existente
- Se realiza en releases mayores
- Cobertura máxima pero tiempo elevado
- Recomendada para cambios arquitectónicos

#### Regresión Selectiva

- Ejecuta solo pruebas relacionadas con los cambios
- Basada en análisis de impacto de código
- Balance entre cobertura y tiempo
- Ideal para desarrollo ágil

#### Regresión de Humo (Smoke Testing)

- Pruebas básicas de funcionalidad crítica
- Ejecutión rápida y frecuente
- Detecta problemas mayores inmediatamente
- Se ejecuta después de cada deployment

### Tipos de Regresión

#### Regresión de Unidad

- Pruebas unitarias que validan componentes individuales
- Se ejecutan con cada commit
- Feedback inmediato al desarrollador
- Alta velocidad de ejecución

#### Regresión de Integración

- Verifica interfaces entre módulos
- Valida comunicación con servicios externos
- Incluye pruebas de API y base de datos
- Detecta problemas de compatibilidad

#### Regresión End-to-End

- Simula flujos completos de usuario
- Verifica funcionalidad desde la perspectiva del usuario final
- Mayor tiempo de ejecución
- Alta confianza en resultados

## ¿Qué debo hacer?

### 1. Configurar automatización de regresión

```yaml
# .github/workflows/regression-testing.yml
name: Regression Testing

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  smoke-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "18"

      - name: Install dependencies
        run: npm ci

      - name: Run smoke tests
        run: npm run test:smoke
        timeout-minutes: 5

  regression-tests:
    needs: smoke-tests
    runs-on: ubuntu-latest
    strategy:
      matrix:
        test-suite: [unit, integration, e2e]

    steps:
      - uses: actions/checkout@v3

      - name: Setup environment
        run: |
          npm ci
          npm run build

      - name: Run regression suite
        run: npm run test:regression:${{ matrix.test-suite }}
        timeout-minutes: 30

      - name: Upload test results
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: test-results-${{ matrix.test-suite }}
          path: test-results/
```

### 2. Implementar selección inteligente de pruebas

```typescript
// src/testing/regression-analyzer.ts
export class RegressionAnalyzer {
  private changedFiles: string[];
  private testMapping: Map<string, string[]>;

  constructor(changedFiles: string[]) {
    this.changedFiles = changedFiles;
    this.testMapping = this.loadTestMapping();
  }

  getAffectedTests(): string[] {
    const affectedTests = new Set<string>();

    this.changedFiles.forEach((file) => {
      // Obtener tests directamente relacionados
      const directTests = this.getDirectTests(file);
      directTests.forEach((test) => affectedTests.add(test));

      // Obtener tests que dependen del archivo
      const dependentTests = this.getDependentTests(file);
      dependentTests.forEach((test) => affectedTests.add(test));
    });

    return Array.from(affectedTests);
  }

  private getDirectTests(file: string): string[] {
    const testFile = file.replace(/\.(ts|tsx|js|jsx)$/, ".test.$1");
    return this.testMapping.get(testFile) || [];
  }

  private getDependentTests(file: string): string[] {
    const dependents: string[] = [];

    this.testMapping.forEach((tests, testFile) => {
      if (this.importsDependency(testFile, file)) {
        dependents.push(...tests);
      }
    });

    return dependents;
  }

  generateRegressionSuite(): RegressionSuite {
    return {
      smokeTests: this.getSmokeTests(),
      affectedTests: this.getAffectedTests(),
      criticalPaths: this.getCriticalPathTests(),
      estimatedDuration: this.calculateDuration(),
    };
  }
}

export interface RegressionSuite {
  smokeTests: string[];
  affectedTests: string[];
  criticalPaths: string[];
  estimatedDuration: number;
}
```

### 3. Configurar pruebas de humo automáticas

```bash
#!/bin/bash
# scripts/smoke-tests.sh

echo "🔥 Iniciando smoke tests de regresión..."

START_TIME=$(date +%s)

# 1. Verificar que la aplicación arranca
echo "🚀 Verificando inicio de aplicación..."
timeout 30 npm start &
APP_PID=$!

sleep 10

if ! curl -f http://localhost:3000/health > /dev/null 2>&1; then
    echo "❌ Error: La aplicación no responde"
    kill $APP_PID 2>/dev/null
    exit 1
fi

echo "✅ Aplicación iniciada correctamente"

# 2. Smoke tests críticos
echo "🧪 Ejecutando smoke tests..."

# Frontend smoke tests
npm run test:smoke:frontend

# Backend smoke tests
npm run test:smoke:backend

# API smoke tests
npm run test:smoke:api

# 3. Cleanup
kill $APP_PID 2>/dev/null

END_TIME=$(date +%s)
DURATION=$((END_TIME - START_TIME))

echo "✅ Smoke tests completados en ${DURATION}s"
```

### 4. Implementar análisis de impacto

```javascript
// scripts/impact-analysis.js
const fs = require("fs");
const path = require("path");
const { execSync } = require("child_process");

class ImpactAnalyzer {
  constructor() {
    this.dependencyGraph = this.buildDependencyGraph();
  }

  analyzePullRequest(baseBranch = "main") {
    // Obtener archivos modificados
    const changedFiles = this.getChangedFiles(baseBranch);

    // Analizar impacto
    const impactAnalysis = {
      changedFiles,
      affectedModules: this.getAffectedModules(changedFiles),
      recommendedTests: this.getRecommendedTests(changedFiles),
      riskLevel: this.calculateRiskLevel(changedFiles),
      estimatedTestTime: this.estimateTestTime(changedFiles),
    };

    this.generateReport(impactAnalysis);
    return impactAnalysis;
  }

  getChangedFiles(baseBranch) {
    try {
      const output = execSync(`git diff --name-only ${baseBranch}...HEAD`, {
        encoding: "utf8",
      });
      return output
        .trim()
        .split("\n")
        .filter((file) => file);
    } catch (error) {
      console.error("Error obteniendo archivos modificados:", error);
      return [];
    }
  }

  getAffectedModules(changedFiles) {
    const modules = new Set();

    changedFiles.forEach((file) => {
      // Determinar módulo basado en path
      const module = this.fileToModule(file);
      if (module) modules.add(module);

      // Obtener dependencias
      const dependencies = this.getDependencies(file);
      dependencies.forEach((dep) => modules.add(dep));
    });

    return Array.from(modules);
  }

  calculateRiskLevel(changedFiles) {
    let riskScore = 0;

    changedFiles.forEach((file) => {
      // Archivos críticos tienen mayor peso
      if (this.isCriticalFile(file)) riskScore += 3;
      // Archivos de configuración
      else if (this.isConfigFile(file)) riskScore += 2;
      // Archivos normales
      else riskScore += 1;
    });

    if (riskScore >= 10) return "HIGH";
    if (riskScore >= 5) return "MEDIUM";
    return "LOW";
  }

  generateReport(analysis) {
    const report = `
# Análisis de Impacto de Regresión

## Resumen
- **Archivos modificados**: ${analysis.changedFiles.length}
- **Módulos afectados**: ${analysis.affectedModules.length}
- **Nivel de riesgo**: ${analysis.riskLevel}
- **Tiempo estimado de testing**: ${analysis.estimatedTestTime} minutos

## Archivos Modificados
${analysis.changedFiles.map((file) => `- ${file}`).join("\n")}

## Módulos Afectados
${analysis.affectedModules.map((module) => `- ${module}`).join("\n")}

## Pruebas Recomendadas
${analysis.recommendedTests.map((test) => `- ${test}`).join("\n")}

## Recomendaciones
${this.getRecommendations(analysis)}
      `;

    fs.writeFileSync("regression-impact-report.md", report);
    console.log("📊 Reporte generado: regression-impact-report.md");
  }
}

// Ejecutar análisis si se llama directamente
if (require.main === module) {
  const analyzer = new ImpactAnalyzer();
  analyzer.analyzePullRequest();
}

module.exports = ImpactAnalyzer;
```

### 5. Configurar regression testing en CI/CD

```typescript
// src/testing/regression-config.ts
export interface RegressionConfig {
  triggers: {
    onCommit: boolean;
    onPullRequest: boolean;
    onRelease: boolean;
    scheduled: string; // cron expression
  };
  testSuites: {
    smoke: {
      enabled: boolean;
      timeout: number;
      failFast: boolean;
    };
    unit: {
      enabled: boolean;
      coverage: number;
      parallel: boolean;
    };
    integration: {
      enabled: boolean;
      databases: string[];
      services: string[];
    };
    e2e: {
      enabled: boolean;
      browsers: string[];
      viewports: string[];
    };
  };
  reporting: {
    slack: {
      enabled: boolean;
      webhook: string;
      channels: string[];
    };
    email: {
      enabled: boolean;
      recipients: string[];
    };
  };
}

export const defaultConfig: RegressionConfig = {
  triggers: {
    onCommit: true,
    onPullRequest: true,
    onRelease: true,
    scheduled: "0 2 * * *", // Daily at 2 AM
  },
  testSuites: {
    smoke: {
      enabled: true,
      timeout: 300, // 5 minutes
      failFast: true,
    },
    unit: {
      enabled: true,
      coverage: 80,
      parallel: true,
    },
    integration: {
      enabled: true,
      databases: ["mysql", "redis"],
      services: ["api", "auth"],
    },
    e2e: {
      enabled: true,
      browsers: ["chrome", "firefox"],
      viewports: ["desktop", "mobile"],
    },
  },
  reporting: {
    slack: {
      enabled: true,
      webhook: process.env.SLACK_WEBHOOK!,
      channels: ["#testing", "#dev-alerts"],
    },
    email: {
      enabled: false,
      recipients: [],
    },
  },
};
```

## Tips

- **Automatización primero**: Las pruebas de regresión deben estar completamente automatizadas
- **Priorización inteligente**: Enfócate en funcionalidades críticas y de alto riesgo
- **Feedback rápido**: Ejecuta smoke tests primero para detectar problemas mayores
- **Análisis de impacto**: Usa herramientas para determinar qué probar basado en cambios
- **Paralelización**: Ejecuta pruebas en paralelo para reducir tiempo total
- **Historial de resultados**: Mantén un registro de resultados para análisis de tendencias
- **Mantenimiento continuo**: Actualiza y mantén las pruebas de regresión regularmente
- **Métricas de calidad**: Mide y reporta métricas de efectividad de las pruebas

## Ejemplos

### Script de Regresión Automatizada

```bash
#!/bin/bash
# scripts/automated-regression.sh

echo "🔄 Iniciando regresión automatizada..."

# Configuración
TEST_ENV=${TEST_ENV:-"staging"}
PARALLEL_JOBS=${PARALLEL_JOBS:-4}
TIMEOUT=${TIMEOUT:-3600} # 1 hour

# Crear directorio de resultados
RESULTS_DIR="regression-results-$(date +%Y%m%d_%H%M%S)"
mkdir -p $RESULTS_DIR

# 1. Análisis de impacto
echo "📊 Analizando impacto de cambios..."
node scripts/impact-analysis.js > $RESULTS_DIR/impact-analysis.json

# 2. Smoke tests (rápidos)
echo "🔥 Ejecutando smoke tests..."
npm run test:smoke 2>&1 | tee $RESULTS_DIR/smoke-tests.log

if [ ${PIPESTATUS[0]} -ne 0 ]; then
    echo "❌ Smoke tests fallaron. Deteniendo regresión."
    exit 1
fi

# 3. Pruebas de regresión por categoría
echo "🧪 Ejecutando pruebas de regresión..."

# Unit tests (paralelas)
echo "⚡ Pruebas unitarias..."
npm run test:unit -- --maxWorkers=$PARALLEL_JOBS \
  --coverage --json --outputFile=$RESULTS_DIR/unit-results.json

# Integration tests
echo "🔗 Pruebas de integración..."
npm run test:integration -- --json \
  --outputFile=$RESULTS_DIR/integration-results.json

# API tests
echo "🌐 Pruebas de API..."
npm run test:api -- --reporter=json \
  --reporter-options output=$RESULTS_DIR/api-results.json

# E2E tests críticos
echo "🎭 Pruebas E2E críticas..."
npm run test:e2e:critical -- --reporter=json \
  --reporter-options output=$RESULTS_DIR/e2e-results.json

# 4. Generar reporte consolidado
echo "📈 Generando reporte consolidado..."
node scripts/generate-regression-report.js $RESULTS_DIR

# 5. Verificar criterios de salida
echo "✅ Verificando criterios de calidad..."
EXIT_CODE=0

# Verificar cobertura mínima
COVERAGE=$(jq '.coverageMap.total.statements.pct' $RESULTS_DIR/unit-results.json)
if (( $(echo "$COVERAGE < 80" | bc -l) )); then
    echo "❌ Cobertura insuficiente: $COVERAGE% (mínimo 80%)"
    EXIT_CODE=1
fi

# Verificar que no hay tests fallidos críticos
FAILED_CRITICAL=$(jq '.numFailedTests' $RESULTS_DIR/e2e-results.json)
if [ "$FAILED_CRITICAL" -gt 0 ]; then
    echo "❌ $FAILED_CRITICAL pruebas críticas fallaron"
    EXIT_CODE=1
fi

# 6. Notificaciones
if [ $EXIT_CODE -eq 0 ]; then
    echo "✅ Regresión completada exitosamente"
    node scripts/notify-success.js $RESULTS_DIR
else
    echo "❌ Regresión falló. Ver: $RESULTS_DIR/"
    node scripts/notify-failure.js $RESULTS_DIR
fi

exit $EXIT_CODE
```

### Suite de Smoke Tests

```typescript
// tests/smoke/smoke-suite.test.ts
describe("Smoke Tests - Regresión", () => {
  const TIMEOUT = 30000;

  describe("Application Startup", () => {
    it(
      "should start application successfully",
      async () => {
        const response = await fetch("http://localhost:3000/health");
        expect(response.status).toBe(200);
      },
      TIMEOUT
    );

    it(
      "should connect to database",
      async () => {
        const response = await fetch("http://localhost:3000/api/health/db");
        expect(response.status).toBe(200);
      },
      TIMEOUT
    );
  });

  describe("Critical API Endpoints", () => {
    it(
      "should authenticate user",
      async () => {
        const response = await fetch("http://localhost:3000/api/auth/login", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            email: "test@example.com",
            password: "password123",
          }),
        });
        expect(response.status).toBe(200);
      },
      TIMEOUT
    );

    it(
      "should fetch user data",
      async () => {
        // Asumir token válido
        const token = process.env.TEST_AUTH_TOKEN;
        const response = await fetch("http://localhost:3000/api/user/profile", {
          headers: { Authorization: `Bearer ${token}` },
        });
        expect(response.status).toBe(200);
      },
      TIMEOUT
    );
  });

  describe("Critical User Flows", () => {
    it(
      "should complete user registration flow",
      async () => {
        const userData = {
          name: `Test User ${Date.now()}`,
          email: `test${Date.now()}@example.com`,
          password: "password123",
        };

        const response = await fetch("http://localhost:3000/api/register", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(userData),
        });

        expect(response.status).toBe(201);
        const result = await response.json();
        expect(result.data.email).toBe(userData.email);
      },
      TIMEOUT
    );
  });
});
```

### Reporte de Regresión

```typescript
// scripts/generate-regression-report.ts
import fs from "fs";
import path from "path";

interface TestResults {
  unit: any;
  integration: any;
  api: any;
  e2e: any;
}

export class RegressionReporter {
  private results: TestResults;
  private resultsDir: string;

  constructor(resultsDir: string) {
    this.resultsDir = resultsDir;
    this.results = this.loadResults();
  }

  generateReport(): void {
    const report = {
      summary: this.generateSummary(),
      coverage: this.analyzeCoverage(),
      performance: this.analyzePerformance(),
      failures: this.analyzeFailures(),
      trends: this.analyzeTrends(),
      recommendations: this.generateRecommendations(),
    };

    this.saveHTMLReport(report);
    this.saveJSONReport(report);
    this.saveSlackReport(report);
  }

  private generateSummary() {
    const totalTests = this.getTotalTests();
    const passedTests = this.getPassedTests();
    const failedTests = this.getFailedTests();

    return {
      totalTests,
      passedTests,
      failedTests,
      passRate: (passedTests / totalTests) * 100,
      duration: this.getTotalDuration(),
      timestamp: new Date().toISOString(),
    };
  }

  private saveHTMLReport(report: any): void {
    const html = `
<!DOCTYPE html>
<html>
<head>
    <title>Reporte de Regresión</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .summary { background: #f5f5f5; padding: 20px; border-radius: 5px; }
        .pass { color: green; }
        .fail { color: red; }
        .chart { margin: 20px 0; }
        table { border-collapse: collapse; width: 100%; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
    </style>
</head>
<body>
    <h1>📊 Reporte de Regresión</h1>
    
    <div class="summary">
        <h2>Resumen</h2>
        <p><strong>Total de pruebas:</strong> ${report.summary.totalTests}</p>
        <p><strong>Exitosas:</strong> <span class="pass">${
          report.summary.passedTests
        }</span></p>
        <p><strong>Fallidas:</strong> <span class="fail">${
          report.summary.failedTests
        }</span></p>
        <p><strong>Tasa de éxito:</strong> ${report.summary.passRate.toFixed(
          2
        )}%</p>
        <p><strong>Duración:</strong> ${report.summary.duration}s</p>
    </div>

    <h2>Cobertura de Código</h2>
    <table>
        <tr><th>Métrica</th><th>Porcentaje</th><th>Estado</th></tr>
        <tr>
            <td>Statements</td>
            <td>${report.coverage.statements}%</td>
            <td>${report.coverage.statements >= 80 ? "✅" : "❌"}</td>
        </tr>
        <tr>
            <td>Branches</td>
            <td>${report.coverage.branches}%</td>
            <td>${report.coverage.branches >= 70 ? "✅" : "❌"}</td>
        </tr>
        <tr>
            <td>Functions</td>
            <td>${report.coverage.functions}%</td>
            <td>${report.coverage.functions >= 80 ? "✅" : "❌"}</td>
        </tr>
    </table>

    ${this.generateFailuresSection(report.failures)}
    ${this.generateRecommendationsSection(report.recommendations)}
</body>
</html>`;

    fs.writeFileSync(
      path.join(this.resultsDir, "regression-report.html"),
      html
    );
  }
}
```

---

## Navegación

**Progreso en Testing y Quality Assurance:**

- ✅ [Testing y QA](./testing-qa.md)
- ✅ [Tipos de Pruebas](./tipos-pruebas.md)
- ✅ [Testing Funcional Automatizado](./testing-funcional-automatizado.md)
- ✅ **Testing de Regresión** ← Estás aquí
- ⏭️ [Checklists QA](./checklists-qa.md)
- ⏭️ [Pruebas de Aceptación del Usuario](./pruebas-aceptacion-usuario.md)
- ⏭️ [Gestión de Reportes de Errores](./gestion-reportes-errores.md)
- ⏭️ [Testing de Performance y Carga](./testing-performance-carga.md)
- ⏭️ [Testing de Seguridad OWASP](./testing-seguridad-owasp.md)
- ⏭️ [Testing de Usabilidad](./testing-usabilidad.md)
- ⏭️ [Code Review y Refactoring](./code-review-refactoring.md)
- ⏭️ [Auditoría de Calidad de Código](./auditoria-calidad-codigo.md)

---

### Siguiente Paso

Continúa con [**Checklists QA**](./checklists-qa.md)

[⬅️ Tipos de Pruebas](./tipos-pruebas.md) | [🏠 README Principal](../../README.md) | [➡️ Checklists QA](./checklists-qa.md)
