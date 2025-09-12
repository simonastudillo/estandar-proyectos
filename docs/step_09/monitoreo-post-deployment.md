# Monitoreo Post-Deployment

## ¿Qué es?

El monitoreo post-deployment es el proceso de observación continua del sistema
una vez que ha sido desplegado en producción. Incluye la recolección, análisis y
visualización de métricas, logs y eventos para garantizar que la aplicación
funcione correctamente, identificar problemas antes de que afecten a los
usuarios y mantener la salud general del sistema.

En nuestra arquitectura Clean Architecture + DDD, el monitoreo debe abarcar
todas las capas (Domain, Application, Infrastructure) y proporcionar visibilidad
sobre el comportamiento de los casos de uso, entidades y servicios de dominio.

## ¿Por qué es importante?

- **Detección temprana**: Identifica problemas antes de que impacten a los
  usuarios
- **Disponibilidad del servicio**: Garantiza alta disponibilidad y tiempo de
  actividad
- **Performance optimization**: Identifica cuellos de botella y oportunidades de
  mejora
- **Capacity planning**: Proporciona datos para planificar el crecimiento de
  recursos
- **Compliance y auditoría**: Cumple con requisitos regulatorios y de auditoría
- **Experiencia del usuario**: Monitorea métricas que impactan directamente a
  los usuarios
- **Reducción de MTTR**: Disminuye el tiempo medio de resolución de incidentes

## ¿Qué debe incluir?

### Métricas de Aplicación

- Response time por endpoint y caso de uso
- Throughput y latencia de requests
- Error rates y tipos de errores
- CPU, memoria y uso de disco
- Conexiones de base de datos activas

### Métricas de Negocio

- Usuarios activos y sesiones
- Conversiones y transacciones completadas
- Revenue metrics en tiempo real
- Feature adoption rates
- Customer satisfaction scores

### Logging y Trazabilidad

- Logs estructurados con correlación IDs
- Distributed tracing entre servicios
- Error tracking y stack traces
- Audit logs para compliance
- Security event logging

### Alertas Inteligentes

- Umbrales dinámicos basados en patrones históricos
- Escalamiento automático de alertas
- Integración con sistemas de notificación
- Supresión de alertas duplicadas
- Context-aware alerting

## ¿Qué debo hacer?

### 1. Configurar Stack de Monitoreo

**Implementar Prometheus + Grafana:**

```yaml
# docker-compose.monitoring.yml
version: "3.8"
services:
   prometheus:
      image: prom/prometheus:latest
      container_name: prometheus
      ports:
         - "9090:9090"
      volumes:
         - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
         - prometheus_data:/prometheus
      command:
         - "--config.file=/etc/prometheus/prometheus.yml"
         - "--storage.tsdb.path=/prometheus"
         - "--web.console.libraries=/etc/prometheus/console_libraries"
         - "--web.console.templates=/etc/prometheus/consoles"
         - "--storage.tsdb.retention.time=200h"
         - "--web.enable-lifecycle"

   grafana:
      image: grafana/grafana:latest
      container_name: grafana
      ports:
         - "3000:3000"
      environment:
         - GF_SECURITY_ADMIN_USER=admin
         - GF_SECURITY_ADMIN_PASSWORD=grafana
      volumes:
         - grafana_data:/var/lib/grafana
         - ./grafana/provisioning:/etc/grafana/provisioning

   node-exporter:
      image: prom/node-exporter:latest
      container_name: node-exporter
      ports:
         - "9100:9100"
      volumes:
         - /proc:/host/proc:ro
         - /sys:/host/sys:ro
         - /:/rootfs:ro
      command:
         - "--path.procfs=/host/proc"
         - "--path.rootfs=/rootfs"
         - "--path.sysfs=/host/sys"
         - "--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)"

volumes:
   prometheus_data:
   grafana_data:
```

### 2. Implementar Métricas Personalizadas

**Backend Laravel - Métricas de aplicación:**

```php
<?php
// app/Infrastructure/Monitoring/PrometheusMetrics.php
namespace App\Infrastructure\Monitoring;

use Prometheus\CollectorRegistry;
use Prometheus\Storage\Redis;

class PrometheusMetrics
{
    private CollectorRegistry $registry;
    
    public function __construct()
    {
        $redis = new Redis(['host' => env('REDIS_HOST', 'localhost')]);
        $this->registry = new CollectorRegistry($redis);
    }
    
    public function incrementUseCaseCounter(string $useCase, string $status): void
    {
        $counter = $this->registry->getOrRegisterCounter(
            'app',
            'use_case_executions_total',
            'Total use case executions',
            ['use_case', 'status']
        );
        
        $counter->inc([$useCase, $status]);
    }
    
    public function recordUseCaseExecutionTime(string $useCase, float $duration): void
    {
        $histogram = $this->registry->getOrRegisterHistogram(
            'app',
            'use_case_duration_seconds',
            'Use case execution duration',
            ['use_case'],
            [0.1, 0.5, 1.0, 2.5, 5.0, 10.0]
        );
        
        $histogram->observe($duration, [$useCase]);
    }
    
    public function setActiveUsers(int $count): void
    {
        $gauge = $this->registry->getOrRegisterGauge(
            'app',
            'active_users_current',
            'Current active users'
        );
        
        $gauge->set($count);
    }
}
```

**Middleware para métricas automáticas:**

```php
<?php
// app/Infrastructure/Http/Middleware/MetricsMiddleware.php
namespace App\Infrastructure\Http\Middleware;

use App\Infrastructure\Monitoring\PrometheusMetrics;
use Closure;
use Illuminate\Http\Request;

class MetricsMiddleware
{
    public function __construct(
        private PrometheusMetrics $metrics
    ) {}
    
    public function handle(Request $request, Closure $next)
    {
        $startTime = microtime(true);
        
        $response = $next($request);
        
        $duration = microtime(true) - $startTime;
        $status = $response->getStatusCode() >= 400 ? 'error' : 'success';
        $endpoint = $request->route()?->getName() ?? 'unknown';
        
        $this->metrics->incrementUseCaseCounter($endpoint, $status);
        $this->metrics->recordUseCaseExecutionTime($endpoint, $duration);
        
        return $response;
    }
}
```

### 3. Configurar Logging Estructurado

**Laravel - Structured logging:**

```php
<?php
// config/logging.php
return [
    'channels' => [
        'structured' => [
            'driver' => 'daily',
            'path' => storage_path('logs/structured.log'),
            'level' => env('LOG_LEVEL', 'debug'),
            'formatter' => App\Infrastructure\Logging\StructuredFormatter::class,
        ],
    ],
];

// app/Infrastructure/Logging/StructuredFormatter.php
namespace App\Infrastructure\Logging;

use Monolog\Formatter\JsonFormatter;

class StructuredFormatter extends JsonFormatter
{
    public function format($record): string
    {
        $record['extra']['correlation_id'] = request()->header('X-Correlation-ID');
        $record['extra']['user_id'] = auth()->id();
        $record['extra']['environment'] = app()->environment();
        
        return parent::format($record);
    }
}
```

### 4. Implementar Health Checks

**Endpoint de health check:**

```php
<?php
// app/Infrastructure/Http/Controllers/HealthController.php
namespace App\Infrastructure\Http\Controllers;

use App\Application\Services\HealthCheckService;
use Illuminate\Http\JsonResponse;

class HealthController extends Controller
{
    public function __construct(
        private HealthCheckService $healthService
    ) {}
    
    public function check(): JsonResponse
    {
        $checks = $this->healthService->performChecks();
        $isHealthy = collect($checks)->every(fn($check) => $check['status'] === 'ok');
        
        return response()->json([
            'status' => $isHealthy ? 'healthy' : 'unhealthy',
            'timestamp' => now()->toISOString(),
            'checks' => $checks
        ], $isHealthy ? 200 : 503);
    }
}

// app/Application/Services/HealthCheckService.php
namespace App\Application\Services;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Redis;

class HealthCheckService
{
    public function performChecks(): array
    {
        return [
            'database' => $this->checkDatabase(),
            'redis' => $this->checkRedis(),
            'storage' => $this->checkStorage(),
            'external_apis' => $this->checkExternalAPIs(),
        ];
    }
    
    private function checkDatabase(): array
    {
        try {
            DB::select('SELECT 1');
            return ['status' => 'ok', 'latency' => $this->measureDbLatency()];
        } catch (\Exception $e) {
            return ['status' => 'error', 'message' => $e->getMessage()];
        }
    }
    
    private function checkRedis(): array
    {
        try {
            Redis::ping();
            return ['status' => 'ok'];
        } catch (\Exception $e) {
            return ['status' => 'error', 'message' => $e->getMessage()];
        }
    }
}
```

## Tips

### Configuración de Alertas Efectivas

- **Evita alert fatigue**: Configura umbrales apropiados para evitar spam
- **Context matters**: Incluye contexto suficiente en las alertas
- **Escalamiento gradual**: Implementa escalamiento por severidad
- **Runbooks**: Documenta procedimientos de respuesta para cada alerta

### Métricas Relevantes

- **Golden Signals**: Latencia, tráfico, errores y saturación
- **Business metrics**: Métricas que impactan directamente el negocio
- **User experience**: Métricas desde la perspectiva del usuario
- **SLI/SLO**: Define Service Level Indicators y Objectives claros

### Performance de Monitoreo

- **Sampling**: Usa sampling para reducir overhead en alto tráfico
- **Batching**: Agrupa métricas antes de enviarlas
- **Asynchronous**: Procesa métricas de forma asíncrona
- **Retention**: Define políticas de retención apropiadas

### Seguridad en Monitoreo

- **Data sanitization**: No logees información sensible
- **Access control**: Controla quien puede acceder a las métricas
- **Encryption**: Encripta datos en tránsito y en reposo
- **Audit trails**: Mantén logs de acceso a sistemas de monitoreo

## Ejemplos

### 1. Dashboard de Grafana para Laravel

```json
{
   "dashboard": {
      "title": "Laravel Application Dashboard",
      "panels": [
         {
            "title": "Request Rate",
            "type": "graph",
            "targets": [
               {
                  "expr": "rate(app_use_case_executions_total[5m])",
                  "legendFormat": "{{use_case}}"
               }
            ]
         },
         {
            "title": "Response Time",
            "type": "graph",
            "targets": [
               {
                  "expr": "histogram_quantile(0.95, rate(app_use_case_duration_seconds_bucket[5m]))",
                  "legendFormat": "95th percentile"
               }
            ]
         },
         {
            "title": "Error Rate",
            "type": "stat",
            "targets": [
               {
                  "expr": "rate(app_use_case_executions_total{status=\"error\"}[5m]) / rate(app_use_case_executions_total[5m]) * 100"
               }
            ]
         }
      ]
   }
}
```

### 2. Alertas de Prometheus

```yaml
# alerts.yml
groups:
   - name: application.rules
     rules:
        - alert: HighErrorRate
          expr: rate(app_use_case_executions_total{status="error"}[5m]) / rate(app_use_case_executions_total[5m]) > 0.05
          for: 5m
          labels:
             severity: warning
          annotations:
             summary: "High error rate detected"
             description: "Error rate is {{ $value | humanizePercentage }} for the last 5 minutes"

        - alert: HighResponseTime
          expr: histogram_quantile(0.95, rate(app_use_case_duration_seconds_bucket[5m])) > 2
          for: 10m
          labels:
             severity: critical
          annotations:
             summary: "High response time detected"
             description: "95th percentile response time is {{ $value }}s"

        - alert: DatabaseDown
          expr: up{job="mysql-exporter"} == 0
          for: 0m
          labels:
             severity: critical
          annotations:
             summary: "Database is down"
             description: "MySQL database has been down for more than 0 minutes"
```

### 3. Script de Monitoreo Custom

```bash
#!/bin/bash
# monitoring-health-check.sh

HEALTH_ENDPOINT="https://api.example.com/health"
PROMETHEUS_GATEWAY="http://prometheus-pushgateway:9091"

# Realizar health check
RESPONSE=$(curl -s -w "%{http_code}" -o /dev/null "$HEALTH_ENDPOINT")
TIMESTAMP=$(date +%s)

# Enviar métrica a Prometheus
if [ "$RESPONSE" = "200" ]; then
    echo "app_health_status 1" | curl -X POST --data-binary @- "$PROMETHEUS_GATEWAY/metrics/job/health-check/instance/$(hostname)"
else
    echo "app_health_status 0" | curl -X POST --data-binary @- "$PROMETHEUS_GATEWAY/metrics/job/health-check/instance/$(hostname)"
    # Enviar alerta a Slack
    curl -X POST -H 'Content-type: application/json' \
        --data "{\"text\":\"🚨 Health check failed: HTTP $RESPONSE\"}" \
        "$SLACK_WEBHOOK_URL"
fi

echo "Health check completed: HTTP $RESPONSE"
```

### 4. Frontend React - Monitoreo de Performance

```typescript
// src/services/monitoring.service.ts
interface PerformanceMetric {
   name: string;
   value: number;
   timestamp: number;
   labels?: Record<string, string>;
}

export class MonitoringService {
   private static instance: MonitoringService;
   private metricsBuffer: PerformanceMetric[] = [];

   static getInstance(): MonitoringService {
      if (!MonitoringService.instance) {
         MonitoringService.instance = new MonitoringService();
      }
      return MonitoringService.instance;
   }

   trackPageLoad(pageName: string, loadTime: number): void {
      this.addMetric({
         name: "page_load_duration",
         value: loadTime,
         timestamp: Date.now(),
         labels: { page: pageName },
      });
   }

   trackApiCall(endpoint: string, duration: number, status: number): void {
      this.addMetric({
         name: "api_call_duration",
         value: duration,
         timestamp: Date.now(),
         labels: {
            endpoint,
            status: status >= 400 ? "error" : "success",
         },
      });
   }

   trackUserAction(action: string): void {
      this.addMetric({
         name: "user_action",
         value: 1,
         timestamp: Date.now(),
         labels: { action },
      });
   }

   private addMetric(metric: PerformanceMetric): void {
      this.metricsBuffer.push(metric);

      if (this.metricsBuffer.length >= 10) {
         this.flushMetrics();
      }
   }

   private async flushMetrics(): Promise<void> {
      if (this.metricsBuffer.length === 0) return;

      const metrics = [...this.metricsBuffer];
      this.metricsBuffer = [];

      try {
         await fetch("/api/metrics", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ metrics }),
         });
      } catch (error) {
         console.error("Failed to send metrics:", error);
         // Re-add metrics to buffer for retry
         this.metricsBuffer.unshift(...metrics);
      }
   }
}

// Hook para tracking automático
export const usePerformanceTracking = (pageName: string) => {
   const monitoring = MonitoringService.getInstance();

   useEffect(() => {
      const startTime = performance.now();

      return () => {
         const loadTime = performance.now() - startTime;
         monitoring.trackPageLoad(pageName, loadTime);
      };
   }, [pageName, monitoring]);

   return monitoring;
};
```

## Navegación

[⬅️ Mantenimiento y Evolución](./mantenimiento-evolucion.md) |
[🏠 README Principal](../../README.md) |
[Bug Fixes y Hotfixes ➡️](./bug-fixes-hotfixes.md)
