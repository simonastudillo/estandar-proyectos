# Backup y Recovery Strategies

## ¿Qué es?

Las estrategias de backup y recovery son procedimientos sistemáticos para crear,
mantener y restaurar copias de seguridad de datos críticos de la aplicación.
Incluye tanto datos de la base de datos como archivos del sistema,
configuraciones y código fuente, garantizando la continuidad del negocio ante
fallos, errores humanos o desastres.

## ¿Por qué es importante?

- **Continuidad del Negocio**: Minimizar downtime y pérdida de datos
- **Protección contra Errores**: Recuperación de errores humanos o bugs
- **Disaster Recovery**: Recuperación ante fallos de hardware o desastres
  naturales
- **Compliance**: Cumplir con regulaciones de retención de datos
- **Migración**: Facilitar migraciones entre servidores o proveedores
- **Testing**: Crear ambientes de testing con datos reales

## ¿Qué debe incluir?

### Backup de Base de Datos

- Backups automáticos diarios de MySQL
- Backups transaccionales para consistency
- Compresión y encriptación de backups
- Retention policies por tipo de backup
- Testing regular de restore procedures

### Backup de Archivos

- Código fuente (aunque esté en Git)
- Archivos subidos por usuarios
- Configuraciones de servidor
- Certificates SSL/TLS
- Logs históricos importantes

### Estrategias de Backup

- **Full Backups**: Backup completo periódico
- **Incremental Backups**: Solo cambios desde último backup
- **Differential Backups**: Cambios desde último full backup
- **Point-in-time Recovery**: Restaurar a momento específico

### Almacenamiento

- Local storage para acceso rápido
- Remote storage para disaster recovery
- Cloud storage para redundancia geográfica
- Multiple locations para alta disponibilidad

## ¿Qué debo hacer?

### 1. Configurar Backup Automático de MySQL

#### Script de Backup de Base de Datos

```bash
#!/bin/bash
# /opt/scripts/mysql-backup.sh

set -e

# Configuration
DB_NAME="app_production"
DB_USER="backup_user"
DB_PASSWORD="secure_backup_password"
BACKUP_DIR="/var/backups/mysql"
RETENTION_DAYS=30
S3_BUCKET="your-backup-bucket"

# Timestamp
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
BACKUP_FILE="${BACKUP_DIR}/${DB_NAME}_${TIMESTAMP}.sql.gz"

# Create backup directory if it doesn't exist
mkdir -p "${BACKUP_DIR}"

# Log function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a /var/log/mysql-backup.log
}

log "Starting backup of database: ${DB_NAME}"

# Create backup
mysqldump \
    --single-transaction \
    --routines \
    --triggers \
    --lock-tables=false \
    --user="${DB_USER}" \
    --password="${DB_PASSWORD}" \
    "${DB_NAME}" | gzip > "${BACKUP_FILE}"

# Verify backup was created
if [ -f "${BACKUP_FILE}" ]; then
    BACKUP_SIZE=$(du -h "${BACKUP_FILE}" | cut -f1)
    log "Backup created successfully: ${BACKUP_FILE} (${BACKUP_SIZE})"
else
    log "ERROR: Backup failed to create"
    exit 1
fi

# Upload to S3 (optional)
if command -v aws &> /dev/null; then
    log "Uploading backup to S3..."
    aws s3 cp "${BACKUP_FILE}" "s3://${S3_BUCKET}/mysql/" \
        --storage-class STANDARD_IA
    log "Backup uploaded to S3 successfully"
fi

# Clean old local backups
find "${BACKUP_DIR}" -name "*.sql.gz" -mtime +${RETENTION_DAYS} -delete
log "Cleaned backups older than ${RETENTION_DAYS} days"

# Test backup integrity
log "Testing backup integrity..."
gunzip -t "${BACKUP_FILE}"
if [ $? -eq 0 ]; then
    log "Backup integrity check passed"
else
    log "ERROR: Backup integrity check failed"
    exit 1
fi

log "Backup process completed successfully"
```

#### Configurar Cron Job

```bash
# Editar crontab
sudo crontab -e

# Backup diario a las 2:00 AM
0 2 * * * /opt/scripts/mysql-backup.sh

# Backup semanal completo los domingos a las 1:00 AM
0 1 * * 0 /opt/scripts/mysql-full-backup.sh

# Verificar logs de backup cada día a las 8:00 AM
0 8 * * * /opt/scripts/check-backup-status.sh
```

### 2. Backup de Archivos del Sistema

#### Script de Backup de Archivos

```bash
#!/bin/bash
# /opt/scripts/files-backup.sh

set -e

# Configuration
SOURCE_DIRS=(
    "/var/www/html/storage/app/public"
    "/etc/nginx/sites-available"
    "/etc/ssl/certs"
    "/var/log/app"
)
BACKUP_DIR="/var/backups/files"
RETENTION_DAYS=14
S3_BUCKET="your-backup-bucket"

# Timestamp
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
BACKUP_FILE="${BACKUP_DIR}/files_${TIMESTAMP}.tar.gz"

# Create backup directory
mkdir -p "${BACKUP_DIR}"

# Log function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a /var/log/files-backup.log
}

log "Starting files backup..."

# Create compressed archive
tar -czf "${BACKUP_FILE}" \
    --exclude='*.tmp' \
    --exclude='cache/*' \
    "${SOURCE_DIRS[@]}"

# Verify backup
if [ -f "${BACKUP_FILE}" ]; then
    BACKUP_SIZE=$(du -h "${BACKUP_FILE}" | cut -f1)
    log "Files backup created: ${BACKUP_FILE} (${BACKUP_SIZE})"
else
    log "ERROR: Files backup failed"
    exit 1
fi

# Upload to cloud storage
if command -v aws &> /dev/null; then
    aws s3 cp "${BACKUP_FILE}" "s3://${S3_BUCKET}/files/"
    log "Files backup uploaded to S3"
fi

# Cleanup old backups
find "${BACKUP_DIR}" -name "files_*.tar.gz" -mtime +${RETENTION_DAYS} -delete
log "Files backup completed successfully"
```

### 3. Configurar Point-in-Time Recovery

#### Habilitar Binary Logging en MySQL

```ini
# /etc/mysql/mysql.conf.d/mysqld.cnf
[mysqld]
# Enable binary logging for point-in-time recovery
log-bin = mysql-bin
binlog_format = ROW
sync_binlog = 1
expire_logs_days = 7
max_binlog_size = 100M

# Server ID (required for replication/binary logging)
server-id = 1

# For point-in-time recovery
binlog_do_db = app_production
```

#### Script de Point-in-Time Recovery

```bash
#!/bin/bash
# /opt/scripts/mysql-point-in-time-recovery.sh

set -e

# Parameters
RECOVERY_DATE="$1"  # Format: 2024-01-15 14:30:00
BACKUP_FILE="$2"    # Full backup file path
BINLOG_DIR="/var/lib/mysql"
TEMP_DIR="/tmp/mysql-recovery"

if [ -z "$RECOVERY_DATE" ] || [ -z "$BACKUP_FILE" ]; then
    echo "Usage: $0 'YYYY-MM-DD HH:MM:SS' /path/to/backup.sql.gz"
    exit 1
fi

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

log "Starting point-in-time recovery to: $RECOVERY_DATE"

# Create temporary directory
mkdir -p "$TEMP_DIR"

# 1. Restore from full backup
log "Restoring from full backup: $BACKUP_FILE"
if [[ "$BACKUP_FILE" == *.gz ]]; then
    gunzip -c "$BACKUP_FILE" | mysql app_production
else
    mysql app_production < "$BACKUP_FILE"
fi

# 2. Get backup timestamp
BACKUP_TIMESTAMP=$(mysql -e "SELECT MAX(created_at) FROM app_production.migrations;" -sN)
log "Backup timestamp: $BACKUP_TIMESTAMP"

# 3. Apply binary logs from backup time to recovery time
log "Applying binary logs from $BACKUP_TIMESTAMP to $RECOVERY_DATE"

# Find binary logs in the time range
mysql -e "SHOW BINARY LOGS;" | awk '{print $1}' | grep -v Log_name | while read binlog; do
    mysqlbinlog \
        --start-datetime="$BACKUP_TIMESTAMP" \
        --stop-datetime="$RECOVERY_DATE" \
        "$BINLOG_DIR/$binlog" >> "$TEMP_DIR/recovery.sql"
done

# Apply the binary log changes
if [ -f "$TEMP_DIR/recovery.sql" ]; then
    mysql app_production < "$TEMP_DIR/recovery.sql"
    log "Binary logs applied successfully"
else
    log "No binary logs found for the specified time range"
fi

# Cleanup
rm -rf "$TEMP_DIR"

log "Point-in-time recovery completed successfully"
```

### 4. Backup de Configuraciones Docker

#### Script para Backup de Contenedores

```bash
#!/bin/bash
# /opt/scripts/docker-backup.sh

set -e

BACKUP_DIR="/var/backups/docker"
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
COMPOSE_FILE="/var/www/docker-compose.yml"

mkdir -p "$BACKUP_DIR"

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a /var/log/docker-backup.log
}

log "Starting Docker backup..."

# Backup Docker volumes
docker run --rm \
    -v docker_db_data:/source:ro \
    -v "$BACKUP_DIR":/backup \
    alpine tar czf "/backup/db_volume_${TIMESTAMP}.tar.gz" -C /source .

# Backup application files
docker run --rm \
    -v docker_app_storage:/source:ro \
    -v "$BACKUP_DIR":/backup \
    alpine tar czf "/backup/app_storage_${TIMESTAMP}.tar.gz" -C /source .

# Backup docker-compose configuration
cp "$COMPOSE_FILE" "$BACKUP_DIR/docker-compose_${TIMESTAMP}.yml"

# Backup environment files
cp /var/www/.env "$BACKUP_DIR/env_${TIMESTAMP}.backup"

log "Docker backup completed"
```

### 5. Monitoring de Backups

#### Script de Verificación de Backups

```bash
#!/bin/bash
# /opt/scripts/check-backup-status.sh

set -e

BACKUP_DIRS=(
    "/var/backups/mysql"
    "/var/backups/files"
    "/var/backups/docker"
)

SLACK_WEBHOOK="$SLACK_BACKUP_WEBHOOK"
MAX_AGE_HOURS=25  # Alert if backup is older than 25 hours

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

send_alert() {
    local message="$1"
    local color="$2"
    
    if [ -n "$SLACK_WEBHOOK" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data "{\"text\":\"🚨 Backup Alert\", \"attachments\":[{\"color\":\"$color\", \"text\":\"$message\"}]}" \
            "$SLACK_WEBHOOK"
    fi
    
    # Also send email
    echo "$message" | mail -s "Backup Alert" admin@yourcompany.com
}

check_backup_freshness() {
    local backup_dir="$1"
    local backup_type="$2"
    
    if [ ! -d "$backup_dir" ]; then
        send_alert "❌ Backup directory not found: $backup_dir" "danger"
        return 1
    fi
    
    # Find most recent backup
    latest_backup=$(find "$backup_dir" -type f -name "*.gz" -o -name "*.tar.gz" | head -1)
    
    if [ -z "$latest_backup" ]; then
        send_alert "❌ No backups found in $backup_dir" "danger"
        return 1
    fi
    
    # Check age
    backup_age_hours=$(( ($(date +%s) - $(stat -c %Y "$latest_backup")) / 3600 ))
    
    if [ $backup_age_hours -gt $MAX_AGE_HOURS ]; then
        send_alert "⚠️ $backup_type backup is $backup_age_hours hours old (file: $latest_backup)" "warning"
        return 1
    else
        log "✅ $backup_type backup is fresh ($backup_age_hours hours old)"
        return 0
    fi
}

# Check all backup types
all_good=true

check_backup_freshness "/var/backups/mysql" "Database" || all_good=false
check_backup_freshness "/var/backups/files" "Files" || all_good=false
check_backup_freshness "/var/backups/docker" "Docker" || all_good=false

# Test backup integrity
latest_db_backup=$(find /var/backups/mysql -name "*.sql.gz" -type f -exec ls -t {} + | head -1)
if [ -n "$latest_db_backup" ]; then
    if gunzip -t "$latest_db_backup" 2>/dev/null; then
        log "✅ Database backup integrity check passed"
    else
        send_alert "❌ Database backup integrity check failed: $latest_db_backup" "danger"
        all_good=false
    fi
fi

if $all_good; then
    log "✅ All backup checks passed"
else
    log "❌ Some backup checks failed - alerts sent"
fi
```

### 6. Restore Procedures

#### Procedimiento de Restore de Base de Datos

```bash
#!/bin/bash
# /opt/scripts/mysql-restore.sh

set -e

BACKUP_FILE="$1"
DB_NAME="${2:-app_production}"
CONFIRMATION="$3"

if [ -z "$BACKUP_FILE" ]; then
    echo "Usage: $0 /path/to/backup.sql.gz [database_name] [CONFIRM]"
    exit 1
fi

if [ "$CONFIRMATION" != "CONFIRM" ]; then
    echo "⚠️  WARNING: This will REPLACE the current database!"
    echo "⚠️  Database: $DB_NAME"
    echo "⚠️  Backup: $BACKUP_FILE"
    echo ""
    echo "To proceed, run:"
    echo "$0 $BACKUP_FILE $DB_NAME CONFIRM"
    exit 1
fi

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

log "Starting database restore..."
log "Database: $DB_NAME"
log "Backup file: $BACKUP_FILE"

# Verify backup file exists
if [ ! -f "$BACKUP_FILE" ]; then
    log "ERROR: Backup file not found: $BACKUP_FILE"
    exit 1
fi

# Test backup integrity
log "Testing backup integrity..."
if [[ "$BACKUP_FILE" == *.gz ]]; then
    if ! gunzip -t "$BACKUP_FILE"; then
        log "ERROR: Backup file is corrupted"
        exit 1
    fi
fi

# Create backup of current database before restore
current_backup="/tmp/pre_restore_backup_$(date +%Y%m%d_%H%M%S).sql.gz"
log "Creating backup of current database: $current_backup"
mysqldump --single-transaction "$DB_NAME" | gzip > "$current_backup"

# Perform restore
log "Dropping existing database..."
mysql -e "DROP DATABASE IF EXISTS $DB_NAME;"

log "Creating new database..."
mysql -e "CREATE DATABASE $DB_NAME CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"

log "Restoring from backup..."
if [[ "$BACKUP_FILE" == *.gz ]]; then
    gunzip -c "$BACKUP_FILE" | mysql "$DB_NAME"
else
    mysql "$DB_NAME" < "$BACKUP_FILE"
fi

log "Database restore completed successfully"
log "Pre-restore backup saved at: $current_backup"
```

## Tips

### Backup Strategy Best Practices

- **3-2-1 Rule**: 3 copias, 2 medios diferentes, 1 offsite
- **Test Regularly**: Probar restores periódicamente
- **Automate Everything**: Backups, testing, y alertas automáticos
- **Monitor Storage**: Alertas cuando el espacio se agote

### Security Considerations

- **Encrypt Backups**: Especialmente para datos sensibles
- **Secure Storage**: Acceso restringido a ubicaciones de backup
- **Retention Policies**: Eliminar backups antiguos según compliance
- **Access Logs**: Auditar acceso a backups

### Performance Optimization

- **Off-peak Hours**: Realizar backups cuando hay menos carga
- **Compression**: Reducir espacio de almacenamiento
- **Incremental Backups**: Para reducir tiempo y recursos
- **Parallel Processing**: Para bases de datos grandes

### Disaster Recovery Planning

- **RTO/RPO Targets**: Recovery Time/Point Objectives claros
- **Runbooks**: Procedimientos documentados paso a paso
- **Communication Plan**: Quién notificar y cómo
- **Alternative Infrastructure**: Plan B para infraestructura

## Ejemplos

### Backup con Encriptación

```bash
# Backup encriptado con GPG
mysqldump --single-transaction app_production | \
gzip | \
gpg --symmetric --cipher-algo AES256 --compress-algo 1 \
--output "backup_$(date +%Y%m%d).sql.gz.gpg"

# Restore desde backup encriptado
gpg --decrypt backup_20240115.sql.gz.gpg | \
gunzip | \
mysql app_production
```

### Backup Differential

```bash
#!/bin/bash
# Backup diferencial usando rsync

FULL_BACKUP_DIR="/var/backups/full"
DIFF_BACKUP_DIR="/var/backups/differential"
SOURCE_DIR="/var/www/html/storage"

# Crear backup diferencial
rsync -av --link-dest="$FULL_BACKUP_DIR/latest" \
    "$SOURCE_DIR/" \
    "$DIFF_BACKUP_DIR/$(date +%Y%m%d_%H%M%S)/"
```

### Automated Testing de Restore

```bash
#!/bin/bash
# Test automático de restore

TEST_DB="test_restore_$(date +%Y%m%d_%H%M%S)"
BACKUP_FILE="/var/backups/mysql/latest.sql.gz"

# Crear DB de test
mysql -e "CREATE DATABASE $TEST_DB;"

# Restore a DB de test
gunzip -c "$BACKUP_FILE" | mysql "$TEST_DB"

# Verificar integridad
table_count=$(mysql -sN -e "SELECT COUNT(*) FROM information_schema.tables WHERE table_schema='$TEST_DB';")

if [ "$table_count" -gt 0 ]; then
    echo "✅ Restore test passed - $table_count tables restored"
else
    echo "❌ Restore test failed - no tables found"
fi

# Cleanup
mysql -e "DROP DATABASE $TEST_DB;"
```

## Navegación

**Progreso en Despliegue y DevOps:**

- ✅ [Despliegue y DevOps](./despliegue-devops.md)
- ✅
  [Configuración de Servidores de Producción](./configuracion-servidores-produccion.md)
- ✅ [CI/CD Pipelines con GitHub Actions](./ci-cd-pipelines-github-actions.md)
- ✅ [Docker y Contenedores](./docker-contenedores.md)
- ✅ [Monitoreo y Logging](./monitoreo-logging.md)
- ✅ [Optimización base de datos](./optimizacion-base-datos.md)
- ✅ **Backup y Recovery** ← Estás aquí
- ⏭️ [SSL y Configuraciones de Seguridad](./ssl-configuraciones-seguridad.md)

---

### Siguiente Paso

Continúa con
[**SSL y Configuraciones de Seguridad**](./ssl-configuraciones-seguridad.md)

[⬅️ Optimización base de datos](./optimizacion-base-datos.md) |
[🏠 README Principal](../../README.md) |
[➡️ SSL y Configuraciones de Seguridad](./ssl-configuraciones-seguridad.md)
