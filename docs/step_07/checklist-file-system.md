# Checklist de File System

## ¿Qué es?

Un checklist integral para verificar y mantener la salud del sistema de archivos
de la aplicación. Incluye verificaciones de seguridad, rendimiento, integridad y
disponibilidad de archivos, así como validación de configuraciones y procesos de
mantenimiento.

## ¿Por qué es importante?

- **Prevención de Fallos**: Detectar problemas antes de que afecten a usuarios
- **Seguridad**: Verificar permisos y configuraciones de seguridad
- **Performance**: Asegurar rendimiento óptimo del sistema de archivos
- **Compliance**: Cumplir con políticas de retención y seguridad
- **Mantenimiento Proactivo**: Identificar necesidades de limpieza y
  optimización
- **Disaster Recovery**: Validar que los backups y procedimientos funcionan

## ¿Qué debe incluir?

### Verificaciones de Seguridad

- Permisos de archivos y directorios
- Configuraciones de acceso y autenticación
- Validación de uploads y downloads
- Escaneo de archivos maliciosos
- Verificación de configuraciones de almacenamiento

### Verificaciones de Rendimiento

- Espacio disponible en disco
- Velocidad de I/O del sistema
- Fragmentación de archivos
- Eficiencia de cache de archivos
- Métricas de tiempo de respuesta

### Verificaciones de Integridad

- Checksums de archivos críticos
- Validación de estructura de directorios
- Verificación de enlaces y referencias
- Testing de funcionalidades básicas
- Consistencia entre BD y archivos físicos

### Verificaciones de Backup

- Estado de backups automáticos
- Testing de restore procedures
- Verificación de retention policies
- Validación de archivos de backup
- Testing de disaster recovery

## ¿Qué debo hacer?

### 1. Checklist Diario de File System

#### ✅ **Espacio y Recursos**

```bash
# 1. Verificar espacio en disco
□ Espacio disponible > 15% en partición principal
□ Espacio disponible > 20% en partición de storage
□ Inodos disponibles > 10% (sistemas Unix)
□ No hay archivos de más de 1GB sin justificación
□ Crecimiento de directorios dentro de rangos esperados

# Comandos de verificación:
df -h                           # Espacio en disco
df -i                          # Inodos disponibles
du -sh /var/www/html/storage/* # Uso por directorio
find /var/www/html -size +1G   # Archivos grandes
```

#### ✅ **Permisos y Seguridad**

```bash
# 2. Verificar permisos críticos
□ Directorio storage: 755 (rwxr-xr-x)
□ Subdirectorios storage/app: 775 (rwxrwxr-x)
□ Archivo .env: 600 (rw-------)
□ Archivos de configuración: 644 (rw-r--r--)
□ Propietario correcto (www-data/nginx)

# Comandos de verificación:
ls -la /var/www/html/.env
ls -ld /var/www/html/storage/
ls -ld /var/www/html/storage/app/
find /var/www/html -name "*.php" -not -perm 644
```

#### ✅ **Funcionalidades Básicas**

```bash
# 3. Testing de funcionalidades
□ Upload de archivos funciona correctamente
□ Download de archivos funciona correctamente
□ Validaciones de tipo de archivo activas
□ Rate limiting funcionando
□ Logging de operaciones activo

# Testing manual:
curl -X POST -F "file=@test.jpg" http://localhost/api/v1/files/upload
curl -X GET http://localhost/api/v1/files/{file_id}/download
```

#### ✅ **Limpieza y Mantenimiento**

```bash
# 4. Verificar limpieza automática
□ Archivos temporales < 100 archivos en /tmp
□ Logs más antiguos de 30 días eliminados
□ Cache expirado limpiado
□ Archivos huérfanos < 10 archivos
□ Sesiones expiradas eliminadas

# Comandos de verificación:
find /tmp -name "php*" | wc -l
find /var/www/html/storage/logs -name "*.log" -mtime +30 | wc -l
php artisan app:cleanup-files --dry-run
```

### 2. Checklist Semanal de File System

#### ✅ **Análisis de Tendencias**

```bash
# 1. Análisis de crecimiento
□ Revisión de reportes de uso de disco
□ Identificación de directorios con crecimiento anómalo
□ Análisis de patrones de uso de archivos
□ Proyección de necesidades futuras de storage
□ Verificación de alertas de monitoreo

# Comandos para análisis:
/opt/scripts/disk-monitor.sh report
grep "WARNING\|ERROR" /var/log/disk-monitor.log
```

#### ✅ **Integridad de Datos**

```bash
# 2. Verificación de integridad
□ Checksums de archivos críticos válidos
□ Consistencia entre BD y archivos físicos
□ Verificación de enlaces simbólicos
□ Testing de restore de backups
□ Validación de archivos de configuración

# Comandos de verificación:
/opt/scripts/file-integrity-check.sh verify
php artisan app:verify-file-consistency
find /var/www/html -type l -exec test ! -e {} \; -print
```

#### ✅ **Optimización**

```bash
# 3. Optimización de rendimiento
□ Desfragmentación si es necesario
□ Optimización de índices de archivos
□ Compresión de archivos antiguos
□ Revisión de configuraciones de cache
□ Balanceamiento de carga de I/O

# Comandos de optimización:
# Linux: tune2fs -l /dev/sda1 | grep "Free blocks"
# Compression: find /var/www/html/storage/logs -name "*.log" -size +100M -exec gzip {} \;
```

### 3. Checklist Mensual de File System

#### ✅ **Backup y Recovery**

```bash
# 1. Verificación completa de backups
□ Backups diarios ejecutándose correctamente
□ Testing de restore completo exitoso
□ Verificación de backups offsite
□ Validación de retention policies
□ Documentación de procedures actualizada

# Testing de backup:
/opt/scripts/mysql-backup.sh
/opt/scripts/files-backup.sh
# Restore test en ambiente staging
```

#### ✅ **Seguridad Avanzada**

```bash
# 2. Auditoría de seguridad
□ Escaneo de archivos maliciosos
□ Verificación de uploads por usuarios
□ Revisión de logs de acceso sospechoso
□ Validación de configuraciones de firewall
□ Testing de vulnerabilidades conocidas

# Comandos de seguridad:
clamscan -r /var/www/html/storage/app/uploads/
grep "REJECTED\|DENIED" /var/log/nginx/access.log
php artisan app:security-scan
```

#### ✅ **Compliance y Auditoría**

```bash
# 3. Verificación de compliance
□ Cumplimiento de políticas de retención
□ Documentación de accesos a archivos sensibles
□ Verificación de encriptación en storage
□ Auditoría de permisos de usuarios
□ Reporte de métricas de uso

# Reporting:
php artisan app:generate-compliance-report
php artisan app:audit-file-access --month=$(date +%m)
```

### 4. Script Automatizado de Checklist

```bash
#!/bin/bash
# /opt/scripts/filesystem-checklist.sh

set -e

# Configuration
APP_ROOT="/var/www/html"
STORAGE_ROOT="$APP_ROOT/storage"
REPORT_FILE="/var/log/filesystem-checklist-$(date +%Y%m%d).log"

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Logging function
log() {
    echo -e "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$REPORT_FILE"
}

# Check function with status
check() {
    local description="$1"
    local command="$2"
    local expected="$3"
    
    log "Checking: $description"
    
    if eval "$command" >/dev/null 2>&1; then
        log "${GREEN}✅ PASS${NC}: $description"
        return 0
    else
        log "${RED}❌ FAIL${NC}: $description"
        return 1
    fi
}

# Warning check
warn() {
    local description="$1"
    local command="$2"
    
    log "Warning check: $description"
    
    if eval "$command" >/dev/null 2>&1; then
        log "${YELLOW}⚠️  WARNING${NC}: $description"
        return 1
    else
        log "${GREEN}✅ OK${NC}: $description"
        return 0
    fi
}

# Main checklist execution
run_daily_checklist() {
    log "🔍 Starting Daily File System Checklist"
    
    local failed=0
    local warnings=0
    
    # Disk space checks
    log "\n📊 DISK SPACE CHECKS"
    
    check "Root partition has >15% free space" \
        "[ \$(df / | awk 'NR==2 {print \$5}' | sed 's/%//') -lt 85 ]"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    check "Storage partition has >20% free space" \
        "[ \$(df $STORAGE_ROOT | awk 'NR==2 {print \$5}' | sed 's/%//') -lt 80 ]"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    check "Inodes usage <90%" \
        "[ \$(df -i / | awk 'NR==2 {print \$5}' | sed 's/%//') -lt 90 ]"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    warn "Large files (>1GB) present" \
        "find $APP_ROOT -size +1G -type f | grep -q ."
    [ $? -ne 0 ] && warnings=$((warnings + 1))
    
    # Permission checks
    log "\n🔒 PERMISSION CHECKS"
    
    check "Storage directory has correct permissions (755)" \
        "[ \$(stat -c '%a' $STORAGE_ROOT) = '755' ]"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    check ".env file has secure permissions (600)" \
        "[ \$(stat -c '%a' $APP_ROOT/.env) = '600' ]"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    check "Storage directory owned by www-data" \
        "[ \$(stat -c '%U' $STORAGE_ROOT) = 'www-data' ]"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    # Functionality checks
    log "\n⚙️ FUNCTIONALITY CHECKS"
    
    check "Upload endpoint responds" \
        "curl -s -o /dev/null -w '%{http_code}' http://localhost/api/v1/files/upload | grep -q '401\|422'"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    check "Storage disk is writable" \
        "touch $STORAGE_ROOT/test-write && rm $STORAGE_ROOT/test-write"
    [ $? -ne 0 ] && failed=$((failed + 1))
    
    # Cleanup checks
    log "\n🧹 CLEANUP CHECKS"
    
    warn "Too many temp files in /tmp" \
        "[ \$(find /tmp -name 'php*' | wc -l) -gt 100 ]"
    [ $? -ne 0 ] && warnings=$((warnings + 1))
    
    warn "Old log files present (>30 days)" \
        "find $STORAGE_ROOT/logs -name '*.log' -mtime +30 | grep -q ."
    [ $? -ne 0 ] && warnings=$((warnings + 1))
    
    # Generate summary
    log "\n📋 CHECKLIST SUMMARY"
    log "Failed checks: $failed"
    log "Warnings: $warnings"
    
    if [ $failed -eq 0 ]; then
        log "${GREEN}✅ All critical checks passed${NC}"
        return 0
    else
        log "${RED}❌ $failed critical checks failed${NC}"
        return 1
    fi
}

# Weekly checklist
run_weekly_checklist() {
    log "🔍 Starting Weekly File System Checklist"
    
    # Integrity checks
    log "\n🔍 INTEGRITY CHECKS"
    /opt/scripts/file-integrity-check.sh verify
    
    # Growth analysis
    log "\n📈 GROWTH ANALYSIS"
    /opt/scripts/disk-monitor.sh analyze
    
    # Performance checks
    log "\n⚡ PERFORMANCE CHECKS"
    
    # Check I/O performance
    local io_time=$(time (dd if=/dev/zero of=$STORAGE_ROOT/test-io bs=1M count=100 && sync) 2>&1 | grep real | awk '{print $2}')
    log "I/O test time: $io_time"
    
    # Cleanup test file
    rm -f $STORAGE_ROOT/test-io
}

# Monthly checklist
run_monthly_checklist() {
    log "🔍 Starting Monthly File System Checklist"
    
    # Backup verification
    log "\n💾 BACKUP VERIFICATION"
    /opt/scripts/mysql-backup.sh verify
    /opt/scripts/files-backup.sh verify
    
    # Security scan
    log "\n🛡️ SECURITY SCAN"
    if command -v clamscan &> /dev/null; then
        clamscan -r $STORAGE_ROOT/app/uploads/ --quiet
    fi
    
    # Compliance report
    log "\n📊 COMPLIANCE REPORT"
    cd $APP_ROOT
    php artisan app:generate-compliance-report --month=$(date +%m)
}

# Main execution
main() {
    case "${1:-daily}" in
        "daily")
            run_daily_checklist
            ;;
        "weekly")
            run_weekly_checklist
            ;;
        "monthly")
            run_monthly_checklist
            ;;
        "all")
            run_daily_checklist
            run_weekly_checklist
            run_monthly_checklist
            ;;
        *)
            echo "Usage: $0 {daily|weekly|monthly|all}"
            exit 1
            ;;
    esac
}

# Execute main function
main "$@"
```

### 5. Integration con CI/CD

```yaml
# .github/workflows/filesystem-check.yml
name: File System Health Check

on:
   schedule:
      - cron: "0 8 * * *" # Daily at 8 AM
   workflow_dispatch:

jobs:
   filesystem-check:
      runs-on: ubuntu-latest

      steps:
         - name: Checkout code
           uses: actions/checkout@v3

         - name: Setup environment
           run: |
              sudo apt-get update
              sudo apt-get install -y tree

         - name: Check file structure
           run: |
              # Verify expected directory structure
              test -d "docs/step_07"
              test -d "docs/step_08"
              test -f "docs/step_08/file-system-maintenance.md"

         - name: Validate file permissions in repo
           run: |
              # Check for files with incorrect permissions
              find . -name "*.md" -not -perm 644 && exit 1 || true
              find . -name "*.sh" -not -perm 755 && exit 1 || true

         - name: Check for large files
           run: |
              # Fail if files > 10MB are committed
              find . -size +10M -type f | grep -v ".git" && exit 1 || true

         - name: Validate markdown links
           run: |
              # Check for broken internal links
              grep -r "\]\(\./" docs/ | while read line; do
                file=$(echo $line | cut -d: -f1)
                link=$(echo $line | sed 's/.*\](\([^)]*\)).*/\1/')
                if [[ $link == ./* ]]; then
                  target=$(dirname $file)/$link
                  if [[ ! -f $target ]]; then
                    echo "Broken link in $file: $link"
                    exit 1
                  fi
                fi
              done
```

## Tips

### 🎯 **Automatización del Checklist**

1. **Scheduling Inteligente**:
   - Ejecutar checks críticos más frecuentemente
   - Usar diferentes horarios para evitar sobrecarga
   - Implementar checks condicionales basados en uso

2. **Alertas Proactivas**:
   - Configurar umbrales de warning antes de critical
   - Integrar con sistemas de monitoreo existentes
   - Crear dashboards para visualizar métricas

3. **Documentación Automática**:
   - Generar reportes automáticos de checks
   - Mantener histórico de issues encontrados
   - Crear runbooks para problemas comunes

### 🚀 **Optimizaciones Avanzadas**

1. **Checks Contextuales**:
   - Ajustar umbrales según el día de la semana
   - Considerar patrones estacionales de uso
   - Adaptar checks según tipo de aplicación

2. **Machine Learning**:
   - Predecir failures basado en patrones históricos
   - Optimizar frecuencia de checks automáticamente
   - Detectar anomalías en patrones de uso

3. **Integration con DevOps**:
   - Incluir checks en pipelines de deployment
   - Validar health antes de releases
   - Automatizar rollbacks si checks fallan

## Ejemplos

### Dashboard de Monitoreo

```bash
#!/bin/bash
# /opt/scripts/filesystem-dashboard.sh

# Generate HTML dashboard
generate_dashboard() {
    cat > /var/www/html/filesystem-status.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>File System Status Dashboard</title>
    <meta http-equiv="refresh" content="300">
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .status-ok { color: green; }
        .status-warning { color: orange; }
        .status-error { color: red; }
        .metric { margin: 10px 0; padding: 10px; border-left: 4px solid #ccc; }
        .metric.ok { border-left-color: green; }
        .metric.warning { border-left-color: orange; }
        .metric.error { border-left-color: red; }
    </style>
</head>
<body>
    <h1>File System Status Dashboard</h1>
    <p>Last updated: $(date)</p>
    
    <h2>Disk Usage</h2>
    <div class="metric $(get_disk_status)">
        <strong>Root Partition:</strong> $(df -h / | awk 'NR==2 {print $5}') used
    </div>
    
    <div class="metric $(get_storage_status)">
        <strong>Storage Partition:</strong> $(df -h /var/www/html/storage | awk 'NR==2 {print $5}') used
    </div>
    
    <h2>Recent Checks</h2>
    <pre>$(tail -20 /var/log/filesystem-checklist-$(date +%Y%m%d).log)</pre>
    
</body>
</html>
EOF
}

# Helper functions for status
get_disk_status() {
    local usage=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')
    if [ $usage -gt 90 ]; then echo "error"
    elif [ $usage -gt 75 ]; then echo "warning"
    else echo "ok"; fi
}

get_storage_status() {
    local usage=$(df /var/www/html/storage | awk 'NR==2 {print $5}' | sed 's/%//')
    if [ $usage -gt 85 ]; then echo "error"
    elif [ $usage -gt 70 ]; then echo "warning"
    else echo "ok"; fi
}

generate_dashboard
```

### Testing Automatizado

```bash
#!/bin/bash
# /opt/scripts/test-filesystem-health.sh

# Test file upload functionality
test_upload() {
    local test_file="/tmp/test-upload.txt"
    echo "Test content $(date)" > "$test_file"
    
    # Test upload via API
    local response=$(curl -s -w "%{http_code}" -o /tmp/upload-response.json \
        -X POST -F "file=@$test_file" \
        -H "Authorization: Bearer $API_TOKEN" \
        http://localhost/api/v1/files/upload)
    
    if [[ "$response" == "201" ]]; then
        echo "✅ Upload test passed"
        
        # Extract file ID and test download
        local file_id=$(jq -r '.data.file_id' /tmp/upload-response.json)
        test_download "$file_id"
    else
        echo "❌ Upload test failed: HTTP $response"
        return 1
    fi
    
    # Cleanup
    rm -f "$test_file" /tmp/upload-response.json
}

# Test file download functionality
test_download() {
    local file_id="$1"
    
    local response=$(curl -s -w "%{http_code}" -o /tmp/download-test.txt \
        -H "Authorization: Bearer $API_TOKEN" \
        "http://localhost/api/v1/files/$file_id/download")
    
    if [[ "$response" == "200" ]]; then
        echo "✅ Download test passed"
        rm -f /tmp/download-test.txt
        return 0
    else
        echo "❌ Download test failed: HTTP $response"
        return 1
    fi
}

# Run all tests
echo "🧪 Running file system functionality tests..."
test_upload
echo "✅ All functionality tests completed"
```

## Navegación

[⬅️ Checklist de Performance](./checklist-performance.md) |
[🏠 README Principal](../../README.md) |
[Optimización del Frontend ➡️](./optimizacion-frontend.md)
