# Gestión de Reportes de Errores

## Conocimientos involucrados
- Herramientas de seguimiento de errores (Jira, Bugzilla)
- Análisis de causa raíz
- Estrategias de priorización y resolución

## Responsable
- Equipo de QA y desarrollo

# Gestión de Reportes de Errores

## ¿Qué es?

La gestión de reportes de errores es el proceso sistemático de captura,
seguimiento, análisis y resolución de defectos encontrados durante el desarrollo
y testing del software. Incluye desde la identificación inicial del problema
hasta su resolución final y verificación, asegurando que todos los bugs sean
documentados, priorizados y resueltos de manera eficiente.

## ¿Por qué es importante?

- **Calidad del software**: Asegura que todos los defectos sean identificados y
  corregidos
- **Trazabilidad**: Mantiene un historial completo de problemas y sus
  resoluciones
- **Priorización efectiva**: Permite enfocar recursos en los defectos más
  críticos
- **Comunicación mejorada**: Facilita la coordinación entre equipos de
  desarrollo y QA
- **Métricas de calidad**: Proporciona datos para evaluar la estabilidad del
  software
- **Prevención de regresiones**: Evita que problemas resueltos reaparezcan
- **Aprendizaje organizacional**: Identifica patrones para prevenir defectos
  futuros

## ¿Qué debe incluir?

### Ciclo de Vida del Defecto

#### Identificación

- Detección del problema
- Reproducción del error
- Documentación inicial
- Clasificación preliminar

#### Reporte

- Creación del ticket de defecto
- Asignación de severidad y prioridad
- Asignación al equipo responsable
- Notificación a stakeholders

#### Análisis

- Investigación de la causa raíz
- Estimación de esfuerzo de corrección
- Evaluación de impacto
- Planificación de la resolución

#### Resolución

- Implementación de la corrección
- Testing de la solución
- Code review del fix
- Deployment de la corrección

#### Verificación

- Validación de la corrección
- Testing de regresión
- Aprobación del fix
- Cierre del defecto

### Clasificación de Defectos

#### Por Severidad

- **Crítica**: Sistema no funcional o pérdida de datos
- **Alta**: Funcionalidad principal afectada
- **Media**: Funcionalidad secundaria con workaround
- **Baja**: Problemas cosméticos o menores

#### Por Prioridad

- **P1**: Resolver inmediatamente
- **P2**: Resolver en el siguiente release
- **P3**: Resolver cuando sea posible
- **P4**: Resolver en el futuro

#### Por Tipo

- **Funcional**: No cumple con requerimientos
- **Performance**: Problemas de rendimiento
- **Usabilidad**: Problemas de experiencia de usuario
- **Seguridad**: Vulnerabilidades de seguridad
- **Compatibilidad**: Problemas entre plataformas

## ¿Qué debo hacer?

### 1. Configurar sistema de tracking

```typescript
// src/bug-tracking/defect-management.ts
export interface DefectReport {
   id: string;
   title: string;
   description: string;
   severity: "critical" | "high" | "medium" | "low";
   priority: "P1" | "P2" | "P3" | "P4";
   type:
      | "functional"
      | "performance"
      | "usability"
      | "security"
      | "compatibility";
   status: DefectStatus;
   reportedBy: string;
   reportedDate: Date;
   assignedTo?: string;
   assignedDate?: Date;
   environment: Environment;
   stepsToReproduce: string[];
   expectedResult: string;
   actualResult: string;
   attachments: Attachment[];
   relatedTestCase?: string;
   tags: string[];
   comments: Comment[];
   resolution?: DefectResolution;
}

export type DefectStatus =
   | "new"
   | "open"
   | "in-progress"
   | "resolved"
   | "closed"
   | "rejected"
   | "duplicate"
   | "deferred";

export interface Environment {
   browser?: string;
   browserVersion?: string;
   operatingSystem: string;
   device?: string;
   appVersion: string;
   buildNumber: string;
   url?: string;
}

export interface DefectResolution {
   type: "fixed" | "wont-fix" | "duplicate" | "not-reproducible" | "by-design";
   description: string;
   resolvedBy: string;
   resolvedDate: Date;
   fixVersion?: string;
   testingNotes?: string;
}

export class DefectManager {
   private defects: Map<string, DefectReport> = new Map();
   private nextId: number = 1;

   createDefect(defectData: Partial<DefectReport>): DefectReport {
      const defect: DefectReport = {
         id: `BUG-${this.nextId.toString().padStart(4, "0")}`,
         title: defectData.title || "",
         description: defectData.description || "",
         severity: defectData.severity || "medium",
         priority: defectData.priority || "P3",
         type: defectData.type || "functional",
         status: "new",
         reportedBy: defectData.reportedBy || "",
         reportedDate: new Date(),
         environment: defectData.environment || this.getDefaultEnvironment(),
         stepsToReproduce: defectData.stepsToReproduce || [],
         expectedResult: defectData.expectedResult || "",
         actualResult: defectData.actualResult || "",
         attachments: defectData.attachments || [],
         tags: defectData.tags || [],
         comments: [],
      };

      this.nextId++;
      this.defects.set(defect.id, defect);

      // Notificar según severidad
      this.notifyStakeholders(defect);

      return defect;
   }

   updateDefectStatus(
      defectId: string,
      newStatus: DefectStatus,
      updatedBy: string,
   ): void {
      const defect = this.defects.get(defectId);
      if (!defect) {
         throw new Error(`Defect ${defectId} not found`);
      }

      const oldStatus = defect.status;
      defect.status = newStatus;

      // Agregar comentario automático
      defect.comments.push({
         author: updatedBy,
         date: new Date(),
         content: `Status changed from ${oldStatus} to ${newStatus}`,
         type: "system",
      });

      // Lógica especial según el nuevo estado
      if (newStatus === "in-progress" && !defect.assignedTo) {
         defect.assignedTo = updatedBy;
         defect.assignedDate = new Date();
      }

      this.notifyStatusChange(defect, oldStatus);
   }

   resolveDefect(defectId: string, resolution: DefectResolution): void {
      const defect = this.defects.get(defectId);
      if (!defect) {
         throw new Error(`Defect ${defectId} not found`);
      }

      defect.resolution = resolution;
      defect.status = "resolved";

      defect.comments.push({
         author: resolution.resolvedBy,
         date: new Date(),
         content: `Defect resolved: ${resolution.description}`,
         type: "resolution",
      });

      this.notifyResolution(defect);
   }

   generateMetrics(startDate?: Date, endDate?: Date): DefectMetrics {
      const filteredDefects = Array.from(this.defects.values()).filter(
         (defect) => {
            if (startDate && defect.reportedDate < startDate) return false;
            if (endDate && defect.reportedDate > endDate) return false;
            return true;
         },
      );

      return {
         total: filteredDefects.length,
         byStatus: this.groupBy(filteredDefects, "status"),
         bySeverity: this.groupBy(filteredDefects, "severity"),
         byType: this.groupBy(filteredDefects, "type"),
         averageResolutionTime: this.calculateAverageResolutionTime(
            filteredDefects,
         ),
         openDefectsAge: this.calculateOpenDefectsAge(filteredDefects),
         defectDiscoveryRate: this.calculateDiscoveryRate(filteredDefects),
      };
   }
}
```

### 2. Crear templates de reporte

```typescript
// src/bug-tracking/report-templates.ts
export class DefectReportTemplate {
   static generateBugReportTemplate(): string {
      return `
# 🐛 Bug Report Template

## Summary
**Brief description of the issue**

## Environment
- **Browser**: Chrome/Firefox/Safari/Edge (version)
- **OS**: Windows/macOS/Linux (version)
- **Device**: Desktop/Mobile/Tablet
- **App Version**: 
- **Build Number**: 
- **URL**: 

## Severity
- [ ] Critical - System unusable
- [ ] High - Major functionality broken  
- [ ] Medium - Functionality impaired with workaround
- [ ] Low - Minor/cosmetic issue

## Priority
- [ ] P1 - Fix immediately
- [ ] P2 - Fix in next release
- [ ] P3 - Fix when possible
- [ ] P4 - Fix in future

## Type
- [ ] Functional
- [ ] Performance
- [ ] Usability
- [ ] Security
- [ ] Compatibility

## Steps to Reproduce
1. 
2. 
3. 

## Expected Result
**What should happen**

## Actual Result
**What actually happens**

## Additional Information
- **Screenshots/Videos**: 
- **Console Errors**: 
- **Network Logs**: 
- **Related Test Case**: 
- **Workaround**: 

## Impact
**Who is affected and how**

---
*Reported by: [Name]*
*Date: [Date]*
*Contact: [Email]*
      `;
   }

   static generateSecurityReportTemplate(): string {
      return `
# 🔒 Security Issue Report

## ⚠️ CONFIDENTIAL - Do not share publicly

## Summary
**Brief description of the security issue**

## Severity
- [ ] Critical - Immediate threat to system/data
- [ ] High - Significant security risk
- [ ] Medium - Moderate security concern
- [ ] Low - Minor security improvement

## Type
- [ ] Injection (SQL, XSS, etc.)
- [ ] Authentication/Authorization
- [ ] Data Exposure
- [ ] Cryptographic Issue
- [ ] Configuration Error
- [ ] Other: ___________

## Affected Components
- **URLs/Endpoints**: 
- **Functions/Methods**: 
- **Database Tables**: 
- **Third-party Services**: 

## Steps to Reproduce
1. 
2. 
3. 

## Proof of Concept
**Include code/screenshots demonstrating the vulnerability**

## Impact Assessment
- **Data at Risk**: 
- **Users Affected**: 
- **Business Impact**: 
- **Compliance Implications**: 

## Recommended Fix
**Suggested solution or mitigation**

## Timeline
- **Disclosure Date**: 
- **Expected Fix Date**: 
- **Verification Date**: 

---
*Reported by: [Name]*
*Security Contact: security@company.com*
*Date: [Date]*
      `;
   }
}
```

### 3. Implementar sistema de notificaciones

```typescript
// src/bug-tracking/notification-system.ts
export interface NotificationRule {
   trigger:
      | "created"
      | "assigned"
      | "status-changed"
      | "resolved"
      | "escalated";
   conditions: NotificationCondition[];
   recipients: NotificationRecipient[];
   channels: NotificationChannel[];
   template: string;
}

export interface NotificationCondition {
   field: keyof DefectReport;
   operator: "equals" | "not-equals" | "greater-than" | "contains";
   value: any;
}

export interface NotificationRecipient {
   type: "user" | "role" | "team";
   identifier: string;
}

export type NotificationChannel = "email" | "slack" | "teams" | "webhook";

export class DefectNotificationSystem {
   private rules: NotificationRule[] = [];

   constructor() {
      this.setupDefaultRules();
   }

   private setupDefaultRules(): void {
      // Regla para defectos críticos
      this.rules.push({
         trigger: "created",
         conditions: [
            { field: "severity", operator: "equals", value: "critical" },
         ],
         recipients: [
            { type: "role", identifier: "tech-lead" },
            { type: "role", identifier: "product-owner" },
            { type: "team", identifier: "dev-team" },
         ],
         channels: ["email", "slack"],
         template: "critical-defect-alert",
      });

      // Regla para defectos de seguridad
      this.rules.push({
         trigger: "created",
         conditions: [{ field: "type", operator: "equals", value: "security" }],
         recipients: [
            { type: "team", identifier: "security-team" },
            { type: "role", identifier: "ciso" },
         ],
         channels: ["email"],
         template: "security-defect-alert",
      });

      // Regla para escalación por tiempo
      this.rules.push({
         trigger: "escalated",
         conditions: [{ field: "priority", operator: "equals", value: "P1" }],
         recipients: [{ type: "role", identifier: "manager" }],
         channels: ["email", "slack"],
         template: "defect-escalation",
      });
   }

   async notifyDefectEvent(defect: DefectReport, event: string): Promise<void> {
      const applicableRules = this.rules.filter(
         (rule) =>
            rule.trigger === event &&
            this.evaluateConditions(defect, rule.conditions),
      );

      for (const rule of applicableRules) {
         await this.sendNotifications(defect, rule);
      }
   }

   private evaluateConditions(
      defect: DefectReport,
      conditions: NotificationCondition[],
   ): boolean {
      return conditions.every((condition) => {
         const fieldValue = defect[condition.field];

         switch (condition.operator) {
            case "equals":
               return fieldValue === condition.value;
            case "not-equals":
               return fieldValue !== condition.value;
            case "contains":
               return String(fieldValue).includes(condition.value);
            default:
               return false;
         }
      });
   }

   private async sendNotifications(
      defect: DefectReport,
      rule: NotificationRule,
   ): Promise<void> {
      const recipients = await this.resolveRecipients(rule.recipients);
      const message = this.generateMessage(defect, rule.template);

      for (const channel of rule.channels) {
         switch (channel) {
            case "email":
               await this.sendEmail(recipients, message);
               break;
            case "slack":
               await this.sendSlackMessage(recipients, message);
               break;
            case "teams":
               await this.sendTeamsMessage(recipients, message);
               break;
            case "webhook":
               await this.sendWebhook(message);
               break;
         }
      }
   }

   private generateMessage(
      defect: DefectReport,
      template: string,
   ): NotificationMessage {
      const templates = {
         "critical-defect-alert": {
            subject: `🚨 CRITICAL DEFECT: ${defect.title}`,
            body: `
A critical defect has been reported:

**ID**: ${defect.id}
**Title**: ${defect.title}
**Severity**: ${defect.severity.toUpperCase()}
**Reporter**: ${defect.reportedBy}
**Environment**: ${defect.environment.appVersion}

**Description**: ${defect.description}

**Immediate Action Required**
Please review and assign this defect immediately.

View defect: ${this.getDefectUrl(defect.id)}
            `,
         },
         "security-defect-alert": {
            subject: `🔒 SECURITY ISSUE: ${defect.title}`,
            body: `
A security issue has been reported:

**ID**: ${defect.id}
**Title**: ${defect.title}
**Type**: ${defect.type}
**Severity**: ${defect.severity.toUpperCase()}

⚠️ CONFIDENTIAL - Handle according to security policy

View defect: ${this.getDefectUrl(defect.id)}
            `,
         },
         "defect-escalation": {
            subject: `⏰ ESCALATION: ${defect.title}`,
            body: `
Defect requires management attention:

**ID**: ${defect.id}
**Title**: ${defect.title}
**Priority**: ${defect.priority}
**Age**: ${this.calculateAge(defect.reportedDate)} days
**Assigned To**: ${defect.assignedTo || "Unassigned"}

This P1 defect requires immediate attention.

View defect: ${this.getDefectUrl(defect.id)}
            `,
         },
      };

      return (
         templates[template] || {
            subject: `Defect Update: ${defect.title}`,
            body: `Defect ${defect.id} has been updated.`,
         }
      );
   }
}
```

### 4. Crear dashboard de métricas

```typescript
// src/bug-tracking/metrics-dashboard.ts
export class DefectMetricsDashboard {
   generateDashboard(metrics: DefectMetrics): DefectDashboard {
      return {
         summary: this.generateSummaryCards(metrics),
         charts: this.generateCharts(metrics),
         trends: this.generateTrends(metrics),
         alerts: this.generateAlerts(metrics),
         recommendations: this.generateRecommendations(metrics),
      };
   }

   private generateSummaryCards(metrics: DefectMetrics): SummaryCard[] {
      return [
         {
            title: "Total Defects",
            value: metrics.total,
            trend: this.calculateTrend(metrics.total),
            color: "blue",
            icon: "bug",
         },
         {
            title: "Critical Defects",
            value: metrics.bySeverity.critical || 0,
            trend: this.calculateTrend(metrics.bySeverity.critical),
            color: metrics.bySeverity.critical > 0 ? "red" : "green",
            icon: "alert-triangle",
         },
         {
            title: "Open Defects",
            value: (metrics.byStatus.open || 0) +
               (metrics.byStatus["in-progress"] || 0),
            trend: this.calculateTrend(metrics.byStatus.open),
            color: "orange",
            icon: "clock",
         },
         {
            title: "Avg Resolution Time",
            value: `${metrics.averageResolutionTime.toFixed(1)} days`,
            trend: this.calculateTrend(metrics.averageResolutionTime),
            color: "purple",
            icon: "zap",
         },
      ];
   }

   private generateCharts(metrics: DefectMetrics): Chart[] {
      return [
         {
            type: "pie",
            title: "Defects by Severity",
            data: Object.entries(metrics.bySeverity).map((
               [severity, count],
            ) => ({
               label: severity,
               value: count,
               color: this.getSeverityColor(severity),
            })),
         },
         {
            type: "bar",
            title: "Defects by Status",
            data: Object.entries(metrics.byStatus).map(([status, count]) => ({
               label: status,
               value: count,
               color: this.getStatusColor(status),
            })),
         },
         {
            type: "line",
            title: "Defect Discovery Rate",
            data: metrics.defectDiscoveryRate.map((point) => ({
               x: point.date,
               y: point.count,
            })),
         },
         {
            type: "histogram",
            title: "Open Defects Age Distribution",
            data: metrics.openDefectsAge.map((age) => ({
               range: `${age.min}-${age.max} days`,
               count: age.count,
            })),
         },
      ];
   }

   generateReport(metrics: DefectMetrics, period: string): string {
      return `
# 📊 Defect Management Report

**Period**: ${period}
**Generated**: ${new Date().toLocaleDateString()}

## Executive Summary
- **Total Defects**: ${metrics.total}
- **Critical Issues**: ${metrics.bySeverity.critical || 0}
- **Average Resolution**: ${metrics.averageResolutionTime.toFixed(1)} days
- **Open Defects**: ${
         (metrics.byStatus.open || 0) + (metrics.byStatus["in-progress"] || 0)
      }

## Key Insights
${this.generateInsights(metrics)}

## Quality Indicators
- **Defect Escape Rate**: ${this.calculateEscapeRate(metrics)}%
- **Fix Rate**: ${this.calculateFixRate(metrics)}%
- **Reopened Rate**: ${this.calculateReopenRate(metrics)}%

## Recommendations
${
         this.generateRecommendations(metrics)
            .map((rec) => `- ${rec}`)
            .join("\n")
      }

## Top Issues by Impact
${this.getTopIssuesByImpact(metrics)}

---
*Report generated automatically by Defect Management System*
      `;
   }
}
```

### 5. Automatizar flujos de trabajo

```yaml
# .github/workflows/defect-management.yml
name: Defect Management Automation

on:
   issues:
      types: [opened, labeled, assigned, closed]
   issue_comment:
      types: [created]

jobs:
   auto-label-defect:
      if: contains(github.event.issue.title, '[BUG]') || contains(github.event.issue.labels.*.name, 'bug')
      runs-on: ubuntu-latest
      steps:
         - name: Auto-assign severity label
           uses: actions/github-script@v6
           with:
              script: |
                 const issue = context.payload.issue;
                 const body = issue.body.toLowerCase();

                 let severity = 'medium';
                 if (body.includes('critical') || body.includes('system down')) {
                   severity = 'critical';
                 } else if (body.includes('high') || body.includes('major')) {
                   severity = 'high';
                 } else if (body.includes('low') || body.includes('minor')) {
                   severity = 'low';
                 }

                 await github.rest.issues.addLabels({
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   issue_number: issue.number,
                   labels: [`severity: ${severity}`]
                 });

   notify-critical-defect:
      if: contains(github.event.issue.labels.*.name, 'severity: critical')
         runs-on: ubuntu-latest
         steps:
            - name: Notify team of critical defect
              uses: 8398a7/action-slack@v3
              with:
                 status: custom
                 custom_payload: |
                    {
                      channel: '#alerts',
                      username: 'Defect Bot',
                      icon_emoji: ':bug:',
                      attachments: [{
                        color: 'danger',
                        fields: [{
                          title: '🚨 CRITICAL DEFECT REPORTED',
                          value: `Issue #${{ github.event.issue.number }}: ${{ github.event.issue.title }}`,
                          short: false
                        }, {
                          title: 'Reporter',
                          value: '${{ github.event.issue.user.login }}',
                          short: true
                        }, {
                          title: 'URL',
                          value: '${{ github.event.issue.html_url }}',
                          short: true
                        }]
                      }]
                    }
              env:
                 SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

   auto-assign-defect:
      if: github.event.action == 'labeled' && contains(github.event.label.name, 'severity:')
      runs-on: ubuntu-latest
      steps:
         - name: Auto-assign based on component
           uses: actions/github-script@v6
           with:
              script: |
                 const issue = context.payload.issue;
                 const body = issue.body.toLowerCase();

                 let assignee = null;
                 if (body.includes('frontend') || body.includes('ui')) {
                   assignee = 'frontend-team-lead';
                 } else if (body.includes('backend') || body.includes('api')) {
                   assignee = 'backend-team-lead';
                 } else if (body.includes('database') || body.includes('sql')) {
                   assignee = 'database-admin';
                 }

                 if (assignee) {
                   await github.rest.issues.addAssignees({
                     owner: context.repo.owner,
                     repo: context.repo.repo,
                     issue_number: issue.number,
                     assignees: [assignee]
                   });
                 }

   generate-defect-report:
      if: github.event.action == 'closed'
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Generate defect metrics
           run: |
              echo "Generating defect resolution report..."
              node scripts/generate-defect-metrics.js \
                --issue-number=${{ github.event.issue.number }} \
                --output=defect-report.json

         - name: Update dashboard
           run: |
              echo "Updating defect dashboard..."
              node scripts/update-defect-dashboard.js \
                --input=defect-report.json
```

## Tips

- **Información completa**: Incluye toda la información necesaria para
  reproducir el defecto
- **Priorización clara**: Usa criterios consistentes para severidad y prioridad
- **Comunicación efectiva**: Mantén a todos los stakeholders informados del
  progreso
- **Métricas útiles**: Rastrea métricas que ayuden a mejorar el proceso de
  desarrollo
- **Automatización**: Automatiza tareas repetitivas como notificaciones y
  asignaciones
- **Templates**: Usa templates para asegurar información consistente
- **Seguimiento activo**: Revisa regularmente el estado de los defectos abiertos
- **Aprendizaje continuo**: Analiza patrones para prevenir defectos similares

## Ejemplos

### Template de Reporte de Bug Detallado

```markdown
# 🐛 BUG-0123: Login failure with special characters in password

## Summary

Users cannot log in when their password contains special characters like @, #, $

## Environment

- **Browser**: Chrome 119.0.6045.105
- **OS**: macOS Ventura 13.6
- **Device**: MacBook Pro M1
- **App Version**: 2.1.4
- **Build**: #456
- **URL**: https://app.example.com/login

## Classification

- **Severity**: High ⚠️
- **Priority**: P2
- **Type**: Functional
- **Component**: Authentication

## Steps to Reproduce

1. Navigate to login page (https://app.example.com/login)
2. Enter valid email: test@example.com
3. Enter password with special chars: MyP@ss#123
4. Click "Sign In" button
5. Observe error message

## Expected Result

User should be successfully authenticated and redirected to dashboard

## Actual Result

Error message displayed: "Invalid credentials" despite correct password

## Evidence

![Login Error Screenshot](screenshots/login-error.png)

### Console Errors

POST /api/auth/login 400 (Bad Request) SyntaxError: Unexpected token @ in JSON
at position 45

### Network Request

{ "email": "test@example.com", "password": "MyP@ss#123" // Special chars not
escaped }

## Impact Analysis

- **Users Affected**: ~15% of users (estimated 150 users)
- **Business Impact**: Medium - affects user onboarding
- **Workaround**: Users can reset password to avoid special chars

## Additional Information

- **Related Test Case**: TC-AUTH-001
- **Similar Issues**: BUG-0098 (special chars in username)
- **Browser Testing**: Issue reproduced in Firefox and Safari

## Root Cause Analysis

The frontend is not properly escaping special characters before sending to API

## Suggested Fix

1. Implement proper input sanitization in login form
2. Update password validation regex
3. Add unit tests for special character handling

---

**Reported by**: Maria Garcia (QA Engineer) **Date**: 2024-12-15 10:30 AM
**Contact**: maria.garcia@company.com **Assignee**: John Smith (Frontend
Developer)
```

## Navegación

[⬅️ Pruebas de Aceptación del Usuario](./pruebas-aceptacion-usuario.md) |
[🏠 README Principal](../../README.md) |
[Testing de Performance y Carga ➡️](./testing-performance-carga.md)
