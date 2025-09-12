# Checklist Específico de Performance

## ¿Qué es?

El checklist específico de performance es una guía estructurada y sistemática
que permite evaluar, validar y optimizar el rendimiento de la aplicación en
todas sus capas. Incluye métricas específicas, thresholds recomendados y
procedimientos de verificación para garantizar que la aplicación cumpla con los
estándares de rendimiento establecidos.

## ¿Por qué es importante?

- **Evaluación sistemática**: Garantiza que no se omitan aspectos críticos de
  performance
- **Estándares consistentes**: Establece criterios uniformes para todo el equipo
- **Detección temprana**: Identifica problemas de rendimiento antes de
  producción
- **Optimización dirigida**: Prioriza las mejoras con mayor impacto
- **Validación continua**: Permite verificar mejoras de forma objetiva
- **Cumplimiento de SLA**: Asegura que se cumplan los acuerdos de nivel de
  servicio
- **Experiencia de usuario**: Garantiza una experiencia fluida y responsive

## ¿Qué debe incluir?

### Categorías de Performance

#### Frontend Performance

- Core Web Vitals (LCP, FID, CLS)
- Time to First Byte (TTFB)
- First Contentful Paint (FCP)
- Speed Index
- Bundle size optimization
- Runtime performance

#### Backend Performance

- API response times
- Database query optimization
- Memory usage
- CPU utilization
- Cache hit rates
- Queue processing times

#### Infrastructure Performance

- Server response times
- Network latency
- CDN effectiveness
- Load balancer distribution
- SSL handshake times

### Métricas y Thresholds

#### Web Vitals Thresholds

- **LCP (Largest Contentful Paint)**: < 2.5s
- **FID (First Input Delay)**: < 100ms
- **CLS (Cumulative Layout Shift)**: < 0.1
- **FCP (First Contentful Paint)**: < 1.8s
- **TTFB (Time to First Byte)**: < 600ms

#### API Performance Thresholds

- **Response Time**: < 200ms (average), < 500ms (95th percentile)
- **Throughput**: > 1000 RPS
- **Error Rate**: < 0.1%
- **Availability**: > 99.9%

#### Database Performance Thresholds

- **Query Time**: < 50ms (average), < 200ms (95th percentile)
- **Connection Pool**: < 80% utilization
- **Index Usage**: > 95% of queries
- **Cache Hit Rate**: > 90%

## ¿Qué debo hacer?

### 1. Frontend Performance Checklist

#### Core Web Vitals Verification

```bash
#!/bin/bash
# frontend-performance-check.sh

echo "🎨 Frontend Performance Checklist"
echo "=================================="

SITE_URL=${1:-"http://localhost:3000"}

# 1. Lighthouse Audit
echo "1. 🔍 Ejecutando Lighthouse Audit..."
lighthouse $SITE_URL \
  --output=json \
  --output-path=./lighthouse-report.json \
  --chrome-flags="--headless --no-sandbox"

# Extract scores
PERFORMANCE_SCORE=$(cat lighthouse-report.json | jq '.categories.performance.score * 100')
LCP=$(cat lighthouse-report.json | jq -r '.audits["largest-contentful-paint"].displayValue')
FID=$(cat lighthouse-report.json | jq -r '.audits["max-potential-fid"].displayValue')
CLS=$(cat lighthouse-report.json | jq -r '.audits["cumulative-layout-shift"].displayValue')
FCP=$(cat lighthouse-report.json | jq -r '.audits["first-contentful-paint"].displayValue')
TTI=$(cat lighthouse-report.json | jq -r '.audits["interactive"].displayValue')

echo "   ✅ Performance Score: $PERFORMANCE_SCORE%"
echo "   📊 LCP: $LCP"
echo "   ⚡ FID: $FID"
echo "   📐 CLS: $CLS"
echo "   🎨 FCP: $FCP"
echo "   ⚙️  TTI: $TTI"

# 2. Bundle Size Analysis
echo "2. 📦 Bundle Size Analysis..."
npm run build > /dev/null 2>&1
BUNDLE_SIZE=$(du -sh dist/ | cut -f1)
echo "   📦 Bundle Size: $BUNDLE_SIZE"

# 3. Core Web Vitals Check
echo "3. 🎯 Core Web Vitals Check..."
node << 'EOF'
const fs = require('fs');
const report = JSON.parse(fs.readFileSync('lighthouse-report.json', 'utf8'));

const lcp = parseFloat(report.audits['largest-contentful-paint'].numericValue) / 1000;
const fid = parseFloat(report.audits['max-potential-fid'].numericValue);
const cls = parseFloat(report.audits['cumulative-layout-shift'].numericValue);

console.log(`   ${lcp <= 2.5 ? '✅' : '❌'} LCP: ${lcp.toFixed(2)}s (Target: ≤ 2.5s)`);
console.log(`   ${fid <= 100 ? '✅' : '❌'} FID: ${fid.toFixed(0)}ms (Target: ≤ 100ms)`);
console.log(`   ${cls <= 0.1 ? '✅' : '❌'} CLS: ${cls.toFixed(3)} (Target: ≤ 0.1)`);

const passed = lcp <= 2.5 && fid <= 100 && cls <= 0.1;
console.log(`\n   Overall: ${passed ? '✅ PASSED' : '❌ FAILED'}`);
process.exit(passed ? 0 : 1);
EOF

echo "4. 🔧 Resource Optimization Check..."
echo "   Checking for unoptimized images..."
find dist/ -name "*.jpg" -o -name "*.png" | head -5 | while read img; do
  size=$(du -h "$img" | cut -f1)
  echo "   📷 $img: $size"
done

echo "   Checking for unminified assets..."
find dist/ -name "*.js" -not -name "*.min.js" | head -3 | while read js; do
  size=$(du -h "$js" | cut -f1)
  echo "   📜 $js: $size"
done

echo "✅ Frontend Performance Check Complete"
```

#### Frontend Performance Checklist Template

```markdown
# Frontend Performance Checklist

## 🎯 Core Web Vitals

- [ ] **LCP < 2.5s**: Largest Contentful Paint

  - [ ] Optimized images (WebP, AVIF)
  - [ ] Resource hints (preload, prefetch)
  - [ ] Server-side rendering implemented
  - [ ] Critical CSS inlined

- [ ] **FID < 100ms**: First Input Delay

  - [ ] JavaScript bundle size optimized
  - [ ] Code splitting implemented
  - [ ] Heavy computations moved to Web Workers
  - [ ] Third-party scripts optimized

- [ ] **CLS < 0.1**: Cumulative Layout Shift
  - [ ] Image dimensions specified
  - [ ] Font display: swap implemented
  - [ ] Ad slots properly sized
  - [ ] Dynamic content properly handled

## 📦 Asset Optimization

- [ ] **Bundle Size**

  - [ ] Main bundle < 150KB (gzipped)
  - [ ] Vendor bundle < 200KB (gzipped)
  - [ ] Tree shaking enabled
  - [ ] Dead code elimination

- [ ] **Images**

  - [ ] Modern formats (WebP, AVIF)
  - [ ] Responsive images (srcset)
  - [ ] Lazy loading implemented
  - [ ] Image compression < 85% quality

- [ ] **CSS & JavaScript**
  - [ ] CSS minification enabled
  - [ ] JavaScript minification enabled
  - [ ] Critical CSS extracted
  - [ ] Unused CSS removed

## 🚀 Loading Performance

- [ ] **Resource Loading**

  - [ ] Preload critical resources
  - [ ] Prefetch next-page resources
  - [ ] DNS prefetch for external domains
  - [ ] Service worker for caching

- [ ] **Rendering Performance**
  - [ ] Server-side rendering (SSR)
  - [ ] Static generation when possible
  - [ ] Progressive enhancement
  - [ ] Skeleton screens for loading states

## 🔧 Runtime Performance

- [ ] **React Performance**

  - [ ] React.memo for expensive components
  - [ ] useMemo for expensive calculations
  - [ ] useCallback for stable references
  - [ ] Virtualization for large lists

- [ ] **Memory Management**
  - [ ] Event listeners cleanup
  - [ ] Component unmounting cleanup
  - [ ] Memory leak detection
  - [ ] Performance profiling

## 📱 Mobile Performance

- [ ] **Mobile Optimization**
  - [ ] Touch targets ≥ 44px
  - [ ] Viewport meta tag configured
  - [ ] Responsive design tested
  - [ ] Mobile-specific optimizations

## 🔍 Monitoring & Testing

- [ ] **Performance Monitoring**
  - [ ] Real User Monitoring (RUM)
  - [ ] Synthetic monitoring setup
  - [ ] Performance budgets defined
  - [ ] Lighthouse CI integration
```

### 2. Backend Performance Checklist

#### API Performance Verification

```php
<?php
// scripts/backend-performance-check.php

class BackendPerformanceChecker
{
    private array $results = [];

    public function runChecklist(): void
    {
        echo "🔧 Backend Performance Checklist\n";
        echo "=================================\n\n";

        $this->checkApiResponseTimes();
        $this->checkDatabasePerformance();
        $this->checkCachePerformance();
        $this->checkMemoryUsage();
        $this->checkQueuePerformance();

        $this->generateReport();
    }

    private function checkApiResponseTimes(): void
    {
        echo "1. 🚀 API Response Times\n";

        $endpoints = [
            '/api/v1/users',
            '/api/v1/products',
            '/api/v1/orders',
            '/api/v1/dashboard'
        ];

        foreach ($endpoints as $endpoint) {
            $times = [];

            // Test 10 requests per endpoint
            for ($i = 0; $i < 10; $i++) {
                $start = microtime(true);

                $response = $this->makeRequest($endpoint);

                $end = microtime(true);
                $times[] = ($end - $start) * 1000; // Convert to ms
            }

            $avgTime = array_sum($times) / count($times);
            $p95Time = $this->calculatePercentile($times, 95);

            $status = $avgTime < 200 && $p95Time < 500 ? '✅' : '❌';
            echo "   $status $endpoint: {$avgTime:.2f}ms avg, {$p95Time:.2f}ms p95\n";

            $this->results['api'][$endpoint] = [
                'avg' => $avgTime,
                'p95' => $p95Time,
                'passed' => $avgTime < 200 && $p95Time < 500
            ];
        }
    }

    private function checkDatabasePerformance(): void
    {
        echo "\n2. 🗃️ Database Performance\n";

        // Check slow queries
        $slowQueries = DB::select("
            SELECT query_time, sql_text
            FROM mysql.slow_log
            WHERE query_time > 0.1
            ORDER BY query_time DESC
            LIMIT 5
        ");

        echo "   Slow Queries (> 100ms):\n";
        foreach ($slowQueries as $query) {
            echo "   ⚠️  {$query->query_time}s: " . substr($query->sql_text, 0, 50) . "...\n";
        }

        // Check connection pool
        $connections = DB::select("SHOW STATUS LIKE 'Threads_connected'")[0];
        $maxConnections = DB::select("SHOW VARIABLES LIKE 'max_connections'")[0];

        $connectionRatio = ($connections->Value / $maxConnections->Value) * 100;
        $connectionStatus = $connectionRatio < 80 ? '✅' : '❌';
        echo "   $connectionStatus Connection Pool: {$connectionRatio:.1f}% utilization\n";

        // Check index usage
        $tables = ['users', 'products', 'orders'];
        foreach ($tables as $table) {
            $indexStats = DB::select("
                SHOW INDEX FROM $table
                WHERE Cardinality > 0
            ");

            $indexCount = count($indexStats);
            $status = $indexCount > 0 ? '✅' : '❌';
            echo "   $status Table '$table': $indexCount indexes\n";
        }
    }

    private function checkCachePerformance(): void
    {
        echo "\n3. 💾 Cache Performance\n";

        // Redis cache hit rate
        $info = Redis::info();
        $hits = $info['keyspace_hits'] ?? 0;
        $misses = $info['keyspace_misses'] ?? 0;

        if ($hits + $misses > 0) {
            $hitRate = ($hits / ($hits + $misses)) * 100;
            $status = $hitRate > 90 ? '✅' : '❌';
            echo "   $status Redis Hit Rate: {$hitRate:.1f}%\n";
        }

        // OPcache status
        if (function_exists('opcache_get_status')) {
            $opcacheStatus = opcache_get_status();
            $hitRate = $opcacheStatus['opcache_statistics']['opcache_hit_rate'];
            $status = $hitRate > 95 ? '✅' : '❌';
            echo "   $status OPcache Hit Rate: {$hitRate:.1f}%\n";
        }

        // Application cache (Laravel)
        $cacheTestKey = 'performance_test_' . time();
        $start = microtime(true);
        Cache::put($cacheTestKey, 'test_value', 60);
        $value = Cache::get($cacheTestKey);
        $end = microtime(true);

        $cacheTime = ($end - $start) * 1000;
        $status = $cacheTime < 10 ? '✅' : '❌';
        echo "   $status Cache Response: {$cacheTime:.2f}ms\n";

        Cache::forget($cacheTestKey);
    }

    private function checkMemoryUsage(): void
    {
        echo "\n4. 🧠 Memory Usage\n";

        $memoryUsage = memory_get_usage(true);
        $memoryPeak = memory_get_peak_usage(true);
        $memoryLimit = ini_get('memory_limit');

        echo "   📊 Current: " . $this->formatBytes($memoryUsage) . "\n";
        echo "   📈 Peak: " . $this->formatBytes($memoryPeak) . "\n";
        echo "   🚫 Limit: $memoryLimit\n";

        // Check if we're using more than 80% of memory limit
        $limitBytes = $this->parseMemoryLimit($memoryLimit);
        $usagePercent = ($memoryPeak / $limitBytes) * 100;
        $status = $usagePercent < 80 ? '✅' : '❌';
        echo "   $status Memory Usage: {$usagePercent:.1f}%\n";
    }

    private function checkQueuePerformance(): void
    {
        echo "\n5. 📋 Queue Performance\n";

        // Check failed jobs
        $failedJobs = DB::table('failed_jobs')->count();
        $status = $failedJobs < 10 ? '✅' : '❌';
        echo "   $status Failed Jobs: $failedJobs\n";

        // Check pending jobs
        $pendingJobs = DB::table('jobs')->count();
        $status = $pendingJobs < 100 ? '✅' : '❌';
        echo "   $status Pending Jobs: $pendingJobs\n";
    }

    private function makeRequest(string $endpoint): array
    {
        // Simulate API request - replace with actual HTTP client
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, 'http://localhost:8000' . $endpoint);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_TIMEOUT, 5);
        curl_setopt($ch, CURLOPT_HEADER, true);

        $response = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        curl_close($ch);

        return ['code' => $httpCode, 'body' => $response];
    }

    private function calculatePercentile(array $values, int $percentile): float
    {
        sort($values);
        $index = ($percentile / 100) * (count($values) - 1);

        if (floor($index) == $index) {
            return $values[$index];
        }

        $lower = $values[floor($index)];
        $upper = $values[ceil($index)];

        return $lower + ($upper - $lower) * ($index - floor($index));
    }

    private function formatBytes(int $bytes): string
    {
        $units = ['B', 'KB', 'MB', 'GB'];
        $factor = floor((strlen($bytes) - 1) / 3);

        return sprintf("%.2f %s", $bytes / pow(1024, $factor), $units[$factor]);
    }

    private function parseMemoryLimit(string $limit): int
    {
        $value = intval($limit);
        $unit = strtolower(substr($limit, -1));

        switch ($unit) {
            case 'g': return $value * 1024 * 1024 * 1024;
            case 'm': return $value * 1024 * 1024;
            case 'k': return $value * 1024;
            default: return $value;
        }
    }

    private function generateReport(): void
    {
        echo "\n📊 Performance Report\n";
        echo "====================\n";

        $passed = 0;
        $total = 0;

        foreach ($this->results as $category => $tests) {
            foreach ($tests as $test => $result) {
                $total++;
                if ($result['passed']) $passed++;
            }
        }

        $score = ($passed / $total) * 100;
        $status = $score >= 80 ? '✅ PASSED' : '❌ FAILED';

        echo "Overall Score: {$score:.1f}% ($passed/$total) - $status\n";
    }
}

// Execute the check
$checker = new BackendPerformanceChecker();
$checker->runChecklist();
```

#### Backend Performance Checklist Template

```markdown
# Backend Performance Checklist

## 🚀 API Performance

- [ ] **Response Times**

  - [ ] Average response time < 200ms
  - [ ] 95th percentile < 500ms
  - [ ] 99th percentile < 1000ms
  - [ ] No endpoints > 2000ms

- [ ] **Throughput**

  - [ ] Can handle 1000+ RPS
  - [ ] Load testing passed
  - [ ] Stress testing passed
  - [ ] No memory leaks under load

- [ ] **Error Rates**
  - [ ] Error rate < 0.1%
  - [ ] 5xx errors < 0.01%
  - [ ] Timeout errors < 0.05%
  - [ ] Proper error handling

## 🗃️ Database Performance

- [ ] **Query Optimization**

  - [ ] No queries > 100ms (avg)
  - [ ] No N+1 query problems
  - [ ] Proper indexing strategy
  - [ ] Query plan analysis done

- [ ] **Connection Management**

  - [ ] Connection pooling configured
  - [ ] Pool utilization < 80%
  - [ ] No connection leaks
  - [ ] Proper timeout settings

- [ ] **Caching Strategy**
  - [ ] Query result caching
  - [ ] Application-level caching
  - [ ] Cache hit rate > 90%
  - [ ] Cache invalidation strategy

## 💾 Cache Performance

- [ ] **Redis Performance**

  - [ ] Hit rate > 90%
  - [ ] Memory usage < 80%
  - [ ] Response time < 10ms
  - [ ] Proper TTL strategy

- [ ] **OPcache**

  - [ ] Hit rate > 95%
  - [ ] Memory usage optimized
  - [ ] Revalidation frequency set
  - [ ] File cache enabled

- [ ] **Application Cache**
  - [ ] Critical data cached
  - [ ] Cache warming strategy
  - [ ] Distributed cache setup
  - [ ] Cache monitoring

## 🧠 Memory Management

- [ ] **Memory Usage**

  - [ ] PHP memory limit appropriate
  - [ ] Peak usage < 80% of limit
  - [ ] No memory leaks
  - [ ] Garbage collection optimized

- [ ] **Resource Management**
  - [ ] File handles properly closed
  - [ ] Database connections closed
  - [ ] External API connections pooled
  - [ ] Image processing optimized

## 📋 Queue Performance

- [ ] **Job Processing**

  - [ ] Job processing time < 30s
  - [ ] Failed job rate < 1%
  - [ ] Queue worker scaling
  - [ ] Dead letter queue setup

- [ ] **Queue Management**
  - [ ] Queue size monitoring
  - [ ] Priority queues configured
  - [ ] Retry logic implemented
  - [ ] Queue health checks
```

### 3. Infrastructure Performance Checklist

#### Infrastructure Performance Verification

```bash
#!/bin/bash
# infrastructure-performance-check.sh

echo "🏗️ Infrastructure Performance Checklist"
echo "========================================"

# 1. Server Performance
echo "1. 🖥️ Server Performance"

# CPU Usage
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | sed 's/%us,//')
CPU_THRESHOLD=80
if (( $(echo "$CPU_USAGE < $CPU_THRESHOLD" | bc -l) )); then
    echo "   ✅ CPU Usage: $CPU_USAGE% (< $CPU_THRESHOLD%)"
else
    echo "   ❌ CPU Usage: $CPU_USAGE% (>= $CPU_THRESHOLD%)"
fi

# Memory Usage
MEMORY_INFO=$(free | grep Mem)
MEMORY_TOTAL=$(echo $MEMORY_INFO | awk '{print $2}')
MEMORY_USED=$(echo $MEMORY_INFO | awk '{print $3}')
MEMORY_USAGE=$(echo "scale=1; $MEMORY_USED * 100 / $MEMORY_TOTAL" | bc)
MEMORY_THRESHOLD=80

if (( $(echo "$MEMORY_USAGE < $MEMORY_THRESHOLD" | bc -l) )); then
    echo "   ✅ Memory Usage: $MEMORY_USAGE% (< $MEMORY_THRESHOLD%)"
else
    echo "   ❌ Memory Usage: $MEMORY_USAGE% (>= $MEMORY_THRESHOLD%)"
fi

# Disk Usage
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
DISK_THRESHOLD=80

if [ "$DISK_USAGE" -lt "$DISK_THRESHOLD" ]; then
    echo "   ✅ Disk Usage: $DISK_USAGE% (< $DISK_THRESHOLD%)"
else
    echo "   ❌ Disk Usage: $DISK_USAGE% (>= $DISK_THRESHOLD%)"
fi

# 2. Network Performance
echo -e "\n2. 🌐 Network Performance"

# Check connectivity to external services
EXTERNAL_SERVICES=("8.8.8.8" "1.1.1.1" "google.com")
for service in "${EXTERNAL_SERVICES[@]}"; do
    PING_RESULT=$(ping -c 1 $service 2>/dev/null | grep "time=" | awk -F'time=' '{print $2}' | awk '{print $1}')
    if [ ! -z "$PING_RESULT" ]; then
        PING_MS=$(echo $PING_RESULT | sed 's/ms//')
        if (( $(echo "$PING_MS < 100" | bc -l) )); then
            echo "   ✅ Ping to $service: ${PING_RESULT}"
        else
            echo "   ❌ Ping to $service: ${PING_RESULT}"
        fi
    else
        echo "   ❌ Ping to $service: Failed"
    fi
done

# 3. Web Server Performance
echo -e "\n3. 🌍 Web Server Performance"

# Check if web server is running
if pgrep nginx > /dev/null; then
    echo "   ✅ Nginx is running"

    # Check nginx worker processes
    WORKER_COUNT=$(pgrep -c nginx)
    CPU_CORES=$(nproc)

    if [ "$WORKER_COUNT" -eq "$CPU_CORES" ] || [ "$WORKER_COUNT" -eq $((CPU_CORES + 1)) ]; then
        echo "   ✅ Nginx workers: $WORKER_COUNT (optimal for $CPU_CORES cores)"
    else
        echo "   ⚠️  Nginx workers: $WORKER_COUNT (consider $CPU_CORES for $CPU_CORES cores)"
    fi
else
    echo "   ❌ Nginx is not running"
fi

# Check PHP-FPM if available
if pgrep php-fpm > /dev/null; then
    echo "   ✅ PHP-FPM is running"

    # Check PHP-FPM pool status
    FPM_STATUS=$(curl -s http://localhost/fpm-status 2>/dev/null || echo "Status not available")
    echo "   📊 PHP-FPM Status: $FPM_STATUS"
else
    echo "   ❌ PHP-FPM is not running"
fi

# 4. SSL Performance
echo -e "\n4. 🔒 SSL Performance"

DOMAIN=${1:-"localhost"}
if command -v openssl &> /dev/null && [ "$DOMAIN" != "localhost" ]; then
    SSL_INFO=$(echo | openssl s_client -connect $DOMAIN:443 -servername $DOMAIN 2>/dev/null)

    # Check SSL handshake time
    SSL_TIME=$(curl -w "@/dev/stdin" -o /dev/null -s "https://$DOMAIN" <<< "time_connect: %{time_connect}\ntime_appconnect: %{time_appconnect}\n" 2>/dev/null)

    if [ ! -z "$SSL_TIME" ]; then
        echo "   ✅ SSL handshake completed"
        echo "$SSL_TIME" | while read line; do
            echo "   📊 $line"
        done
    fi

    # Check certificate expiry
    CERT_EXPIRY=$(echo "$SSL_INFO" | openssl x509 -noout -dates 2>/dev/null | grep notAfter | cut -d= -f2)
    if [ ! -z "$CERT_EXPIRY" ]; then
        EXPIRY_DATE=$(date -d "$CERT_EXPIRY" +%s 2>/dev/null)
        CURRENT_DATE=$(date +%s)
        DAYS_LEFT=$(( (EXPIRY_DATE - CURRENT_DATE) / 86400 ))

        if [ "$DAYS_LEFT" -gt 30 ]; then
            echo "   ✅ SSL Certificate: $DAYS_LEFT days remaining"
        elif [ "$DAYS_LEFT" -gt 7 ]; then
            echo "   ⚠️  SSL Certificate: $DAYS_LEFT days remaining (renew soon)"
        else
            echo "   ❌ SSL Certificate: $DAYS_LEFT days remaining (renew immediately)"
        fi
    fi
fi

# 5. Database Performance
echo -e "\n5. 🗃️ Database Performance"

# Check if MySQL/MariaDB is running
if pgrep mysqld > /dev/null || pgrep mariadbd > /dev/null; then
    echo "   ✅ Database server is running"

    # Check database connections (if mysql client is available)
    if command -v mysql &> /dev/null; then
        DB_CONNECTIONS=$(mysql -e "SHOW STATUS LIKE 'Threads_connected';" 2>/dev/null | tail -1 | awk '{print $2}' || echo "N/A")
        MAX_CONNECTIONS=$(mysql -e "SHOW VARIABLES LIKE 'max_connections';" 2>/dev/null | tail -1 | awk '{print $2}' || echo "N/A")

        if [ "$DB_CONNECTIONS" != "N/A" ] && [ "$MAX_CONNECTIONS" != "N/A" ]; then
            CONNECTION_RATIO=$(echo "scale=1; $DB_CONNECTIONS * 100 / $MAX_CONNECTIONS" | bc 2>/dev/null || echo "N/A")
            if [ "$CONNECTION_RATIO" != "N/A" ]; then
                if (( $(echo "$CONNECTION_RATIO < 80" | bc -l) )); then
                    echo "   ✅ Database connections: $DB_CONNECTIONS/$MAX_CONNECTIONS ($CONNECTION_RATIO%)"
                else
                    echo "   ❌ Database connections: $DB_CONNECTIONS/$MAX_CONNECTIONS ($CONNECTION_RATIO%)"
                fi
            fi
        fi
    fi
else
    echo "   ❌ Database server is not running"
fi

echo -e "\n✅ Infrastructure Performance Check Complete"
```

#### Infrastructure Performance Checklist Template

```markdown
# Infrastructure Performance Checklist

## 🖥️ Server Performance

- [ ] **CPU Usage**

  - [ ] Average CPU usage < 70%
  - [ ] Peak CPU usage < 90%
  - [ ] No CPU throttling
  - [ ] Proper core allocation

- [ ] **Memory Usage**

  - [ ] RAM usage < 80%
  - [ ] No memory swapping
  - [ ] Sufficient buffer cache
  - [ ] Memory leaks monitored

- [ ] **Disk Performance**
  - [ ] Disk usage < 80%
  - [ ] I/O wait < 10%
  - [ ] SSD for database
  - [ ] Regular disk health checks

## 🌐 Network Performance

- [ ] **Connectivity**

  - [ ] Latency < 100ms
  - [ ] Packet loss < 0.1%
  - [ ] Bandwidth sufficient
  - [ ] CDN properly configured

- [ ] **Load Balancing**
  - [ ] Even traffic distribution
  - [ ] Health checks working
  - [ ] Failover tested
  - [ ] Session persistence

## 🌍 Web Server

- [ ] **Nginx/Apache Configuration**

  - [ ] Worker processes = CPU cores
  - [ ] Keep-alive connections
  - [ ] Gzip compression enabled
  - [ ] HTTP/2 enabled

- [ ] **PHP Configuration**
  - [ ] OPcache enabled
  - [ ] FPM pool sized correctly
  - [ ] Memory limit appropriate
  - [ ] Max execution time set

## 🔒 SSL/TLS Performance

- [ ] **SSL Configuration**

  - [ ] TLS 1.3 enabled
  - [ ] OCSP stapling
  - [ ] Session resumption
  - [ ] Certificate chain optimized

- [ ] **Security Headers**
  - [ ] HSTS enabled
  - [ ] CSP configured
  - [ ] Security headers set
  - [ ] Certificate monitoring

## 📊 Monitoring & Alerting

- [ ] **System Monitoring**

  - [ ] CPU, Memory, Disk alerts
  - [ ] Network monitoring
  - [ ] Log aggregation
  - [ ] Uptime monitoring

- [ ] **Application Monitoring**
  - [ ] APM tool configured
  - [ ] Error tracking
  - [ ] Performance metrics
  - [ ] User experience monitoring
```

### 4. Automated Performance Testing

```bash
#!/bin/bash
# automated-performance-test.sh

echo "🚀 Automated Performance Testing Suite"
echo "====================================="

# Configuration
API_URL="http://localhost:8000"
WEB_URL="http://localhost:3000"
RESULTS_DIR="performance-results-$(date +%Y%m%d_%H%M%S)"

mkdir -p $RESULTS_DIR

# 1. Frontend Performance Test
echo "1. 🎨 Frontend Performance Testing..."
./frontend-performance-check.sh $WEB_URL > $RESULTS_DIR/frontend-results.txt

# 2. Backend Performance Test
echo "2. 🔧 Backend Performance Testing..."
php scripts/backend-performance-check.php > $RESULTS_DIR/backend-results.txt

# 3. Infrastructure Performance Test
echo "3. 🏗️ Infrastructure Performance Testing..."
./infrastructure-performance-check.sh > $RESULTS_DIR/infrastructure-results.txt

# 4. Load Testing
echo "4. 📈 Load Testing..."
if command -v k6 &> /dev/null; then
    k6 run tests/performance/load-test.js --out json=$RESULTS_DIR/load-test-results.json
else
    echo "   ⚠️  K6 not installed, skipping load tests"
fi

# 5. Generate Summary Report
echo "5. 📊 Generating Summary Report..."
cat > $RESULTS_DIR/performance-summary.md << EOF
# Performance Test Summary

**Date:** $(date)
**Test Duration:** Generated at $(date)

## Test Results

### Frontend Performance
\`\`\`
$(cat $RESULTS_DIR/frontend-results.txt | tail -20)
\`\`\`

### Backend Performance
\`\`\`
$(cat $RESULTS_DIR/backend-results.txt | tail -20)
\`\`\`

### Infrastructure Performance
\`\`\`
$(cat $RESULTS_DIR/infrastructure-results.txt | tail -20)
\`\`\`

### Load Test Results
$(if [ -f "$RESULTS_DIR/load-test-results.json" ]; then
    echo "Load test completed. See detailed results in load-test-results.json"
else
    echo "Load test skipped (K6 not available)"
fi)

## Recommendations

Based on the test results, consider the following optimizations:

1. **Frontend**: Check Core Web Vitals scores
2. **Backend**: Review slow queries and cache hit rates
3. **Infrastructure**: Monitor resource usage trends
4. **Load Testing**: Verify system can handle expected traffic

## Next Steps

- [ ] Review detailed results in each test file
- [ ] Address any failed checks
- [ ] Schedule regular performance testing
- [ ] Update performance budgets if needed

EOF

echo "✅ Performance Testing Complete"
echo "📁 Results saved to: $RESULTS_DIR/"
echo "📊 Summary report: $RESULTS_DIR/performance-summary.md"

# Check if any tests failed
FAILED_TESTS=0
if grep -q "❌" $RESULTS_DIR/*.txt; then
    FAILED_TESTS=1
    echo "⚠️  Some performance tests failed. Review the results for details."
fi

exit $FAILED_TESTS
```

## Tips

- **Automatización**: Ejecuta checklists de forma automática en CI/CD
- **Thresholds personalizados**: Ajusta los umbrales según tu aplicación
  específica
- **Baseline**: Establece una línea base antes de optimizaciones
- **Monitoreo continuo**: Ejecuta verificaciones regularmente, no solo antes del
  deploy
- **Priorización**: Enfócate en las métricas que más impactan la experiencia del
  usuario
- **Contexto**: Considera el contexto del negocio al interpretar resultados
- **Documentación**: Registra cambios y su impacto en las métricas
- **Evolución**: Actualiza los checklists conforme evoluciona la aplicación

## Ejemplos

### Performance Budget Configuration

```json
{
   "budgets": {
      "frontend": {
         "lighthouse": {
            "performance": 90,
            "accessibility": 90,
            "best-practices": 90,
            "seo": 90
         },
         "webVitals": {
            "lcp": 2500,
            "fid": 100,
            "cls": 0.1
         },
         "bundles": {
            "main": "150KB",
            "vendor": "200KB",
            "total": "500KB"
         }
      },
      "backend": {
         "api": {
            "responseTime": 200,
            "p95ResponseTime": 500,
            "errorRate": 0.1,
            "throughput": 1000
         },
         "database": {
            "queryTime": 50,
            "connectionUtilization": 80,
            "cacheHitRate": 90
         }
      },
      "infrastructure": {
         "server": {
            "cpuUsage": 70,
            "memoryUsage": 80,
            "diskUsage": 80
         },
         "network": {
            "latency": 100,
            "packetLoss": 0.1
         }
      }
   }
}
```

### CI/CD Integration

```yaml
# .github/workflows/performance-check.yml
name: Performance Check

on:
   pull_request:
      branches: [main]
   schedule:
      - cron: "0 2 * * *" # Daily at 2 AM

jobs:
   performance-check:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Setup Node.js
           uses: actions/setup-node@v3
           with:
              node-version: "18"

         - name: Setup PHP
           uses: shivammathur/setup-php@v2
           with:
              php-version: "8.2"

         - name: Install dependencies
           run: |
              npm ci
              composer install

         - name: Start services
           run: |
              npm run build
              npm run preview &
              php artisan serve &
              sleep 10

         - name: Run performance checklist
           run: |
              chmod +x scripts/automated-performance-test.sh
              ./scripts/automated-performance-test.sh

         - name: Upload performance results
           uses: actions/upload-artifact@v3
           with:
              name: performance-results
              path: performance-results-*

         - name: Comment PR with results
           if: github.event_name == 'pull_request'
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const glob = require('glob');

                 const resultFiles = glob.sync('performance-results-*/performance-summary.md');
                 if (resultFiles.length > 0) {
                   const report = fs.readFileSync(resultFiles[0], 'utf8');

                   github.rest.issues.createComment({
                     issue_number: context.issue.number,
                     owner: context.repo.owner,
                     repo: context.repo.repo,
                     body: `## 🚀 Performance Check Results\n\n${report}`
                   });
                 }
```

## Navegación

[⬅️ Auditoría de Calidad de Código](./auditoria-calidad-codigo.md) |
[🏠 README Principal](../../README.md) |
[Revisión General del Código ➡️](./revision-general-codigo.md)
