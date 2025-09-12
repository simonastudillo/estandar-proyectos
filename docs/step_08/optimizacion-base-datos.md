# Optimización de Base de Datos

## ¿Qué es?

La optimización de base de datos es el proceso sistemático de mejorar el
rendimiento, eficiencia y escalabilidad de los sistemas de gestión de bases de
datos (SGBD). Incluye la optimización de queries, índices, configuración del
servidor, y arquitectura de datos para garantizar tiempos de respuesta óptimos y
uso eficiente de recursos.

## ¿Por qué es importante?

- **Performance**: Reduce significativamente los tiempos de respuesta de la
  aplicación
- **Escalabilidad**: Permite manejar mayor volumen de datos y usuarios
  concurrentes
- **Eficiencia de recursos**: Optimiza el uso de CPU, memoria y almacenamiento
- **Experiencia del usuario**: Mejora la velocidad y responsividad de la
  aplicación
- **Reducción de costos**: Minimiza la necesidad de hardware adicional
- **Estabilidad**: Previene bloqueos y timeouts bajo alta carga
- **Mantenibilidad**: Facilita el mantenimiento y evolución del sistema

## ¿Qué debe incluir?

### Optimización de Queries

#### Análisis y Reescritura

- EXPLAIN ANALYZE para análisis de planes de ejecución
- Identificación de queries N+1
- Optimización de JOINs y subconsultas
- Uso eficiente de WHERE, GROUP BY, ORDER BY

#### Indexing Strategy

- Índices primarios y secundarios
- Índices compuestos y parciales
- Análisis de cardinality y selectividad
- Mantenimiento y limpieza de índices

### Configuración del Motor

#### MySQL/MariaDB Tuning

- InnoDB buffer pool sizing
- Query cache optimization
- Connection pooling
- Thread management

#### Performance Schema

- Instrumentación de performance
- Monitoring de eventos
- Análisis de bottlenecks
- Histórico de métricas

### Arquitectura de Datos

#### Normalización vs Denormalización

- Balancing entre integridad y performance
- Estrategias de desnormalización controlada
- Materialized views y tablas de resumen
- Particionado horizontal y vertical

#### Caching Strategies

- Query result caching
- Application-level caching
- Distributed caching con Redis
- Cache invalidation strategies

## ¿Qué debo hacer?

### 1. Análisis Inicial de Performance

#### Script de Diagnóstico Inicial

```bash
#!/bin/bash
# db-performance-diagnosis.sh

echo "🔍 Database Performance Diagnosis"
echo "================================="

DB_HOST=${1:-"localhost"}
DB_USER=${2:-"root"}
DB_NAME=${3:-"laravel"}

# 1. Información del servidor
echo "1. 📊 Server Information"
mysql -h$DB_HOST -u$DB_USER -p$DB_NAME -e "
SELECT
    @@version as mysql_version,
    @@innodb_version as innodb_version,
    @@max_connections as max_connections,
    @@innodb_buffer_pool_size / 1024 / 1024 / 1024 as buffer_pool_gb,
    @@query_cache_size / 1024 / 1024 as query_cache_mb;
"

# 2. Status crítico
echo -e "\n2. 🚨 Critical Status"
mysql -h$DB_HOST -u$DB_USER -p$DB_NAME -e "
SELECT
    Variable_name,
    Value
FROM INFORMATION_SCHEMA.GLOBAL_STATUS
WHERE Variable_name IN (
    'Threads_connected',
    'Slow_queries',
    'Questions',
    'Uptime',
    'Qcache_hits',
    'Qcache_inserts',
    'Innodb_buffer_pool_read_requests',
    'Innodb_buffer_pool_reads'
);
"

# 3. Top 10 tablas más grandes
echo -e "\n3. 📋 Largest Tables"
mysql -h$DB_HOST -u$DB_USER -p$DB_NAME -e "
SELECT
    table_name,
    table_rows,
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb,
    ROUND((data_length / 1024 / 1024), 2) AS data_mb,
    ROUND((index_length / 1024 / 1024), 2) AS index_mb
FROM information_schema.tables
WHERE table_schema = '$DB_NAME'
ORDER BY (data_length + index_length) DESC
LIMIT 10;
"

# 4. Índices no utilizados (requiere Performance Schema habilitado)
echo -e "\n4. 🔍 Unused Indexes Analysis"
mysql -h$DB_HOST -u$DB_USER -p$DB_NAME -e "
SELECT
    object_schema,
    object_name,
    index_name
FROM performance_schema.table_io_waits_summary_by_index_usage
WHERE index_name IS NOT NULL
AND count_star = 0
AND object_schema = '$DB_NAME'
ORDER BY object_schema, object_name;
" 2>/dev/null || echo "Performance Schema not available or not enabled"

# 5. Queries más lentas recientes
echo -e "\n5. 🐌 Recent Slow Queries"
mysql -h$DB_HOST -u$DB_USER -p$DB_NAME -e "
SELECT
    sql_text,
    query_time,
    lock_time,
    rows_sent,
    rows_examined,
    start_time
FROM mysql.slow_log
WHERE start_time >= DATE_SUB(NOW(), INTERVAL 1 HOUR)
ORDER BY query_time DESC
LIMIT 5;
" 2>/dev/null || echo "Slow query log not enabled or accessible"

echo -e "\n✅ Diagnosis complete!"
```

### 2. Configuración Optimizada de MySQL

#### my.cnf Optimizado para Producción

```ini
# /etc/mysql/my.cnf - Configuración optimizada para producción

[mysqld]
# Configuración básica
bind-address = 0.0.0.0
port = 3306
datadir = /var/lib/mysql
socket = /var/run/mysqld/mysqld.sock
pid-file = /var/run/mysqld/mysqld.pid

# Configuración de conexiones
max_connections = 500
max_connect_errors = 1000
connect_timeout = 10
wait_timeout = 600
interactive_timeout = 600

# InnoDB Configuration
default-storage-engine = innodb
innodb_file_per_table = 1

# Buffer Pool (75% de RAM disponible)
innodb_buffer_pool_size = 8G
innodb_buffer_pool_instances = 8
innodb_buffer_pool_chunk_size = 128M

# Log Configuration
innodb_log_file_size = 2G
innodb_log_files_in_group = 2
innodb_log_buffer_size = 64M
innodb_flush_log_at_trx_commit = 2

# Thread Configuration
innodb_thread_concurrency = 0
innodb_read_io_threads = 8
innodb_write_io_threads = 8

# Query Cache (considerar deshabilitar en MySQL 8.0+)
query_cache_type = 1
query_cache_size = 256M
query_cache_limit = 2M

# Temporary Tables
tmp_table_size = 512M
max_heap_table_size = 512M

# MyISAM Configuration (si se usa)
key_buffer_size = 256M
table_open_cache = 4000
table_definition_cache = 2000

# Logging
log_error = /var/log/mysql/error.log
slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow.log
long_query_time = 0.5
log_queries_not_using_indexes = 1

# Binary Logging (para replicación)
log_bin = /var/log/mysql/mysql-bin.log
expire_logs_days = 7
max_binlog_size = 1G
binlog_format = ROW

# Performance Schema
performance_schema = ON
performance_schema_max_table_instances = 20000
performance_schema_max_sql_text_length = 4096

# Security
local_infile = 0
symbolic_links = 0

[mysql]
default-character-set = utf8mb4

[client]
default-character-set = utf8mb4
port = 3306
socket = /var/run/mysqld/mysqld.sock
```

### 3. Optimización de Queries en Laravel

#### Query Optimization Service

```php
<?php
// app/Services/QueryOptimizationService.php

namespace App\Services;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Log;
use Illuminate\Database\Query\Builder;

class QueryOptimizationService
{
    /**
     * Analizar y optimizar consultas automáticamente
     */
    public static function optimizeQuery(Builder $query): Builder
    {
        $sql = $query->toSql();
        $bindings = $query->getBindings();

        // Log de la query original para análisis
        Log::info('Query optimization analysis', [
            'sql' => $sql,
            'bindings' => $bindings
        ]);

        // Aplicar optimizaciones automáticas
        $optimizedQuery = self::applyOptimizations($query);

        return $optimizedQuery;
    }

    /**
     * Aplicar optimizaciones automáticas
     */
    private static function applyOptimizations(Builder $query): Builder
    {
        // 1. Optimizar SELECT *
        $query = self::optimizeSelectAll($query);

        // 2. Añadir índices sugeridos
        $query = self::addIndexHints($query);

        // 3. Optimizar ORDER BY
        $query = self::optimizeOrderBy($query);

        // 4. Optimizar JOINs
        $query = self::optimizeJoins($query);

        return $query;
    }

    /**
     * Optimizar SELECT * reemplazando con campos específicos
     */
    private static function optimizeSelectAll(Builder $query): Builder
    {
        // Esta es una implementación simplificada
        // En un caso real, necesitarías analizar el AST de la query
        return $query;
    }

    /**
     * Añadir hints de índices cuando sea apropiado
     */
    private static function addIndexHints(Builder $query): Builder
    {
        // Ejemplo de implementación para añadir hints
        // $query->from(DB::raw('users USE INDEX (idx_email)'));
        return $query;
    }

    /**
     * Optimizar cláusulas ORDER BY
     */
    private static function optimizeOrderBy(Builder $query): Builder
    {
        // Verificar si existe índice para campos de ORDER BY
        return $query;
    }

    /**
     * Optimizar JOINs
     */
    private static function optimizeJoins(Builder $query): Builder
    {
        // Convertir subconsultas a JOINs cuando sea más eficiente
        return $query;
    }

    /**
     * Analizar plan de ejecución de una query
     */
    public static function analyzeExecutionPlan(string $sql, array $bindings = []): array
    {
        // Preparar la query con bindings
        $analyzeSql = "EXPLAIN FORMAT=JSON " . $sql;

        $result = DB::select($analyzeSql, $bindings);
        $plan = json_decode($result[0]->EXPLAIN, true);

        return [
            'plan' => $plan,
            'analysis' => self::analyzeQueryPlan($plan),
            'recommendations' => self::generateOptimizationRecommendations($plan)
        ];
    }

    /**
     * Analizar el plan de ejecución
     */
    private static function analyzeQueryPlan(array $plan): array
    {
        $analysis = [
            'estimated_cost' => 0,
            'estimated_rows' => 0,
            'table_scans' => [],
            'index_usage' => [],
            'join_types' => []
        ];

        // Extraer información del plan de ejecución
        $queryBlock = $plan['query_block'] ?? [];

        if (isset($queryBlock['cost_info'])) {
            $analysis['estimated_cost'] = $queryBlock['cost_info']['query_cost'] ?? 0;
        }

        // Analizar tablas y accesos
        if (isset($queryBlock['table'])) {
            $analysis = self::analyzeTableAccess($queryBlock['table'], $analysis);
        }

        return $analysis;
    }

    /**
     * Analizar accesos a tablas
     */
    private static function analyzeTableAccess(array $tableInfo, array $analysis): array
    {
        $tableName = $tableInfo['table_name'] ?? 'unknown';
        $accessType = $tableInfo['access_type'] ?? 'unknown';

        if ($accessType === 'ALL') {
            $analysis['table_scans'][] = $tableName;
        }

        if (isset($tableInfo['key'])) {
            $analysis['index_usage'][] = [
                'table' => $tableName,
                'index' => $tableInfo['key'],
                'key_length' => $tableInfo['key_length'] ?? null
            ];
        }

        if (isset($tableInfo['rows_examined_per_scan'])) {
            $analysis['estimated_rows'] += $tableInfo['rows_examined_per_scan'];
        }

        return $analysis;
    }

    /**
     * Generar recomendaciones de optimización
     */
    private static function generateOptimizationRecommendations(array $plan): array
    {
        $recommendations = [];

        $analysis = self::analyzeQueryPlan($plan);

        // Recomendar índices para table scans
        if (!empty($analysis['table_scans'])) {
            foreach ($analysis['table_scans'] as $table) {
                $recommendations[] = [
                    'type' => 'index',
                    'priority' => 'high',
                    'message' => "Consider adding an index to table '{$table}' to avoid full table scan",
                    'table' => $table
                ];
            }
        }

        // Recomendar optimización para queries costosas
        if ($analysis['estimated_cost'] > 1000) {
            $recommendations[] = [
                'type' => 'query',
                'priority' => 'medium',
                'message' => "Query has high estimated cost ({$analysis['estimated_cost']}). Consider optimization.",
            ];
        }

        // Recomendar optimización para queries que examinan muchas filas
        if ($analysis['estimated_rows'] > 10000) {
            $recommendations[] = [
                'type' => 'query',
                'priority' => 'medium',
                'message' => "Query examines many rows ({$analysis['estimated_rows']}). Consider adding WHERE conditions or better indexes.",
            ];
        }

        return $recommendations;
    }

    /**
     * Generar reporte de optimización para una consulta
     */
    public static function generateOptimizationReport(string $sql, array $bindings = []): array
    {
        // Ejecutar query y medir tiempo
        $startTime = microtime(true);
        $result = DB::select($sql, $bindings);
        $executionTime = (microtime(true) - $startTime) * 1000; // ms

        // Analizar plan de ejecución
        $planAnalysis = self::analyzeExecutionPlan($sql, $bindings);

        return [
            'sql' => $sql,
            'bindings' => $bindings,
            'execution_time_ms' => round($executionTime, 2),
            'result_count' => count($result),
            'plan_analysis' => $planAnalysis,
            'performance_grade' => self::calculatePerformanceGrade($executionTime, $planAnalysis['analysis']),
            'generated_at' => now()->toISOString()
        ];
    }

    /**
     * Calcular calificación de performance
     */
    private static function calculatePerformanceGrade(float $executionTime, array $analysis): string
    {
        $score = 100;

        // Penalizar por tiempo de ejecución
        if ($executionTime > 1000) $score -= 30; // > 1 segundo
        elseif ($executionTime > 500) $score -= 20; // > 500ms
        elseif ($executionTime > 100) $score -= 10; // > 100ms

        // Penalizar por table scans
        $score -= count($analysis['table_scans']) * 15;

        // Penalizar por alto costo
        if ($analysis['estimated_cost'] > 1000) $score -= 20;

        // Penalizar por muchas filas examinadas
        if ($analysis['estimated_rows'] > 10000) $score -= 15;

        $score = max(0, $score);

        if ($score >= 90) return 'A+';
        if ($score >= 80) return 'A';
        if ($score >= 70) return 'B';
        if ($score >= 60) return 'C';
        if ($score >= 50) return 'D';
        return 'F';
    }
}
```

### 4. Middleware de Profiling Automático

```php
<?php
// app/Http/Middleware/DatabaseProfilingMiddleware.php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Log;
use App\Services\QueryOptimizationService;

class DatabaseProfilingMiddleware
{
    private $queryLog = [];
    private $startTime;

    public function handle(Request $request, Closure $next)
    {
        // Solo en ambiente de desarrollo o con flag específico
        if (!app()->environment('local') && !config('app.db_profiling')) {
            return $next($request);
        }

        $this->startTime = microtime(true);

        // Activar query logging
        DB::enableQueryLog();

        // Listener para queries lentas
        DB::listen(function ($query) {
            if ($query->time > 100) { // Queries > 100ms
                $this->queryLog[] = [
                    'sql' => $query->sql,
                    'bindings' => $query->bindings,
                    'time' => $query->time,
                    'connection' => $query->connectionName,
                    'slow' => true
                ];

                // Generar reporte de optimización para queries lentas
                $report = QueryOptimizationService::generateOptimizationReport(
                    $query->sql,
                    $query->bindings
                );

                Log::warning('Slow query detected', $report);
            }
        });

        $response = $next($request);

        // Analizar queries ejecutadas
        $this->analyzeQueries($request);

        return $response;
    }

    private function analyzeQueries(Request $request): void
    {
        $queries = DB::getQueryLog();
        $totalTime = (microtime(true) - $this->startTime) * 1000;
        $dbTime = array_sum(array_column($queries, 'time'));

        $analysis = [
            'route' => $request->route()->getName() ?? $request->path(),
            'method' => $request->method(),
            'total_queries' => count($queries),
            'total_db_time' => round($dbTime, 2),
            'total_request_time' => round($totalTime, 2),
            'db_time_percentage' => round(($dbTime / $totalTime) * 100, 2),
            'slow_queries' => count($this->queryLog),
            'n_plus_one_detected' => $this->detectNPlusOne($queries),
            'duplicate_queries' => $this->detectDuplicateQueries($queries)
        ];

        // Log si hay problemas de performance
        if ($analysis['slow_queries'] > 0 ||
            $analysis['n_plus_one_detected'] ||
            $analysis['total_queries'] > 50) {

            Log::info('Database performance analysis', $analysis);
        }

        // Añadir headers de debug en desarrollo
        if (app()->environment('local')) {
            response()->header('X-DB-Queries', $analysis['total_queries']);
            response()->header('X-DB-Time', $analysis['total_db_time'] . 'ms');
        }
    }

    private function detectNPlusOne(array $queries): bool
    {
        $queryPatterns = [];

        foreach ($queries as $query) {
            // Simplificar query removiendo valores específicos
            $pattern = preg_replace('/\d+/', '?', $query['sql']);
            $pattern = preg_replace('/[\'"][^\'\"]*[\'"]/', '?', $pattern);

            if (!isset($queryPatterns[$pattern])) {
                $queryPatterns[$pattern] = 0;
            }
            $queryPatterns[$pattern]++;
        }

        // Si hay más de 10 queries similares, posible N+1
        return max($queryPatterns) > 10;
    }

    private function detectDuplicateQueries(array $queries): int
    {
        $seen = [];
        $duplicates = 0;

        foreach ($queries as $query) {
            $signature = md5($query['sql'] . serialize($query['bindings']));

            if (in_array($signature, $seen)) {
                $duplicates++;
            } else {
                $seen[] = $signature;
            }
        }

        return $duplicates;
    }
}
```

### 5. Estrategias de Indexing Inteligente

#### Comando Artisan para Análisis de Índices

```php
<?php
// app/Console/Commands/AnalyzeIndexes.php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;

class AnalyzeIndexes extends Command
{
    protected $signature = 'db:analyze-indexes {--table=} {--suggest} {--apply}';
    protected $description = 'Analyze database indexes and suggest optimizations';

    public function handle()
    {
        $this->info('🔍 Analyzing database indexes...');

        $tables = $this->option('table')
            ? [$this->option('table')]
            : $this->getAllTables();

        foreach ($tables as $table) {
            $this->analyzeTable($table);
        }

        if ($this->option('suggest')) {
            $this->suggestIndexes();
        }
    }

    private function getAllTables(): array
    {
        return DB::select('SHOW TABLES');
    }

    private function analyzeTable(string $table): void
    {
        $this->line("\n📋 Analyzing table: {$table}");

        // 1. Información básica de la tabla
        $tableInfo = $this->getTableInfo($table);
        $this->displayTableInfo($tableInfo);

        // 2. Análisis de índices existentes
        $indexes = $this->getTableIndexes($table);
        $this->displayIndexAnalysis($indexes);

        // 3. Análisis de queries comunes
        $this->analyzeCommonQueries($table);
    }

    private function getTableInfo(string $table): array
    {
        $result = DB::select("
            SELECT
                table_rows,
                ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb,
                ROUND((data_length / 1024 / 1024), 2) AS data_mb,
                ROUND((index_length / 1024 / 1024), 2) AS index_mb,
                ROUND((index_length / data_length * 100), 2) AS index_ratio
            FROM information_schema.tables
            WHERE table_schema = DATABASE()
            AND table_name = ?
        ", [$table]);

        return $result ? (array) $result[0] : [];
    }

    private function displayTableInfo(array $info): void
    {
        if (empty($info)) return;

        $this->table(
            ['Metric', 'Value'],
            [
                ['Rows', number_format($info['table_rows'])],
                ['Total Size', $info['size_mb'] . ' MB'],
                ['Data Size', $info['data_mb'] . ' MB'],
                ['Index Size', $info['index_mb'] . ' MB'],
                ['Index Ratio', $info['index_ratio'] . '%']
            ]
        );
    }

    private function getTableIndexes(string $table): array
    {
        return DB::select("SHOW INDEX FROM {$table}");
    }

    private function displayIndexAnalysis(array $indexes): void
    {
        if (empty($indexes)) {
            $this->warn('   ⚠️  No indexes found!');
            return;
        }

        $indexData = [];
        foreach ($indexes as $index) {
            $indexData[] = [
                $index->Key_name,
                $index->Column_name,
                $index->Cardinality ?: 'N/A',
                $index->Index_type,
                $index->Non_unique ? 'Non-unique' : 'Unique'
            ];
        }

        $this->table(
            ['Index Name', 'Column', 'Cardinality', 'Type', 'Unique'],
            $indexData
        );

        // Análisis de problemas
        $this->analyzeIndexProblems($indexes);
    }

    private function analyzeIndexProblems(array $indexes): void
    {
        $problems = [];

        // Detectar índices con baja cardinalidad
        foreach ($indexes as $index) {
            if ($index->Cardinality !== null && $index->Cardinality < 10 && $index->Key_name !== 'PRIMARY') {
                $problems[] = "Low cardinality index: {$index->Key_name} (cardinality: {$index->Cardinality})";
            }
        }

        // Detectar índices duplicados o redundantes
        $columnGroups = [];
        foreach ($indexes as $index) {
            if (!isset($columnGroups[$index->Key_name])) {
                $columnGroups[$index->Key_name] = [];
            }
            $columnGroups[$index->Key_name][] = $index->Column_name;
        }

        foreach ($columnGroups as $indexName => $columns) {
            foreach ($columnGroups as $otherIndexName => $otherColumns) {
                if ($indexName !== $otherIndexName &&
                    count($columns) < count($otherColumns) &&
                    array_slice($otherColumns, 0, count($columns)) === $columns) {
                    $problems[] = "Redundant index: {$indexName} is covered by {$otherIndexName}";
                }
            }
        }

        if (!empty($problems)) {
            $this->warn('   ⚠️  Index Problems:');
            foreach ($problems as $problem) {
                $this->line("      - {$problem}");
            }
        } else {
            $this->info('   ✅ No index problems detected');
        }
    }

    private function analyzeCommonQueries(string $table): void
    {
        // Analizar queries del slow log que afectan esta tabla
        $slowQueries = $this->getSlowQueriesForTable($table);

        if (!empty($slowQueries)) {
            $this->line("\n   🐌 Slow queries affecting this table:");
            foreach ($slowQueries as $query) {
                $this->line("      " . substr($query->sql_text, 0, 100) . "...");
                $this->line("      Time: {$query->query_time}s, Rows examined: {$query->rows_examined}");
            }
        }
    }

    private function getSlowQueriesForTable(string $table): array
    {
        try {
            return DB::select("
                SELECT sql_text, query_time, rows_examined
                FROM mysql.slow_log
                WHERE sql_text LIKE ?
                AND start_time >= DATE_SUB(NOW(), INTERVAL 24 HOUR)
                ORDER BY query_time DESC
                LIMIT 5
            ", ["%{$table}%"]);
        } catch (\Exception $e) {
            return [];
        }
    }

    private function suggestIndexes(): void
    {
        $this->info("\n💡 Index Suggestions:");

        // Análisis de queries del slow log para sugerir índices
        $suggestions = $this->generateIndexSuggestions();

        if (empty($suggestions)) {
            $this->info('   ✅ No index suggestions at this time');
            return;
        }

        foreach ($suggestions as $suggestion) {
            $this->line("   📝 {$suggestion['table']}: {$suggestion['description']}");
            $this->line("      SQL: {$suggestion['sql']}");

            if ($this->option('apply') && $this->confirm("Apply this index?")) {
                try {
                    DB::statement($suggestion['sql']);
                    $this->info("      ✅ Index created successfully");
                } catch (\Exception $e) {
                    $this->error("      ❌ Failed to create index: " . $e->getMessage());
                }
            }
        }
    }

    private function generateIndexSuggestions(): array
    {
        $suggestions = [];

        // Aquí implementarías lógica para analizar el slow log
        // y generar sugerencias inteligentes de índices

        // Ejemplo básico:
        $commonPatterns = [
            'users' => [
                'email' => 'CREATE INDEX idx_users_email ON users(email)',
                'created_at' => 'CREATE INDEX idx_users_created_at ON users(created_at)',
            ],
            'orders' => [
                'user_id,created_at' => 'CREATE INDEX idx_orders_user_created ON orders(user_id, created_at)',
                'status' => 'CREATE INDEX idx_orders_status ON orders(status)',
            ]
        ];

        foreach ($commonPatterns as $table => $patterns) {
            if (Schema::hasTable($table)) {
                foreach ($patterns as $description => $sql) {
                    // Verificar si el índice ya existe
                    if (!$this->indexExists($table, $description)) {
                        $suggestions[] = [
                            'table' => $table,
                            'description' => "Index on {$description}",
                            'sql' => $sql
                        ];
                    }
                }
            }
        }

        return $suggestions;
    }

    private function indexExists(string $table, string $columns): bool
    {
        $indexes = DB::select("SHOW INDEX FROM {$table}");

        foreach ($indexes as $index) {
            if (strpos($columns, $index->Column_name) !== false) {
                return true;
            }
        }

        return false;
    }
}
```

### 6. Caching Inteligente

#### Service de Cache Inteligente

```php
<?php
// app/Services/IntelligentCacheService.php

namespace App\Services;

use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Log;

class IntelligentCacheService
{
    private static $queryCache = [];
    private static $cacheStats = [];

    /**
     * Cache inteligente para queries
     */
    public static function cacheQuery(string $sql, array $bindings, int $ttl = 300): array
    {
        $cacheKey = self::generateCacheKey($sql, $bindings);

        // Verificar si está en cache
        $cached = Cache::get($cacheKey);
        if ($cached !== null) {
            self::recordCacheHit($cacheKey);
            return $cached;
        }

        // Ejecutar query y cachear resultado
        $result = DB::select($sql, $bindings);

        // Determinar TTL inteligente basado en el tipo de query
        $intelligentTtl = self::calculateIntelligentTtl($sql, $ttl);

        Cache::put($cacheKey, $result, $intelligentTtl);
        self::recordCacheMiss($cacheKey);

        return $result;
    }

    /**
     * Generar clave de cache determinística
     */
    private static function generateCacheKey(string $sql, array $bindings): string
    {
        $normalized = self::normalizeSql($sql);
        return 'query:' . md5($normalized . serialize($bindings));
    }

    /**
     * Normalizar SQL para caching
     */
    private static function normalizeSql(string $sql): string
    {
        // Remover espacios extra y convertir a minúsculas
        $normalized = preg_replace('/\s+/', ' ', strtolower(trim($sql)));

        // Remover comentarios
        $normalized = preg_replace('/--.*$/m', '', $normalized);
        $normalized = preg_replace('/\/\*.*?\*\//s', '', $normalized);

        return $normalized;
    }

    /**
     * Calcular TTL inteligente basado en el tipo de query
     */
    private static function calculateIntelligentTtl(string $sql, int $defaultTtl): int
    {
        $sql = strtolower($sql);

        // Queries de configuración - cache largo
        if (strpos($sql, 'config') !== false || strpos($sql, 'settings') !== false) {
            return 3600; // 1 hora
        }

        // Queries de lookup/reference data - cache mediano
        if (strpos($sql, 'categories') !== false ||
            strpos($sql, 'countries') !== false ||
            strpos($sql, 'currencies') !== false) {
            return 1800; // 30 minutos
        }

        // Queries con ORDER BY y LIMIT - cache corto (posiblemente paginación)
        if (strpos($sql, 'order by') !== false && strpos($sql, 'limit') !== false) {
            return 300; // 5 minutos
        }

        // Queries con JOIN - cache corto
        if (strpos($sql, 'join') !== false) {
            return 600; // 10 minutos
        }

        // Queries con agregaciones - cache mediano
        if (strpos($sql, 'count(') !== false ||
            strpos($sql, 'sum(') !== false ||
            strpos($sql, 'avg(') !== false) {
            return 900; // 15 minutos
        }

        return $defaultTtl;
    }

    /**
     * Invalidación inteligente de cache
     */
    public static function invalidateTableCache(string $table): void
    {
        $pattern = "query:*{$table}*";

        // En Redis, usar SCAN para encontrar claves
        if (Cache::getStore() instanceof \Illuminate\Cache\RedisStore) {
            $redis = Cache::getStore()->getRedis();
            $keys = $redis->keys($pattern);

            if (!empty($keys)) {
                $redis->del($keys);
                Log::info("Invalidated cache for table: {$table}", ['keys_count' => count($keys)]);
            }
        } else {
            // Para otros stores, usar tags si están disponibles
            Cache::tags(["table:{$table}"])->flush();
        }
    }

    /**
     * Cache automático para modelos Eloquent
     */
    public static function cacheModel(string $model, string $method, array $params = [], int $ttl = 300): mixed
    {
        $cacheKey = "model:{$model}:{$method}:" . md5(serialize($params));

        return Cache::remember($cacheKey, $ttl, function() use ($model, $method, $params) {
            $instance = new $model;
            return call_user_func_array([$instance, $method], $params);
        });
    }

    /**
     * Cache con warming automático
     */
    public static function warmCache(array $queries): void
    {
        Log::info('Starting cache warming process', ['queries_count' => count($queries)]);

        foreach ($queries as $query) {
            try {
                self::cacheQuery(
                    $query['sql'],
                    $query['bindings'] ?? [],
                    $query['ttl'] ?? 300
                );
            } catch (\Exception $e) {
                Log::error('Cache warming failed for query', [
                    'sql' => $query['sql'],
                    'error' => $e->getMessage()
                ]);
            }
        }

        Log::info('Cache warming process completed');
    }

    /**
     * Estadísticas de cache
     */
    public static function getCacheStats(): array
    {
        return [
            'hits' => array_sum(array_column(self::$cacheStats, 'hits')),
            'misses' => array_sum(array_column(self::$cacheStats, 'misses')),
            'hit_rate' => self::calculateHitRate(),
            'total_queries' => count(self::$cacheStats),
            'top_cached_queries' => self::getTopCachedQueries()
        ];
    }

    private static function recordCacheHit(string $key): void
    {
        if (!isset(self::$cacheStats[$key])) {
            self::$cacheStats[$key] = ['hits' => 0, 'misses' => 0];
        }
        self::$cacheStats[$key]['hits']++;
    }

    private static function recordCacheMiss(string $key): void
    {
        if (!isset(self::$cacheStats[$key])) {
            self::$cacheStats[$key] = ['hits' => 0, 'misses' => 0];
        }
        self::$cacheStats[$key]['misses']++;
    }

    private static function calculateHitRate(): float
    {
        $totalHits = array_sum(array_column(self::$cacheStats, 'hits'));
        $totalMisses = array_sum(array_column(self::$cacheStats, 'misses'));
        $total = $totalHits + $totalMisses;

        return $total > 0 ? ($totalHits / $total) * 100 : 0;
    }

    private static function getTopCachedQueries(): array
    {
        $queries = self::$cacheStats;

        // Ordenar por número total de accesos
        uasort($queries, function($a, $b) {
            return ($b['hits'] + $b['misses']) - ($a['hits'] + $a['misses']);
        });

        return array_slice($queries, 0, 10, true);
    }

    /**
     * Limpieza automática de cache
     */
    public static function cleanupExpiredCache(): void
    {
        // Esta funcionalidad depende del driver de cache usado
        Log::info('Cache cleanup process started');

        if (Cache::getStore() instanceof \Illuminate\Cache\RedisStore) {
            // Redis maneja automáticamente la expiración
            $redis = Cache::getStore()->getRedis();
            $info = $redis->info('memory');

            Log::info('Redis cache status', [
                'used_memory' => $info['used_memory_human'] ?? 'unknown',
                'expired_keys' => $redis->get('expired_keys_total') ?? 0
            ]);
        }
    }
}
```

## Tips

- **Monitoreo continuo**: Implementa métricas y alertas para detectar
  degradación de performance
- **Testing de carga**: Realiza pruebas regulares con datos realistas de
  producción
- **Baseline establecido**: Mantén métricas históricas para comparar mejoras
- **Indexing estratégico**: No sobre-indexes, cada índice tiene costo de
  mantenimiento
- **Particionado inteligente**: Considera particionado para tablas muy grandes
- **Configuración contextual**: Ajusta configuración según patrones de uso
  específicos
- **Cache warming**: Pre-calentamiento de cache para datos críticos
- **Cleanup regular**: Limpieza periódica de datos obsoletos y optimización de
  tablas

## Ejemplos

### Script de Mantenimiento Automático

```bash
#!/bin/bash
# db-maintenance.sh

echo "🔧 Database Maintenance Script"
echo "============================="

DB_NAME=${1:-"laravel"}

# 1. Análizar y optimizar tablas
echo "1. 🔧 Optimizing tables..."
mysql -e "
    SELECT CONCAT('OPTIMIZE TABLE ', table_name, ';') as 'Optimize Commands'
    FROM information_schema.tables
    WHERE table_schema = '$DB_NAME'
    AND data_free > 0;" | grep -v "Optimize Commands" | mysql

# 2. Actualizar estadísticas de tablas
echo "2. 📊 Updating table statistics..."
mysql -e "
    SELECT CONCAT('ANALYZE TABLE ', table_name, ';') as 'Analyze Commands'
    FROM information_schema.tables
    WHERE table_schema = '$DB_NAME';" | grep -v "Analyze Commands" | mysql

# 3. Limpiar slow query log
echo "3. 🧹 Cleaning slow query log..."
mysql -e "TRUNCATE TABLE mysql.slow_log;"

# 4. Verificar integridad
echo "4. ✅ Checking table integrity..."
mysqlcheck --check --all-databases

echo "✅ Database maintenance completed!"
```

### Dashboard de Performance en tiempo real

```php
<?php
// app/Http/Controllers/DatabaseDashboardController.php

namespace App\Http\Controllers;

use App\Services\DatabaseMonitoringService;
use App\Services\IntelligentCacheService;
use Illuminate\Http\Request;

class DatabaseDashboardController extends Controller
{
    public function index()
    {
        $monitoring = new DatabaseMonitoringService();

        $data = [
            'database_metrics' => $monitoring->getDatabaseMetrics(),
            'optimization_report' => $monitoring->generateOptimizationReport(),
            'cache_stats' => IntelligentCacheService::getCacheStats(),
            'real_time_queries' => $this->getRealTimeQueries(),
        ];

        return view('admin.database-dashboard', $data);
    }

    public function apiMetrics()
    {
        $monitoring = new DatabaseMonitoringService();

        return response()->json([
            'timestamp' => now(),
            'metrics' => $monitoring->getDatabaseMetrics(),
            'health_score' => $monitoring->calculateHealthScore(),
            'alerts' => $monitoring->getActiveAlerts(),
        ]);
    }

    private function getRealTimeQueries(): array
    {
        return collect(DB::getQueryLog())
            ->sortByDesc('time')
            ->take(10)
            ->map(function ($query) {
                return [
                    'sql' => $query['query'],
                    'time' => $query['time'],
                    'bindings' => $query['bindings'],
                ];
            })
            ->values()
            ->toArray();
    }
}
```

## Navegación

[⬅️ Monitoreo y Logging](./monitoreo-logging.md) |
[🏠 README Principal](../../README.md) |
[Backup y Recovery ➡️](./backup-recovery-strategies.md)
