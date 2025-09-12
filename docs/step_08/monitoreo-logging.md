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
- Cache hit/miss ratios
- Queue job processing times
- User session metrics
- Business KPIs y conversions

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

### 5. Monitoreo Avanzado de Base de Datos

#### Métricas Específicas de Performance DB

```php
<?php
// app/Services/DatabaseMonitoringService.php

namespace App\Services;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;

class DatabaseMonitoringService
{
    /**
     * Obtener métricas de performance de la base de datos
     */
    public function getDatabaseMetrics(): array
    {
        return [
            'connections' => $this->getConnectionMetrics(),
            'queries' => $this->getQueryMetrics(),
            'performance' => $this->getPerformanceMetrics(),
            'storage' => $this->getStorageMetrics(),
            'replication' => $this->getReplicationMetrics(),
        ];
    }

    /**
     * Métricas de conexiones
     */
    private function getConnectionMetrics(): array
    {
        $connections = DB::select("SHOW STATUS LIKE 'Threads_connected'")[0];
        $maxConnections = DB::select("SHOW VARIABLES LIKE 'max_connections'")[0];

        return [
            'active_connections' => (int) $connections->Value,
            'max_connections' => (int) $maxConnections->Value,
            'connection_usage_percent' => round(($connections->Value / $maxConnections->Value) * 100, 2),
            'connections_per_second' => $this->getConnectionsPerSecond(),
        ];
    }

    /**
     * Métricas de queries
     */
    private function getQueryMetrics(): array
    {
        $status = collect(DB::select("SHOW GLOBAL STATUS"))
            ->keyBy('Variable_name')
            ->map(fn($item) => $item->Value);

        return [
            'queries_per_second' => $this->getQueriesPerSecond(),
            'slow_queries' => (int) $status->get('Slow_queries', 0),
            'select_queries' => (int) $status->get('Com_select', 0),
            'insert_queries' => (int) $status->get('Com_insert', 0),
            'update_queries' => (int) $status->get('Com_update', 0),
            'delete_queries' => (int) $status->get('Com_delete', 0),
            'slow_query_log_enabled' => $this->isSlowQueryLogEnabled(),
        ];
    }

    /**
     * Métricas de performance
     */
    private function getPerformanceMetrics(): array
    {
        $status = collect(DB::select("SHOW GLOBAL STATUS"))
            ->keyBy('Variable_name')
            ->map(fn($item) => $item->Value);

        return [
            'query_cache_hit_rate' => $this->calculateQueryCacheHitRate($status),
            'innodb_buffer_pool_hit_rate' => $this->calculateInnoDBBufferPoolHitRate($status),
            'table_locks_waited' => (int) $status->get('Table_locks_waited', 0),
            'key_reads' => (int) $status->get('Key_reads', 0),
            'key_read_requests' => (int) $status->get('Key_read_requests', 0),
            'avg_query_response_time' => $this->getAverageQueryResponseTime(),
        ];
    }

    /**
     * Métricas de almacenamiento
     */
    private function getStorageMetrics(): array
    {
        $databases = DB::select("
            SELECT
                table_schema as 'database_name',
                ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'size_mb'
            FROM information_schema.tables
            WHERE table_schema NOT IN ('information_schema', 'performance_schema', 'mysql', 'sys')
            GROUP BY table_schema
        ");

        $totalSize = array_sum(array_column($databases, 'size_mb'));

        return [
            'total_size_mb' => $totalSize,
            'databases' => $databases,
            'largest_tables' => $this->getLargestTables(),
            'index_usage' => $this->getIndexUsageStats(),
        ];
    }

    /**
     * Métricas de replicación (si aplica)
     */
    private function getReplicationMetrics(): array
    {
        try {
            $slaveStatus = DB::select("SHOW SLAVE STATUS");

            if (empty($slaveStatus)) {
                return ['replication_enabled' => false];
            }

            $status = $slaveStatus[0];

            return [
                'replication_enabled' => true,
                'slave_io_running' => $status->Slave_IO_Running === 'Yes',
                'slave_sql_running' => $status->Slave_SQL_Running === 'Yes',
                'seconds_behind_master' => $status->Seconds_Behind_Master,
                'master_log_file' => $status->Master_Log_File,
                'relay_log_file' => $status->Relay_Log_File,
            ];
        } catch (\Exception $e) {
            return ['replication_enabled' => false, 'error' => $e->getMessage()];
        }
    }

    /**
     * Obtener las tablas más grandes
     */
    private function getLargestTables(): array
    {
        return DB::select("
            SELECT
                table_name,
                table_rows,
                ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb,
                ROUND((data_length / 1024 / 1024), 2) AS data_mb,
                ROUND((index_length / 1024 / 1024), 2) AS index_mb
            FROM information_schema.TABLES
            WHERE table_schema = DATABASE()
            ORDER BY (data_length + index_length) DESC
            LIMIT 10
        ");
    }

    /**
     * Estadísticas de uso de índices
     */
    private function getIndexUsageStats(): array
    {
        return DB::select("
            SELECT
                t.table_name,
                s.index_name,
                s.column_name,
                s.cardinality,
                CASE
                    WHEN s.cardinality = 0 THEN 'Unused'
                    WHEN s.cardinality < 10 THEN 'Low selectivity'
                    ELSE 'Good'
                END as index_quality
            FROM information_schema.statistics s
            JOIN information_schema.tables t ON s.table_name = t.table_name
            WHERE s.table_schema = DATABASE()
            AND t.table_type = 'BASE TABLE'
            ORDER BY s.table_name, s.index_name, s.seq_in_index
        ");
    }

    /**
     * Calcular hit rate del query cache
     */
    private function calculateQueryCacheHitRate($status): float
    {
        $hits = (int) $status->get('Qcache_hits', 0);
        $inserts = (int) $status->get('Qcache_inserts', 0);

        if ($hits + $inserts === 0) {
            return 0;
        }

        return round(($hits / ($hits + $inserts)) * 100, 2);
    }

    /**
     * Calcular hit rate del InnoDB buffer pool
     */
    private function calculateInnoDBBufferPoolHitRate($status): float
    {
        $reads = (int) $status->get('Innodb_buffer_pool_reads', 0);
        $readRequests = (int) $status->get('Innodb_buffer_pool_read_requests', 0);

        if ($readRequests === 0) {
            return 0;
        }

        return round(((1 - ($reads / $readRequests)) * 100), 2);
    }

    /**
     * Verificar si el slow query log está habilitado
     */
    private function isSlowQueryLogEnabled(): bool
    {
        $result = DB::select("SHOW VARIABLES LIKE 'slow_query_log'")[0];
        return $result->Value === 'ON';
    }

    /**
     * Obtener conexiones por segundo (requiere almacenamiento de métricas)
     */
    private function getConnectionsPerSecond(): float
    {
        // Implementar lógica para calcular conexiones por segundo
        // basado en métricas históricas almacenadas
        return Cache::remember('db_connections_per_second', 60, function () {
            // Lógica de cálculo basada en datos históricos
            return 0.0;
        });
    }

    /**
     * Obtener queries por segundo
     */
    private function getQueriesPerSecond(): float
    {
        return Cache::remember('db_queries_per_second', 60, function () {
            // Lógica de cálculo basada en datos históricos
            return 0.0;
        });
    }

    /**
     * Obtener tiempo promedio de respuesta de queries
     */
    private function getAverageQueryResponseTime(): float
    {
        return Cache::remember('db_avg_query_time', 60, function () {
            // Lógica de cálculo basada en datos históricos
            return 0.0;
        });
    }

    /**
     * Generar reporte de optimización
     */
    public function generateOptimizationReport(): array
    {
        $metrics = $this->getDatabaseMetrics();
        $recommendations = [];

        // Analizar métricas y generar recomendaciones
        if ($metrics['connections']['connection_usage_percent'] > 80) {
            $recommendations[] = [
                'type' => 'warning',
                'category' => 'connections',
                'message' => 'Alto uso de conexiones. Considerar aumentar max_connections o implementar connection pooling.',
                'priority' => 'high'
            ];
        }

        if ($metrics['performance']['query_cache_hit_rate'] < 90) {
            $recommendations[] = [
                'type' => 'warning',
                'category' => 'performance',
                'message' => 'Baja tasa de hit del query cache. Revisar configuración de query_cache_size.',
                'priority' => 'medium'
            ];
        }

        if ($metrics['performance']['innodb_buffer_pool_hit_rate'] < 95) {
            $recommendations[] = [
                'type' => 'warning',
                'category' => 'performance',
                'message' => 'Baja tasa de hit del InnoDB buffer pool. Considerar aumentar innodb_buffer_pool_size.',
                'priority' => 'high'
            ];
        }

        if ($metrics['queries']['slow_queries'] > 100) {
            $recommendations[] = [
                'type' => 'error',
                'category' => 'performance',
                'message' => 'Alto número de queries lentas. Revisar y optimizar queries problemáticas.',
                'priority' => 'high'
            ];
        }

        return [
            'metrics' => $metrics,
            'recommendations' => $recommendations,
            'health_score' => $this->calculateHealthScore($metrics),
            'generated_at' => now()->toISOString(),
        ];
    }

    /**
     * Calcular score de salud de la base de datos
     */
    private function calculateHealthScore(array $metrics): int
    {
        $score = 100;

        // Penalizar por alto uso de conexiones
        if ($metrics['connections']['connection_usage_percent'] > 80) {
            $score -= 20;
        }

        // Penalizar por baja tasa de hit del cache
        if ($metrics['performance']['query_cache_hit_rate'] < 90) {
            $score -= 15;
        }

        // Penalizar por baja tasa de hit del buffer pool
        if ($metrics['performance']['innodb_buffer_pool_hit_rate'] < 95) {
            $score -= 20;
        }

        // Penalizar por queries lentas
        if ($metrics['queries']['slow_queries'] > 100) {
            $score -= 25;
        }

        return max(0, $score);
    }
}
```

#### Endpoint para Métricas de Base de Datos

```php
<?php
// routes/api.php

Route::get('/metrics/database', function () {
    $dbMonitoring = new App\Services\DatabaseMonitoringService();
    return response()->json($dbMonitoring->generateOptimizationReport());
})->middleware('auth:sanctum');
```

#### Dashboard para Métricas de DB

```php
<?php
// resources/views/admin/database-metrics.blade.php

@extends('layouts.admin')

@section('content')
<div class="container-fluid">
    <div class="row">
        <div class="col-12">
            <h1>Database Performance Metrics</h1>
        </div>
    </div>

    <div class="row" id="db-metrics">
        <!-- Contenido se carga vía JavaScript -->
    </div>
</div>

<script>
async function loadDatabaseMetrics() {
    try {
        const response = await fetch('/api/metrics/database');
        const data = await response.json();

        renderMetrics(data);
    } catch (error) {
        console.error('Error loading database metrics:', error);
    }
}

function renderMetrics(data) {
    const container = document.getElementById('db-metrics');

    container.innerHTML = `
        <div class="col-md-3">
            <div class="card">
                <div class="card-body">
                    <h5>Health Score</h5>
                    <h2 class="${data.health_score >= 80 ? 'text-success' : data.health_score >= 60 ? 'text-warning' : 'text-danger'}">
                        ${data.health_score}/100
                    </h2>
                </div>
            </div>
        </div>

        <div class="col-md-3">
            <div class="card">
                <div class="card-body">
                    <h5>Active Connections</h5>
                    <h2>${data.metrics.connections.active_connections}</h2>
                    <small>Max: ${data.metrics.connections.max_connections}</small>
                </div>
            </div>
        </div>

        <div class="col-md-3">
            <div class="card">
                <div class="card-body">
                    <h5>Query Cache Hit Rate</h5>
                    <h2>${data.metrics.performance.query_cache_hit_rate}%</h2>
                </div>
            </div>
        </div>

        <div class="col-md-3">
            <div class="card">
                <div class="card-body">
                    <h5>Slow Queries</h5>
                    <h2 class="${data.metrics.queries.slow_queries > 100 ? 'text-danger' : 'text-success'}">
                        ${data.metrics.queries.slow_queries}
                    </h2>
                </div>
            </div>
        </div>

        <div class="col-12 mt-4">
            <div class="card">
                <div class="card-header">
                    <h5>Optimization Recommendations</h5>
                </div>
                <div class="card-body">
                    ${data.recommendations.length === 0 ?
                        '<p class="text-success">No recommendations at this time. Database is performing well!</p>' :
                        data.recommendations.map(rec => `
                            <div class="alert alert-${rec.type === 'error' ? 'danger' : 'warning'}">
                                <strong>${rec.category.toUpperCase()}:</strong> ${rec.message}
                                <span class="badge badge-${rec.priority === 'high' ? 'danger' : 'warning'} float-right">
                                    ${rec.priority} priority
                                </span>
                            </div>
                        `).join('')
                    }
                </div>
            </div>
        </div>
    `;
}

// Cargar métricas al cargar la página
document.addEventListener('DOMContentLoaded', loadDatabaseMetrics);

// Actualizar cada 30 segundos
setInterval(loadDatabaseMetrics, 30000);
</script>
@endsection
```

### 6. Configurar Monitoreo con Prometheus y Grafana

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

### Monitoreo Avanzado de Performance

#### Service de Métricas Personalizadas

```php
<?php
// app/Services/CustomMetricsService.php

namespace App\Services;

use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Redis;

class CustomMetricsService
{
    /**
     * Recopilar todas las métricas personalizadas
     */
    public function collectAllMetrics(): array
    {
        return [
            'business_metrics' => $this->getBusinessMetrics(),
            'application_metrics' => $this->getApplicationMetrics(),
            'resource_metrics' => $this->getResourceMetrics(),
            'user_metrics' => $this->getUserMetrics(),
            'performance_trends' => $this->getPerformanceTrends(),
        ];
    }

    /**
     * Métricas de negocio
     */
    private function getBusinessMetrics(): array
    {
        return [
            'daily_active_users' => $this->getDailyActiveUsers(),
            'conversion_rate' => $this->getConversionRate(),
            'revenue_per_user' => $this->getRevenuePerUser(),
            'churn_rate' => $this->getChurnRate(),
            'feature_adoption' => $this->getFeatureAdoption(),
        ];
    }

    /**
     * Métricas de aplicación
     */
    private function getApplicationMetrics(): array
    {
        return [
            'api_response_times' => $this->getApiResponseTimes(),
            'error_rates_by_endpoint' => $this->getErrorRatesByEndpoint(),
            'cache_performance' => $this->getCachePerformance(),
            'queue_health' => $this->getQueueHealth(),
            'memory_usage_trends' => $this->getMemoryUsageTrends(),
        ];
    }

    /**
     * Métricas de recursos
     */
    private function getResourceMetrics(): array
    {
        return [
            'cpu_usage_by_process' => $this->getCpuUsageByProcess(),
            'disk_io_patterns' => $this->getDiskIOPatterns(),
            'network_throughput' => $this->getNetworkThroughput(),
            'memory_allocation' => $this->getMemoryAllocation(),
        ];
    }

    /**
     * Métricas de usuarios
     */
    private function getUserMetrics(): array
    {
        return [
            'concurrent_users' => $this->getConcurrentUsers(),
            'session_duration' => $this->getAverageSessionDuration(),
            'page_load_times' => $this->getPageLoadTimes(),
            'user_journey_analytics' => $this->getUserJourneyAnalytics(),
        ];
    }

    /**
     * Tendencias de performance
     */
    private function getPerformanceTrends(): array
    {
        return [
            'response_time_trend' => $this->getResponseTimeTrend(),
            'error_rate_trend' => $this->getErrorRateTrend(),
            'throughput_trend' => $this->getThroughputTrend(),
            'resource_utilization_trend' => $this->getResourceUtilizationTrend(),
        ];
    }

    // Implementación de métodos específicos...

    private function getDailyActiveUsers(): int
    {
        return Cache::remember('metrics.daily_active_users', 300, function () {
            return DB::table('user_sessions')
                ->whereDate('created_at', today())
                ->distinct('user_id')
                ->count();
        });
    }

    private function getConversionRate(): float
    {
        return Cache::remember('metrics.conversion_rate', 600, function () {
            $visitors = DB::table('page_views')
                ->whereDate('created_at', today())
                ->distinct('session_id')
                ->count();

            $conversions = DB::table('orders')
                ->whereDate('created_at', today())
                ->count();

            return $visitors > 0 ? ($conversions / $visitors) * 100 : 0;
        });
    }

    private function getApiResponseTimes(): array
    {
        return Cache::remember('metrics.api_response_times', 300, function () {
            return DB::table('api_logs')
                ->where('created_at', '>=', now()->subHour())
                ->groupBy('endpoint')
                ->selectRaw('
                    endpoint,
                    AVG(response_time) as avg_response_time,
                    MAX(response_time) as max_response_time,
                    COUNT(*) as request_count
                ')
                ->get()
                ->toArray();
        });
    }

    private function getCachePerformance(): array
    {
        $redisInfo = Redis::info();

        return [
            'hit_rate' => $this->calculateCacheHitRate($redisInfo),
            'memory_usage' => $redisInfo['used_memory_human'] ?? 'N/A',
            'connected_clients' => $redisInfo['connected_clients'] ?? 0,
            'ops_per_sec' => $redisInfo['instantaneous_ops_per_sec'] ?? 0,
            'keyspace_hits' => $redisInfo['keyspace_hits'] ?? 0,
            'keyspace_misses' => $redisInfo['keyspace_misses'] ?? 0,
        ];
    }

    private function calculateCacheHitRate(array $redisInfo): float
    {
        $hits = $redisInfo['keyspace_hits'] ?? 0;
        $misses = $redisInfo['keyspace_misses'] ?? 0;

        if ($hits + $misses === 0) {
            return 0;
        }

        return round(($hits / ($hits + $misses)) * 100, 2);
    }

    private function getQueueHealth(): array
    {
        return [
            'pending_jobs' => DB::table('jobs')->count(),
            'failed_jobs' => DB::table('failed_jobs')->count(),
            'processed_today' => Cache::remember('queue.processed_today', 300, function () {
                return DB::table('job_batches')
                    ->whereDate('created_at', today())
                    ->sum('total_jobs');
            }),
        ];
    }

    private function getConcurrentUsers(): int
    {
        return Cache::remember('metrics.concurrent_users', 60, function () {
            return DB::table('user_sessions')
                ->where('last_activity', '>=', now()->subMinutes(5))
                ->distinct('user_id')
                ->count();
        });
    }

    private function getResponseTimeTrend(): array
    {
        return Cache::remember('metrics.response_time_trend', 900, function () {
            return DB::table('api_logs')
                ->where('created_at', '>=', now()->subDay())
                ->groupBy(DB::raw('HOUR(created_at)'))
                ->selectRaw('
                    HOUR(created_at) as hour,
                    AVG(response_time) as avg_response_time,
                    COUNT(*) as request_count
                ')
                ->orderBy('hour')
                ->get()
                ->toArray();
        });
    }

    /**
     * Generar alertas basadas en métricas
     */
    public function generateAlerts(): array
    {
        $metrics = $this->collectAllMetrics();
        $alerts = [];

        // Alert por alta tasa de error
        if (isset($metrics['application_metrics']['error_rates_by_endpoint'])) {
            foreach ($metrics['application_metrics']['error_rates_by_endpoint'] as $endpoint) {
                if ($endpoint['error_rate'] > 5) { // 5% error rate
                    $alerts[] = [
                        'type' => 'error',
                        'severity' => 'high',
                        'message' => "High error rate on {$endpoint['endpoint']}: {$endpoint['error_rate']}%",
                        'timestamp' => now(),
                    ];
                }
            }
        }

        // Alert por baja performance de cache
        if ($metrics['application_metrics']['cache_performance']['hit_rate'] < 85) {
            $alerts[] = [
                'type' => 'warning',
                'severity' => 'medium',
                'message' => "Low cache hit rate: {$metrics['application_metrics']['cache_performance']['hit_rate']}%",
                'timestamp' => now(),
            ];
        }

        // Alert por alto número de trabajos fallidos
        if ($metrics['application_metrics']['queue_health']['failed_jobs'] > 50) {
            $alerts[] = [
                'type' => 'error',
                'severity' => 'high',
                'message' => "High number of failed queue jobs: {$metrics['application_metrics']['queue_health']['failed_jobs']}",
                'timestamp' => now(),
            ];
        }

        return $alerts;
    }

    /**
     * Exportar métricas para Prometheus
     */
    public function exportPrometheusMetrics(): string
    {
        $metrics = $this->collectAllMetrics();
        $output = '';

        // Business metrics
        $output .= "# HELP daily_active_users Number of daily active users\n";
        $output .= "# TYPE daily_active_users gauge\n";
        $output .= "daily_active_users {$metrics['business_metrics']['daily_active_users']}\n\n";

        $output .= "# HELP conversion_rate Current conversion rate percentage\n";
        $output .= "# TYPE conversion_rate gauge\n";
        $output .= "conversion_rate {$metrics['business_metrics']['conversion_rate']}\n\n";

        // Application metrics
        $output .= "# HELP cache_hit_rate Cache hit rate percentage\n";
        $output .= "# TYPE cache_hit_rate gauge\n";
        $output .= "cache_hit_rate {$metrics['application_metrics']['cache_performance']['hit_rate']}\n\n";

        $output .= "# HELP queue_pending_jobs Number of pending queue jobs\n";
        $output .= "# TYPE queue_pending_jobs gauge\n";
        $output .= "queue_pending_jobs {$metrics['application_metrics']['queue_health']['pending_jobs']}\n\n";

        $output .= "# HELP queue_failed_jobs Number of failed queue jobs\n";
        $output .= "# TYPE queue_failed_jobs gauge\n";
        $output .= "queue_failed_jobs {$metrics['application_metrics']['queue_health']['failed_jobs']}\n\n";

        return $output;
    }
}
```

#### Comando Artisan para Recopilar Métricas

```php
<?php
// app/Console/Commands/CollectMetrics.php

namespace App\Console\Commands;

use App\Services\CustomMetricsService;
use App\Services\DatabaseMonitoringService;
use Illuminate\Console\Command;
use Illuminate\Support\Facades\Storage;

class CollectMetrics extends Command
{
    protected $signature = 'metrics:collect {--format=json} {--output=storage}';
    protected $description = 'Collect and store application metrics';

    public function handle()
    {
        $this->info('🔍 Collecting application metrics...');

        $customMetrics = new CustomMetricsService();
        $dbMetrics = new DatabaseMonitoringService();

        // Recopilar todas las métricas
        $allMetrics = [
            'timestamp' => now()->toISOString(),
            'custom_metrics' => $customMetrics->collectAllMetrics(),
            'database_metrics' => $dbMetrics->getDatabaseMetrics(),
            'alerts' => $customMetrics->generateAlerts(),
        ];

        // Determinar formato de salida
        $format = $this->option('format');
        $output = $this->option('output');

        if ($format === 'prometheus') {
            $content = $customMetrics->exportPrometheusMetrics();
            $filename = 'metrics.prom';
        } else {
            $content = json_encode($allMetrics, JSON_PRETTY_PRINT);
            $filename = 'metrics-' . now()->format('Y-m-d-H-i-s') . '.json';
        }

        // Guardar métricas
        if ($output === 'storage') {
            Storage::disk('local')->put("metrics/{$filename}", $content);
            $this->info("✅ Metrics saved to storage/app/metrics/{$filename}");
        } else {
            $this->line($content);
        }

        // Mostrar resumen
        $this->info('📊 Metrics Summary:');
        $this->table(
            ['Metric', 'Value'],
            [
                ['Daily Active Users', $allMetrics['custom_metrics']['business_metrics']['daily_active_users']],
                ['Conversion Rate', $allMetrics['custom_metrics']['business_metrics']['conversion_rate'] . '%'],
                ['Cache Hit Rate', $allMetrics['custom_metrics']['application_metrics']['cache_performance']['hit_rate'] . '%'],
                ['Pending Jobs', $allMetrics['custom_metrics']['application_metrics']['queue_health']['pending_jobs']],
                ['Failed Jobs', $allMetrics['custom_metrics']['application_metrics']['queue_health']['failed_jobs']],
                ['DB Health Score', $allMetrics['database_metrics']['health_score'] ?? 'N/A'],
            ]
        );

        // Mostrar alertas si las hay
        if (!empty($allMetrics['alerts'])) {
            $this->warn('⚠️  Active Alerts:');
            foreach ($allMetrics['alerts'] as $alert) {
                $this->line("  [{$alert['severity']}] {$alert['message']}");
            }
        } else {
            $this->info('✅ No active alerts');
        }

        return 0;
    }
}
```

#### Programar Recolección de Métricas

```php
<?php
// app/Console/Kernel.php

protected function schedule(Schedule $schedule)
{
    // Recopilar métricas cada 5 minutos
    $schedule->command('metrics:collect --format=json')
             ->everyFiveMinutes()
             ->withoutOverlapping();

    // Exportar métricas para Prometheus cada minuto
    $schedule->command('metrics:collect --format=prometheus --output=storage')
             ->everyMinute()
             ->withoutOverlapping();

    // Generar reporte diario de métricas
    $schedule->command('metrics:collect --format=json')
             ->daily()
             ->appendOutputTo(storage_path('logs/daily-metrics.log'));
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
- ⏭️ [Optimización base de datos](./optimizacion-base-datos.md)
- ⏭️ [Backup y Recovery](./backup-recovery-strategies.md)
- ⏭️ [SSL y Configuraciones de Seguridad](./ssl-configuraciones-seguridad.md)

---

### Siguiente Paso

Continúa con [**Optimización base de datos**](./optimizacion-base-datos.md)

[⬅️ Docker y Contenedores](./docker-contenedores.md) |
[🏠 README Principal](../../README.md) |
[➡️ Optimización base de datos](./optimizacion-base-datos.md)
