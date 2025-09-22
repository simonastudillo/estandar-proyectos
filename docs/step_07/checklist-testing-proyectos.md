# Checklist de Testing por Tipo de Proyecto

## Conocimientos involucrados
- Estrategias de testing automatizado
- Herramientas como Jest, Cypress, Selenium
- Buenas prácticas de QA

## Responsable
- Equipo de QA y desarrollo

# Checklist de Testing por Tipo de Proyecto

## ¿Qué es?

Un checklist de testing por tipo de proyecto es una guía verificable y
específica que define los requisitos mínimos de testing según el stack
tecnológico, criticidad del proyecto y tipo de aplicación. Proporciona listas de
verificación concretas para asegurar que se cubran todos los aspectos esenciales
de testing antes de cada release, adaptadas a las características específicas de
cada tipo de proyecto.

## ¿Por qué es importante?

- **Consistency**: Garantiza estándares uniformes de testing en todos los
  proyectos
- **Quality Gates**: Define criterios claros de aceptación para cada fase
- **Risk Management**: Identifica y mitiga riesgos específicos por tipo de
  proyecto
- **Efficiency**: Evita testing innecesario y enfoca esfuerzos en lo crítico
- **Compliance**: Asegura cumplimiento de estándares y regulaciones
- **Onboarding**: Facilita que nuevos desarrolladores entiendan requisitos
- **Documentation**: Proporciona evidencia de testing completo

## ¿Qué debe incluir?

### Frontend Web (React + TypeScript + Vite)

#### Checklist Pre-Development

- [ ] **Setup Inicial**
  - [ ] Jest + React Testing Library configurado
  - [ ] ESLint + Prettier configurado con reglas de testing
  - [ ] Coverage thresholds definidos (mínimo 85%)
  - [ ] MSW configurado para mocking de APIs
  - [ ] Testing utilities y helpers creados
  - [ ] CI/CD pipeline configurado para tests

- [ ] **Estructura de Proyecto**
  - [ ] Carpeta `__tests__` organizada por módulos
  - [ ] Archivos de configuración de testing en su lugar
  - [ ] Mocks centralizados en `__mocks__`
  - [ ] Test utilities reutilizables definidas
  - [ ] Convenciones de naming establecidas

#### Checklist Durante Desarrollo

- [ ] **Testing Unitario**
  - [ ] Todos los componentes tienen tests básicos
  - [ ] Custom hooks están completamente probados
  - [ ] Utilidades y helpers tienen 100% coverage
  - [ ] Redux slices y actions están probadas
  - [ ] Validaciones de formularios cubiertas
  - [ ] Error boundaries probados

- [ ] **Testing de Integración**
  - [ ] Flujos de autenticación funcionan
  - [ ] Integración con APIs externas probada
  - [ ] Navegación entre páginas validada
  - [ ] Estado global persistente funciona
  - [ ] Manejo de errores de red probado

- [ ] **Testing Visual y UX**
  - [ ] Componentes responsive probados
  - [ ] Estados de loading implementados y probados
  - [ ] Estados de error manejados correctamente
  - [ ] Accessibility (a11y) básico validado
  - [ ] Cross-browser compatibility en navegadores principales

#### Checklist Pre-Release

- [ ] **Coverage y Quality**
  - [ ] Coverage mínimo 85% alcanzado
  - [ ] No tests marcados como `skip` o `todo`
  - [ ] Todos los tests pasan en CI/CD
  - [ ] Performance tests básicos ejecutados
  - [ ] Bundle size dentro de límites establecidos

- [ ] **End-to-End**
  - [ ] User journeys críticos probados
  - [ ] Formularios principales funcionan
  - [ ] Autenticación completa probada
  - [ ] Flujos de error manejados
  - [ ] Mobile responsiveness validado

#### Testing Scripts

```typescript
// scripts/frontend-checklist.ts
interface FrontendTestingChecklist {
   setup: ChecklistItem[];
   development: ChecklistItem[];
   preRelease: ChecklistItem[];
}

interface ChecklistItem {
   id: string;
   description: string;
   category: string;
   priority: "critical" | "high" | "medium" | "low";
   automated: boolean;
   command?: string;
}

export const FRONTEND_CHECKLIST: FrontendTestingChecklist = {
   setup: [
      {
         id: "jest-config",
         description: "Jest configurado con TypeScript",
         category: "configuration",
         priority: "critical",
         automated: true,
         command: "npm run test:config-check",
      },
      {
         id: "coverage-threshold",
         description: "Coverage thresholds configurados",
         category: "quality",
         priority: "critical",
         automated: true,
         command: "npm run test:coverage-check",
      },
   ],
   development: [
      {
         id: "component-tests",
         description: "Todos los componentes tienen tests",
         category: "unit",
         priority: "high",
         automated: true,
         command: "npm run test:components",
      },
      {
         id: "hook-tests",
         description: "Custom hooks probados",
         category: "unit",
         priority: "high",
         automated: true,
         command: "npm run test:hooks",
      },
   ],
   preRelease: [
      {
         id: "e2e-critical",
         description: "E2E tests críticos pasan",
         category: "integration",
         priority: "critical",
         automated: true,
         command: "npm run test:e2e:critical",
      },
   ],
};
```

### Backend API (Laravel + Clean Architecture)

#### Checklist Pre-Development

- [ ] **Setup Inicial**
  - [ ] PHPUnit configurado con Clean Architecture
  - [ ] Base de datos de testing configurada
  - [ ] Factories y seeders para datos de prueba
  - [ ] Mocks para servicios externos
  - [ ] Coverage configurado por layers
  - [ ] CI/CD pipeline con testing automatizado

- [ ] **Arquitectura Testing**
  - [ ] Tests organizados por Domain/Application/Infrastructure
  - [ ] TestCase base configurado por layer
  - [ ] Helpers para testing de cada capa
  - [ ] Validación de arquitectura automatizada
  - [ ] Conventions de naming establecidas

#### Checklist Durante Desarrollo

- [ ] **Domain Layer Testing**
  - [ ] Entities completamente probadas
  - [ ] Value Objects validados (incluyendo edge cases)
  - [ ] Domain Services con business logic probados
  - [ ] Domain Events y handlers probados
  - [ ] Excepciones de dominio cubiertas
  - [ ] Invariantes de negocio validadas

- [ ] **Application Layer Testing**
  - [ ] Use Cases con todos los escenarios probados
  - [ ] DTOs y mappers validados
  - [ ] Commands y Queries probados
  - [ ] Event handlers de aplicación probados
  - [ ] Authorization y permissions probados
  - [ ] Transaction handling probado

- [ ] **Infrastructure Layer Testing**
  - [ ] Repositories con operaciones CRUD probados
  - [ ] External service integrations mockeados
  - [ ] Event dispatching probado
  - [ ] Database migrations validadas
  - [ ] API Controllers probados
  - [ ] Middleware y filters probados

#### Checklist Pre-Release

- [ ] **API Testing**
  - [ ] Todos los endpoints documentados y probados
  - [ ] Validación de request/response schemas
  - [ ] Rate limiting probado
  - [ ] Authentication y authorization completos
  - [ ] Error handling consistente probado
  - [ ] API versioning funcionando

- [ ] **Performance y Security**
  - [ ] N+1 queries identificados y resueltos
  - [ ] Database performance probado
  - [ ] Security vulnerabilities escaneadas
  - [ ] Input validation completa
  - [ ] SQL injection prevention validado
  - [ ] OWASP Top 10 coverage

#### Backend Testing Configuration

```php
<?php
// tests/ChecklistValidator.php
class BackendTestingChecklistValidator
{
    private array $checklist = [
        'domain' => [
            'entities_coverage' => 95,
            'value_objects_coverage' => 100,
            'domain_services_coverage' => 90,
            'domain_events_coverage' => 85,
        ],
        'application' => [
            'use_cases_coverage' => 90,
            'dtos_coverage' => 85,
            'commands_coverage' => 90,
            'queries_coverage' => 85,
        ],
        'infrastructure' => [
            'repositories_coverage' => 80,
            'controllers_coverage' => 85,
            'external_services_coverage' => 75,
        ]
    ];

    public function validateCoverage(): bool
    {
        $coverageData = $this->loadCoverageData();
        $passed = true;

        foreach ($this->checklist as $layer => $requirements) {
            echo "\n🏗️  Validating {$layer} layer:\n";
            
            foreach ($requirements as $component => $threshold) {
                $actual = $coverageData[$layer][$component] ?? 0;
                $status = $actual >= $threshold ? '✅' : '❌';
                
                echo "{$status} {$component}: {$actual}% (required: {$threshold}%)\n";
                
                if ($actual < $threshold) {
                    $passed = false;
                }
            }
        }

        return $passed;
    }

    public function validateArchitecture(): bool
    {
        $violations = [];

        // Check dependencies between layers
        $violations = array_merge($violations, $this->checkDomainDependencies());
        $violations = array_merge($violations, $this->checkApplicationDependencies());
        $violations = array_merge($violations, $this->checkInfrastructureDependencies());

        if (!empty($violations)) {
            echo "❌ Architecture violations found:\n";
            foreach ($violations as $violation) {
                echo "  - {$violation}\n";
            }
            return false;
        }

        echo "✅ Architecture validation passed\n";
        return true;
    }
}
```

### Mobile App (React Native + TypeScript)

#### Checklist Pre-Development

- [ ] **Setup Inicial**
  - [ ] Jest configurado para React Native
  - [ ] Detox configurado para E2E testing
  - [ ] Testing en iOS y Android simulators
  - [ ] Mocks para APIs nativas (Camera, GPS, etc.)
  - [ ] Performance testing tools configurados
  - [ ] Device testing strategy definida

#### Checklist Durante Desarrollo

- [ ] **Platform Testing**
  - [ ] Componentes probados en iOS y Android
  - [ ] Navigation flows validados
  - [ ] Platform-specific features probados
  - [ ] Permissions handling probado
  - [ ] Deep linking funcionando
  - [ ] Push notifications probadas

- [ ] **Native Features Testing**
  - [ ] Camera functionality probada
  - [ ] GPS/Location services probados
  - [ ] File system access validado
  - [ ] Biometric authentication probado
  - [ ] Device storage funcionando
  - [ ] Network connectivity handling

#### Checklist Pre-Release

- [ ] **Device Testing**
  - [ ] Testing en dispositivos físicos reales
  - [ ] Different screen sizes y densities
  - [ ] Performance en dispositivos de gama baja
  - [ ] Battery usage optimizado
  - [ ] Memory leaks identificados y resueltos
  - [ ] Crash reporting configurado

- [ ] **Store Preparation**
  - [ ] App builds correctamente para release
  - [ ] App store guidelines cumplidas
  - [ ] Privacy policy implementada
  - [ ] Analytics y crash reporting configurados
  - [ ] App signing y certificates listos

### Full-Stack Project (Frontend + Backend + Mobile)

#### Checklist Integración Completa

- [ ] **API Integration**
  - [ ] Frontend consume APIs correctamente
  - [ ] Mobile app sincroniza con backend
  - [ ] Real-time features funcionando (WebSockets)
  - [ ] File upload/download en todas las plataformas
  - [ ] Authentication flow completo
  - [ ] Error handling consistente

- [ ] **Data Consistency**
  - [ ] Estado sincronizado entre plataformas
  - [ ] Offline capabilities funcionando
  - [ ] Data migration scripts probados
  - [ ] Backup y restore procedures probados
  - [ ] Cross-platform data validation

- [ ] **Performance y Scalability**
  - [ ] Load testing en backend APIs
  - [ ] Frontend performance optimizado
  - [ ] Mobile app memory efficient
  - [ ] Database query optimization
  - [ ] CDN y caching estrategies probadas

## ¿Qué debo hacer?

### 1. Crear scripts de validación automatizada

```bash
#!/bin/bash
# scripts/validate-testing-checklist.sh

PROJECT_TYPE=$1
ENVIRONMENT=$2

if [ -z "$PROJECT_TYPE" ]; then
    echo "❌ Project type required (frontend|backend|mobile|fullstack)"
    exit 1
fi

echo "🔍 Validating testing checklist for $PROJECT_TYPE project"

case $PROJECT_TYPE in
    "frontend")
        npm run test:checklist:frontend
        ;;
    "backend")
        php scripts/validate-backend-checklist.php
        ;;
    "mobile")
        npm run test:checklist:mobile
        ;;
    "fullstack")
        npm run test:checklist:fullstack
        ;;
    *)
        echo "❌ Unknown project type: $PROJECT_TYPE"
        exit 1
        ;;
esac

if [ $? -eq 0 ]; then
    echo "✅ All checklist items validated successfully"
    exit 0
else
    echo "❌ Some checklist items failed validation"
    exit 1
fi
```

### 2. Implementar quality gates por tipo de proyecto

```typescript
// src/quality-gates/project-gates.ts
interface QualityGate {
   name: string;
   description: string;
   threshold: number;
   command: string;
   blocking: boolean;
}

export const QUALITY_GATES_BY_PROJECT: Record<string, QualityGate[]> = {
   frontend: [
      {
         name: "Unit Test Coverage",
         description: "Minimum unit test coverage",
         threshold: 85,
         command: "npm run test:coverage",
         blocking: true,
      },
      {
         name: "E2E Critical Paths",
         description: "Critical user journeys must pass",
         threshold: 100,
         command: "npm run test:e2e:critical",
         blocking: true,
      },
      {
         name: "Bundle Size",
         description: "Bundle size within limits",
         threshold: 2048, // KB
         command: "npm run build:analyze",
         blocking: true,
      },
   ],

   backend: [
      {
         name: "Domain Layer Coverage",
         description: "Domain logic must be fully tested",
         threshold: 95,
         command: "php scripts/check-domain-coverage.php",
         blocking: true,
      },
      {
         name: "API Tests",
         description: "All endpoints must be tested",
         threshold: 100,
         command: "php artisan test --testsuite=Feature",
         blocking: true,
      },
      {
         name: "Security Scan",
         description: "No high severity vulnerabilities",
         threshold: 0,
         command: "php scripts/security-scan.php",
         blocking: true,
      },
   ],

   mobile: [
      {
         name: "Cross-Platform Tests",
         description: "Tests pass on iOS and Android",
         threshold: 100,
         command: "npm run test:platforms",
         blocking: true,
      },
      {
         name: "Device Performance",
         description: "App performs well on low-end devices",
         threshold: 95,
         command: "npm run test:performance",
         blocking: false,
      },
   ],
};
```

### 3. Configurar reporting automático

```typescript
// src/reporting/checklist-reporter.ts
class ChecklistReporter {
   async generateReport(projectType: string): Promise<ChecklistReport> {
      const checklist = this.getChecklistForProject(projectType);
      const results = await this.runChecklistValidation(checklist);

      const report: ChecklistReport = {
         projectType,
         timestamp: new Date().toISOString(),
         totalItems: checklist.length,
         passedItems: results.filter((r) => r.passed).length,
         failedItems: results.filter((r) => !r.passed).length,
         coverage: this.calculateCoverage(results),
         details: results,
         recommendations: this.generateRecommendations(results),
      };

      await this.saveReport(report);
      await this.sendNotifications(report);

      return report;
   }

   private generateRecommendations(results: ChecklistResult[]): string[] {
      const recommendations: string[] = [];

      const failedCritical = results.filter((r) =>
         !r.passed && r.priority === "critical"
      );
      if (failedCritical.length > 0) {
         recommendations.push(
            `🚨 ${failedCritical.length} critical items failed - blocking release`,
         );
      }

      const lowCoverage = results.filter((r) => r.coverage && r.coverage < 80);
      if (lowCoverage.length > 0) {
         recommendations.push(
            `📊 ${lowCoverage.length} items have low coverage - consider adding tests`,
         );
      }

      return recommendations;
   }
}
```

### 4. Integrar con CI/CD

```yaml
# .github/workflows/testing-checklist.yml
name: Testing Checklist Validation

on:
   pull_request:
      branches: [main, develop]
   push:
      branches: [main]

jobs:
   validate-checklist:
      runs-on: ubuntu-latest

      strategy:
         matrix:
            project-type: [frontend, backend, mobile]

      steps:
         - uses: actions/checkout@v4

         - name: Setup Environment
           uses: ./.github/actions/setup-${{ matrix.project-type }}

         - name: Run Checklist Validation
           run: |
              chmod +x scripts/validate-testing-checklist.sh
              ./scripts/validate-testing-checklist.sh ${{ matrix.project-type }}

         - name: Generate Checklist Report
           run: |
              npm run checklist:report ${{ matrix.project-type }}

         - name: Upload Report
           uses: actions/upload-artifact@v3
           with:
              name: checklist-report-${{ matrix.project-type }}
              path: reports/checklist-${{ matrix.project-type }}.json

         - name: Comment PR
           if: github.event_name == 'pull_request'
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const report = JSON.parse(fs.readFileSync('reports/checklist-${{ matrix.project-type }}.json'));

                 const comment = `## 📋 Testing Checklist - ${{ matrix.project-type }}

                 **Overall Status**: ${report.failedItems === 0 ? '✅ PASSED' : '❌ FAILED'}

                 - ✅ Passed: ${report.passedItems}/${report.totalItems}
                 - ❌ Failed: ${report.failedItems}
                 - 📊 Coverage: ${report.coverage}%

                 ${report.recommendations.map(r => `- ${r}`).join('\n')}
                 `;

                 github.rest.issues.createComment({
                   issue_number: context.issue.number,
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   body: comment
                 });
```

## Tips

- **Automatización progresiva**: Comienza con checks básicos y añade complejidad
  gradualmente
- **Priorización**: Enfócate en items críticos primero, luego nice-to-have
- **Customización**: Adapta checklists a las necesidades específicas de tu
  proyecto
- **Documentación**: Mantén checklists actualizados con cambios en el proyecto
- **Team buy-in**: Asegúrate de que el equipo entienda y use los checklists
- **Continuous improvement**: Actualiza checklists basado en lecciones
  aprendidas
- **Tool integration**: Integra con herramientas existentes de CI/CD y project
  management

## Templates de Checklist

### Checklist Básico Universal

```markdown
# Testing Checklist - [PROJECT_NAME]

## Pre-Development ✅

- [ ] Testing strategy defined
- [ ] Tools and frameworks selected
- [ ] CI/CD pipeline configured
- [ ] Quality gates established

## During Development ✅

- [ ] Unit tests written for new code
- [ ] Integration points tested
- [ ] Code coverage maintained
- [ ] Performance considerations addressed

## Pre-Release ✅

- [ ] All tests passing in CI/CD
- [ ] Manual testing completed
- [ ] Performance testing done
- [ ] Security review completed
- [ ] Documentation updated

## Post-Release ✅

- [ ] Monitoring in place
- [ ] Rollback plan tested
- [ ] Team retrospective scheduled
- [ ] Lessons learned documented
```

### Checklist Específico por Criticidad

```typescript
// Critical System Checklist
export const CRITICAL_SYSTEM_CHECKLIST = {
   requiredCoverage: 95,
   mandatoryTests: [
      "authentication",
      "authorization",
      "payment-processing",
      "data-integrity",
      "security-boundaries",
   ],
   performanceThresholds: {
      responseTime: 200, // ms
      throughput: 1000, // requests/second
      availability: 99.9, // percent
   },
   securityRequirements: [
      "input-validation",
      "sql-injection-prevention",
      "xss-prevention",
      "csrf-protection",
      "rate-limiting",
   ],
};
```

## Navegación

[Testing por Entorno ⬅️](./testing-por-entorno.md) | [Inicio](../../README.md) |
[Testing Funcional Automatizado ➡️](./testing-funcional-automatizado.md)
