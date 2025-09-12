# Checklists QA

## ¿Qué es?

Los checklists de Quality Assurance son listas de verificación sistemáticas que
garantizan que todas las actividades de testing y validación se ejecuten de
manera consistente y completa. Estos checklists sirven como guías estructuradas
para asegurar que no se omitan aspectos críticos durante el proceso de
aseguramiento de calidad.

## ¿Por qué es importante?

- **Consistencia**: Garantiza que el proceso de QA sea uniforme entre diferentes
  proyectos y equipos
- **Completitud**: Asegura que no se omitan verificaciones importantes
- **Trazabilidad**: Proporciona evidencia documentada de las actividades de QA
  realizadas
- **Eficiencia**: Acelera el proceso de testing al tener una guía estructurada
- **Reducción de errores**: Minimiza la posibilidad de pasar por alto defectos
  críticos
- **Estandardización**: Establece un marco común para todo el equipo de QA
- **Mejora continua**: Permite identificar y refinar procesos basados en
  experiencias anteriores

## ¿Qué debe incluir?

### Checklists por Fase del Proyecto

#### Checklist de Pre-desarrollo

- Validación de requerimientos
- Revisión de criterios de aceptación
- Definición de estrategia de testing
- Configuración de ambiente de testing

#### Checklist de Desarrollo

- Code review completado
- Pruebas unitarias ejecutadas
- Cobertura de código verificada
- Estándares de codificación cumplidos

#### Checklist de Pre-release

- Todas las pruebas de regresión ejecutadas
- Pruebas de performance validadas
- Seguridad verificada según OWASP
- Documentación actualizada

### Checklists por Tipo de Testing

#### Testing Funcional

- Casos de uso principales validados
- Flujos alternativos probados
- Manejo de errores verificado
- Validaciones de entrada confirmadas

#### Testing No Funcional

- Performance dentro de parámetros aceptables
- Seguridad evaluada y aprobada
- Usabilidad validada con usuarios
- Compatibilidad verificada

## ¿Qué debo hacer?

### 1. Crear checklist de pre-desarrollo

```markdown
# 📋 Checklist Pre-Desarrollo

## Análisis de Requerimientos

- [ ] Requerimientos funcionales definidos y claros
- [ ] Requerimientos no funcionales especificados
- [ ] Criterios de aceptación documentados
- [ ] User stories priorizadas y estimadas
- [ ] Dependencias identificadas y documentadas
- [ ] Riesgos evaluados y mitigaciones definidas

## Planificación de Testing

- [ ] Estrategia de testing definida
- [ ] Tipos de pruebas identificadas
- [ ] Ambientes de testing configurados
- [ ] Datos de prueba preparados
- [ ] Herramientas de testing seleccionadas
- [ ] Cronograma de testing establecido

## Configuración de Proyecto

- [ ] Repositorio de código configurado
- [ ] Ramas de desarrollo establecidas
- [ ] CI/CD pipeline configurado
- [ ] Estándares de código definidos
- [ ] Code review process establecido
- [ ] Documentación inicial creada

## Criterios de Entrada

- [ ] Todos los requerimientos aprobados por stakeholders
- [ ] Arquitectura técnica definida y revisada
- [ ] Equipo de desarrollo asignado y capacitado
- [ ] Ambientes de desarrollo listos
- [ ] Herramientas y licencias disponibles
```

### 2. Implementar checklist de desarrollo

```typescript
// src/qa/development-checklist.ts
export interface DevelopmentChecklist {
   featureId: string;
   developerId: string;
   date: string;
   checks: {
      codeQuality: CodeQualityChecks;
      testing: TestingChecks;
      documentation: DocumentationChecks;
      security: SecurityChecks;
   };
   approved: boolean;
   reviewedBy: string;
}

export interface CodeQualityChecks {
   lintingPassed: boolean;
   formattingConsistent: boolean;
   noCodeSmells: boolean;
   complexityAcceptable: boolean;
   naming conventions: boolean;
   commentedCode: boolean;
}

export interface TestingChecks {
   unitTestsWritten: boolean;
   unitTestsPassing: boolean;
   coverageTargetMet: boolean;
   integrationTestsIncluded: boolean;
   edgeCasesConsidered: boolean;
   errorHandlingTested: boolean;
}

export class DevelopmentChecklistValidator {
   validateChecklist(checklist: DevelopmentChecklist): ValidationResult {
      const errors: string[] = [];
      const warnings: string[] = [];

      // Validar code quality
      if (!checklist.checks.codeQuality.lintingPassed) {
         errors.push('Linting no ha pasado');
      }

      if (!checklist.checks.codeQuality.noCodeSmells) {
         warnings.push('Se detectaron code smells');
      }

      // Validar testing
      if (!checklist.checks.testing.unitTestsPassing) {
         errors.push('Pruebas unitarias fallando');
      }

      if (!checklist.checks.testing.coverageTargetMet) {
         warnings.push('Cobertura de código por debajo del objetivo');
      }

      // Validar documentación
      if (!checklist.checks.documentation.apiDocumented) {
         warnings.push('API no documentada');
      }

      return {
         isValid: errors.length === 0,
         errors,
         warnings,
         score: this.calculateScore(checklist)
      };
   }

   private calculateScore(checklist: DevelopmentChecklist): number {
      const allChecks = [
         ...Object.values(checklist.checks.codeQuality),
         ...Object.values(checklist.checks.testing),
         ...Object.values(checklist.checks.documentation),
         ...Object.values(checklist.checks.security)
      ];

      const passedChecks = allChecks.filter(check => check === true).length;
      return (passedChecks / allChecks.length) * 100;
   }
}
```

### 3. Crear checklist de release

```yaml
# .github/PULL_REQUEST_TEMPLATE/release_checklist.md
# 🚀 Release Checklist

## Pre-Release Verification
- [ ] All features completed and tested
- [ ] All bugs fixed and verified
- [ ] Performance tests passed
- [ ] Security scan completed
- [ ] Documentation updated
- [ ] Database migrations tested
- [ ] Environment variables updated
- [ ] Third-party integrations verified

## Testing Completion
- [ ] Unit tests: 100% passing
- [ ] Integration tests: 100% passing
- [ ] E2E tests: 100% passing
- [ ] Regression tests: 100% passing
- [ ] Performance tests: Within acceptable limits
- [ ] Security tests: No critical vulnerabilities
- [ ] Accessibility tests: WCAG 2.1 compliant

## Code Quality
- [ ] Code review completed and approved
- [ ] Static analysis passed
- [ ] Code coverage >= 80%
- [ ] No critical security vulnerabilities
- [ ] Documentation updated
- [ ] CHANGELOG updated

## Deployment Readiness
- [ ] Deployment scripts tested
- [ ] Database backup completed
- [ ] Rollback plan documented
- [ ] Monitoring alerts configured
- [ ] Feature flags configured
- [ ] Load balancer configured

## Post-Release Verification
- [ ] Health checks passing
- [ ] Key metrics monitoring
- [ ] User acceptance testing
- [ ] Performance monitoring
- [ ] Error rate monitoring
- [ ] Business metrics tracking

## Stakeholder Approval
- [ ] Product Owner approval
- [ ] Technical Lead approval
- [ ] QA Lead approval
- [ ] Security team approval (if applicable)
- [ ] DevOps team approval
```

### 4. Automatizar validación de checklists

```javascript
// scripts/validate-checklist.js
const fs = require("fs");
const yaml = require("js-yaml");

class ChecklistValidator {
   constructor(configPath) {
      this.config = yaml.safeLoad(fs.readFileSync(configPath, "utf8"));
   }

   async validateReleaseChecklist() {
      const results = {
         passed: [],
         failed: [],
         warnings: [],
         score: 0,
      };

      console.log("🔍 Validando checklist de release...");

      // Validar tests
      await this.validateTests(results);

      // Validar cobertura
      await this.validateCoverage(results);

      // Validar seguridad
      await this.validateSecurity(results);

      // Validar performance
      await this.validatePerformance(results);

      // Calcular score final
      results.score = this.calculateFinalScore(results);

      this.generateReport(results);
      return results;
   }

   async validateTests(results) {
      try {
         // Verificar que todas las pruebas pasen
         const { execSync } = require("child_process");
         execSync("npm test", { stdio: "pipe" });
         results.passed.push("✅ Todas las pruebas pasan");
      } catch (error) {
         results.failed.push("❌ Hay pruebas fallando");
      }
   }

   async validateCoverage(results) {
      try {
         const coverage = JSON.parse(
            fs.readFileSync("coverage/coverage-summary.json", "utf8"),
         );

         const totalCoverage = coverage.total.statements.pct;
         const minCoverage = this.config.quality.minCoverage || 80;

         if (totalCoverage >= minCoverage) {
            results.passed.push(
               `✅ Cobertura: ${totalCoverage}% (>= ${minCoverage}%)`,
            );
         } else {
            results.failed.push(
               `❌ Cobertura insuficiente: ${totalCoverage}% (< ${minCoverage}%)`,
            );
         }
      } catch (error) {
         results.failed.push("❌ No se pudo verificar cobertura");
      }
   }

   async validateSecurity(results) {
      try {
         const { execSync } = require("child_process");
         const auditOutput = execSync("npm audit --json", { stdio: "pipe" });
         const audit = JSON.parse(auditOutput.toString());

         if (audit.metadata.vulnerabilities.critical > 0) {
            results.failed.push(
               `❌ ${audit.metadata.vulnerabilities.critical} vulnerabilidades críticas`,
            );
         } else if (audit.metadata.vulnerabilities.high > 0) {
            results.warnings.push(
               `⚠️ ${audit.metadata.vulnerabilities.high} vulnerabilidades altas`,
            );
         } else {
            results.passed.push("✅ Sin vulnerabilidades críticas");
         }
      } catch (error) {
         results.warnings.push("⚠️ No se pudo ejecutar audit de seguridad");
      }
   }

   generateReport(results) {
      const report = `
# 📊 Reporte de Validación de Checklist

## Resumen
- **Score**: ${results.score}/100
- **Checks pasados**: ${results.passed.length}
- **Checks fallidos**: ${results.failed.length}
- **Advertencias**: ${results.warnings.length}

## ✅ Checks Exitosos
${results.passed.map((item) => `- ${item}`).join("\n")}

## ❌ Checks Fallidos
${results.failed.map((item) => `- ${item}`).join("\n")}

## ⚠️ Advertencias
${results.warnings.map((item) => `- ${item}`).join("\n")}

## Recomendaciones
${this.generateRecommendations(results)}
      `;

      fs.writeFileSync("checklist-validation-report.md", report);
      console.log("📋 Reporte generado: checklist-validation-report.md");
   }
}

module.exports = ChecklistValidator;
```

### 5. Crear checklist de code review

```markdown
# 🔍 Code Review Checklist

## Funcionalidad

- [ ] El código hace lo que se supone que debe hacer
- [ ] La lógica es correcta y fácil de entender
- [ ] No hay código duplicado o redundante
- [ ] Los casos edge están manejados apropiadamente
- [ ] Los errores son manejados correctamente

## Arquitectura y Diseño

- [ ] El código sigue los principios SOLID
- [ ] La arquitectura es consistente con el resto del proyecto
- [ ] Las abstracciones son apropiadas
- [ ] No hay violaciones de dependencias
- [ ] El código es extensible y mantenible

## Legibilidad y Estilo

- [ ] El código es fácil de leer y entender
- [ ] Los nombres de variables y funciones son descriptivos
- [ ] El formato y estilo es consistente
- [ ] Los comentarios son útiles y necesarios
- [ ] No hay código comentado innecesario

## Performance

- [ ] No hay problemas obvios de performance
- [ ] Las consultas a BD son eficientes
- [ ] No hay memory leaks potenciales
- [ ] Los algoritmos son eficientes
- [ ] El código no bloquea el thread principal

## Seguridad

- [ ] No hay vulnerabilidades obvias
- [ ] La validación de entrada es adecuada
- [ ] Los datos sensibles están protegidos
- [ ] La autenticación/autorización es correcta
- [ ] No hay información sensible en logs

## Testing

- [ ] Hay pruebas para el nuevo código
- [ ] Las pruebas cubren casos edge
- [ ] Las pruebas son claras y mantenibles
- [ ] No se rompen pruebas existentes
- [ ] Los mocks son apropiados
```

## Tips

- **Automatización**: Automatiza las validaciones donde sea posible
- **Granularidad**: Mantén checklists específicos para diferentes contextos
- **Evolución**: Actualiza los checklists basado en lecciones aprendidas
- **Trazabilidad**: Documenta quién completó cada checklist y cuándo
- **Integración**: Integra checklists en tu workflow de desarrollo
- **Educación**: Usa checklists como herramientas de entrenamiento para nuevo
  personal
- **Flexibilidad**: Adapta checklists según el tipo y complejidad del proyecto
- **Retroalimentación**: Recopila feedback del equipo para mejorar checklists

## Ejemplos

### Checklist Automatizado en GitHub Actions

```yaml
# .github/workflows/qa-checklist.yml
name: QA Checklist Validation

on:
   pull_request:
      types: [opened, synchronize]

jobs:
   validate-checklist:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Install dependencies
           run: npm ci

         - name: Validate Code Quality Checklist
           run: |
              echo "🔍 Validando calidad de código..."
              npm run lint
              npm run type-check

         - name: Validate Testing Checklist
           run: |
              echo "🧪 Validando testing..."
              npm run test:coverage
              node scripts/validate-test-requirements.js

         - name: Validate Security Checklist
           run: |
              echo "🔒 Validando seguridad..."
              npm audit --audit-level moderate
              npm run security-scan

         - name: Generate Checklist Report
           run: |
              echo "📊 Generando reporte..."
              node scripts/generate-qa-checklist-report.js

         - name: Comment PR with Results
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const report = fs.readFileSync('qa-checklist-report.md', 'utf8');

                 github.rest.issues.createComment({
                   issue_number: context.issue.number,
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   body: report
                 });
```

### Checklist Interactivo de Testing

```html
<!DOCTYPE html>
<html>
   <head>
      <title>QA Testing Checklist</title>
      <style>
         body {
            font-family: Arial, sans-serif;
            margin: 20px;
         }
         .checklist-item {
            margin: 10px 0;
         }
         .category {
            background: #f5f5f5;
            padding: 15px;
            margin: 10px 0;
            border-radius: 5px;
         }
         .progress-bar {
            width: 100%;
            height: 20px;
            background: #ddd;
            border-radius: 10px;
            overflow: hidden;
         }
         .progress-fill {
            height: 100%;
            background: #4caf50;
            width: 0%;
            transition: width 0.3s;
         }
         input[type="checkbox"] {
            margin-right: 10px;
         }
         .completed {
            text-decoration: line-through;
            color: #888;
         }
      </style>
   </head>
   <body>
      <h1>📋 QA Testing Checklist</h1>

      <div class="progress-container">
         <h3>Progreso: <span id="progress-text">0%</span></h3>
         <div class="progress-bar">
            <div class="progress-fill" id="progress-fill"></div>
         </div>
      </div>

      <div class="category">
         <h3>🧪 Testing Funcional</h3>
         <div class="checklist-item">
            <input type="checkbox" id="func-1" onchange="updateProgress()" />
            <label for="func-1">Casos de uso principales validados</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="func-2" onchange="updateProgress()" />
            <label for="func-2">Flujos alternativos probados</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="func-3" onchange="updateProgress()" />
            <label for="func-3">Manejo de errores verificado</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="func-4" onchange="updateProgress()" />
            <label for="func-4">Validaciones de entrada confirmadas</label>
         </div>
      </div>

      <div class="category">
         <h3>⚡ Testing de Performance</h3>
         <div class="checklist-item">
            <input type="checkbox" id="perf-1" onchange="updateProgress()" />
            <label for="perf-1"
            >Tiempos de respuesta dentro de límites aceptables</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="perf-2" onchange="updateProgress()" />
            <label for="perf-2">Pruebas de carga ejecutadas</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="perf-3" onchange="updateProgress()" />
            <label for="perf-3">Memory usage optimizado</label>
         </div>
      </div>

      <div class="category">
         <h3>🔒 Testing de Seguridad</h3>
         <div class="checklist-item">
            <input type="checkbox" id="sec-1" onchange="updateProgress()" />
            <label for="sec-1">Validación de entrada implementada</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="sec-2" onchange="updateProgress()" />
            <label for="sec-2">Autenticación y autorización verificadas</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="sec-3" onchange="updateProgress()" />
            <label for="sec-3">Scan de vulnerabilidades ejecutado</label>
         </div>
      </div>

      <div class="category">
         <h3>♿ Testing de Accesibilidad</h3>
         <div class="checklist-item">
            <input type="checkbox" id="a11y-1" onchange="updateProgress()" />
            <label for="a11y-1">Navegación por teclado funcional</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="a11y-2" onchange="updateProgress()" />
            <label for="a11y-2">Screen readers compatibles</label>
         </div>
         <div class="checklist-item">
            <input type="checkbox" id="a11y-3" onchange="updateProgress()" />
            <label for="a11y-3">Contraste de colores adecuado</label>
         </div>
      </div>

      <button onclick="generateReport()" id="generate-report" disabled>
         📊 Generar Reporte
      </button>

      <script>
         function updateProgress() {
            const checkboxes = document.querySelectorAll(
               'input[type="checkbox"]',
            );
            const checkedBoxes = document.querySelectorAll(
               'input[type="checkbox"]:checked',
            );
            const progress =
               (checkedBoxes.length / checkboxes.length) * 100;

            document.getElementById("progress-fill").style.width =
               progress + "%";
            document.getElementById("progress-text").textContent =
               Math.round(progress) + "%";

            // Enable report button when all items are checked
            document.getElementById("generate-report").disabled =
               progress < 100;

            // Update visual feedback for completed items
            checkboxes.forEach((checkbox) => {
               const label = document.querySelector(
                  `label[for="${checkbox.id}"]`,
               );
               if (checkbox.checked) {
                  label.classList.add("completed");
               } else {
                  label.classList.remove("completed");
               }
            });

            // Save progress to localStorage
            const progress_data = {};
            checkboxes.forEach((cb) => {
               progress_data[cb.id] = cb.checked;
            });
            localStorage.setItem(
               "qa_checklist_progress",
               JSON.stringify(progress_data),
            );
         }

         function loadProgress() {
            const saved = localStorage.getItem(
               "qa_checklist_progress",
            );
            if (saved) {
               const progress_data = JSON.parse(saved);
               Object.keys(progress_data).forEach((id) => {
                  const checkbox = document.getElementById(id);
                  if (checkbox) {
                     checkbox.checked = progress_data[id];
                  }
               });
               updateProgress();
            }
         }

         function generateReport() {
            const checkedItems = [];
            const uncheckedItems = [];

            document
               .querySelectorAll('input[type="checkbox"]')
               .forEach((checkbox) => {
                  const label = document.querySelector(
                     `label[for="${checkbox.id}"]`,
                  ).textContent;
                  if (checkbox.checked) {
                     checkedItems.push(label);
                  } else {
                     uncheckedItems.push(label);
                  }
               });

            const report = `
# 📊 QA Checklist Report

**Fecha**: ${new Date().toLocaleDateString()}
**Completado**: ${checkedItems.length}/${
               checkedItems.length + uncheckedItems.length
            } items

## ✅ Items Completados
${checkedItems.map((item) => `- ${item}`).join("\n")}

## ❌ Items Pendientes
${uncheckedItems.map((item) => `- ${item}`).join("\n")}

---
*Reporte generado automáticamente por QA Checklist Tool*
            `;

            // Download report
            const blob = new Blob([report], {
               type: "text/markdown",
            });
            const url = URL.createObjectURL(blob);
            const a = document.createElement("a");
            a.href = url;
            a.download = `qa-checklist-report-${
               new Date().toISOString().split("T")[0]
            }.md`;
            a.click();
            URL.revokeObjectURL(url);
         }

         // Load saved progress on page load
         window.onload = loadProgress;
      </script>
   </body>
</html>
```

## Navegación

[⬅️ Testing de Regresión](./testing-regresion.md) |
[🏠 README Principal](../../README.md) |
[Pruebas de Aceptación del Usuario ➡️](./pruebas-aceptacion-usuario.md)
