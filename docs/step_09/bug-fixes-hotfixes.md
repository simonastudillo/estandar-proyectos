# Bug Fixes y Hotfixes

## ¿Qué es?

Los bug fixes y hotfixes son procesos críticos de mantenimiento que permiten
corregir errores en el software de manera controlada y eficiente. Los bug fixes
son correcciones planificadas para errores no críticos que se incluyen en
releases regulares, mientras que los hotfixes son correcciones urgentes para
problemas críticos que requieren despliegue inmediato en producción.

En el contexto de Clean Architecture + DDD, las correcciones deben mantener la
integridad de las capas y no comprometer las reglas de negocio establecidas. Es
crucial que los fixes preserven la separación de responsabilidades y no
introduzcan coupling no deseado entre capas.

## ¿Por qué es importante?

- **Continuidad del servicio**: Mantiene la aplicación funcionando correctamente
- **Experiencia del usuario**: Previene frustración y abandono de usuarios
- **Credibilidad del negocio**: Mantiene la confianza de los stakeholders
- **Mitigación de riesgos**: Reduce el impacto de vulnerabilidades de seguridad
- **Cumplimiento de SLAs**: Asegura el cumplimiento de acuerdos de nivel de
  servicio
- **Prevención de escalamiento**: Evita que problemas menores se conviertan en
  crisis
- **Aprendizaje organizacional**: Genera conocimiento para prevenir futuros
  problemas

## ¿Qué debe incluir?

### Clasificación de Errores

- **Severidad**: Critical, High, Medium, Low
- **Impacto**: Número de usuarios afectados
- **Urgencia**: Tiempo máximo para resolución
- **Categoría**: Funcional, Performance, Seguridad, UI/UX

### Proceso de Bug Fixes

- Triage y priorización de bugs
- Asignación a desarrolladores apropiados
- Análisis de causa raíz
- Implementación de fix con tests
- Code review y testing
- Despliegue controlado

### Proceso de Hotfixes

- Escalamiento inmediato para issues críticos
- Bypass de procesos normales cuando sea necesario
- Comunicación urgente a stakeholders
- Rollback plan preparado
- Post-mortem obligatorio

### Documentación y Seguimiento

- Tracking de bugs en sistema de tickets
- Documentación de reprodución
- Análisis de impacto y solución
- Comunicación de status a stakeholders
- Métricas de resolución

## ¿Qué debo hacer?

### 1. Establecer Clasificación de Severidad

**Definir niveles de severidad:**

```php
<?php
// app/Domain/Support/ValueObjects/BugSeverity.php
namespace App\Domain\Support\ValueObjects;

enum BugSeverity: string
{
    case CRITICAL = 'critical';    // Aplicación no funciona, data loss
    case HIGH = 'high';           // Funcionalidad principal afectada
    case MEDIUM = 'medium';       // Funcionalidad secundaria afectada  
    case LOW = 'low';            // Problemas menores, mejoras

    public function getMaxResolutionTime(): int
    {
        return match($this) {
            self::CRITICAL => 2,    // 2 horas
            self::HIGH => 24,       // 1 día
            self::MEDIUM => 168,    // 1 semana
            self::LOW => 720,       // 1 mes
        };
    }

    public function requiresHotfix(): bool
    {
        return in_array($this, [self::CRITICAL, self::HIGH]);
    }
}
```

### 2. Implementar Workflow de Bug Fix

**Proceso estándar para bug fixes:**

```yaml
# .github/workflows/bug-fix.yml
name: Bug Fix Workflow

on:
   push:
      branches: [bugfix/*]

jobs:
   validate-bug-fix:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Environment
           uses: ./.github/actions/setup-environment

         - name: Run Tests
           run: |
              # Tests unitarios específicos
              ./vendor/bin/phpunit --group=bug-fix
              npm run test:unit

         - name: Run Integration Tests
           run: |
              ./vendor/bin/phpunit --group=integration
              npm run test:integration

         - name: Validate Fix
           run: |
              # Validar que el bug específico está corregido
              ./scripts/validate-bug-fix.sh ${{ github.event.head_commit.message }}

         - name: Performance Test
           run: |
              # Asegurar que el fix no afecta performance
              ./scripts/performance-regression-test.sh

   security-scan:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
         - name: Security Scan
           run: |
              composer audit
              npm audit
              ./scripts/security-scan.sh
```

### 3. Configurar Proceso de Hotfix

**Workflow de emergencia para hotfixes:**

```yaml
# .github/workflows/hotfix.yml
name: Emergency Hotfix

on:
   push:
      branches: [hotfix/*]

jobs:
   emergency-deploy:
      runs-on: ubuntu-latest
      if: contains(github.event.head_commit.message, '[EMERGENCY]')
      steps:
         - uses: actions/checkout@v3

         - name: Notify Team
           run: |
              curl -X POST ${{ secrets.SLACK_WEBHOOK }} \
                -H 'Content-type: application/json' \
                --data '{
                  "text": "🚨 EMERGENCY HOTFIX INITIATED",
                  "attachments": [{
                    "color": "danger",
                    "fields": [{
                      "title": "Commit",
                      "value": "${{ github.event.head_commit.message }}",
                      "short": false
                    }]
                  }]
                }'

         - name: Critical Tests Only
           run: |
              # Solo tests críticos para hotfix rápido
              ./vendor/bin/phpunit --group=critical
              npm run test:critical

         - name: Deploy to Production
           run: |
              ./scripts/emergency-deploy.sh

         - name: Health Check
           run: |
              sleep 30
              ./scripts/health-check.sh

         - name: Rollback on Failure
           if: failure()
           run: |
              ./scripts/rollback.sh

         - name: Post-Deploy Notification
           run: |
              STATUS="${{ job.status }}"
              COLOR=$([ "$STATUS" = "success" ] && echo "good" || echo "danger")
              curl -X POST ${{ secrets.SLACK_WEBHOOK }} \
                -H 'Content-type: application/json' \
                --data "{
                  \"text\": \"Hotfix deployment $STATUS\",
                  \"attachments\": [{
                    \"color\": \"$COLOR\",
                    \"fields\": [{
                      \"title\": \"Status\",
                      \"value\": \"$STATUS\",
                      \"short\": true
                    }]
                  }]
                }"
```

### 4. Sistema de Tracking de Bugs

**Service para gestión de bugs:**

```php
<?php
// app/Application/Services/BugTrackingService.php
namespace App\Application\Services;

use App\Domain\Support\Entities\Bug;
use App\Domain\Support\ValueObjects\BugSeverity;
use App\Domain\Support\Repositories\BugRepositoryInterface;

class BugTrackingService
{
    public function __construct(
        private BugRepositoryInterface $bugRepository,
        private NotificationService $notificationService
    ) {}

    public function reportBug(
        string $title,
        string $description,
        BugSeverity $severity,
        array $metadata = []
    ): Bug {
        $bug = Bug::create(
            title: $title,
            description: $description,
            severity: $severity,
            metadata: $metadata
        );

        $this->bugRepository->save($bug);

        // Notificar según severidad
        if ($severity->requiresHotfix()) {
            $this->notificationService->sendUrgentAlert($bug);
        }

        return $bug;
    }

    public function assignBug(string $bugId, string $assigneeId): void
    {
        $bug = $this->bugRepository->findById($bugId);
        $bug->assignTo($assigneeId);
        
        $this->bugRepository->save($bug);
        
        $this->notificationService->notifyAssignment($bug, $assigneeId);
    }

    public function resolveBug(string $bugId, string $resolution): void
    {
        $bug = $this->bugRepository->findById($bugId);
        $bug->resolve($resolution);
        
        $this->bugRepository->save($bug);
        
        $this->notificationService->notifyResolution($bug);
    }

    public function generateBugReport(): array
    {
        return [
            'total_bugs' => $this->bugRepository->count(),
            'open_bugs' => $this->bugRepository->countByStatus('open'),
            'critical_bugs' => $this->bugRepository->countBySeverity(BugSeverity::CRITICAL),
            'avg_resolution_time' => $this->bugRepository->getAverageResolutionTime(),
            'bugs_by_component' => $this->bugRepository->getBugsByComponent(),
        ];
    }
}
```

## Tips

### Identificación Rápida de Bugs

- **Monitoring activo**: Usa alertas automáticas para detectar anomalías
- **User feedback**: Implementa sistemas fáciles de reporte para usuarios
- **Log analysis**: Analiza logs regularmente para identificar patrones
- **Performance monitoring**: Detecta degradación de performance

### Priorización Efectiva

- **Business impact**: Considera el impacto en objectives de negocio
- **User impact**: Evalúa cuántos usuarios están afectados
- **Workarounds**: Considera si existen soluciones temporales
- **Technical debt**: Evalúa si el fix puede generar más problemas

### Hotfix Best Practices

- **Minimal changes**: Hace solo los cambios necesarios
- **Thorough testing**: Aunque sea urgente, testing crítico es obligatorio
- **Clear communication**: Mantén a todos informados del progreso
- **Documentation**: Documenta la razón y solución del hotfix

### Prevención de Regresiones

- **Regression tests**: Crea tests específicos para cada bug fix
- **Code review**: Revisión obligatoria incluso para hotfixes
- **Automated testing**: Expande cobertura de tests automáticos
- **Root cause analysis**: Identifica y soluciona causas fundamentales

## Ejemplos

### 1. Bug Entity (Domain Layer)

```php
<?php
// app/Domain/Support/Entities/Bug.php
namespace App\Domain\Support\Entities;

use App\Domain\Support\ValueObjects\BugSeverity;
use App\Domain\Support\ValueObjects\BugStatus;

class Bug
{
    private function __construct(
        private string $id,
        private string $title,
        private string $description,
        private BugSeverity $severity,
        private BugStatus $status,
        private ?string $assigneeId,
        private array $metadata,
        private \DateTimeImmutable $createdAt,
        private ?\DateTimeImmutable $resolvedAt = null
    ) {}

    public static function create(
        string $title,
        string $description,
        BugSeverity $severity,
        array $metadata = []
    ): self {
        return new self(
            id: uniqid('bug_'),
            title: $title,
            description: $description,
            severity: $severity,
            status: BugStatus::OPEN,
            assigneeId: null,
            metadata: $metadata,
            createdAt: new \DateTimeImmutable()
        );
    }

    public function assignTo(string $assigneeId): void
    {
        $this->assigneeId = $assigneeId;
        $this->status = BugStatus::ASSIGNED;
    }

    public function resolve(string $resolution): void
    {
        $this->status = BugStatus::RESOLVED;
        $this->resolvedAt = new \DateTimeImmutable();
        $this->metadata['resolution'] = $resolution;
    }

    public function escalate(): void
    {
        if ($this->severity !== BugSeverity::CRITICAL) {
            // Escalar severidad
            $this->severity = match($this->severity) {
                BugSeverity::LOW => BugSeverity::MEDIUM,
                BugSeverity::MEDIUM => BugSeverity::HIGH,
                BugSeverity::HIGH => BugSeverity::CRITICAL,
                default => $this->severity
            };
        }
    }

    public function isOverdue(): bool
    {
        $maxHours = $this->severity->getMaxResolutionTime();
        $deadline = $this->createdAt->modify("+{$maxHours} hours");
        
        return new \DateTimeImmutable() > $deadline && $this->status !== BugStatus::RESOLVED;
    }

    // Getters...
    public function getId(): string { return $this->id; }
    public function getSeverity(): BugSeverity { return $this->severity; }
    public function getStatus(): BugStatus { return $this->status; }
    public function isResolved(): bool { return $this->status === BugStatus::RESOLVED; }
}
```

### 2. Script de Validación de Bug Fix

```bash
#!/bin/bash
# scripts/validate-bug-fix.sh

BUG_ID=$1
if [ -z "$BUG_ID" ]; then
    echo "Error: Bug ID requerido"
    exit 1
fi

echo "Validando fix para bug: $BUG_ID"

# Extraer información del bug
BUG_INFO=$(curl -s "http://api.example.com/bugs/$BUG_ID")
REPRODUCTION_STEPS=$(echo "$BUG_INFO" | jq -r '.reproduction_steps')

# Ejecutar tests específicos del bug
if [ -f "tests/bugs/test_bug_$BUG_ID.php" ]; then
    echo "Ejecutando test específico del bug..."
    ./vendor/bin/phpunit "tests/bugs/test_bug_$BUG_ID.php"
    
    if [ $? -ne 0 ]; then
        echo "❌ Test específico del bug falló"
        exit 1
    fi
else
    echo "⚠️ No se encontró test específico para el bug"
fi

# Validar que el bug no puede reproducirse
echo "Validando que el bug no puede reproducirse..."
REPRODUCTION_RESULT=$(./scripts/reproduce-bug.sh "$BUG_ID")

if echo "$REPRODUCTION_RESULT" | grep -q "BUG_REPRODUCED"; then
    echo "❌ El bug aún puede reproducirse"
    exit 1
fi

echo "✅ Validación del fix completada exitosamente"
```

### 3. Sistema de Alertas para Bugs Críticos

```php
<?php
// app/Infrastructure/Monitoring/BugAlertService.php
namespace App\Infrastructure\Monitoring;

use App\Domain\Support\Entities\Bug;
use App\Domain\Support\ValueObjects\BugSeverity;

class BugAlertService
{
    public function __construct(
        private SlackNotifier $slack,
        private EmailNotifier $email,
        private SmsNotifier $sms
    ) {}

    public function sendBugAlert(Bug $bug): void
    {
        $message = $this->formatBugMessage($bug);

        switch ($bug->getSeverity()) {
            case BugSeverity::CRITICAL:
                $this->sendCriticalAlert($bug, $message);
                break;
                
            case BugSeverity::HIGH:
                $this->sendHighPriorityAlert($bug, $message);
                break;
                
            case BugSeverity::MEDIUM:
                $this->slack->send($message);
                break;
                
            case BugSeverity::LOW:
                // Solo logging para bugs de baja prioridad
                logger()->info("New low priority bug reported", ['bug_id' => $bug->getId()]);
                break;
        }
    }

    private function sendCriticalAlert(Bug $bug, string $message): void
    {
        // Múltiples canales para bugs críticos
        $this->slack->sendToChannel('#emergencies', $message);
        $this->email->sendToOnCallTeam($message);
        $this->sms->sendToOnCallPrimary($message);
        
        // Crear incident en PagerDuty/OpsGenie
        $this->createIncident($bug);
    }

    private function sendHighPriorityAlert(Bug $bug, string $message): void
    {
        $this->slack->sendToChannel('#bugs-high-priority', $message);
        $this->email->sendToTeamLead($message);
    }

    private function formatBugMessage(Bug $bug): string
    {
        $emoji = match($bug->getSeverity()) {
            BugSeverity::CRITICAL => '🚨',
            BugSeverity::HIGH => '🔥',
            BugSeverity::MEDIUM => '⚠️',
            BugSeverity::LOW => 'ℹ️'
        };

        return sprintf(
            "%s **Bug Reportado** - %s\n" .
            "**ID:** %s\n" .
            "**Severidad:** %s\n" .
            "**Descripción:** %s\n" .
            "**Link:** %s",
            $emoji,
            $bug->getTitle(),
            $bug->getId(),
            $bug->getSeverity()->value,
            substr($bug->getDescription(), 0, 200),
            "https://bugs.example.com/{$bug->getId()}"
        );
    }
}
```

### 4. Frontend - Error Boundary para Bugs

```typescript
// src/components/ErrorBoundary.tsx
import React, { Component, ErrorInfo, ReactNode } from "react";

interface Props {
   children: ReactNode;
   fallback?: ReactNode;
}

interface State {
   hasError: boolean;
   error?: Error;
   errorInfo?: ErrorInfo;
}

export class ErrorBoundary extends Component<Props, State> {
   constructor(props: Props) {
      super(props);
      this.state = { hasError: false };
   }

   static getDerivedStateFromError(error: Error): State {
      return { hasError: true, error };
   }

   componentDidCatch(error: Error, errorInfo: ErrorInfo) {
      this.setState({ errorInfo });

      // Reportar el error automáticamente
      this.reportError(error, errorInfo);
   }

   private async reportError(error: Error, errorInfo: ErrorInfo) {
      const bugReport = {
         title: `Frontend Error: ${error.message}`,
         description: `
        Error: ${error.message}
        Stack: ${error.stack}
        Component Stack: ${errorInfo.componentStack}
        User Agent: ${navigator.userAgent}
        URL: ${window.location.href}
        Timestamp: ${new Date().toISOString()}
      `,
         severity: "high",
         metadata: {
            type: "frontend_error",
            browser: navigator.userAgent,
            url: window.location.href,
            userId: this.getCurrentUserId(),
         },
      };

      try {
         await fetch("/api/bugs", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(bugReport),
         });
      } catch (reportError) {
         console.error("Failed to report error:", reportError);
      }
   }

   private getCurrentUserId(): string | null {
      // Obtener ID del usuario actual
      return localStorage.getItem("userId") || null;
   }

   render() {
      if (this.state.hasError) {
         return this.props.fallback || (
            <div className="error-boundary">
               <h2>Oops! Algo salió mal</h2>
               <p>
                  Ha ocurrido un error inesperado. El equipo técnico ha sido
                  notificado.
               </p>
               <button onClick={() => window.location.reload()}>
                  Recargar página
               </button>
            </div>
         );
      }

      return this.props.children;
   }
}
```

## Navegación

[⬅️ Monitoreo Post-Deployment](./monitoreo-post-deployment.md) |
[🏠 README Principal](../../README.md) |
[Actualización de Dependencias ➡️](./actualizacion-dependencias.md)
