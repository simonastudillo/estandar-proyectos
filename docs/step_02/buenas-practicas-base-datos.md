# Buenas Prácticas de Base de Datos

## Conocimientos involucrados
- Diseño y modelado de bases de datos
- Optimización de consultas SQL
- Seguridad en bases de datos
- Administración de sistemas de gestión de bases de datos (DBMS)

## Responsable
- Equipo de desarrollo

## ¿Qué es?

Las buenas prácticas de base de datos son un conjunto de reglas, convenciones y
metodologías probadas que garantizan el diseño, implementación y mantenimiento
eficiente, seguro y escalable de sistemas de gestión de datos. Estas prácticas
abarcan desde el modelado conceptual hasta la optimización de consultas, pasando
por la seguridad, el backup y la administración del sistema.

En el contexto de Clean Architecture y DDD con Laravel, estas prácticas deben
alinearse con los principios de separación de responsabilidades y independencia
de la infraestructura.

## ¿Por qué es importante?

- **Consistencia**: Establece estándares uniformes en todo el equipo de
  desarrollo
- **Performance**: Garantiza consultas eficientes y tiempos de respuesta óptimos
- **Escalabilidad**: Prepara la base de datos para el crecimiento futuro
- **Mantenibilidad**: Facilita la evolución y modificación del sistema
- **Seguridad**: Protege los datos contra accesos no autorizados y ataques
- **Integridad**: Asegura la consistencia y validez de los datos
- **Recuperación**: Facilita la restauración ante fallos o desastres
- **Colaboración**: Mejora la comunicación entre desarrolladores y DBAs

## ¿Qué debe incluir?

### Diseño y Modelado

#### Principios de Normalización

- **Aplicación de formas normales**: 1FN, 2FN, 3FN, BCNF según necesidad
- **Desnormalización controlada**: Solo cuando la performance lo justifique
- **Separación de concerns**: Diferentes tipos de datos en diferentes tablas
- **Evitar redundancia**: Minimizar duplicación de información

#### Convenciones de Nomenclatura

- **Tablas**: Nombres en inglés, plural, snake_case (`users`, `order_items`)
- **Columnas**: Nombres descriptivos, singular, snake_case (`first_name`,
  `created_at`)
- **Índices**: Prefijo descriptivo (`idx_users_email`, `uk_products_sku`)
- **Constraints**: Nombres específicos (`fk_orders_user_id`,
  `chk_users_email_format`)

### Tipos de Datos y Restricciones

#### Selección de Tipos Apropiados

- **Precisión de datos**: Usar tipos que representen exactamente los datos
- **Eficiencia de almacenamiento**: Optimizar el espacio sin comprometer
  funcionalidad
- **Validación a nivel BD**: Constraints que complementen validaciones de
  aplicación

### Seguridad y Acceso

#### Control de Acceso

- **Principio de menor privilegio**: Solo permisos necesarios por rol
- **Segregación de usuarios**: Diferentes usuarios para diferentes propósitos
- **Auditoría de accesos**: Registro de operaciones sensibles

## ¿Qué debo hacer?

### 1. Establecer Convenciones de Nomenclatura

#### Guía de Estilo para Nombres

```sql
-- ✅ CORRECTO: Nombres de tablas
CREATE TABLE users (id, email, name);
CREATE TABLE user_profiles (id, user_id, bio);
CREATE TABLE order_items (id, order_id, product_id, quantity);

-- ❌ INCORRECTO: Nombres inconsistentes
CREATE TABLE User (ID, Email, Name);
CREATE TABLE userProfile (id, userId, bio);
CREATE TABLE orderItem (id, orderId, productId, qty);

-- ✅ CORRECTO: Nombres de columnas
ALTER TABLE users ADD COLUMN phone_number VARCHAR(20);
ALTER TABLE users ADD COLUMN date_of_birth DATE;
ALTER TABLE users ADD COLUMN is_email_verified BOOLEAN;

-- ❌ INCORRECTO: Nombres ambiguos
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
ALTER TABLE users ADD COLUMN birth DATE;
ALTER TABLE users ADD COLUMN verified BOOLEAN;

-- ✅ CORRECTO: Nombres de índices
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_created ON orders(user_id, created_at);
CREATE UNIQUE INDEX uk_products_sku ON products(sku);

-- ❌ INCORRECTO: Nombres genéricos
CREATE INDEX index1 ON users(email);
CREATE INDEX user_index ON orders(user_id, created_at);
CREATE INDEX products_unique ON products(sku);
```

#### Documento de Convenciones

```markdown
# Database Naming Conventions

## Tables

- **Format**: snake_case, plural
- **Examples**: `users`, `product_categories`, `order_items`
- **Avoid**: Prefixes (tbl_), abbreviations, singular names

## Columns

- **Format**: snake_case, descriptive
- **Examples**: `first_name`, `created_at`, `is_active`
- **Booleans**: Prefix with `is_`, `has_`, `can_`
- **Dates**: Suffix with `_at` for timestamps, `_date` for dates
- **Foreign Keys**: `{table_singular}_id` format

## Indexes

- **Primary Keys**: `pk_{table}`
- **Unique Indexes**: `uk_{table}_{column(s)}`
- **Regular Indexes**: `idx_{table}_{column(s)}`
- **Foreign Keys**: `fk_{table}_{column}`

## Constraints

- **Check Constraints**: `chk_{table}_{column}_{rule}`
- **Foreign Keys**: `fk_{table}_{referenced_table}`
- **Unique Constraints**: `uk_{table}_{column(s)}`
```

### 2. Implementar Validaciones y Constraints

#### Constraints a Nivel de Base de Datos

```sql
-- Crear tabla con constraints completos
CREATE TABLE users (
    id CHAR(36) PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    birth_date DATE,
    salary DECIMAL(10,2),
    status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    -- Constraints de validación
    CONSTRAINT chk_users_email_format 
        CHECK (email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'),
    CONSTRAINT chk_users_name_length 
        CHECK (LENGTH(name) >= 2),
    CONSTRAINT chk_users_birth_date 
        CHECK (birth_date >= '1900-01-01' AND birth_date <= CURDATE()),
    CONSTRAINT chk_users_salary_positive 
        CHECK (salary > 0),
    CONSTRAINT chk_users_phone_format 
        CHECK (phone IS NULL OR phone REGEXP '^[+]?[0-9\s\-\(\)]{7,20}$')
);

-- Índices para optimización
CREATE UNIQUE INDEX uk_users_email ON users(email);
CREATE INDEX idx_users_status_created ON users(status, created_at);
CREATE INDEX idx_users_deleted_at ON users(deleted_at);
CREATE INDEX idx_users_name_search ON users(name(10)); -- Prefix index
```

#### Template de Validaciones Laravel

```php
<?php
// app/Domain/Entities/User.php

namespace App\Domain\Entities;

use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\Phone;

class User
{
    private string $id;
    private Email $email;
    private string $name;
    private ?Phone $phone;
    private ?\DateTimeImmutable $birthDate;
    private bool $isVerified;
    private UserStatus $status;
    private \DateTimeImmutable $createdAt;
    private \DateTimeImmutable $updatedAt;
    private ?\DateTimeImmutable $deletedAt;

    public function __construct(
        string $id,
        Email $email,
        string $name,
        ?Phone $phone = null,
        ?\DateTimeImmutable $birthDate = null
    ) {
        $this->validateName($name);
        $this->validateBirthDate($birthDate);
        
        $this->id = $id;
        $this->email = $email;
        $this->name = $name;
        $this->phone = $phone;
        $this->birthDate = $birthDate;
        $this->isVerified = false;
        $this->status = UserStatus::ACTIVE;
        $this->createdAt = new \DateTimeImmutable();
        $this->updatedAt = new \DateTimeImmutable();
        $this->deletedAt = null;
    }

    private function validateName(string $name): void
    {
        if (strlen(trim($name)) < 2) {
            throw new \InvalidArgumentException('Name must be at least 2 characters long');
        }

        if (strlen($name) > 100) {
            throw new \InvalidArgumentException('Name cannot exceed 100 characters');
        }

        if (!preg_match('/^[a-zA-ZÀ-ÿ\s\-\'\.]+$/u', $name)) {
            throw new \InvalidArgumentException('Name contains invalid characters');
        }
    }

    private function validateBirthDate(?\DateTimeImmutable $birthDate): void
    {
        if ($birthDate === null) {
            return;
        }

        $minDate = new \DateTimeImmutable('1900-01-01');
        $maxDate = new \DateTimeImmutable();

        if ($birthDate < $minDate || $birthDate > $maxDate) {
            throw new \InvalidArgumentException('Birth date must be between 1900-01-01 and today');
        }
    }

    // Getters y métodos de dominio...
}
```

### 3. Estrategias de Indexing

#### Análisis de Consultas Frecuentes

```php
<?php
// tools/query-analysis.php

class QueryAnalysisService
{
    public function analyzeSlowQueries(): array
    {
        $slowQueries = DB::select("
            SELECT 
                sql_text,
                exec_count,
                avg_timer_wait/1000000000 as avg_time_seconds,
                sum_timer_wait/1000000000 as total_time_seconds,
                sum_rows_examined,
                sum_rows_sent,
                (sum_rows_examined / sum_rows_sent) as examine_ratio
            FROM performance_schema.events_statements_summary_by_digest 
            WHERE schema_name = DATABASE()
            AND avg_timer_wait > 1000000000  -- > 1 second
            ORDER BY avg_timer_wait DESC 
            LIMIT 20
        ");

        return $this->generateIndexRecommendations($slowQueries);
    }

    private function generateIndexRecommendations(array $queries): array
    {
        $recommendations = [];

        foreach ($queries as $query) {
            $analysis = $this->parseQuery($query->sql_text);
            
            if ($analysis['type'] === 'SELECT') {
                $recommendations[] = [
                    'query' => substr($query->sql_text, 0, 100) . '...',
                    'avg_time' => $query->avg_time_seconds,
                    'suggested_indexes' => $this->suggestIndexes($analysis),
                    'priority' => $this->calculatePriority($query)
                ];
            }
        }

        return $recommendations;
    }

    private function suggestIndexes(array $analysis): array
    {
        $suggestions = [];

        // Analizar WHERE clauses
        foreach ($analysis['where_columns'] as $column) {
            $suggestions[] = "CREATE INDEX idx_{$analysis['table']}_{$column} ON {$analysis['table']}({$column})";
        }

        // Analizar ORDER BY
        if (!empty($analysis['order_by'])) {
            $orderColumns = implode(', ', $analysis['order_by']);
            $suggestions[] = "CREATE INDEX idx_{$analysis['table']}_order ON {$analysis['table']}({$orderColumns})";
        }

        // Analizar JOIN conditions
        foreach ($analysis['join_conditions'] as $condition) {
            $suggestions[] = "CREATE INDEX idx_{$condition['table']}_{$condition['column']} ON {$condition['table']}({$condition['column']})";
        }

        return array_unique($suggestions);
    }

    private function calculatePriority(object $query): string
    {
        $timeWeight = $query->avg_time_seconds * $query->exec_count;
        
        if ($timeWeight > 100) return 'HIGH';
        if ($timeWeight > 10) return 'MEDIUM';
        return 'LOW';
    }
}
```

#### Estrategias de Índices Compuestos

```sql
-- Índices para consultas comunes
-- Consulta: WHERE user_id = ? AND created_at > ? ORDER BY created_at DESC
CREATE INDEX idx_orders_user_created_desc ON orders(user_id, created_at DESC);

-- Consulta: WHERE status = ? AND priority = ? AND assigned_to = ?
CREATE INDEX idx_tickets_status_priority_assigned ON tickets(status, priority, assigned_to);

-- Consulta: WHERE category_id = ? AND is_active = 1 AND price BETWEEN ? AND ?
CREATE INDEX idx_products_category_active_price ON products(category_id, is_active, price);

-- Índice covering (incluye todas las columnas necesarias)
-- Consulta: SELECT id, name, email FROM users WHERE status = 'active'
CREATE INDEX idx_users_status_covering ON users(status, id, name, email);

-- Índices parciales (solo registros que cumplen condición)
-- Solo usuarios activos
CREATE INDEX idx_users_active_email ON users(email) WHERE is_active = 1;

-- Solo registros no eliminados
CREATE INDEX idx_orders_not_deleted ON orders(user_id, created_at) WHERE deleted_at IS NULL;
```

### 4. Gestión de Datos Sensibles

#### Encriptación y Hashing

```php
<?php
// app/Domain/ValueObjects/EncryptedValue.php

namespace App\Domain\ValueObjects;

class EncryptedValue
{
    private string $encryptedValue;
    private string $algorithm;

    public function __construct(string $plainValue, string $algorithm = 'AES-256-CBC')
    {
        $this->algorithm = $algorithm;
        $this->encryptedValue = $this->encrypt($plainValue);
    }

    public static function fromEncrypted(string $encryptedValue, string $algorithm = 'AES-256-CBC'): self
    {
        $instance = new self.__construct();
        $instance->encryptedValue = $encryptedValue;
        $instance->algorithm = $algorithm;
        return $instance;
    }

    public function decrypt(): string
    {
        return decrypt($this->encryptedValue);
    }

    public function getEncrypted(): string
    {
        return $this->encryptedValue;
    }

    private function encrypt(string $value): string
    {
        return encrypt($value);
    }
}

// app/Domain/ValueObjects/HashedValue.php
class HashedValue
{
    private string $hash;
    private string $algorithm;
    private array $options;

    public function __construct(string $plainValue, string $algorithm = 'argon2id', array $options = [])
    {
        $this->algorithm = $algorithm;
        $this->options = $options;
        $this->hash = $this->hashValue($plainValue);
    }

    public function verify(string $plainValue): bool
    {
        return password_verify($plainValue, $this->hash);
    }

    public function getHash(): string
    {
        return $this->hash;
    }

    private function hashValue(string $value): string
    {
        return password_hash($value, PASSWORD_ARGON2ID, [
            'memory_cost' => 65536, // 64 MB
            'time_cost' => 4,       // 4 iterations
            'threads' => 3,         // 3 threads
        ]);
    }
}
```

#### Políticas de Retención de Datos

```sql
-- Crear tabla para auditoría con política de retención
CREATE TABLE user_audit_logs (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id CHAR(36) NOT NULL,
    action VARCHAR(50) NOT NULL,
    old_data JSON,
    new_data JSON,
    ip_address VARCHAR(45),
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_audit_user_created (user_id, created_at),
    INDEX idx_audit_action_created (action, created_at),
    
    -- Particionado por mes para facilitar eliminación
    PARTITION BY RANGE (YEAR(created_at) * 100 + MONTH(created_at)) (
        PARTITION p202401 VALUES LESS THAN (202402),
        PARTITION p202402 VALUES LESS THAN (202403),
        PARTITION p202403 VALUES LESS THAN (202404),
        -- Continuar según necesidad
        PARTITION p_max VALUES LESS THAN MAXVALUE
    )
);

-- Procedimiento para limpieza automática
DELIMITER //
CREATE PROCEDURE CleanOldAuditLogs()
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE partition_name VARCHAR(64);
    DECLARE partition_description VARCHAR(255);
    
    -- Cursor para particiones a eliminar (más de 2 años)
    DECLARE partition_cursor CURSOR FOR
        SELECT 
            PARTITION_NAME,
            PARTITION_DESCRIPTION
        FROM INFORMATION_SCHEMA.PARTITIONS
        WHERE TABLE_SCHEMA = DATABASE()
        AND TABLE_NAME = 'user_audit_logs'
        AND PARTITION_NAME IS NOT NULL
        AND PARTITION_DESCRIPTION < (YEAR(DATE_SUB(NOW(), INTERVAL 2 YEAR)) * 100 + MONTH(DATE_SUB(NOW(), INTERVAL 2 YEAR)));

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

    OPEN partition_cursor;

    read_loop: LOOP
        FETCH partition_cursor INTO partition_name, partition_description;
        
        IF done THEN
            LEAVE read_loop;
        END IF;

        SET @sql = CONCAT('ALTER TABLE user_audit_logs DROP PARTITION ', partition_name);
        PREPARE stmt FROM @sql;
        EXECUTE stmt;
        DEALLOCATE PREPARE stmt;
        
        SELECT CONCAT('Dropped partition: ', partition_name) as message;
    END LOOP;

    CLOSE partition_cursor;
END //
DELIMITER ;

-- Evento para ejecutar limpieza mensualmente
CREATE EVENT IF NOT EXISTS CleanAuditLogsEvent
ON SCHEDULE EVERY 1 MONTH
STARTS '2024-01-01 02:00:00'
DO
    CALL CleanOldAuditLogs();
```

### 5. Backup y Recuperación

#### Estrategia de Backup Automatizada

```bash
#!/bin/bash
# scripts/database-backup.sh

set -euo pipefail

# Configuración
BACKUP_DIR="/var/backups/mysql"
DB_NAME="${DB_DATABASE}"
DB_HOST="${DB_HOST:-localhost}"
DB_USER="${DB_USERNAME}"
DB_PASS="${DB_PASSWORD}"
RETENTION_DAYS=30
S3_BUCKET="${BACKUP_S3_BUCKET:-}"

# Crear directorio si no existe
mkdir -p "$BACKUP_DIR"

# Función de logging
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$BACKUP_DIR/backup.log"
}

# Función de backup completo
full_backup() {
    local timestamp=$(date +"%Y%m%d_%H%M%S")
    local backup_file="$BACKUP_DIR/full_backup_${DB_NAME}_${timestamp}.sql.gz"
    
    log "Starting full backup: $backup_file"
    
    mysqldump \
        --host="$DB_HOST" \
        --user="$DB_USER" \
        --password="$DB_PASS" \
        --single-transaction \
        --routines \
        --triggers \
        --events \
        --add-drop-database \
        --databases "$DB_NAME" | gzip > "$backup_file"
    
    if [ $? -eq 0 ]; then
        log "Full backup completed successfully: $backup_file"
        
        # Verificar integridad del archivo
        if gzip -t "$backup_file"; then
            log "Backup file integrity verified"
        else
            log "ERROR: Backup file is corrupted"
            return 1
        fi
        
        # Subir a S3 si está configurado
        if [ -n "$S3_BUCKET" ]; then
            upload_to_s3 "$backup_file"
        fi
        
    else
        log "ERROR: Full backup failed"
        return 1
    fi
}

# Función de backup incremental
incremental_backup() {
    local timestamp=$(date +"%Y%m%d_%H%M%S")
    local backup_file="$BACKUP_DIR/incremental_backup_${DB_NAME}_${timestamp}.sql.gz"
    local last_backup_time=$(find "$BACKUP_DIR" -name "full_backup_${DB_NAME}_*.sql.gz" -printf '%T@ %p\n' | sort -n | tail -1 | cut -d' ' -f2 | xargs stat -c %Y)
    
    log "Starting incremental backup since: $(date -d @$last_backup_time)"
    
    # Backup solo tablas modificadas desde el último backup completo
    mysqldump \
        --host="$DB_HOST" \
        --user="$DB_USER" \
        --password="$DB_PASS" \
        --single-transaction \
        --where="updated_at >= FROM_UNIXTIME($last_backup_time)" \
        "$DB_NAME" | gzip > "$backup_file"
    
    log "Incremental backup completed: $backup_file"
}

# Función para subir a object storage (DigitalOcean Spaces por defecto)
upload_to_object_storage() {
    local file="$1"
    local s3_key="database-backups/$(basename "$file")"

    OBJECT_STORAGE_CLI="${OBJECT_STORAGE_CLI:-aws}"
    OBJECT_STORAGE_BUCKET="${OBJECT_STORAGE_BUCKET:-${S3_BUCKET}}"
    OBJECT_STORAGE_ENDPOINT="${OBJECT_STORAGE_ENDPOINT:-}"
    OBJECT_STORAGE_PROFILE_ARG=""
    if [ -n "${OBJECT_STORAGE_PROFILE:-}" ]; then
        OBJECT_STORAGE_PROFILE_ARG="--profile ${OBJECT_STORAGE_PROFILE}"
    fi

    log "Uploading to object storage: s3://${OBJECT_STORAGE_BUCKET}/${s3_key}"

    if [ -n "${OBJECT_STORAGE_ENDPOINT}" ]; then
        ${OBJECT_STORAGE_CLI} s3 cp "$file" "s3://${OBJECT_STORAGE_BUCKET}/${s3_key}" \
            --endpoint-url "${OBJECT_STORAGE_ENDPOINT}" ${OBJECT_STORAGE_PROFILE_ARG} \
            --storage-class GLACIER \
            --metadata "database=$DB_NAME,backup-type=full,created-at=$(date -Iseconds)"
    else
        ${OBJECT_STORAGE_CLI} s3 cp "$file" "s3://${OBJECT_STORAGE_BUCKET}/${s3_key}" \
            ${OBJECT_STORAGE_PROFILE_ARG} --storage-class GLACIER \
            --metadata "database=$DB_NAME,backup-type=full,created-at=$(date -Iseconds)"
    fi

    if [ $? -eq 0 ]; then
        log "Object storage upload completed successfully"
    else
        log "ERROR: Object storage upload failed"
        return 1
    fi
}

# Función de limpieza
cleanup_old_backups() {
    log "Cleaning up backups older than $RETENTION_DAYS days"
    
    find "$BACKUP_DIR" -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete
    
    log "Cleanup completed"
}

# Función principal
main() {
    case "${1:-full}" in
        "full")
            full_backup
            ;;
        "incremental")
            incremental_backup
            ;;
        "cleanup")
            cleanup_old_backups
            ;;
        *)
            echo "Usage: $0 {full|incremental|cleanup}"
            exit 1
            ;;
    esac
}

# Ejecutar función principal
main "$@"
```

#### Plan de Recuperación

```bash
#!/bin/bash
# scripts/database-restore.sh

set -euo pipefail

# Función de restauración
restore_database() {
    local backup_file="$1"
    local target_db="${2:-$DB_DATABASE}"
    
    echo "WARNING: This will completely replace database: $target_db"
    echo "Backup file: $backup_file"
    read -p "Are you sure? (yes/no): " confirm
    
    if [ "$confirm" != "yes" ]; then
        echo "Restore cancelled"
        exit 1
    fi
    
    # Verificar que el archivo existe y es válido
    if [ ! -f "$backup_file" ]; then
        echo "ERROR: Backup file not found: $backup_file"
        exit 1
    fi
    
    if ! gzip -t "$backup_file" 2>/dev/null; then
        echo "ERROR: Backup file is not a valid gzip file"
        exit 1
    fi
    
    echo "Creating database if not exists..."
    mysql -h"$DB_HOST" -u"$DB_USER" -p"$DB_PASS" -e "CREATE DATABASE IF NOT EXISTS $target_db"
    
    echo "Restoring database from: $backup_file"
    gunzip -c "$backup_file" | mysql -h"$DB_HOST" -u"$DB_USER" -p"$DB_PASS" "$target_db"
    
    echo "Database restored successfully"
    echo "Running post-restore verification..."
    
    # Verificar integridad
    mysql -h"$DB_HOST" -u"$DB_USER" -p"$DB_PASS" "$target_db" -e "CHECK TABLE users, orders, products"
}

# Función para restaurar desde S3
restore_from_object_storage() {
    local s3_path="$1"
    local temp_file="/tmp/restore_$(date +%s).sql.gz"

    OBJECT_STORAGE_CLI="${OBJECT_STORAGE_CLI:-aws}"
    OBJECT_STORAGE_ENDPOINT="${OBJECT_STORAGE_ENDPOINT:-}"
    OBJECT_STORAGE_PROFILE_ARG=""
    if [ -n "${OBJECT_STORAGE_PROFILE:-}" ]; then
        OBJECT_STORAGE_PROFILE_ARG="--profile ${OBJECT_STORAGE_PROFILE}"
    fi

    echo "Downloading from object storage: $s3_path"
    if [ -n "${OBJECT_STORAGE_ENDPOINT}" ]; then
        ${OBJECT_STORAGE_CLI} s3 cp "$s3_path" "$temp_file" --endpoint-url "${OBJECT_STORAGE_ENDPOINT}" ${OBJECT_STORAGE_PROFILE_ARG}
    else
        ${OBJECT_STORAGE_CLI} s3 cp "$s3_path" "$temp_file" ${OBJECT_STORAGE_PROFILE_ARG}
    fi

    restore_database "$temp_file"

    rm -f "$temp_file"
}

# Mostrar backups disponibles
list_backups() {
    echo "Available local backups:"
    find "$BACKUP_DIR" -name "*.sql.gz" -printf '%TY-%Tm-%Td %TH:%TM %s bytes %p\n' | sort -r
    
    if [ -n "${BACKUP_S3_BUCKET:-}" ] || [ -n "${OBJECT_STORAGE_BUCKET:-}" ]; then
        echo -e "\nAvailable object storage backups:"
        OBJECT_STORAGE_CLI="${OBJECT_STORAGE_CLI:-aws}"
        OBJECT_STORAGE_ENDPOINT="${OBJECT_STORAGE_ENDPOINT:-}"
        BUCKET_TO_USE="${OBJECT_STORAGE_BUCKET:-${BACKUP_S3_BUCKET}}"
        if [ -n "${OBJECT_STORAGE_ENDPOINT}" ]; then
            ${OBJECT_STORAGE_CLI} s3 ls "s3://${BUCKET_TO_USE}/database-backups/" --human-readable --endpoint-url "${OBJECT_STORAGE_ENDPOINT}"
        else
            ${OBJECT_STORAGE_CLI} s3 ls "s3://${BUCKET_TO_USE}/database-backups/" --human-readable
        fi
    fi
}

# Función principal
case "${1:-}" in
    "restore")
        restore_database "$2" "${3:-}"
        ;;
    "restore-s3")
        restore_from_s3 "$2"
        ;;
    "list")
        list_backups
        ;;
    *)
        echo "Usage: $0 {restore|restore-s3|list} [backup-file] [target-database]"
        echo ""
        echo "Examples:"
        echo "  $0 list"
        echo "  $0 restore /path/to/backup.sql.gz"
        echo "  $0 restore /path/to/backup.sql.gz target_database"
        echo "  $0 restore-s3 s3://bucket/path/backup.sql.gz"
        exit 1
        ;;
esac
```

### Base de Datos: MySQL como Estándar Único

#### Selección de Base de Datos

- **Única Base de Datos Permitida**: MySQL 8.0+.
- **Motivo**: Consistencia en el stack, soporte robusto y optimización para la
  mayoría de casos de uso.
- **Prohibido**: Uso de PostgreSQL u otras bases de datos en proyectos estándar.

#### Configuraciones Específicas

- **Optimización**: Configurar índices y consultas para alta concurrencia.
- **Seguridad**: Implementar cifrado en tránsito (TLS) y en reposo.
- **Auditoría**: Activar logs de consultas y cambios críticos.

#### Herramientas de Gestión

- **Migraciones**: Laravel Migrations.
- **Seeders**: Laravel Database Seeders.

## Tips

### Principios Fundamentales

- **ACID compliance**: Garantizar Atomicidad, Consistencia, Aislamiento y
  Durabilidad
- **Normalización inteligente**: Aplicar formas normales pero considerar
  performance
- **Índices estratégicos**: Crear índices basados en patrones de consulta reales
- **Validación en capas**: Validar en aplicación Y en base de datos
- **Documentación viva**: Mantener documentación actualizada del esquema
- **Monitoreo proactivo**: Supervisar performance y detectar problemas temprano
- **Testing de schemas**: Probar migraciones y cambios de estructura

### Herramientas Recomendadas

- **MySQL Workbench**: Diseño visual y administración
- **Percona Toolkit**: Análisis y optimización de MySQL
- **pt-query-digest**: Análisis de slow query log
- **MySQLTuner**: Recomendaciones de configuración
- **Adminer/phpMyAdmin**: Administración web
- **Laravel Telescope**: Debugging de queries en desarrollo

### Errores Comunes a Evitar

- **Sobre-indexing**: Crear demasiados índices innecesarios
- **Falta de constraints**: No validar datos a nivel de BD
- **Tipo de datos incorrectos**: Usar VARCHAR para fechas o números
- **No usar transacciones**: Operaciones complejas sin control transaccional
- **Backup sin testing**: No probar regularmente la restauración
- **Ignorar logs**: No monitorear slow query log y error log
- **Hardcodear valores**: Usar magic numbers en lugar de constantes

## Ejemplos

### Schema de E-commerce Optimizado

```sql
-- Tabla de productos con todas las mejores prácticas
CREATE TABLE products (
    id CHAR(36) PRIMARY KEY COMMENT 'UUID identifier',
    sku VARCHAR(50) NOT NULL COMMENT 'Stock Keeping Unit',
    name VARCHAR(255) NOT NULL COMMENT 'Product name',
    description TEXT COMMENT 'Product description',
    short_description VARCHAR(500) COMMENT 'Brief product description',
    price DECIMAL(10,2) NOT NULL COMMENT 'Current price',
    cost_price DECIMAL(10,2) COMMENT 'Cost price for margin calculation',
    weight DECIMAL(8,3) COMMENT 'Weight in kg',
    dimensions JSON COMMENT 'Length, width, height in cm',
    stock_quantity INT UNSIGNED DEFAULT 0 COMMENT 'Available stock',
    min_stock_level INT UNSIGNED DEFAULT 0 COMMENT 'Minimum stock alert level',
    category_id CHAR(36) NOT NULL COMMENT 'Product category',
    brand_id CHAR(36) COMMENT 'Product brand',
    status ENUM('draft', 'active', 'inactive', 'discontinued') DEFAULT 'draft',
    is_featured BOOLEAN DEFAULT FALSE COMMENT 'Featured product flag',
    meta_title VARCHAR(255) COMMENT 'SEO meta title',
    meta_description VARCHAR(500) COMMENT 'SEO meta description',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL COMMENT 'Soft delete timestamp',
    
    -- Constraints
    CONSTRAINT chk_products_price_positive CHECK (price > 0),
    CONSTRAINT chk_products_cost_price_positive CHECK (cost_price IS NULL OR cost_price >= 0),
    CONSTRAINT chk_products_weight_positive CHECK (weight IS NULL OR weight > 0),
    CONSTRAINT chk_products_stock_positive CHECK (stock_quantity >= 0),
    CONSTRAINT chk_products_min_stock_positive CHECK (min_stock_level >= 0),
    
    -- Foreign Keys
    CONSTRAINT fk_products_category 
        FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE RESTRICT,
    CONSTRAINT fk_products_brand 
        FOREIGN KEY (brand_id) REFERENCES brands(id) ON DELETE SET NULL,
    
    -- Indexes
    UNIQUE INDEX uk_products_sku (sku),
    INDEX idx_products_category_status (category_id, status),
    INDEX idx_products_brand_status (brand_id, status),
    INDEX idx_products_status_featured (status, is_featured),
    INDEX idx_products_price_range (price),
    INDEX idx_products_stock_level (stock_quantity, min_stock_level),
    INDEX idx_products_created_at (created_at),
    INDEX idx_products_updated_at (updated_at),
    INDEX idx_products_deleted_at (deleted_at),
    
    -- Full-text search
    FULLTEXT INDEX ft_products_search (name, description, short_description)
) ENGINE=InnoDB 
  DEFAULT CHARSET=utf8mb4 
  COLLATE=utf8mb4_unicode_ci 
  COMMENT='Product catalog table';
```

### Configuración de Usuario y Permisos

```sql
-- Crear usuarios específicos por propósito
-- Usuario para la aplicación (solo datos)
CREATE USER 'app_user'@'%' IDENTIFIED BY 'secure_app_password';
GRANT SELECT, INSERT, UPDATE, DELETE ON ecommerce.* TO 'app_user'@'%';

-- Usuario para migraciones (estructura)
CREATE USER 'migration_user'@'%' IDENTIFIED BY 'secure_migration_password';
GRANT ALL PRIVILEGES ON ecommerce.* TO 'migration_user'@'%';

-- Usuario de solo lectura para reportes
CREATE USER 'readonly_user'@'%' IDENTIFIED BY 'secure_readonly_password';
GRANT SELECT ON ecommerce.* TO 'readonly_user'@'%';

-- Usuario para backups
CREATE USER 'backup_user'@'localhost' IDENTIFIED BY 'secure_backup_password';
GRANT SELECT, LOCK TABLES, SHOW VIEW, EVENT, TRIGGER ON ecommerce.* TO 'backup_user'@'localhost';

-- Aplicar cambios
FLUSH PRIVILEGES;
```

## Navegación

[⬅️ Modelado de datos](./modelado-datos.md) |
[🏠 README Principal](../../README.md) |
[Diseño de Base de Datos ➡️](./diseno-base-datos.md)
