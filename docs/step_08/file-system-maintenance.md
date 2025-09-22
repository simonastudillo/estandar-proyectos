# Mantenimiento del File System

## Conocimientos involucrados
- Administración de sistemas
- Monitoreo de almacenamiento
- Seguridad de datos

## Responsable
- Equipo de DevOps

## ¿Qué es?

El mantenimiento del file system es un conjunto de procedimientos automatizados
y manuales para garantizar la integridad, rendimiento y disponibilidad del
sistema de archivos de la aplicación. Incluye limpieza automática de archivos
temporales, monitoreo de espacio en disco, verificación de integridad y
optimización del almacenamiento.

## ¿Por qué es importante?

- **Prevención de Fallos**: Evita problemas por falta de espacio en disco
- **Rendimiento**: Mantiene el rendimiento óptimo del sistema de archivos
- **Integridad de Datos**: Garantiza que los archivos no se corrompan
- **Seguridad**: Elimina archivos temporales que podrían contener información
  sensible
- **Cumplimiento**: Mantiene políticas de retención de datos
- **Costos**: Optimiza el uso del almacenamiento para reducir costos

## ¿Qué debe incluir?

### Limpieza Automática

- Archivos temporales y de sesión
- Logs antiguos y rotación automática
- Archivos de cache expirados
- Uploads fallidos o incompletos
- Archivos huérfanos sin referencias en BD

### Monitoreo del Sistema

- Espacio disponible en disco
- Crecimiento de directorios específicos
- Archivos de gran tamaño
- Inodos disponibles (sistemas Unix)
- Rendimiento de I/O del disco

### Verificación de Integridad

- Checksums de archivos críticos
- Verificación de permisos
- Validación de estructura de directorios
- Testing de funcionalidades de archivo
- Verificación de enlaces simbólicos

### Optimización

- Desfragmentación de archivos
- Compresión de archivos antiguos
- Migración a almacenamiento más económico
- Indexación para búsquedas rápidas
- Balanceamiento entre discos

## ¿Qué debo hacer?

### 1. Configurar Limpieza Automática de Archivos

#### Script Principal de Limpieza

```bash
#!/bin/bash
# /opt/scripts/filesystem-cleanup.sh

set -e

# Configuration
APP_ROOT="/var/www/html"
STORAGE_ROOT="$APP_ROOT/storage"
LOG_DIR="/var/log/app"
TEMP_DIR="/tmp"
RETENTION_DAYS_LOGS=30
RETENTION_DAYS_TEMP=7
RETENTION_DAYS_CACHE=14

# Log function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a /var/log/filesystem-cleanup.log
}

# Verificar espacio en disco antes de iniciar
check_disk_space() {
    log "Checking disk space..."
    
    USAGE=$(df -h "$APP_ROOT" | awk 'NR==2 {print $5}' | sed 's/%//')
    
    if [ "$USAGE" -gt 85 ]; then
        log "WARNING: Disk usage is ${USAGE}% - Running aggressive cleanup"
        AGGRESSIVE_MODE=true
    else
        log "Disk usage is ${USAGE}% - Normal cleanup mode"
        AGGRESSIVE_MODE=false
    fi
}

# Limpiar archivos temporales
cleanup_temp_files() {
    log "Cleaning temporary files..."
    
    # Laravel temp files
    find "$STORAGE_ROOT/framework/cache" -name "*.php" -mtime +$RETENTION_DAYS_CACHE -delete 2>/dev/null || true
    find "$STORAGE_ROOT/framework/sessions" -name "sess_*" -mtime +1 -delete 2>/dev/null || true
    find "$STORAGE_ROOT/framework/views" -name "*.php" -mtime +$RETENTION_DAYS_CACHE -delete 2>/dev/null || true
    
    # System temp files
    find "$TEMP_DIR" -name "php*" -mtime +1 -delete 2>/dev/null || true
    find "$TEMP_DIR" -name "tmp*" -mtime +1 -delete 2>/dev/null || true
    
    # Upload temp files
    find "$STORAGE_ROOT/app/temp" -type f -mtime +$RETENTION_DAYS_TEMP -delete 2>/dev/null || true
    
    log "Temporary files cleanup completed"
}

# Limpiar logs antiguos
cleanup_old_logs() {
    log "Cleaning old log files..."
    
    # Application logs
    find "$STORAGE_ROOT/logs" -name "*.log" -mtime +$RETENTION_DAYS_LOGS -delete 2>/dev/null || true
    find "$LOG_DIR" -name "*.log" -mtime +$RETENTION_DAYS_LOGS -delete 2>/dev/null || true
    
    # Nginx/Apache logs (si están en el directorio de la app)
    find "$LOG_DIR" -name "access.log.*" -mtime +$RETENTION_DAYS_LOGS -delete 2>/dev/null || true
    find "$LOG_DIR" -name "error.log.*" -mtime +$RETENTION_DAYS_LOGS -delete 2>/dev/null || true
    
    # Comprimir logs grandes actuales
    find "$STORAGE_ROOT/logs" -name "*.log" -size +100M -exec gzip {} \; 2>/dev/null || true
    
    log "Log cleanup completed"
}

# Limpiar archivos huérfanos
cleanup_orphaned_files() {
    log "Cleaning orphaned files..."
    
    # Ejecutar comando artisan para limpieza de archivos huérfanos
    cd "$APP_ROOT"
    php artisan app:cleanup-files --orphaned --expired
    
    log "Orphaned files cleanup completed"
}

# Limpiar cache expirado
cleanup_expired_cache() {
    log "Cleaning expired cache..."
    
    cd "$APP_ROOT"
    
    # Laravel cache
    php artisan cache:clear
    php artisan view:clear
    php artisan config:clear
    php artisan route:clear
    
    # Redis cache (si está configurado)
    if command -v redis-cli &> /dev/null; then
        redis-cli --scan --pattern "cache:*" | while read key; do
            ttl=$(redis-cli ttl "$key")
            if [ "$ttl" = "-2" ]; then
                redis-cli del "$key"
            fi
        done
    fi
    
    log "Cache cleanup completed"
}

# Verificar y reparar permisos
fix_permissions() {
    log "Fixing file permissions..."
    
    # Laravel storage permissions
    chmod -R 755 "$STORAGE_ROOT"
    chmod -R 775 "$STORAGE_ROOT/app"
    chmod -R 775 "$STORAGE_ROOT/framework"
    chmod -R 775 "$STORAGE_ROOT/logs"
    
    # Ownership
    chown -R www-data:www-data "$STORAGE_ROOT"
    
    log "Permissions fixed"
}

# Generar reporte de uso de disco
generate_disk_report() {
    log "Generating disk usage report..."
    
    REPORT_FILE="/var/log/disk-usage-$(date +%Y%m%d).log"
    
    {
        echo "=== DISK USAGE REPORT - $(date) ==="
        echo ""
        echo "Overall disk usage:"
        df -h
        echo ""
        echo "Top 20 largest directories:"
        du -ah "$APP_ROOT" 2>/dev/null | sort -rh | head -20
        echo ""
        echo "Files larger than 100MB:"
        find "$APP_ROOT" -type f -size +100M -exec ls -lh {} \; 2>/dev/null
        echo ""
        echo "Storage breakdown:"
        du -sh "$STORAGE_ROOT"/* 2>/dev/null
    } > "$REPORT_FILE"
    
    log "Disk report generated: $REPORT_FILE"
}

# Función principal
main() {
    log "Starting filesystem maintenance..."
    
    check_disk_space
    cleanup_temp_files
    cleanup_old_logs
    cleanup_orphaned_files
    cleanup_expired_cache
    fix_permissions
    generate_disk_report
    
    # Si está en modo agresivo, ejecutar limpieza adicional
    if [ "$AGGRESSIVE_MODE" = true ]; then
        log "Running aggressive cleanup..."
        
        # Reducir retención temporalmente
        RETENTION_DAYS_LOGS=7
        RETENTION_DAYS_CACHE=3
        
        cleanup_old_logs
        cleanup_expired_cache
        
        # Comprimir archivos grandes
        find "$STORAGE_ROOT" -name "*.log" -size +50M -exec gzip {} \;
    fi
    
    # Verificación final
    FINAL_USAGE=$(df -h "$APP_ROOT" | awk 'NR==2 {print $5}' | sed 's/%//')
    log "Filesystem maintenance completed. Final disk usage: ${FINAL_USAGE}%"
    
    if [ "$FINAL_USAGE" -gt 90 ]; then
        log "CRITICAL: Disk usage still high after cleanup. Manual intervention required."
        exit 1
    fi
}

# Ejecutar función principal
main "$@"
```

### 2. Comando Artisan para Limpieza Avanzada

```php
<?php
// app/Infrastructure/Console/Commands/FileSystemCleanupCommand.php

namespace App\Infrastructure\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\DB;
use App\Infrastructure\Persistence\Eloquent\Models\FileUploadModel;

class FileSystemCleanupCommand extends Command
{
    protected $signature = 'app:cleanup-files 
                           {--orphaned : Clean orphaned files}
                           {--expired : Clean expired files}
                           {--temp : Clean temporary files}
                           {--dry-run : Show what would be deleted without actually deleting}
                           {--days=30 : Days to keep files}';

    protected $description = 'Clean up file system and remove unnecessary files';

    public function handle(): int
    {
        $this->info('🧹 Starting file system cleanup...');
        
        $dryRun = $this->option('dry-run');
        $days = (int) $this->option('days');
        
        if ($dryRun) {
            $this->warn('DRY RUN MODE - No files will be deleted');
        }
        
        $totalCleaned = 0;
        $totalSize = 0;
        
        if ($this->option('orphaned') || $this->option('all')) {
            [$count, $size] = $this->cleanupOrphanedFiles($dryRun);
            $totalCleaned += $count;
            $totalSize += $size;
        }
        
        if ($this->option('expired') || $this->option('all')) {
            [$count, $size] = $this->cleanupExpiredFiles($dryRun, $days);
            $totalCleaned += $count;
            $totalSize += $size;
        }
        
        if ($this->option('temp') || $this->option('all')) {
            [$count, $size] = $this->cleanupTempFiles($dryRun);
            $totalCleaned += $count;
            $totalSize += $size;
        }
        
        $this->info("✅ Cleanup completed!");
        $this->info("Files processed: {$totalCleaned}");
        $this->info("Space freed: " . $this->formatBytes($totalSize));
        
        return Command::SUCCESS;
    }
    
    private function cleanupOrphanedFiles(bool $dryRun): array
    {
        $this->info('🔍 Scanning for orphaned files...');
        
        $allFiles = Storage::disk('private')->allFiles('uploads');
        $registeredFiles = FileUploadModel::pluck('path')->toArray();
        
        $orphanedFiles = array_diff($allFiles, $registeredFiles);
        $totalSize = 0;
        
        if (empty($orphanedFiles)) {
            $this->info('No orphaned files found');
            return [0, 0];
        }
        
        $this->warn(count($orphanedFiles) . ' orphaned files found');
        
        foreach ($orphanedFiles as $file) {
            $size = Storage::disk('private')->size($file);
            $totalSize += $size;
            
            $this->line("  - {$file} (" . $this->formatBytes($size) . ")");
            
            if (!$dryRun) {
                Storage::disk('private')->delete($file);
            }
        }
        
        return [count($orphanedFiles), $totalSize];
    }
    
    private function cleanupExpiredFiles(bool $dryRun, int $days): array
    {
        $this->info('🗓️ Scanning for expired files...');
        
        $expiredFiles = FileUploadModel::where('expires_at', '<', now())
            ->orWhere('created_at', '<', now()->subDays($days))
            ->get();
        
        if ($expiredFiles->isEmpty()) {
            $this->info('No expired files found');
            return [0, 0];
        }
        
        $totalSize = 0;
        $this->warn($expiredFiles->count() . ' expired files found');
        
        foreach ($expiredFiles as $file) {
            $size = $file->size ?? 0;
            $totalSize += $size;
            
            $this->line("  - {$file->original_name} (" . $this->formatBytes($size) . ")");
            
            if (!$dryRun) {
                if (Storage::disk('private')->exists($file->path)) {
                    Storage::disk('private')->delete($file->path);
                }
                $file->forceDelete();
            }
        }
        
        return [$expiredFiles->count(), $totalSize];
    }
    
    private function cleanupTempFiles(bool $dryRun): array
    {
        $this->info('🗂️ Scanning for temporary files...');
        
        $tempPaths = [
            'temp/',
            'cache/',
            'sessions/',
            'framework/cache/',
            'framework/sessions/',
            'framework/views/',
        ];
        
        $totalFiles = 0;
        $totalSize = 0;
        
        foreach ($tempPaths as $path) {
            if (!Storage::disk('private')->exists($path)) {
                continue;
            }
            
            $files = Storage::disk('private')->files($path);
            
            foreach ($files as $file) {
                $lastModified = Storage::disk('private')->lastModified($file);
                
                // Eliminar archivos temporales de más de 1 día
                if (time() - $lastModified > 86400) {
                    $size = Storage::disk('private')->size($file);
                    $totalSize += $size;
                    $totalFiles++;
                    
                    $this->line("  - {$file} (" . $this->formatBytes($size) . ")");
                    
                    if (!$dryRun) {
                        Storage::disk('private')->delete($file);
                    }
                }
            }
        }
        
        if ($totalFiles === 0) {
            $this->info('No temporary files found');
        }
        
        return [$totalFiles, $totalSize];
    }
    
    private function formatBytes(int $bytes): string
    {
        $units = ['B', 'KB', 'MB', 'GB', 'TB'];
        
        for ($i = 0; $bytes > 1024 && $i < count($units) - 1; $i++) {
            $bytes /= 1024;
        }
        
        return round($bytes, 2) . ' ' . $units[$i];
    }
}
```

### 3. Monitoreo de Espacio en Disco

#### Script de Monitoreo

```bash
#!/bin/bash
# /opt/scripts/disk-monitor.sh

set -e

# Configuration
ALERT_THRESHOLD=85
CRITICAL_THRESHOLD=95
APP_ROOT="/var/www/html"
STORAGE_ROOT="$APP_ROOT/storage"
LOG_FILE="/var/log/disk-monitor.log"
SLACK_WEBHOOK_URL="${SLACK_WEBHOOK_URL:-}"

# Log function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Send alert function
send_alert() {
    local level=$1
    local message=$2
    
    log "$level: $message"
    
    # Send to Slack if webhook is configured
    if [ -n "$SLACK_WEBHOOK_URL" ]; then
        curl -s -X POST -H 'Content-type: application/json' \
            --data "{\"text\":\"🚨 Disk Alert: $message\"}" \
            "$SLACK_WEBHOOK_URL"
    fi
    
    # Send email (if mail is configured)
    if command -v mail &> /dev/null; then
        echo "$message" | mail -s "Disk Space Alert - $level" admin@yourdomain.com
    fi
}

# Check disk usage
check_disk_usage() {
    local path=$1
    local name=$2
    
    if [ ! -d "$path" ]; then
        return
    fi
    
    local usage=$(df "$path" | awk 'NR==2 {print $5}' | sed 's/%//')
    local available=$(df -h "$path" | awk 'NR==2 {print $4}')
    
    log "$name usage: ${usage}% (${available} available)"
    
    if [ "$usage" -ge "$CRITICAL_THRESHOLD" ]; then
        send_alert "CRITICAL" "$name disk usage is ${usage}% - Immediate action required!"
        return 2
    elif [ "$usage" -ge "$ALERT_THRESHOLD" ]; then
        send_alert "WARNING" "$name disk usage is ${usage}% - Cleanup recommended"
        return 1
    fi
    
    return 0
}

# Check inode usage (Unix systems)
check_inode_usage() {
    local path=$1
    local name=$2
    
    if [ ! -d "$path" ]; then
        return
    fi
    
    local inode_usage=$(df -i "$path" | awk 'NR==2 {print $5}' | sed 's/%//')
    
    log "$name inode usage: ${inode_usage}%"
    
    if [ "$inode_usage" -ge "$CRITICAL_THRESHOLD" ]; then
        send_alert "CRITICAL" "$name inode usage is ${inode_usage}% - Too many files!"
        return 2
    elif [ "$inode_usage" -ge "$ALERT_THRESHOLD" ]; then
        send_alert "WARNING" "$name inode usage is ${inode_usage}% - Consider cleanup"
        return 1
    fi
    
    return 0
}

# Check large files
check_large_files() {
    log "Checking for large files (>500MB)..."
    
    find "$APP_ROOT" -type f -size +500M -exec ls -lh {} \; 2>/dev/null | while read line; do
        log "Large file found: $line"
    done
}

# Check rapid growth
check_rapid_growth() {
    local current_size=$(du -s "$STORAGE_ROOT" | cut -f1)
    local previous_size_file="/tmp/storage_size_previous"
    
    if [ -f "$previous_size_file" ]; then
        local previous_size=$(cat "$previous_size_file")
        local growth=$((current_size - previous_size))
        local growth_percent=$((growth * 100 / previous_size))
        
        if [ "$growth_percent" -gt 20 ]; then
            send_alert "WARNING" "Storage directory grew by ${growth_percent}% since last check"
        fi
    fi
    
    echo "$current_size" > "$previous_size_file"
}

# Main monitoring function
main() {
    log "Starting disk monitoring..."
    
    local exit_code=0
    
    # Check main application disk
    check_disk_usage "$APP_ROOT" "Application"
    if [ $? -gt $exit_code ]; then exit_code=$?; fi
    
    # Check storage disk (if different)
    check_disk_usage "$STORAGE_ROOT" "Storage"
    if [ $? -gt $exit_code ]; then exit_code=$?; fi
    
    # Check inodes
    check_inode_usage "$APP_ROOT" "Application"
    check_inode_usage "$STORAGE_ROOT" "Storage"
    
    # Check for large files
    check_large_files
    
    # Check rapid growth
    check_rapid_growth
    
    log "Disk monitoring completed with exit code: $exit_code"
    
    # If critical threshold reached, trigger automated cleanup
    if [ $exit_code -eq 2 ]; then
        log "Critical threshold reached - Triggering automated cleanup"
        /opt/scripts/filesystem-cleanup.sh
    fi
    
    exit $exit_code
}

# Execute main function
main "$@"
```

### 4. Verificación de Integridad de Archivos

#### Script de Verificación

```bash
#!/bin/bash
# /opt/scripts/file-integrity-check.sh

set -e

# Configuration
APP_ROOT="/var/www/html"
CHECKSUM_FILE="/var/backups/file-checksums.sha256"
CRITICAL_FILES=(
    "$APP_ROOT/.env"
    "$APP_ROOT/config/app.php"
    "$APP_ROOT/config/database.php"
    "$APP_ROOT/composer.json"
    "$APP_ROOT/composer.lock"
)

# Log function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a /var/log/file-integrity.log
}

# Generate checksums for critical files
generate_checksums() {
    log "Generating checksums for critical files..."
    
    > "$CHECKSUM_FILE.new"
    
    for file in "${CRITICAL_FILES[@]}"; do
        if [ -f "$file" ]; then
            sha256sum "$file" >> "$CHECKSUM_FILE.new"
        fi
    done
    
    # Add application files
    find "$APP_ROOT/app" -name "*.php" -type f -exec sha256sum {} \; >> "$CHECKSUM_FILE.new"
    find "$APP_ROOT/config" -name "*.php" -type f -exec sha256sum {} \; >> "$CHECKSUM_FILE.new"
    
    mv "$CHECKSUM_FILE.new" "$CHECKSUM_FILE"
    log "Checksums generated: $CHECKSUM_FILE"
}

# Verify file integrity
verify_integrity() {
    log "Verifying file integrity..."
    
    if [ ! -f "$CHECKSUM_FILE" ]; then
        log "No previous checksums found - generating initial checksums"
        generate_checksums
        return 0
    fi
    
    local errors=0
    
    while IFS= read -r line; do
        local expected_hash=$(echo "$line" | cut -d' ' -f1)
        local file_path=$(echo "$line" | cut -d' ' -f3-)
        
        if [ -f "$file_path" ]; then
            local current_hash=$(sha256sum "$file_path" | cut -d' ' -f1)
            
            if [ "$expected_hash" != "$current_hash" ]; then
                log "ERROR: File modified: $file_path"
                errors=$((errors + 1))
            fi
        else
            log "WARNING: File missing: $file_path"
        fi
    done < "$CHECKSUM_FILE"
    
    if [ $errors -eq 0 ]; then
        log "File integrity check passed"
        return 0
    else
        log "File integrity check failed with $errors errors"
        return 1
    fi
}

# Check file permissions
check_permissions() {
    log "Checking file permissions..."
    
    local errors=0
    
    # Check critical file permissions
    for file in "${CRITICAL_FILES[@]}"; do
        if [ -f "$file" ]; then
            local perms=$(stat -c "%a" "$file")
            
            case "$file" in
                *".env")
                    if [ "$perms" != "600" ]; then
                        log "ERROR: $file has incorrect permissions: $perms (should be 600)"
                        errors=$((errors + 1))
                    fi
                    ;;
                *)
                    if [ "$perms" != "644" ]; then
                        log "WARNING: $file has permissions: $perms"
                    fi
                    ;;
            esac
        fi
    done
    
    # Check storage directory permissions
    local storage_perms=$(stat -c "%a" "$APP_ROOT/storage")
    if [ "$storage_perms" != "755" ]; then
        log "ERROR: Storage directory has incorrect permissions: $storage_perms"
        errors=$((errors + 1))
    fi
    
    return $errors
}

# Main function
main() {
    case "${1:-verify}" in
        "generate")
            generate_checksums
            ;;
        "verify")
            verify_integrity
            check_permissions
            ;;
        "check-permissions")
            check_permissions
            ;;
        *)
            echo "Usage: $0 {generate|verify|check-permissions}"
            exit 1
            ;;
    esac
}

# Execute main function
main "$@"
```

### 5. Configuración de Cron Jobs

```bash
# Editar crontab
sudo crontab -e

# Limpieza diaria a las 2:00 AM
0 2 * * * /opt/scripts/filesystem-cleanup.sh

# Monitoreo cada hora
0 * * * * /opt/scripts/disk-monitor.sh

# Verificación de integridad diaria a las 3:00 AM
0 3 * * * /opt/scripts/file-integrity-check.sh verify

# Comando artisan de limpieza semanal
0 1 * * 0 cd /var/www/html && php artisan app:cleanup-files --orphaned --expired --temp

# Reporte de uso de disco semanal
0 8 * * 1 /opt/scripts/disk-monitor.sh report
```

## Tips

### 🎯 **Mejores Prácticas de Mantenimiento**

1. **Automatización Progressive**:
   - Comenzar con limpieza manual supervisada
   - Implementar gradually scripts automáticos
   - Siempre tener modo dry-run para testing

2. **Monitoreo Proactivo**:
   - Configurar alertas antes de llegar a límites críticos
   - Monitorear tendencias de crecimiento
   - Establecer baselines de uso normal

3. **Respaldos Antes de Limpieza**:
   - Siempre respaldar antes de limpiezas masivas
   - Mantener logs detallados de operaciones
   - Tener procedimientos de rollback

4. **Documentación de Operaciones**:
   - Documentar qué se limpia y cuándo
   - Mantener inventario de archivos críticos
   - Registrar excepciones y casos especiales

### 🚀 **Optimizaciones Avanzadas**

1. **Storage Tiering**:
   - Mover archivos antiguos a storage más barato
   - Comprimir automáticamente archivos por edad
   - Implementar lifecycle policies

2. **Indexación Inteligente**:
   - Indexar archivos para búsquedas rápidas
   - Mantener metadata en base de datos
   - Implementar búsqueda full-text en contenidos

3. **Análisis de Patrones**:
   - Analizar patrones de uso de archivos
   - Predecir necesidades de storage
   - Optimizar ubicación por frecuencia de acceso

## Ejemplos

### Configuración de Alertas Avanzadas

```bash
#!/bin/bash
# /opt/scripts/advanced-disk-alerts.sh

# Configuration
PROMETHEUS_GATEWAY="http://localhost:9091"
GRAFANA_WEBHOOK="https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK"

# Send metrics to Prometheus
send_metrics() {
    local disk_usage=$1
    local path=$2
    
    curl -s -X POST "$PROMETHEUS_GATEWAY/metrics/job/disk_monitor/instance/$HOSTNAME" \
        --data-binary "disk_usage_percent{path=\"$path\"} $disk_usage"
}

# Advanced disk analysis
analyze_disk_growth() {
    local path=$1
    local history_file="/var/log/disk-history-$(basename "$path").log"
    
    # Record current usage
    local current_usage=$(df "$path" | awk 'NR==2 {print $3}')
    echo "$(date '+%s') $current_usage" >> "$history_file"
    
    # Calculate growth rate (last 7 days)
    if [ -f "$history_file" ]; then
        local week_ago=$(($(date '+%s') - 604800))
        local old_usage=$(awk -v cutoff="$week_ago" '$1 >= cutoff {print $2; exit}' "$history_file")
        
        if [ -n "$old_usage" ] && [ "$old_usage" -gt 0 ]; then
            local growth_rate=$(echo "scale=2; ($current_usage - $old_usage) * 100 / $old_usage" | bc)
            echo "Growth rate for $path: ${growth_rate}% per week"
            
            # Alert if growth > 50% per week
            if (( $(echo "$growth_rate > 50" | bc -l) )); then
                send_alert "HIGH_GROWTH" "Disk usage growing at ${growth_rate}% per week for $path"
            fi
        fi
    fi
}

# Predict disk full date
predict_disk_full() {
    local path=$1
    local history_file="/var/log/disk-history-$(basename "$path").log"
    
    if [ -f "$history_file" ] && [ $(wc -l < "$history_file") -gt 7 ]; then
        # Calculate trend using linear regression (simplified)
        local trend=$(tail -7 "$history_file" | awk '
            BEGIN { n=0; sx=0; sy=0; sxx=0; sxy=0 }
            { n++; sx+=$1; sy+=$2; sxx+=$1*$1; sxy+=$1*$2 }
            END { 
                if (n > 1) {
                    slope = (n*sxy - sx*sy) / (n*sxx - sx*sx)
                    print slope
                }
            }
        ')
        
        if [ -n "$trend" ] && (( $(echo "$trend > 0" | bc -l) )); then
            local current_free=$(df "$path" | awk 'NR==2 {print $4}')
            local days_to_full=$(echo "scale=0; $current_free / $trend / 86400" | bc)
            
            if [ "$days_to_full" -lt 30 ]; then
                send_alert "DISK_FULL_PREDICTION" "Disk $path predicted to be full in $days_to_full days"
            fi
        fi
    fi
}
```

### Testing de Mantenimiento

```bash
#!/bin/bash
# /opt/scripts/test-maintenance.sh

# Test filesystem maintenance scripts
test_cleanup_script() {
    echo "Testing cleanup script..."
    
    # Create test files
    mkdir -p /tmp/test-cleanup
    touch /tmp/test-cleanup/old-file
    touch /tmp/test-cleanup/new-file
    
    # Age the old file
    touch -d "2 days ago" /tmp/test-cleanup/old-file
    
    # Run cleanup in dry-run mode
    /opt/scripts/filesystem-cleanup.sh --dry-run
    
    # Verify files still exist
    if [ -f /tmp/test-cleanup/old-file ] && [ -f /tmp/test-cleanup/new-file ]; then
        echo "✅ Dry-run test passed"
    else
        echo "❌ Dry-run test failed"
    fi
    
    # Cleanup test files
    rm -rf /tmp/test-cleanup
}

# Test disk monitoring
test_disk_monitor() {
    echo "Testing disk monitor..."
    
    # Run monitor in test mode
    /opt/scripts/disk-monitor.sh --test
    
    echo "✅ Disk monitor test completed"
}

# Run all tests
echo "🧪 Running maintenance tests..."
test_cleanup_script
test_disk_monitor
echo "✅ All tests completed"
```

## Navegación

[⬅️ Backup y Recovery](./backup-recovery-strategies.md) |
[🏠 README Principal](../../README.md) |
[SSL y Configuraciones de Seguridad ➡️](./ssl-configuraciones-seguridad.md)
