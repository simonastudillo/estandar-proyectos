# Pruebas de Aceptación del Usuario (UAT)

## ¿Qué es?

Las Pruebas de Aceptación del Usuario (User Acceptance Testing - UAT) son el
proceso final de validación donde los usuarios finales o representantes del
negocio verifican que el sistema cumple con sus requerimientos y expectativas.
Es la última fase de testing antes del despliegue a producción y determina si el
software está listo para ser utilizado en el entorno real.

## ¿Por qué es importante?

- **Validación de requerimientos**: Confirma que el sistema cumple con las
  necesidades del negocio
- **Perspectiva del usuario**: Evalúa el software desde el punto de vista del
  usuario final
- **Detección de gaps**: Identifica discrepancias entre lo esperado y lo
  desarrollado
- **Reducción de riesgo**: Minimiza la probabilidad de rechazo después del
  despliegue
- **Confianza del stakeholder**: Genera confianza en la calidad del producto
- **Aprobación formal**: Proporciona la aprobación oficial para el go-live
- **Feedback valioso**: Obtiene retroalimentación directa de los usuarios
  finales

## ¿Qué debe incluir?

### Tipos de UAT

#### UAT de Negocio (Business UAT)

- Verificación de procesos de negocio
- Validación de flujos de trabajo
- Confirmación de reglas de negocio
- Evaluación de la eficiencia operacional

#### UAT de Usuario Final (End-User UAT)

- Pruebas de usabilidad y experiencia
- Validación de interfaces de usuario
- Verificación de accesibilidad
- Confirmación de intuitividad

#### UAT de Conformidad (Compliance UAT)

- Verificación de cumplimiento regulatorio
- Validación de estándares de seguridad
- Confirmación de políticas corporativas
- Evaluación de requisitos legales

#### UAT de Performance (Performance UAT)

- Validación de tiempos de respuesta
- Verificación de capacidad del sistema
- Evaluación de escalabilidad
- Confirmación de disponibilidad

### Proceso de UAT

#### Planificación

- Definición de criterios de aceptación
- Selección de usuarios de prueba
- Preparación de escenarios de testing
- Configuración de ambiente de UAT

#### Ejecución

- Capacitación de usuarios de prueba
- Ejecución de casos de prueba
- Documentación de resultados
- Reporte de defectos encontrados

#### Cierre

- Evaluación de resultados
- Toma de decisión go/no-go
- Documentación de aprobación
- Entrega formal del producto

## ¿Qué debo hacer?

### 1. Planificar el proceso de UAT

```typescript
// src/uat/uat-planning.ts
export interface UATPlanning {
   project: {
      id: string;
      name: string;
      version: string;
      description: string;
   };
   objectives: string[];
   scope: {
      included: string[];
      excluded: string[];
      assumptions: string[];
      constraints: string[];
   };
   timeline: {
      startDate: Date;
      endDate: Date;
      milestones: Milestone[];
   };
   resources: {
      users: UATUser[];
      environments: UATEnvironment[];
      tools: string[];
   };
   criteria: AcceptanceCriteria[];
}

export interface UATUser {
   id: string;
   name: string;
   role: string;
   department: string;
   experience: "beginner" | "intermediate" | "expert";
   availability: string;
   responsibilities: string[];
}

export interface AcceptanceCriteria {
   id: string;
   description: string;
   priority: "high" | "medium" | "low";
   testable: boolean;
   measurable: boolean;
   assignedTo: string;
}

export class UATPlanner {
   createUATPlan(requirements: any[]): UATPlanning {
      return {
         project: this.extractProjectInfo(requirements),
         objectives: this.defineObjectives(requirements),
         scope: this.defineScope(requirements),
         timeline: this.createTimeline(requirements),
         resources: this.allocateResources(requirements),
         criteria: this.defineCriteria(requirements),
      };
   }

   validateUATPlan(plan: UATPlanning): ValidationResult {
      const issues: string[] = [];

      // Validar completitud
      if (!plan.objectives.length) {
         issues.push("Objetivos no definidos");
      }

      if (!plan.resources.users.length) {
         issues.push("Usuarios de UAT no asignados");
      }

      if (!plan.criteria.length) {
         issues.push("Criterios de aceptación no definidos");
      }

      // Validar timeline
      if (plan.timeline.startDate >= plan.timeline.endDate) {
         issues.push("Timeline inválido");
      }

      return {
         isValid: issues.length === 0,
         issues,
         recommendations: this.generateRecommendations(plan),
      };
   }
}
```

### 2. Crear casos de prueba de UAT

```typescript
// src/uat/uat-test-cases.ts
export interface UATTestCase {
   id: string;
   title: string;
   description: string;
   category: "business" | "usability" | "performance" | "compliance";
   priority: "critical" | "high" | "medium" | "low";
   preconditions: string[];
   steps: TestStep[];
   expectedResults: string;
   actualResults?: string;
   status: "not-started" | "in-progress" | "passed" | "failed" | "blocked";
   assignedTo: string;
   executedBy?: string;
   executionDate?: Date;
   comments: string[];
   attachments: string[];
}

export interface TestStep {
   stepNumber: number;
   action: string;
   data?: string;
   expectedResult: string;
}

export class UATTestCaseGenerator {
   generateFromUserStories(userStories: any[]): UATTestCase[] {
      return userStories.map((story) => this.convertToUATTestCase(story));
   }

   convertToUATTestCase(userStory: any): UATTestCase {
      return {
         id: `UAT-${userStory.id}`,
         title: `UAT: ${userStory.title}`,
         description: userStory.description,
         category: this.determinateCategory(userStory),
         priority: this.mapPriority(userStory.priority),
         preconditions: this.extractPreconditions(userStory),
         steps: this.generateTestSteps(userStory),
         expectedResults: userStory.acceptanceCriteria.join("; "),
         status: "not-started",
         assignedTo: this.assignUser(userStory),
         comments: [],
         attachments: [],
      };
   }

   generateTestSteps(userStory: any): TestStep[] {
      const steps: TestStep[] = [];

      // Paso de setup/login si es necesario
      if (userStory.requiresAuthentication) {
         steps.push({
            stepNumber: 1,
            action: "Iniciar sesión en el sistema",
            data: "usuario: test@example.com, password: Test123",
            expectedResult: "Usuario autenticado exitosamente",
         });
      }

      // Pasos principales basados en el flujo
      userStory.flow.forEach((flowStep: any, index: number) => {
         steps.push({
            stepNumber: steps.length + 1,
            action: flowStep.action,
            data: flowStep.testData,
            expectedResult: flowStep.expectedOutcome,
         });
      });

      return steps;
   }
}
```

### 3. Configurar ambiente de UAT

```bash
#!/bin/bash
# scripts/setup-uat-environment.sh

echo "🚀 Configurando ambiente de UAT..."

# 1. Variables de ambiente
export NODE_ENV=uat
export API_BASE_URL=https://uat-api.example.com
export FRONTEND_URL=https://uat.example.com
export DATABASE_URL=postgresql://uat_user:password@uat-db:5432/uat_database

# 2. Preparar base de datos
echo "🗄️ Preparando base de datos de UAT..."
npm run db:migrate:uat
npm run db:seed:uat

# 3. Configurar datos de prueba
echo "📊 Generando datos de prueba..."
node scripts/generate-uat-test-data.js

# 4. Desplegar aplicación
echo "🔧 Desplegando aplicación en UAT..."
npm run build:uat
npm run deploy:uat

# 5. Configurar monitoreo
echo "📊 Configurando monitoreo de UAT..."
node scripts/setup-uat-monitoring.js

# 6. Ejecutar health checks
echo "🏥 Verificando salud del sistema..."
npm run health-check:uat

# 7. Notificar a usuarios de UAT
echo "📧 Notificando inicio de UAT..."
node scripts/notify-uat-users.js

echo "✅ Ambiente de UAT configurado y listo"
echo "🌐 UAT Frontend: $FRONTEND_URL"
echo "🔗 UAT API: $API_BASE_URL"
```

### 4. Implementar herramientas de seguimiento

```typescript
// src/uat/uat-tracking.ts
export class UATTracker {
   private testCases: Map<string, UATTestCase> = new Map();
   private defects: UATDefect[] = [];
   private sessions: UATSession[] = [];

   trackTestExecution(testCaseId: string, execution: TestExecution): void {
      const testCase = this.testCases.get(testCaseId);
      if (!testCase) {
         throw new Error(`Test case ${testCaseId} not found`);
      }

      // Actualizar estado del test case
      testCase.status = execution.status;
      testCase.executedBy = execution.executedBy;
      testCase.executionDate = new Date();
      testCase.actualResults = execution.actualResults;
      testCase.comments.push(...execution.comments);

      // Si hay defectos, crear reportes
      if (execution.defects?.length) {
         execution.defects.forEach(defect => {
            this.reportDefect({
               ...defect,
               testCaseId,
               reportedBy: execution.executedBy,
               reportedDate: new Date()
            });
         });
      }

      this.updateMetrics();
   }

   generateProgressReport(): UATProgressReport {
      const totalTests = this.testCases.size;
      const completedTests = Array.from(this.testCases.values())
         .filter(tc => tc.status === 'passed' || tc.status === 'failed').length;
      const passedTests = Array.from(this.testCases.values())
         .filter(tc => tc.status === 'passed').length;
      const failedTests = Array.from(this.testCases.values())
         .filter(tc => tc.status === 'failed').length;

      return {
         summary: {
            totalTests,
            completedTests,
            passedTests,
            failedTests,
            completionRate: (completedTests / totalTests) * 100,
            passRate: completedTests > 0 ? (passedTests / completedTests) * 100 : 0
         },
         defectSummary: {
            totalDefects: this.defects.length,
            openDefects: this.defects.filter(d => d.status === 'open').length,
            closedDefects: this.defects.filter(d => d.status === 'closed').length,
            criticalDefects: this.defects.filter(d => d.severity === 'critical').length
         },
         timeline: this.generateTimeline(),
         recommendations: this.generateRecommendations()
      };
   }

   isUATReadyForSign off(): UATSignOffStatus {
      const progressReport = this.generateProgressReport();
      const criteria = {
         minCompletionRate: 95,
         minPassRate: 90,
         maxCriticalDefects: 0,
         maxHighDefects: 2
      };

      const issues: string[] = [];

      if (progressReport.summary.completionRate < criteria.minCompletionRate) {
         issues.push(`Completion rate ${progressReport.summary.completionRate}% < ${criteria.minCompletionRate}%`);
      }

      if (progressReport.summary.passRate < criteria.minPassRate) {
         issues.push(`Pass rate ${progressReport.summary.passRate}% < ${criteria.minPassRate}%`);
      }

      if (progressReport.defectSummary.criticalDefects > criteria.maxCriticalDefects) {
         issues.push(`${progressReport.defectSummary.criticalDefects} critical defects > ${criteria.maxCriticalDefects}`);
      }

      return {
         readyForSignOff: issues.length === 0,
         issues,
         recommendation: issues.length === 0 ? 'APPROVE' : 'REJECT',
         signOffCriteria: criteria
      };
   }
}

export interface UATDefect {
   id: string;
   testCaseId: string;
   title: string;
   description: string;
   severity: 'critical' | 'high' | 'medium' | 'low';
   status: 'open' | 'in-progress' | 'resolved' | 'closed';
   reportedBy: string;
   reportedDate: Date;
   assignedTo?: string;
   steps: string[];
   expectedResult: string;
   actualResult: string;
   screenshots: string[];
   environment: string;
}
```

### 5. Crear proceso de sign-off

```typescript
// src/uat/uat-signoff.ts
export interface UATSignOff {
   project: {
      id: string;
      name: string;
      version: string;
   };
   summary: {
      totalTestCases: number;
      passedTestCases: number;
      failedTestCases: number;
      defectsFound: number;
      criticalDefects: number;
   };
   stakeholders: SignOffStakeholder[];
   approval: {
      status: "pending" | "approved" | "rejected" | "conditional";
      conditions?: string[];
      approvedBy?: string[];
      rejectedBy?: string[];
      approvalDate?: Date;
   };
   comments: string[];
   attachments: string[];
}

export interface SignOffStakeholder {
   name: string;
   role: string;
   email: string;
   status: "pending" | "approved" | "rejected";
   comments?: string;
   signOffDate?: Date;
}

export class UATSignOffManager {
   generateSignOffDocument(
      project: any,
      testResults: UATProgressReport,
      stakeholders: SignOffStakeholder[],
   ): UATSignOff {
      return {
         project: {
            id: project.id,
            name: project.name,
            version: project.version,
         },
         summary: {
            totalTestCases: testResults.summary.totalTests,
            passedTestCases: testResults.summary.passedTests,
            failedTestCases: testResults.summary.failedTests,
            defectsFound: testResults.defectSummary.totalDefects,
            criticalDefects: testResults.defectSummary.criticalDefects,
         },
         stakeholders: stakeholders.map((s) => ({
            ...s,
            status: "pending",
         })),
         approval: {
            status: "pending",
         },
         comments: [],
         attachments: [],
      };
   }

   processStakeholderApproval(
      signOff: UATSignOff,
      stakeholderEmail: string,
      approval: boolean,
      comments?: string,
   ): void {
      const stakeholder = signOff.stakeholders.find(
         (s) => s.email === stakeholderEmail,
      );
      if (!stakeholder) {
         throw new Error(`Stakeholder ${stakeholderEmail} not found`);
      }

      stakeholder.status = approval ? "approved" : "rejected";
      stakeholder.comments = comments;
      stakeholder.signOffDate = new Date();

      // Actualizar estado general
      this.updateOverallStatus(signOff);
   }

   private updateOverallStatus(signOff: UATSignOff): void {
      const approvals = signOff.stakeholders.filter(
         (s) => s.status === "approved",
      );
      const rejections = signOff.stakeholders.filter(
         (s) => s.status === "rejected",
      );
      const pending = signOff.stakeholders.filter((s) =>
         s.status === "pending"
      );

      if (rejections.length > 0) {
         signOff.approval.status = "rejected";
         signOff.approval.rejectedBy = rejections.map((r) => r.name);
      } else if (pending.length === 0) {
         signOff.approval.status = "approved";
         signOff.approval.approvedBy = approvals.map((a) => a.name);
         signOff.approval.approvalDate = new Date();
      }
   }

   generateSignOffReport(signOff: UATSignOff): string {
      return `
# 📋 UAT Sign-Off Report

## Project Information
- **Project**: ${signOff.project.name}
- **Version**: ${signOff.project.version}
- **Date**: ${new Date().toLocaleDateString()}

## Test Summary
- **Total Test Cases**: ${signOff.summary.totalTestCases}
- **Passed**: ${signOff.summary.passedTestCases}
- **Failed**: ${signOff.summary.failedTestCases}
- **Pass Rate**: ${
         (
            (signOff.summary.passedTestCases / signOff.summary.totalTestCases) *
            100
         ).toFixed(2)
      }%

## Defect Summary
- **Total Defects**: ${signOff.summary.defectsFound}
- **Critical Defects**: ${signOff.summary.criticalDefects}

## Stakeholder Approvals
${
         signOff.stakeholders
            .map(
               (s) => `
- **${s.name}** (${s.role}): ${s.status.toUpperCase()}
  ${s.comments ? `  - Comments: ${s.comments}` : ""}
  ${s.signOffDate ? `  - Date: ${s.signOffDate.toLocaleDateString()}` : ""}
`,
            )
            .join("")
      }

## Overall Decision
**Status**: ${signOff.approval.status.toUpperCase()}
${
         signOff.approval.approvalDate
            ? `**Approval Date**: ${signOff.approval.approvalDate.toLocaleDateString()}`
            : ""
      }

## Recommendations
${this.generateRecommendations(signOff)}
      `;
   }
}
```

## Tips

- **Involucrar usuarios temprano**: Incluye a los usuarios finales desde la
  planificación
- **Criterios claros**: Define criterios de aceptación específicos y medibles
- **Ambiente representativo**: Usa datos y configuraciones similares a
  producción
- **Documentación simple**: Crea instrucciones claras y fáciles de seguir
- **Soporte continuo**: Proporciona asistencia durante las pruebas
- **Feedback estructurado**: Usa formularios y herramientas para capturar
  feedback
- **Timeline realista**: Permite tiempo suficiente para una evaluación completa
- **Comunicación efectiva**: Mantén a todos los stakeholders informados del
  progreso

## Ejemplos

### Script de UAT Automatizado

```bash
#!/bin/bash
# scripts/uat-automation.sh

echo "🎯 Iniciando proceso automatizado de UAT..."

# Configuración
UAT_ENV="uat"
REPORT_DIR="uat-reports-$(date +%Y%m%d_%H%M%S)"
mkdir -p $REPORT_DIR

# 1. Verificar ambiente UAT
echo "🔍 Verificando ambiente UAT..."
if ! curl -f https://uat.example.com/health > /dev/null 2>&1; then
    echo "❌ Ambiente UAT no disponible"
    exit 1
fi

# 2. Ejecutar smoke tests previos
echo "🔥 Ejecutando smoke tests..."
npm run test:smoke:uat || {
    echo "❌ Smoke tests fallaron"
    exit 1
}

# 3. Generar datos de prueba
echo "📊 Generando datos de prueba..."
node scripts/generate-uat-data.js --environment=$UAT_ENV

# 4. Ejecutar casos de prueba automatizados
echo "🤖 Ejecutando casos de UAT automatizados..."
npm run test:uat:automated -- --reporter=json --outputFile=$REPORT_DIR/automated-results.json

# 5. Generar formularios para testing manual
echo "📝 Generando formularios de UAT manual..."
node scripts/generate-uat-forms.js --output=$REPORT_DIR

# 6. Enviar notificaciones a usuarios de UAT
echo "📧 Notificando usuarios de UAT..."
node scripts/notify-uat-start.js --reports-dir=$REPORT_DIR

# 7. Configurar monitoreo de progreso
echo "📈 Configurando dashboard de progreso..."
node scripts/setup-uat-dashboard.js --reports-dir=$REPORT_DIR

echo "✅ UAT iniciado. Dashboard: https://uat-dashboard.example.com"
echo "📁 Reportes en: $REPORT_DIR"
```

### Template de Caso de Prueba UAT

```markdown
# UAT-001: Login de Usuario

## Información General

- **ID**: UAT-001
- **Título**: Verificar funcionalidad de login de usuario
- **Categoría**: Funcional
- **Prioridad**: Alta
- **Asignado a**: Usuario de Negocio - María González
- **Fecha**: 2024-12-15

## Descripción

Verificar que los usuarios pueden autenticarse exitosamente en el sistema usando
credenciales válidas.

## Precondiciones

1. Usuario registrado en el sistema
2. Credenciales válidas disponibles
3. Sistema UAT operativo
4. Navegador web actualizado

## Pasos de Ejecución

### Paso 1

**Acción**: Navegar a la página de login **URL**: https://uat.example.com/login
**Resultado Esperado**: Se muestra la página de login con campos de email y
password

### Paso 2

**Acción**: Ingresar email válido **Datos**: usuario.uat@example.com **Resultado
Esperado**: Email se ingresa correctamente sin errores

### Paso 3

**Acción**: Ingresar password válido **Datos**: UATPassword123! **Resultado
Esperado**: Password se oculta con asteriscos

### Paso 4

**Acción**: Hacer clic en botón "Iniciar Sesión" **Resultado Esperado**: Usuario
es redirigido al dashboard principal

### Paso 5

**Acción**: Verificar elementos del dashboard **Resultado Esperado**: Se muestra
mensaje de bienvenida con nombre del usuario

## Criterios de Aceptación

- [ ] Login exitoso con credenciales válidas
- [ ] Redirección correcta al dashboard
- [ ] Mensaje de bienvenida personalizado
- [ ] Menú de navegación visible
- [ ] Sesión mantenida por 24 horas

## Resultados de Ejecución

**Ejecutado por**: ********\_******** **Fecha de ejecución**: ********\_********
**Estado**: [ ] Pasó [ ] Falló [ ] Bloqueado **Resultado actual**:
********\_******** **Comentarios**: ********\_********

## Defectos Encontrados

1. **ID Defecto**: ********\_********
2. **Descripción**: ********\_********
3. **Severidad**: [ ] Crítica [ ] Alta [ ] Media [ ] Baja
4. **Evidencia**: ********\_********

## Aprobación

**Aprobado por**: ********\_******** **Firma**: ********\_******** **Fecha**:
********\_********
```

## Navegación

[⬅️ Checklists QA](./checklists-qa.md) | [🏠 README Principal](../../README.md)
| [Gestión de Reportes de Errores ➡️](./gestion-reportes-errores.md)
