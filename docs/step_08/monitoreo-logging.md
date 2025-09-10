# Monitoreo y Logging

## ¿Qué es?

El monitoreo y logging es la práctica de recopilar, analizar y visualizar
métricas de rendimiento, logs de aplicación y eventos del sistema para mantener
la salud, seguridad y performance de la aplicación en producción. Incluye tanto
monitoring proactivo como logging reactivo para troubleshooting.

## ¿Por qué es importante?

- **Detección Temprana**: Identificar problemas antes de que afecten a usuarios
- **Performance**: Optimizar el rendimiento basado en métricas reales
- **Debugging**: Facilitar la resolución de errores y bugs en producción
- **Seguridad**: Detectar actividades sospechosas y intentos de intrusión
- **Compliance**: Cumplir con regulaciones que requieren auditoría
- **Capacity Planning**: Planificar escalamiento basado en tendencias de uso

## ¿Qué debe incluir?

### Métricas de Aplicación

- Response time y latencia de APIs
- Throughput de requests por segundo
- Error rates y status codes HTTP
- Database query performance
- Memory usage y CPU utilization

### Métricas de Infraestructura

- Server resources (CPU, RAM, Disk, Network)
- Database performance y connections
- Load balancer status
- SSL certificate expiration
- Container health y resource usage

### Logging Estructurado

- Application logs (Laravel logs con contexto)
- Access logs (Nginx/Apache)
- Error logs centralizados
- Security logs (authentication, authorization)
- Audit trails para cambios críticos

### Alerting y Notificaciones

- Thresholds para métricas críticas
- Escalation policies
- Integration con Slack, email, SMS
- On-call rotation management

## ¿Qué debo hacer?

### 1. Configurar Application Performance Monitoring (APM)

#### Instalación de Laravel Telescope (Desarrollo)

```bash
# En el proyecto Laravel
composer require laravel/telescope --dev

# Publicar assets
php artisan telescope:install

# Migrar base de datos
php artisan migrate
```

#### Configuración de Telescope

```php
// config/telescope.php
<?php

return [
    'enabled' => env('TELESCOPE_ENABLED', true),
    
    'storage' => [
        'database' => [
            'connection' => env('DB_CONNECTION', 'mysql'),
            'chunk' => 1000,
        ],
    ],

    'watchers' => [
        Watchers\CacheWatcher::class => env('TELESCOPE_CACHE_WATCHER', true),
        Watchers\CommandWatcher::class => env('TELESCOPE_COMMAND_WATCHER', true),
        Watchers\DumpWatcher::class => env('TELESCOPE_DUMP_WATCHER', true),
        Watchers\EventWatcher::class => env('TELESCOPE_EVENT_WATCHER', true),
        Watchers\ExceptionWatcher::class => env('TELESCOPE_EXCEPTION_WATCHER', true),
        Watchers\JobWatcher::class => env('TELESCOPE_JOB_WATCHER', true),
        Watchers\LogWatcher::class => env('TELESCOPE_LOG_WATCHER', true),
        Watchers\MailWatcher::class => env('TELESCOPE_MAIL_WATCHER', true),
        Watchers\ModelWatcher::class => [
            'enabled' => env('TELESCOPE_MODEL_WATCHER', true),
            'hydrations' => true,
        ],
        Watchers\NotificationWatcher::class => env('TELESCOPE_NOTIFICATION_WATCHER', true),
        Watchers\QueryWatcher::class => [
            'enabled' => env('TELESCOPE_QUERY_WATCHER', true),
            'slow' => 500, // milliseconds
        ],
        Watchers\RedisWatcher::class => env('TELESCOPE_REDIS_WATCHER', true),
        Watchers\RequestWatcher::class => [
            'enabled' => env('TELESCOPE_REQUEST_WATCHER', true),
            'size_limit' => env('TELESCOPE_RESPONSE_SIZE_LIMIT', 64),
        ],
    ],
];
```

### 2. Implementar Logging Estructurado

#### Configurar Laravel Logging

```php
// config/logging.php
<?php

return [
    'default' => env('LOG_CHANNEL', 'stack'),

    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['single', 'slack'],
            'ignore_exceptions' => false,
        ],

        'single' => [
            'driver' => 'single',
            'path' => storage_path('logs/laravel.log'),
            'level' => env('LOG_LEVEL', 'debug'),
            'replace_placeholders' => true,
        ],

        'structured' => [
            'driver' => 'monolog',
            'handler' => StreamHandler::class,
            'formatter' => JsonFormatter::class,
            'with' => [
                'stream' => storage_path('logs/structured.log'),
            ],
        ],

        'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'Laravel Log',
            'emoji' => ':boom:',
            'level' => env('LOG_LEVEL', 'critical'),
        ],

        'syslog' => [
            'driver' => 'syslog',
            'level' => env('LOG_LEVEL', 'debug'),
        ],
    ],
];
```

#### Custom Logging Middleware

```php
<?php
// app/Http/Middleware/LogRequests.php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Str;

class LogRequests
{
    public function handle(Request $request, Closure $next)
    {
        $startTime = microtime(true);
        
        // Generate request ID
        $requestId = Str::uuid();
        $request->headers->set('X-Request-ID', $requestId);

        // Log request start
        Log::info('Request started', [
            'request_id' => $requestId,
            'method' => $request->method(),
            'url' => $request->fullUrl(),
            'user_agent' => $request->userAgent(),
            'ip' => $request->ip(),
            'user_id' => auth()->id(),
        ]);

        $response = $next($request);

        // Log request completion
        $endTime = microtime(true);
        $duration = ($endTime - $startTime) * 1000; // ms

        Log::info('Request completed', [
            'request_id' => $requestId,
            'status_code' => $response->getStatusCode(),
            'duration_ms' => round($duration, 2),
            'memory_usage' => memory_get_peak_usage(true),
        ]);

        return $response;
    }
}
```

### 3. Configurar Nginx Access Logs

```nginx
# /etc/nginx/sites-available/app
http {
    # Define log format
    log_format structured escape=json
    '{'
        '"timestamp":"$time_iso8601",'
        '"remote_addr":"$remote_addr",'
        '"request_method":"$request_method",'
        '"request_uri":"$request_uri",'
        '"status":$status,'
        '"body_bytes_sent":$body_bytes_sent,'
        '"request_time":$request_time,'
        '"upstream_response_time":"$upstream_response_time",'
        '"http_referer":"$http_referer",'
        '"http_user_agent":"$http_user_agent",'
        '"http_x_forwarded_for":"$http_x_forwarded_for",'
        '"request_id":"$http_x_request_id"'
    '}';

    server {
        # Access log with structured format
        access_log /var/log/nginx/access.log structured;
        error_log /var/log/nginx/error.log warn;
        
        # Your server configuration...
    }
}
```

### 4. Implementar Health Check Endpoints

#### Laravel Health Check

```php
<?php
// routes/api.php

Route::get('/health', function () {
    $checks = [
        'database' => checkDatabase(),
        'redis' => checkRedis(),
        'storage' => checkStorage(),
        'queue' => checkQueue(),
    ];

    $healthy = collect($checks)->every(fn($check) => $check['status'] === 'ok');

    return response()->json([
        'status' => $healthy ? 'healthy' : 'unhealthy',
        'timestamp' => now()->toISOString(),
        'checks' => $checks,
    ], $healthy ? 200 : 503);
});

function checkDatabase(): array
{
    try {
        DB::connection()->getPdo();
        return ['status' => 'ok', 'message' => 'Database connection successful'];
    } catch (Exception $e) {
        return ['status' => 'error', 'message' => 'Database connection failed: ' . $e->getMessage()];
    }
}

function checkRedis(): array
{
    try {
        Redis::ping();
        return ['status' => 'ok', 'message' => 'Redis connection successful'];
    } catch (Exception $e) {
        return ['status' => 'error', 'message' => 'Redis connection failed: ' . $e->getMessage()];
    }
}

function checkStorage(): array
{
    try {
        $testFile = 'health-check.txt';
        Storage::put($testFile, 'test');
        Storage::delete($testFile);
        return ['status' => 'ok', 'message' => 'Storage write/read successful'];
    } catch (Exception $e) {
        return ['status' => 'error', 'message' => 'Storage check failed: ' . $e->getMessage()];
    }
}

function checkQueue(): array
{
    try {
        $size = Queue::size();
        return ['status' => 'ok', 'message' => "Queue operational, {$size} jobs pending"];
    } catch (Exception $e) {
        return ['status' => 'error', 'message' => 'Queue check failed: ' . $e->getMessage()];
    }
}
```

### 5. Configurar Monitoreo con Prometheus y Grafana

#### Docker Compose para Monitoreo

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
         - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml
         - prometheus_data:/prometheus
      command:
         - "--config.file=/etc/prometheus/prometheus.yml"
         - "--storage.tsdb.path=/prometheus"
         - "--web.console.libraries=/etc/prometheus/console_libraries"
         - "--web.console.templates=/etc/prometheus/consoles"

   grafana:
      image: grafana/grafana:latest
      container_name: grafana
      ports:
         - "3001:3000"
      environment:
         - GF_SECURITY_ADMIN_PASSWORD=admin123
      volumes:
         - grafana_data:/var/lib/grafana
         - ./monitoring/grafana/dashboards:/etc/grafana/provisioning/dashboards
         - ./monitoring/grafana/datasources:/etc/grafana/provisioning/datasources

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
         - "--path.sysfs=/host/sys"
         - "--collector.filesystem.ignored-mount-points=^/(sys|proc|dev|host|etc)($$|/)"

volumes:
   prometheus_data:
   grafana_data:
```

#### Configuración Prometheus

```yaml
# monitoring/prometheus.yml
global:
   scrape_interval: 15s
   evaluation_interval: 15s

rule_files:
   - "rules/*.yml"

scrape_configs:
   # Application health endpoint
   - job_name: "laravel-app"
     static_configs:
        - targets: ["app:80"]
     metrics_path: /api/metrics
     scrape_interval: 30s

   # System metrics
   - job_name: "node-exporter"
     static_configs:
        - targets: ["node-exporter:9100"]

   # Nginx metrics
   - job_name: "nginx"
     static_configs:
        - targets: ["nginx-exporter:9113"]

alerting:
   alertmanagers:
      - static_configs:
           - targets:
                - alertmanager:9093
```

### 6. Implementar Alerting

#### Alert Rules

```yaml
# monitoring/rules/alerts.yml
groups:
   - name: application_alerts
     rules:
        - alert: HighErrorRate
          expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
          for: 5m
          labels:
             severity: critical
          annotations:
             summary: "High error rate detected"
             description: "Error rate is {{ $value }} errors per second"

        - alert: HighResponseTime
          expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 1
          for: 10m
          labels:
             severity: warning
          annotations:
             summary: "High response time detected"
             description: "95th percentile response time is {{ $value }} seconds"

        - alert: DatabaseConnectionFailed
          expr: up{job="mysql-exporter"} == 0
          for: 5m
          labels:
             severity: critical
          annotations:
             summary: "Database connection failed"
             description: "Cannot connect to MySQL database"

        - alert: HighMemoryUsage
          expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 80
          for: 10m
          labels:
             severity: warning
          annotations:
             summary: "High memory usage"
             description: "Memory usage is above 80%"
```

## Tips

### Logging Best Practices

- **Structured Logging**: Usa JSON format para facilitar parsing
- **Contextual Information**: Incluye request IDs, user IDs, timestamps
- **Log Levels**: Usa levels apropiados (DEBUG, INFO, WARNING, ERROR, CRITICAL)
- **Sensitive Data**: Nunca loggees passwords, tokens, o datos personales

### Métricas Importantes

- **RED Method**: Rate, Errors, Duration para servicios
- **USE Method**: Utilization, Saturation, Errors para recursos
- **Custom Metrics**: Métricas específicas del negocio
- **SLI/SLO**: Service Level Indicators y Objectives

### Performance de Monitoring

- **Sampling**: No monitorees todo en producción de alto volumen
- **Retention**: Define políticas de retención de logs y métricas
- **Compression**: Comprime logs antiguos para ahorrar espacio
- **Indexing**: Configura índices apropiados para queries de logs

### Alerting Strategy

- **Actionable Alerts**: Solo alerta sobre cosas que requieren acción
- **Alert Fatigue**: Evita demasiadas alertas que se ignoren
- **Escalation**: Define escalation paths para alertas críticas
- **Runbooks**: Documenta pasos para resolver cada tipo de alerta

## Ejemplos

### Custom Metrics en Laravel

```php
<?php
// app/Http/Middleware/MetricsMiddleware.php

use Prometheus\CollectorRegistry;
use Prometheus\RenderTextFormat;

class MetricsMiddleware
{
    private $registry;
    private $requestDuration;
    private $requestCount;

    public function __construct(CollectorRegistry $registry)
    {
        $this->registry = $registry;
        
        $this->requestDuration = $this->registry->getOrRegisterHistogram(
            'app',
            'http_request_duration_seconds',
            'Request duration in seconds',
            ['method', 'route', 'status_code'],
            [0.1, 0.25, 0.5, 1.0, 2.5, 5.0, 10.0]
        );

        $this->requestCount = $this->registry->getOrRegisterCounter(
            'app',
            'http_requests_total',
            'Total HTTP requests',
            ['method', 'route', 'status_code']
        );
    }

    public function handle($request, Closure $next)
    {
        $start = microtime(true);
        
        $response = $next($request);
        
        $duration = microtime(true) - $start;
        $route = $request->route() ? $request->route()->getName() : 'unknown';
        
        $this->requestDuration->observe(
            $duration,
            [$request->method(), $route, $response->getStatusCode()]
        );
        
        $this->requestCount->inc([
            $request->method(), 
            $route, 
            $response->getStatusCode()
        ]);

        return $response;
    }
}
```

### Log Aggregation con ELK Stack

```yaml
# docker-compose.elk.yml
version: "3.8"

services:
   elasticsearch:
      image: docker.elastic.co/elasticsearch/elasticsearch:8.8.0
      environment:
         - discovery.type=single-node
         - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      ports:
         - "9200:9200"
      volumes:
         - es_data:/usr/share/elasticsearch/data

   logstash:
      image: docker.elastic.co/logstash/logstash:8.8.0
      ports:
         - "5044:5044"
      volumes:
         - ./elk/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
      depends_on:
         - elasticsearch

   kibana:
      image: docker.elastic.co/kibana/kibana:8.8.0
      ports:
         - "5601:5601"
      environment:
         - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
      depends_on:
         - elasticsearch

volumes:
   es_data:
```

### Grafana Dashboard Configuration

```json
{
   "dashboard": {
      "title": "Application Metrics",
      "panels": [
         {
            "title": "Request Rate",
            "type": "graph",
            "targets": [
               {
                  "expr": "rate(http_requests_total[5m])",
                  "legendFormat": "{{method}} {{route}}"
               }
            ]
         },
         {
            "title": "Response Time (95th percentile)",
            "type": "graph",
            "targets": [
               {
                  "expr": "histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))",
                  "legendFormat": "95th percentile"
               }
            ]
         },
         {
            "title": "Error Rate",
            "type": "graph",
            "targets": [
               {
                  "expr": "rate(http_requests_total{status=~\"5..\"}[5m])",
                  "legendFormat": "5xx errors"
               }
            ]
         }
      ]
   }
}
```

## Navegación

**Progreso en Despliegue y DevOps:**

- ✅ [Despliegue y DevOps](./despliegue-devops.md)
- ✅
  [Configuración de Servidores de Producción](./configuracion-servidores-produccion.md)
- ✅ [CI/CD Pipelines con GitHub Actions](./ci-cd-pipelines-github-actions.md)
- ✅ [Docker y Contenedores](./docker-contenedores.md)
- ✅ **Monitoreo y Logging** ← Estás aquí
- ⏭️ [Backup y Recovery](./backup-recovery-strategies.md)
- ⏭️ [SSL y Configuraciones de Seguridad](./ssl-configuraciones-seguridad.md)

---

### Siguiente Paso

Continúa con [**Backup y Recovery**](./backup-recovery-strategies.md)

[⬅️ Docker y Contenedores](./docker-contenedores.md) |
[🏠 README Principal](../../README.md) |
[➡️ Backup y Recovery](./backup-recovery-strategies.md)
