# Testing de Performance y Carga

## ¿Qué es?

El testing de performance y carga es el proceso de evaluación del comportamiento
del sistema bajo diferentes niveles de demanda para garantizar que cumple con
los requerimientos no funcionales de rendimiento. Incluye pruebas de carga,
estrés, volumen y resistencia para identificar cuellos de botella y optimizar el
rendimiento del sistema.

## ¿Por qué es importante?

- **Experiencia del usuario**: Asegura tiempos de respuesta aceptables
- **Escalabilidad**: Verifica que el sistema soporte el crecimiento esperado
- **Estabilidad**: Identifica problemas bajo condiciones de alta demanda
- **Optimización**: Detecta cuellos de botella para mejoras de rendimiento
- **Planificación de recursos**: Ayuda a dimensionar infraestructura adecuada
- **Prevención de caídas**: Evita fallos del sistema en producción
- **Cumplimiento de SLA**: Garantiza el cumplimiento de acuerdos de nivel de
  servicio

## ¿Qué debe incluir?

### Tipos de Testing de Performance

#### Testing de Carga (Load Testing)

- Comportamiento bajo carga normal esperada
- Validación de tiempos de respuesta
- Throughput y concurrencia
- Uso de recursos del sistema

#### Testing de Estrés (Stress Testing)

- Comportamiento bajo carga extrema
- Punto de ruptura del sistema
- Recuperación después del estrés
- Manejo de errores bajo presión

#### Testing de Volumen (Volume Testing)

- Manejo de grandes cantidades de datos
- Performance de base de datos
- Capacidad de almacenamiento
- Procesamiento de lotes grandes

#### Testing de Resistencia (Endurance Testing)

- Comportamiento durante períodos prolongados
- Detección de memory leaks
- Degradación gradual del rendimiento
- Estabilidad a largo plazo

### Métricas Clave

- **Tiempo de respuesta**: Tiempo para procesar una petición
- **Throughput**: Número de transacciones por segundo
- **Utilización de recursos**: CPU, memoria, disco, red
- **Tasa de errores**: Porcentaje de peticiones fallidas
- **Concurrencia**: Número de usuarios simultáneos

## ¿Qué debo hacer?

### 1. Definir requerimientos de performance

```yaml
# performance-requirements.yml
response_time:
   api_endpoints:
      maximum: 200ms
      average: 100ms
   page_load:
      maximum: 2s
      average: 1s

throughput:
   api_requests: 1000 rps
   concurrent_users: 500
   daily_transactions: 100000

resource_utilization:
   cpu: 70%
   memory: 80%
   disk_io: 60%
   network: 50%

availability:
   uptime: 99.9%
   max_downtime: 8.76h/year
```

### 2. Configurar herramientas de testing

#### Apache JMeter

```bash
# Descargar e instalar JMeter
wget https://downloads.apache.org//jmeter/binaries/apache-jmeter-5.5.zip
unzip apache-jmeter-5.5.zip
cd apache-jmeter-5.5/bin
./jmeter
```

#### Artillery.js (para APIs)

```bash
# Instalar Artillery
npm install -g artillery

# Crear configuración de test
cat > load-test.yml << EOF
config:
  target: 'http://localhost:8000'
  phases:
    - duration: 60
      arrivalRate: 10
    - duration: 120
      arrivalRate: 50
    - duration: 60
      arrivalRate: 100

scenarios:
  - name: "API Load Test"
    weight: 100
    flow:
      - get:
          url: "/api/v1/users"
      - post:
          url: "/api/v1/users"
          json:
            name: "Test User"
            email: "test@example.com"
            password: "password123"
EOF

# Ejecutar test
artillery run load-test.yml
```

#### K6 (Grafana)

```bash
# Instalar k6
curl https://github.com/grafana/k6/releases/download/v0.47.0/k6-v0.47.0-linux-amd64.tar.gz -L | tar xvz
sudo cp k6-v0.47.0-linux-amd64/k6 /usr/bin

# Crear script de test
cat > load-test.js << 'EOF'
import http from 'k6/http';
import { check, sleep } from 'k6';

export let options = {
  stages: [
    { duration: '1m', target: 10 },
    { duration: '3m', target: 50 },
    { duration: '1m', target: 100 },
    { duration: '2m', target: 100 },
    { duration: '1m', target: 0 },
  ],
  thresholds: {
    http_req_duration: ['p(95)<200'],
    http_req_failed: ['rate<0.1'],
  },
};

export default function() {
  let response = http.get('http://localhost:8000/api/v1/users');

  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 200ms': (r) => r.timings.duration < 200,
  });

  sleep(1);
}
EOF

# Ejecutar test
k6 run load-test.js
```

### 3. Implementar testing de APIs

#### Ejemplo con Artillery para API REST

```yaml
# api-performance-test.yml
config:
   target: "http://localhost:8000"
   phases:
      # Warm-up
      - duration: 30
        arrivalRate: 5
        name: "Warm-up"
      # Load test
      - duration: 120
        arrivalRate: 20
        name: "Load test"
      # Stress test
      - duration: 60
        arrivalRate: 50
        name: "Stress test"
      # Cool down
      - duration: 30
        arrivalRate: 5
        name: "Cool down"

   variables:
      # Variables para datos dinámicos
      userId: "{{ $randomInt(1, 1000) }}"
      email: "user{{ $randomInt(1, 10000) }}@example.com"

scenarios:
   - name: "User Management API"
     weight: 40
     flow:
        # Autenticación
        - post:
             url: "/api/v1/auth/login"
             json:
                email: "admin@example.com"
                password: "password123"
             capture:
                json: "$.token"
                as: "authToken"

        # Listar usuarios
        - get:
             url: "/api/v1/users"
             headers:
                Authorization: "Bearer {{ authToken }}"
             expect:
                - statusCode: 200
                - hasProperty: "data"

        # Crear usuario
        - post:
             url: "/api/v1/users"
             headers:
                Authorization: "Bearer {{ authToken }}"
             json:
                name: "Test User {{ userId }}"
                email: "{{ email }}"
                password: "password123"
             capture:
                json: "$.data.id"
                as: "newUserId"
             expect:
                - statusCode: 201

        # Obtener usuario específico
        - get:
             url: "/api/v1/users/{{ newUserId }}"
             headers:
                Authorization: "Bearer {{ authToken }}"
             expect:
                - statusCode: 200

   - name: "Public API"
     weight: 30
     flow:
        # Endpoints públicos
        - get:
             url: "/api/v1/public/stats"
             expect:
                - statusCode: 200
                - contentType: "application/json"

   - name: "File Upload"
     weight: 30
     flow:
        # Test de subida de archivos
        - post:
             url: "/api/v1/files/upload"
             beforeRequest: "generateFileData"
             expect:
                - statusCode: 200

# Funciones personalizadas
functions:
   generateFileData:
      - log: "Generating file data for upload test"
```

### 4. Configurar testing de frontend

#### Lighthouse para Performance Web

```bash
# Instalar Lighthouse
npm install -g lighthouse

# Ejecutar auditoría de performance
lighthouse http://localhost:3000 --output=json --output-path=./lighthouse-report.json

# Script automatizado
cat > performance-audit.js << 'EOF'
const lighthouse = require('lighthouse');
const chromeLauncher = require('chrome-launcher');

async function runLighthouse(url) {
  const chrome = await chromeLauncher.launch({chromeFlags: ['--headless']});
  const options = {
    logLevel: 'info',
    output: 'json',
    onlyCategories: ['performance'],
    port: chrome.port,
  };

  const runnerResult = await lighthouse(url, options);
  await chrome.kill();

  return runnerResult.lhr;
}

// Ejecutar para múltiples páginas
const urls = [
  'http://localhost:3000',
  'http://localhost:3000/dashboard',
  'http://localhost:3000/profile'
];

urls.forEach(async (url) => {
  const result = await runLighthouse(url);
  console.log(`Performance score for ${url}: ${result.categories.performance.score * 100}`);
});
EOF

node performance-audit.js
```

#### Playwright para E2E Performance

```typescript
// tests/performance/page-load.spec.ts
import { expect, test } from "@playwright/test";

test.describe("Page Load Performance", () => {
   test("home page should load within 2 seconds", async ({ page }) => {
      const startTime = Date.now();

      await page.goto("/");
      await page.waitForLoadState("networkidle");

      const loadTime = Date.now() - startTime;
      expect(loadTime).toBeLessThan(2000);

      // Verificar métricas de performance
      const performanceMetrics = await page.evaluate(() => {
         const navigation = performance.getEntriesByType(
            "navigation",
         )[0] as PerformanceNavigationTiming;
         return {
            domContentLoaded: navigation.domContentLoadedEventEnd -
               navigation.domContentLoadedEventStart,
            firstPaint: performance.getEntriesByName("first-paint")[0]
               ?.startTime,
            firstContentfulPaint: performance.getEntriesByName(
               "first-contentful-paint",
            )[0]?.startTime,
         };
      });

      expect(performanceMetrics.domContentLoaded).toBeLessThan(1000);
      expect(performanceMetrics.firstContentfulPaint).toBeLessThan(1500);
   });

   test("dashboard should handle large datasets efficiently", async ({page,}) => {
      // Navegar al dashboard con muchos datos
      await page.goto("/dashboard?limit=1000");

      // Medir tiempo de renderizado
      const startTime = Date.now();
      await page.waitForSelector("[data-testid=data-table]");
      const renderTime = Date.now() - startTime;

      expect(renderTime).toBeLessThan(3000);

      // Verificar que la tabla sea responsive
      const scrollPerformance = await page.evaluate(() => {
         const table = document.querySelector("[data-testid=data-table]");
         const startScroll = performance.now();

         table?.scrollTo({ top: 1000, behavior: "instant" });

         return performance.now() - startScroll;
      });

      expect(scrollPerformance).toBeLessThan(100);
   });
});
```

### 5. Testing de base de datos

#### Script de performance para MySQL

```sql
-- performance-test.sql

-- Test de inserción masiva
DELIMITER $$
CREATE PROCEDURE BulkInsertUsers(IN num_records INT)
BEGIN
  DECLARE i INT DEFAULT 1;
  DECLARE start_time DATETIME;
  DECLARE end_time DATETIME;

  SET start_time = NOW(3);

  WHILE i <= num_records DO
    INSERT INTO users (name, email, password, created_at, updated_at)
    VALUES (
      CONCAT('User ', i),
      CONCAT('user', i, '@example.com'),
      '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi',
      NOW(),
      NOW()
    );
    SET i = i + 1;
  END WHILE;

  SET end_time = NOW(3);
  SELECT CONCAT('Inserted ', num_records, ' records in ',
                TIMESTAMPDIFF(MICROSECOND, start_time, end_time) / 1000, ' ms') AS result;
END$$
DELIMITER ;

-- Ejecutar test
CALL BulkInsertUsers(10000);

-- Test de consultas complejas
EXPLAIN ANALYZE
SELECT u.*, p.title, COUNT(o.id) as order_count
FROM users u
LEFT JOIN profiles p ON u.id = p.user_id
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at >= DATE_SUB(NOW(), INTERVAL 30 DAY)
GROUP BY u.id
HAVING order_count > 5
ORDER BY order_count DESC
LIMIT 100;

-- Análisis de índices
SELECT
  TABLE_NAME,
  INDEX_NAME,
  CARDINALITY,
  PAGES,
  FILTER_CONDITION
FROM INFORMATION_SCHEMA.STATISTICS
WHERE TABLE_SCHEMA = 'your_database'
ORDER BY CARDINALITY DESC;

-- Test de performance de diferentes tipos de JOIN
-- 1. INNER JOIN vs LEFT JOIN performance
SELECT 'INNER JOIN Test' as test_type;
SET @start_time = NOW(3);
SELECT COUNT(*) FROM users u INNER JOIN orders o ON u.id = o.user_id;
SET @end_time = NOW(3);
SELECT TIMESTAMPDIFF(MICROSECOND, @start_time, @end_time) / 1000 as execution_time_ms;

SELECT 'LEFT JOIN Test' as test_type;
SET @start_time = NOW(3);
SELECT COUNT(*) FROM users u LEFT JOIN orders o ON u.id = o.user_id;
SET @end_time = NOW(3);
SELECT TIMESTAMPDIFF(MICROSECOND, @start_time, @end_time) / 1000 as execution_time_ms;

-- 2. Test de subconsultas vs JOINs
SELECT 'Subquery Test' as test_type;
SET @start_time = NOW(3);
SELECT * FROM users WHERE id IN (SELECT user_id FROM orders WHERE total > 100);
SET @end_time = NOW(3);
SELECT TIMESTAMPDIFF(MICROSECOND, @start_time, @end_time) / 1000 as execution_time_ms;

SELECT 'JOIN Test' as test_type;
SET @start_time = NOW(3);
SELECT DISTINCT u.* FROM users u INNER JOIN orders o ON u.id = o.user_id WHERE o.total > 100;
SET @end_time = NOW(3);
SELECT TIMESTAMPDIFF(MICROSECOND, @start_time, @end_time) / 1000 as execution_time_ms;

-- 3. Análisis de EXPLAIN para optimización
EXPLAIN FORMAT=JSON
SELECT u.name, u.email, COUNT(o.id) as order_count, SUM(o.total) as total_spent
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at >= DATE_SUB(NOW(), INTERVAL 1 YEAR)
GROUP BY u.id, u.name, u.email
HAVING order_count > 0
ORDER BY total_spent DESC
LIMIT 50;

-- 4. Test de índices compuestos
-- Crear índice compuesto para testing
CREATE INDEX idx_orders_user_date_total ON orders(user_id, created_at, total);

-- Test query que se beneficia del índice compuesto
EXPLAIN ANALYZE
SELECT * FROM orders
WHERE user_id = 123
  AND created_at >= DATE_SUB(NOW(), INTERVAL 30 DAY)
  AND total > 50
ORDER BY created_at DESC;

-- 5. Test de particionado (ejemplo conceptual)
-- Crear tabla particionada para testing de performance
CREATE TABLE orders_partitioned (
    id INT AUTO_INCREMENT,
    user_id INT NOT NULL,
    total DECIMAL(10,2),
    created_at TIMESTAMP,
    PRIMARY KEY (id, created_at)
) PARTITION BY RANGE (YEAR(created_at)) (
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p2025 VALUES LESS THAN (2026)
);

-- Test de performance entre tabla normal y particionada
SET @start_time = NOW(3);
SELECT COUNT(*) FROM orders WHERE YEAR(created_at) = 2024;
SET @end_time = NOW(3);
SELECT 'Normal Table' as table_type, TIMESTAMPDIFF(MICROSECOND, @start_time, @end_time) / 1000 as execution_time_ms;

SET @start_time = NOW(3);
SELECT COUNT(*) FROM orders_partitioned WHERE YEAR(created_at) = 2024;
SET @end_time = NOW(3);
SELECT 'Partitioned Table' as table_type, TIMESTAMPDIFF(MICROSECOND, @start_time, @end_time) / 1000 as execution_time_ms;
```

#### Script de análisis de performance de DB

```php
<?php
// scripts/db-performance-analyzer.php

class DatabasePerformanceAnalyzer
{
    private $pdo;
    private $results = [];

    public function __construct()
    {
        $this->pdo = new PDO(
            "mysql:host=" . env('DB_HOST') . ";dbname=" . env('DB_DATABASE'),
            env('DB_USERNAME'),
            env('DB_PASSWORD')
        );
    }

    public function runAnalysis(): array
    {
        echo "🗃️ Database Performance Analysis\n";
        echo "==============================\n\n";

        $this->analyzeSlowQueries();
        $this->analyzeIndexUsage();
        $this->analyzeTableSizes();
        $this->analyzeConnectionPool();
        $this->analyzeCacheHitRates();
        $this->analyzeQueryPatterns();

        return $this->results;
    }

    private function analyzeSlowQueries(): void
    {
        echo "1. 🐌 Slow Query Analysis\n";

        // Enable slow query log if not enabled
        $this->pdo->exec("SET GLOBAL slow_query_log = 'ON'");
        $this->pdo->exec("SET GLOBAL long_query_time = 0.1"); // Log queries > 100ms

        // Get slow queries from the last hour
        $stmt = $this->pdo->query("
            SELECT sql_text, query_time, lock_time, rows_sent, rows_examined
            FROM mysql.slow_log
            WHERE start_time >= DATE_SUB(NOW(), INTERVAL 1 HOUR)
            ORDER BY query_time DESC
            LIMIT 10
        ");

        $slowQueries = $stmt->fetchAll(PDO::FETCH_ASSOC);

        foreach ($slowQueries as $query) {
            echo "   ⚠️  Query Time: {$query['query_time']}s\n";
            echo "      Query: " . substr($query['sql_text'], 0, 100) . "...\n";
            echo "      Rows Examined: {$query['rows_examined']}\n\n";
        }

        $this->results['slow_queries'] = $slowQueries;
    }

    private function analyzeIndexUsage(): void
    {
        echo "2. 📇 Index Usage Analysis\n";

        // Find tables without primary keys
        $stmt = $this->pdo->query("
            SELECT table_name
            FROM information_schema.tables
            WHERE table_schema = DATABASE()
            AND table_name NOT IN (
                SELECT table_name
                FROM information_schema.key_column_usage
                WHERE constraint_name = 'PRIMARY'
                AND table_schema = DATABASE()
            )
        ");

        $tablesWithoutPK = $stmt->fetchAll(PDO::FETCH_COLUMN);

        if (!empty($tablesWithoutPK)) {
            echo "   ❌ Tables without primary key:\n";
            foreach ($tablesWithoutPK as $table) {
                echo "      - $table\n";
            }
        }

        // Find unused indexes
        $stmt = $this->pdo->query("
            SELECT s.table_name, s.index_name, s.cardinality
            FROM information_schema.statistics s
            LEFT JOIN information_schema.key_column_usage k
                ON s.table_name = k.table_name AND s.index_name = k.constraint_name
            WHERE s.table_schema = DATABASE()
            AND k.constraint_name IS NULL
            AND s.cardinality < 10
            ORDER BY s.cardinality ASC
        ");

        $lowCardinalityIndexes = $stmt->fetchAll(PDO::FETCH_ASSOC);

        if (!empty($lowCardinalityIndexes)) {
            echo "   ⚠️  Indexes with low cardinality (potentially unused):\n";
            foreach ($lowCardinalityIndexes as $index) {
                echo "      - {$index['table_name']}.{$index['index_name']} (cardinality: {$index['cardinality']})\n";
            }
        }

        $this->results['index_analysis'] = [
            'tables_without_pk' => $tablesWithoutPK,
            'low_cardinality_indexes' => $lowCardinalityIndexes
        ];
    }

    private function analyzeTableSizes(): void
    {
        echo "\n3. 📊 Table Size Analysis\n";

        $stmt = $this->pdo->query("
            SELECT
                table_name,
                table_rows,
                ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb,
                ROUND((data_length / 1024 / 1024), 2) AS data_mb,
                ROUND((index_length / 1024 / 1024), 2) AS index_mb,
                ROUND((index_length / data_length * 100), 2) AS index_ratio
            FROM information_schema.tables
            WHERE table_schema = DATABASE()
            AND table_type = 'BASE TABLE'
            ORDER BY (data_length + index_length) DESC
            LIMIT 10
        ");

        $tableSizes = $stmt->fetchAll(PDO::FETCH_ASSOC);

        foreach ($tableSizes as $table) {
            echo "   📋 {$table['table_name']}: {$table['size_mb']} MB\n";
            echo "      Rows: " . number_format($table['table_rows']) . "\n";
            echo "      Data: {$table['data_mb']} MB, Index: {$table['index_mb']} MB\n";
            echo "      Index Ratio: {$table['index_ratio']}%\n\n";
        }

        $this->results['table_sizes'] = $tableSizes;
    }

    private function analyzeConnectionPool(): void
    {
        echo "4. 🔗 Connection Pool Analysis\n";

        $stmt = $this->pdo->query("SHOW STATUS LIKE 'Threads_connected'");
        $connections = $stmt->fetch(PDO::FETCH_ASSOC);

        $stmt = $this->pdo->query("SHOW VARIABLES LIKE 'max_connections'");
        $maxConnections = $stmt->fetch(PDO::FETCH_ASSOC);

        $connectionRatio = ($connections['Value'] / $maxConnections['Value']) * 100;

        echo "   🔌 Active Connections: {$connections['Value']}\n";
        echo "   🎯 Max Connections: {$maxConnections['Value']}\n";
        echo "   📊 Usage: " . round($connectionRatio, 2) . "%\n";

        if ($connectionRatio > 80) {
            echo "   ⚠️  High connection usage! Consider increasing max_connections or connection pooling.\n";
        }

        $this->results['connections'] = [
            'active' => $connections['Value'],
            'max' => $maxConnections['Value'],
            'usage_percent' => round($connectionRatio, 2)
        ];
    }

    private function analyzeCacheHitRates(): void
    {
        echo "\n5. 💾 Cache Hit Rates\n";

        // Query Cache
        $stmt = $this->pdo->query("SHOW STATUS LIKE 'Qcache_hits'");
        $qcacheHits = $stmt->fetch(PDO::FETCH_ASSOC);

        $stmt = $this->pdo->query("SHOW STATUS LIKE 'Qcache_inserts'");
        $qcacheInserts = $stmt->fetch(PDO::FETCH_ASSOC);

        if ($qcacheHits && $qcacheInserts) {
            $qcacheHitRate = ($qcacheHits['Value'] / ($qcacheHits['Value'] + $qcacheInserts['Value'])) * 100;
            echo "   🎯 Query Cache Hit Rate: " . round($qcacheHitRate, 2) . "%\n";
        }

        // InnoDB Buffer Pool
        $stmt = $this->pdo->query("SHOW STATUS LIKE 'Innodb_buffer_pool_read_requests'");
        $bufferReadRequests = $stmt->fetch(PDO::FETCH_ASSOC);

        $stmt = $this->pdo->query("SHOW STATUS LIKE 'Innodb_buffer_pool_reads'");
        $bufferReads = $stmt->fetch(PDO::FETCH_ASSOC);

        if ($bufferReadRequests && $bufferReads) {
            $bufferHitRate = (1 - ($bufferReads['Value'] / $bufferReadRequests['Value'])) * 100;
            echo "   🗃️ InnoDB Buffer Pool Hit Rate: " . round($bufferHitRate, 2) . "%\n";
        }

        $this->results['cache_hit_rates'] = [
            'query_cache' => round($qcacheHitRate ?? 0, 2),
            'innodb_buffer_pool' => round($bufferHitRate ?? 0, 2)
        ];
    }

    private function analyzeQueryPatterns(): void
    {
        echo "\n6. 🔍 Query Pattern Analysis\n";

        $stmt = $this->pdo->query("
            SHOW STATUS WHERE Variable_name IN (
                'Com_select', 'Com_insert', 'Com_update', 'Com_delete',
                'Com_insert_select', 'Com_replace', 'Com_replace_select'
            )
        ");

        $queryStats = $stmt->fetchAll(PDO::FETCH_KEY_PAIR);

        echo "   📊 Query Distribution:\n";
        foreach ($queryStats as $type => $count) {
            echo "      " . str_replace('Com_', '', $type) . ": " . number_format($count) . "\n";
        }

        $this->results['query_patterns'] = $queryStats;
    }

    public function generateRecommendations(): array
    {
        $recommendations = [];

        // Recommendations based on analysis results
        if (!empty($this->results['slow_queries'])) {
            $recommendations[] = "Optimize slow queries identified in the analysis";
        }

        if (!empty($this->results['index_analysis']['tables_without_pk'])) {
            $recommendations[] = "Add primary keys to tables: " . implode(', ', $this->results['index_analysis']['tables_without_pk']);
        }

        if ($this->results['connections']['usage_percent'] > 80) {
            $recommendations[] = "Consider increasing max_connections or implementing connection pooling";
        }

        if ($this->results['cache_hit_rates']['query_cache'] < 90) {
            $recommendations[] = "Optimize query cache configuration";
        }

        if ($this->results['cache_hit_rates']['innodb_buffer_pool'] < 95) {
            $recommendations[] = "Consider increasing innodb_buffer_pool_size";
        }

        return $recommendations;
    }
}

// Execute the analyzer
$analyzer = new DatabasePerformanceAnalyzer();
$results = $analyzer->runAnalysis();
$recommendations = $analyzer->generateRecommendations();

echo "\n📋 Recommendations:\n";
foreach ($recommendations as $recommendation) {
    echo "   - $recommendation\n";
}
```

### 6. Monitoreo durante las pruebas

#### Script de monitoreo de recursos

```bash
#!/bin/bash
# system-monitor.sh

DURATION=${1:-300}  # 5 minutos por defecto
INTERVAL=${2:-5}    # cada 5 segundos
OUTPUT_FILE="performance-monitor-$(date +%Y%m%d_%H%M%S).log"

echo "Iniciando monitoreo de sistema por ${DURATION} segundos..."
echo "Timestamp,CPU%,Memory%,DiskIO,NetworkRX,NetworkTX" > $OUTPUT_FILE

END_TIME=$(($(date +%s) + $DURATION))

while [ $(date +%s) -lt $END_TIME ]; do
  TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')

  # CPU usage
  CPU=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | sed 's/%us,//')

  # Memory usage
  MEMORY=$(free | grep Mem | awk '{printf "%.1f", $3/$2 * 100.0}')

  # Disk I/O
  DISK_IO=$(iostat -d 1 1 | grep -A1 "Device" | tail -1 | awk '{print $4}')

  # Network
  NETWORK=$(cat /proc/net/dev | grep eth0 | awk '{print $2","$10}')

  echo "$TIMESTAMP,$CPU,$MEMORY,$DISK_IO,$NETWORK" >> $OUTPUT_FILE

  sleep $INTERVAL
done

echo "Monitoreo completado. Resultados en: $OUTPUT_FILE"
```

### 7. Configurar CI/CD para performance testing

#### GitHub Actions

```yaml
# .github/workflows/performance-tests.yml
name: Performance Tests

on:
   schedule:
      - cron: "0 2 * * *" # Diario a las 2 AM
   pull_request:
      branches: [main]
      paths: ["src/**", "api/**"]

jobs:
   api-performance:
      runs-on: ubuntu-latest
      services:
         mysql:
            image: mysql:8.0
            env:
               MYSQL_ROOT_PASSWORD: password
               MYSQL_DATABASE: test_db
            ports:
               - 3306:3306

      steps:
         - uses: actions/checkout@v3

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"

         - name: Install dependencies
           run: composer install --no-dev --optimize-autoloader

         - name: Start Laravel server
           run: |
              php artisan migrate --seed
              php artisan serve &
              sleep 5
           env:
              DB_CONNECTION: mysql
              DB_HOST: 127.0.0.1
              DB_PORT: 3306
              DB_DATABASE: test_db
              DB_USERNAME: root
              DB_PASSWORD: password

         - name: Install Artillery
           run: npm install -g artillery

         - name: Run API performance tests
           run: artillery run tests/performance/api-load-test.yml

         - name: Upload results
           uses: actions/upload-artifact@v3
           with:
              name: performance-results
              path: artillery-report.json

   frontend-performance:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Install dependencies
           run: npm ci

         - name: Build application
           run: npm run build

         - name: Start server
           run: |
              npm run preview &
              sleep 5

         - name: Install Lighthouse
           run: npm install -g lighthouse

         - name: Run Lighthouse audit
           run: |
              lighthouse http://localhost:4173 \
                --output=json \
                --output-path=./lighthouse-report.json \
                --chrome-flags="--headless --no-sandbox"

         - name: Check performance score
           run: |
              SCORE=$(cat lighthouse-report.json | jq '.categories.performance.score * 100')
              echo "Performance score: $SCORE"
              if (( $(echo "$SCORE < 90" | bc -l) )); then
                echo "Performance score below threshold (90)"
                exit 1
              fi

         - name: Upload Lighthouse report
           uses: actions/upload-artifact@v3
           with:
              name: lighthouse-report
              path: lighthouse-report.json
```

## Tips

- **Baseline establecido**: Siempre compara contra una línea base conocida
- **Ambiente controlado**: Usa entornos dedicados para testing de performance
- **Datos realistas**: Utiliza datasets similares a producción
- **Monitoreo continuo**: Ejecuta pruebas regularmente, no solo antes del
  release
- **Métricas específicas**: Define umbrales claros para cada métrica
- **Escalabilidad gradual**: Incrementa la carga gradualmente para identificar
  puntos de quiebre
- **Limpieza de datos**: Limpia datos entre pruebas para resultados consistentes
- **Documentación**: Registra configuraciones y resultados para comparaciones
  futuras

## Ejemplos

### Script completo de performance testing

```bash
#!/bin/bash
# complete-performance-test.sh

set -e

echo "🚀 Iniciando suite completo de performance testing..."

# Variables
API_URL="http://localhost:8000"
WEB_URL="http://localhost:3000"
RESULTS_DIR="performance-results-$(date +%Y%m%d_%H%M%S)"

mkdir -p $RESULTS_DIR

# 1. Verificar que los servicios estén ejecutándose
echo "📡 Verificando servicios..."
curl -f $API_URL/health > /dev/null || { echo "API no disponible"; exit 1; }
curl -f $WEB_URL > /dev/null || { echo "Frontend no disponible"; exit 1; }

# 2. Ejecutar tests de API
echo "🔧 Ejecutando tests de performance de API..."
artillery run tests/performance/api-load-test.yml \
  --output $RESULTS_DIR/api-results.json

# 3. Ejecutar tests de frontend
echo "🎨 Ejecutando auditoría de frontend..."
lighthouse $WEB_URL \
  --output=json \
  --output-path=$RESULTS_DIR/lighthouse-report.json \
  --chrome-flags="--headless"

# 4. Tests de base de datos
echo "🗃️ Ejecutando tests de base de datos..."
mysql -u root -p"$DB_PASSWORD" < tests/performance/db-performance-test.sql \
  > $RESULTS_DIR/db-results.txt

# 5. Generar reporte consolidado
echo "📊 Generando reporte consolidado..."
cat > $RESULTS_DIR/summary.md << EOF
# Performance Test Report

**Fecha**: $(date)
**Duración total**: $(date -d@$SECONDS -u +%H:%M:%S)

## Resultados de API
$(cat $RESULTS_DIR/api-results.json | jq '.aggregate')

## Resultados de Frontend
**Performance Score**: $(cat $RESULTS_DIR/lighthouse-report.json | jq '.categories.performance.score * 100')%
**First Contentful Paint**: $(cat $RESULTS_DIR/lighthouse-report.json | jq '.audits["first-contentful-paint"].displayValue')
**Speed Index**: $(cat $RESULTS_DIR/lighthouse-report.json | jq '.audits["speed-index"].displayValue')

## Resultados de Base de Datos
$(cat $RESULTS_DIR/db-results.txt)

EOF

echo "✅ Performance testing completado. Resultados en: $RESULTS_DIR"
echo "📈 Ver reporte: cat $RESULTS_DIR/summary.md"
```

### Configuración avanzada de K6

```javascript
// advanced-load-test.js
import http from "k6/http";
import { check, group, sleep } from "k6";
import { Counter, Rate, Trend } from "k6/metrics";

// Métricas personalizadas
const failureRate = new Rate("failed_requests");
const apiResponseTime = new Trend("api_response_time");
const apiCalls = new Counter("api_calls_total");

export let options = {
   stages: [
      // Ramp-up
      { duration: "2m", target: 20 },
      // Stay at 20 users
      { duration: "5m", target: 20 },
      // Ramp-up to 50 users
      { duration: "2m", target: 50 },
      // Stay at 50 users
      { duration: "5m", target: 50 },
      // Ramp-up to 100 users
      { duration: "2m", target: 100 },
      // Stay at 100 users
      { duration: "5m", target: 100 },
      // Ramp-down
      { duration: "3m", target: 0 },
   ],
   thresholds: {
      // HTTP errors should be less than 1%
      http_req_failed: ["rate<0.01"],
      // 95% of requests should be below 200ms
      http_req_duration: ["p(95)<200"],
      // 99% of requests should be below 500ms
      "http_req_duration{expected_response:true}": ["p(99)<500"],
      // Custom metrics
      failed_requests: ["rate<0.01"],
      api_response_time: ["p(95)<150"],
   },
   ext: {
      loadimpact: {
         distribution: {
            "amazon:us:ashburn": { loadZone: "amazon:us:ashburn", percent: 50 },
            "amazon:eu:dublin": { loadZone: "amazon:eu:dublin", percent: 50 },
         },
      },
   },
};

const BASE_URL = "http://localhost:8000/api/v1";

export function setup() {
   // Autenticación inicial
   const loginRes = http.post(`${BASE_URL}/auth/login`, {
      email: "admin@example.com",
      password: "password123",
   });

   return {
      authToken: loginRes.json("token"),
   };
}

export default function (data) {
   group("User Management Flow", function () {
      const headers = {
         Authorization: `Bearer ${data.authToken}`,
         "Content-Type": "application/json",
      };

      group("List Users", function () {
         const response = http.get(`${BASE_URL}/users`, { headers });

         const success = check(response, {
            "status is 200": (r) => r.status === 200,
            "has users array": (r) => r.json("data").length >= 0,
            "response time < 100ms": (r) => r.timings.duration < 100,
         });

         failureRate.add(!success);
         apiResponseTime.add(response.timings.duration);
         apiCalls.add(1);
      });

      group("Create User", function () {
         const userData = {
            name: `User ${__VU}_${__ITER}`,
            email: `user${__VU}_${__ITER}@example.com`,
            password: "password123",
         };

         const response = http.post(
            `${BASE_URL}/users`,
            JSON.stringify(userData),
            { headers },
         );

         const success = check(response, {
            "status is 201": (r) => r.status === 201,
            "user created": (r) => r.json("data.id") !== undefined,
            "response time < 200ms": (r) => r.timings.duration < 200,
         });

         failureRate.add(!success);
         apiResponseTime.add(response.timings.duration);
         apiCalls.add(1);

         if (success) {
            const userId = response.json("data.id");

            group("Get User Details", function () {
               const userResponse = http.get(`${BASE_URL}/users/${userId}`, {
                  headers,
               });

               check(userResponse, {
                  "user details retrieved": (r) => r.status === 200,
                  "correct user data": (r) =>
                     r.json("data.email") === userData.email,
               });

               apiCalls.add(1);
            });
         }
      });
   });

   // Simular tiempo de thinking del usuario
   sleep(Math.random() * 3 + 1);
}

export function teardown(data) {
   // Limpieza si es necesario
   console.log("Test completed");
}
```

---

## Navegación

**Progreso en Testing y Quality Assurance:**

- ✅ [Testing y QA](./testing-qa.md)
- ✅ [Tipos de Pruebas](./tipos-pruebas.md)
- ✅ [Testing Funcional Automatizado](./testing-funcional-automatizado.md)
- ✅ [Testing de Regresión](./testing-regresion.md)
- ✅ [Checklists QA](./checklists-qa.md)
- ✅ [Pruebas de Aceptación del Usuario](./pruebas-aceptacion-usuario.md)
- ✅ [Gestión de Reportes de Errores](./gestion-reportes-errores.md)
- ✅ **Testing de Performance y Carga** ← Estás aquí
- ⏭️ [Testing de Seguridad OWASP](./testing-seguridad-owasp.md)
- ⏭️ [Testing de Usabilidad](./testing-usabilidad.md)
- ⏭️ [Code Review y Refactoring](./code-review-refactoring.md)
- ⏭️ [Auditoría de Calidad de Código](./auditoria-calidad-codigo.md)
- ⏭️ [Checklist Específico de Performance](./checklist-performance.md)

---

### Siguiente Paso

Continúa con [**Testing de Seguridad OWASP**](./testing-seguridad-owasp.md)

[⬅️ Gestión de Reportes de Errores](./gestion-reportes-errores.md) |
[🏠 README Principal](../../README.md) |
[➡️ Testing de Seguridad OWASP](./testing-seguridad-owasp.md)
