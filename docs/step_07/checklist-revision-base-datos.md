# Checklist de Revisión de Base de Datos

## ¿Qué es?

El checklist de revisión de base de datos es una herramienta sistemática de
Quality Assurance que garantiza que el diseño, implementación y configuración de
la base de datos cumple con los estándares de calidad, performance, seguridad y
mantenibilidad establecidos. Es una lista verificable de criterios que debe
aplicarse antes de cualquier despliegue o cambio significativo en la estructura
de datos.

En el contexto de Clean Architecture y DDD, este checklist debe validar que la
persistencia no comprometa las reglas de dominio y mantenga la independencia de
la infraestructura.

## ¿Por qué es importante?

- **Calidad consistente**: Garantiza que todos los cambios de BD cumplan
  estándares mínimos
- **Prevención de errores**: Detecta problemas antes del despliegue a producción
- **Performance óptima**: Verifica optimizaciones y configuraciones de
  rendimiento
- **Seguridad robusta**: Valida medidas de protección de datos
- **Mantenibilidad**: Asegura que la BD sea fácil de mantener y evolucionar
- **Documentación**: Fuerza la documentación adecuada de cambios
- **Compliance**: Verifica cumplimiento de regulaciones y políticas
- **Reducción de riesgos**: Minimiza posibilidad de fallos en producción

## ¿Qué debe incluir?

### Revisión de Diseño

#### Estructura y Modelado

- **Normalización apropiada**: Verificación de formas normales aplicadas
- **Relaciones correctas**: Validación de cardinalidades y foreign keys
- **Nomenclatura consistente**: Adherencia a convenciones establecidas
- **Tipos de datos apropiados**: Selección óptima para cada campo

#### Documentación

- **Esquemas actualizados**: Diagramas ER reflejan cambios actuales
- **Diccionario de datos**: Documentación completa de tablas y campos
- **Justificación de cambios**: Razones de negocio documentadas
- **Impacto evaluado**: Análisis de efectos en sistemas existentes

### Revisión de Performance

#### Índices y Optimización

- **Estrategia de indexing**: Índices apropiados para consultas frecuentes
- **Performance de queries**: Análisis de planes de ejecución
- **Estadísticas actualizadas**: Información de cardinalidad correcta
- **Queries problemáticas**: Identificación y corrección de consultas lentas

### Revisión de Seguridad

#### Acceso y Permisos

- **Principio de menor privilegio**: Usuarios con permisos mínimos necesarios
- **Datos sensibles protegidos**: Encriptación y hashing apropiados
- **Auditoría habilitada**: Logging de operaciones críticas
- **Backup y recovery**: Estrategias de respaldo validadas

## ¿Qué debo hacer?

### 1. Checklist Pre-Despliegue

#### Lista de Verificación Completa

```markdown
# Database Pre-Deployment Checklist

## 📋 Información General

- [ ] **Número de ticket/issue**: **\*\***\_\_\_**\*\***
- [ ] **Desarrollador responsable**: **\*\***\_\_\_**\*\***
- [ ] **Revisor asignado**: **\*\***\_\_\_**\*\***
- [ ] **Fecha de revisión**: **\*\***\_\_\_**\*\***
- [ ] **Entorno objetivo**: [ ] Dev [ ] Staging [ ] Production

## 🏗️ Diseño y Estructura

### Modelado de Datos

- [ ] **Normalización**: Aplicadas formas normales apropiadas (1FN, 2FN, 3FN)
- [ ] **Desnormalización justificada**: Razones documentadas para casos
      específicos
- [ ] **Relaciones correctas**: Foreign keys y cardinalidades verificadas
- [ ] **Integridad referencial**: Constraints de FK implementadas
- [ ] **Reglas de negocio**: Check constraints reflejan reglas de dominio

### Nomenclatura y Convenciones

- [ ] **Nombres de tablas**: Plural, snake_case, en inglés
- [ ] **Nombres de columnas**: Descriptivos, singular, snake_case
- [ ] **Nombres de índices**: Convención `idx_table_column(s)`
- [ ] **Nombres de constraints**: Convención `fk_/uk_/chk_table_description`
- [ ] **Consistencia general**: Nomenclatura uniforme en todo el esquema

### Tipos de Datos

- [ ] **Precisión apropiada**: VARCHAR con longitudes realistas
- [ ] **Tipos numéricos**: DECIMAL para moneda, INT para contadores
- [ ] **Fechas y timestamps**: TIMESTAMP vs DATETIME apropiados
- [ ] **Campos booleanos**: BOOLEAN o TINYINT(1) consistente
- [ ] **Campos JSON**: Validación de estructura cuando aplique
- [ ] **Campos de texto**: TEXT vs VARCHAR según necesidad

## 🚀 Performance y Optimización

### Índices

- [ ] **Primary keys**: Todas las tablas tienen PK definida
- [ ] **Foreign keys indexadas**: Todas las FK tienen índices apropiados
- [ ] **Consultas frecuentes**: Índices para queries comunes identificados
- [ ] **Índices compuestos**: Orden de columnas optimizado
- [ ] **Índices únicos**: UK implementadas donde corresponde
- [ ] **Índices innecesarios**: No hay índices redundantes o no utilizados

### Queries y Performance

- [ ] **EXPLAIN analizado**: Planes de ejecución revisados
- [ ] **Table scans evitados**: No hay full table scans en queries críticas
- [ ] **JOIN optimization**: Estrategias de JOIN apropiadas
- [ ] **WHERE clauses**: Condiciones sargables implementadas
- [ ] **LIMIT/PAGINATION**: Paginación implementada para resultados grandes
- [ ] **Query complexity**: Consultas complejas divididas apropiadamente

### Configuración de Tablas

- [ ] **Engine apropiado**: InnoDB para transacciones, MyISAM para solo lectura
- [ ] **Charset correcto**: utf8mb4 para soporte Unicode completo
- [ ] **Collation apropiada**: utf8mb4_unicode_ci para comparaciones correctas
- [ ] **Row format**: DYNAMIC u otros según necesidad
- [ ] **Tablespace**: Configuración apropiada para tablas grandes

## 🔒 Seguridad y Acceso

### Control de Acceso

- [ ] **Usuarios específicos**: No uso de root para aplicaciones
- [ ] **Permisos mínimos**: Principio de menor privilegio aplicado
- [ ] **Passwords seguros**: Contraseñas robustas para usuarios BD
- [ ] **Conexiones seguras**: SSL/TLS configurado apropiadamente
- [ ] **Host restrictions**: Acceso limitado por IP/hostname

### Protección de Datos

- [ ] **Datos sensibles**: PII y datos críticos identificados
- [ ] **Encriptación**: Campos sensibles encriptados apropiadamente
- [ ] **Hashing**: Passwords y datos críticos hasheados
- [ ] **Audit trail**: Logging de operaciones críticas habilitado
- [ ] **Data masking**: Datos enmascarados en entornos no-prod

### Compliance

- [ ] **GDPR compliance**: Derecho al olvido implementado (soft deletes)
- [ ] **Retención de datos**: Políticas de retención definidas
- [ ] **Backup encryption**: Backups encriptados apropiadamente
- [ ] **Access logging**: Registro de accesos a datos sensibles

## 📚 Documentación y Procedimientos

### Documentación Técnica

- [ ] **Diagrama ER actualizado**: Esquema visual refleja cambios
- [ ] **Diccionario de datos**: Tabla y campos documentados
- [ ] **Scripts de migración**: Migraciones up/down implementadas
- [ ] **Scripts de rollback**: Procedimientos de reversión documentados
- [ ] **Datos de prueba**: Seeders actualizados apropiadamente

### Documentación de Negocio

- [ ] **Justificación de cambios**: Razones de negocio documentadas
- [ ] **Impacto en aplicación**: Efectos en código de aplicación evaluados
- [ ] **Breaking changes**: Cambios incompatibles identificados y comunicados
- [ ] **Timeline de migración**: Plan de implementación definido
- [ ] **Comunicación a equipo**: Stakeholders notificados de cambios

## 🔄 Testing y Validación

### Testing de Estructura

- [ ] **Schema tests**: Tests de estructura implementados
- [ ] **Migration tests**: Migraciones up/down probadas
- [ ] **Constraint tests**: Validaciones de constraints verificadas
- [ ] **Data integrity**: Integridad de datos existentes verificada
- [ ] **Performance tests**: Benchmarks antes/después comparados

### Testing de Aplicación

- [ ] **Unit tests**: Tests unitarios pasan con nuevos cambios
- [ ] **Integration tests**: Tests de integración validados
- [ ] **API tests**: Endpoints afectados probados
- [ ] **UI tests**: Interfaces afectadas validadas
- [ ] **Regression tests**: No hay regresiones introducidas

## 💾 Backup y Recovery

### Estrategia de Backup

- [ ] **Backup antes de cambios**: Respaldo completo pre-migración
- [ ] **Backup strategy**: Estrategia de backup evaluada/actualizada
- [ ] **Recovery testing**: Procedimientos de recuperación probados
- [ ] **Point-in-time recovery**: PITR configurado apropiadamente
- [ ] **Backup storage**: Almacenamiento seguro y accesible

### Plan de Contingencia

- [ ] **Rollback plan**: Plan de reversión documentado y probado
- [ ] **Emergency contacts**: Contactos de emergencia identificados
- [ ] **Downtime window**: Ventana de mantenimiento acordada
- [ ] **Communication plan**: Plan de comunicación de incidencias
- [ ] **Monitoring alerts**: Alertas de monitoreo configuradas

## ✅ Aprobaciones

### Revisiones Técnicas

- [ ] **Database Administrator**: \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** Fecha: **\_\_\_**
- [ ] **Senior Developer**: \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** Fecha: **\_\_\_**
- [ ] **Architecture Review**: \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** Fecha: **\_\_\_**
- [ ] **Security Review**: \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** Fecha: **\_\_\_**

### Aprobaciones de Negocio

- [ ] **Product Owner**: \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** Fecha: **\_\_\_**
- [ ] **Technical Lead**: \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** Fecha: **\_\_\_**
- [ ] **DevOps/SRE**: \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** Fecha: **\_\_\_**

## 📝 Comentarios y Observaciones

**Observaciones del revisor:**

---

---

---

**Acciones pendientes:**

---

---

---

**Riesgos identificados:**

---

---

---

## 🎯 Aprobación Final

- [ ] **Todos los criterios cumplidos**
- [ ] **Documentación completa**
- [ ] **Tests validados**
- [ ] **Aprobaciones obtenidas**
- [ ] **Ready for deployment**

**Firma del revisor:** \***\*\*\*\*\***\_\_\_\***\*\*\*\*\*** **Fecha:** **\_\_\_**
```

### 2. Scripts de Validación Automatizada

#### Validador de Schema

```php
<?php
// tools/database-schema-validator.php

namespace Tools\Database;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;

class SchemaValidator
{
    private array $violations = [];
    private array $warnings = [];

    public function validateSchema(): array
    {
        $this->validateNamingConventions();
        $this->validateIndexStrategy();
        $this->validateConstraints();
        $this->validateDataTypes();
        $this->validateSecuritySettings();

        return [
            'violations' => $this->violations,
            'warnings' => $this->warnings,
            'status' => empty($this->violations) ? 'PASS' : 'FAIL'
        ];
    }

    private function validateNamingConventions(): void
    {
        $tables = $this->getAllTables();

        foreach ($tables as $table) {
            // Validar nombre de tabla
            if (!$this->isValidTableName($table)) {
                $this->violations[] = "Table '{$table}' doesn't follow naming convention (should be plural, snake_case)";
            }

            // Validar nombres de columnas
            $columns = Schema::getColumnListing($table);
            foreach ($columns as $column) {
                if (!$this->isValidColumnName($column)) {
                    $this->violations[] = "Column '{$table}.{$column}' doesn't follow naming convention (should be snake_case)";
                }
            }

            // Validar nombres de índices
            $indexes = $this->getTableIndexes($table);
            foreach ($indexes as $index) {
                if (!$this->isValidIndexName($index['name'], $table)) {
                    $this->warnings[] = "Index '{$index['name']}' on table '{$table}' doesn't follow naming convention";
                }
            }
        }
    }

    private function validateIndexStrategy(): void
    {
        $tables = $this->getAllTables();

        foreach ($tables as $table) {
            // Verificar que todas las FK tienen índices
            $foreignKeys = $this->getForeignKeys($table);
            foreach ($foreignKeys as $fk) {
                if (!$this->hasIndexOnColumn($table, $fk['column'])) {
                    $this->violations[] = "Foreign key '{$table}.{$fk['column']}' should have an index";
                }
            }

            // Verificar índices redundantes
            $redundantIndexes = $this->findRedundantIndexes($table);
            foreach ($redundantIndexes as $redundant) {
                $this->warnings[] = "Potentially redundant index '{$redundant}' on table '{$table}'";
            }

            // Verificar índices no utilizados
            $unusedIndexes = $this->findUnusedIndexes($table);
            foreach ($unusedIndexes as $unused) {
                $this->warnings[] = "Potentially unused index '{$unused}' on table '{$table}'";
            }
        }
    }

    private function validateConstraints(): void
    {
        $tables = $this->getAllTables();

        foreach ($tables as $table) {
            // Verificar que tablas principales tienen PK
            if (!$this->hasPrimaryKey($table)) {
                $this->violations[] = "Table '{$table}' must have a primary key";
            }

            // Verificar constraints de validación
            $this->validateBusinessConstraints($table);

            // Verificar integridad referencial
            $this->validateReferentialIntegrity($table);
        }
    }

    private function validateDataTypes(): void
    {
        $tables = $this->getAllTables();

        foreach ($tables as $table) {
            $columns = $this->getColumnDetails($table);

            foreach ($columns as $column) {
                // Validar tipos de datos apropiados
                $this->validateColumnDataType($table, $column);

                // Verificar campos NOT NULL apropiados
                $this->validateNullability($table, $column);

                // Verificar valores por defecto
                $this->validateDefaultValues($table, $column);
            }
        }
    }

    private function validateSecuritySettings(): void
    {
        // Verificar usuarios y permisos
        $this->validateDatabaseUsers();

        // Verificar configuración SSL
        $this->validateSSLConfiguration();

        // Verificar logging de auditoría
        $this->validateAuditLogging();

        // Verificar configuración de backup
        $this->validateBackupConfiguration();
    }

    private function isValidTableName(string $table): bool
    {
        // Debe ser plural, snake_case, inglés
        return preg_match('/^[a-z][a-z0-9_]*s$/', $table) === 1;
    }

    private function isValidColumnName(string $column): bool
    {
        // Debe ser snake_case
        return preg_match('/^[a-z][a-z0-9_]*$/', $column) === 1;
    }

    private function isValidIndexName(string $indexName, string $table): bool
    {
        // Debe seguir convención idx_table_column(s)
        $patterns = [
            '/^pk_' . $table . '$/',          // Primary key
            '/^uk_' . $table . '_.*$/',       // Unique key
            '/^idx_' . $table . '_.*$/',      // Regular index
            '/^fk_' . $table . '_.*$/',       // Foreign key
        ];

        foreach ($patterns as $pattern) {
            if (preg_match($pattern, $indexName)) {
                return true;
            }
        }

        return false;
    }

    private function getAllTables(): array
    {
        return DB::connection()
            ->getDoctrineSchemaManager()
            ->listTableNames();
    }

    private function getTableIndexes(string $table): array
    {
        $indexes = DB::select("SHOW INDEX FROM {$table}");

        return array_map(function($index) {
            return [
                'name' => $index->Key_name,
                'column' => $index->Column_name,
                'unique' => !$index->Non_unique
            ];
        }, $indexes);
    }

    private function validateBusinessConstraints(string $table): void
    {
        // Ejemplo: verificar que campos de email tienen formato válido
        if (Schema::hasColumn($table, 'email')) {
            $constraint = $this->hasCheckConstraint($table, 'email');
            if (!$constraint) {
                $this->warnings[] = "Table '{$table}' should have email format validation constraint";
            }
        }

        // Verificar campos monetarios son positivos
        $moneyColumns = ['price', 'cost', 'amount', 'salary'];
        foreach ($moneyColumns as $column) {
            if (Schema::hasColumn($table, $column)) {
                $constraint = $this->hasCheckConstraint($table, $column);
                if (!$constraint) {
                    $this->warnings[] = "Table '{$table}' should have positive value constraint on '{$column}'";
                }
            }
        }
    }

    private function hasCheckConstraint(string $table, string $column): bool
    {
        $constraints = DB::select("
            SELECT CONSTRAINT_NAME
            FROM INFORMATION_SCHEMA.CHECK_CONSTRAINTS
            WHERE CONSTRAINT_SCHEMA = DATABASE()
            AND CONSTRAINT_NAME LIKE '%{$table}%{$column}%'
        ");

        return !empty($constraints);
    }

    // Métodos auxiliares adicionales...
    private function getForeignKeys(string $table): array { /* Implementation */ return []; }
    private function hasIndexOnColumn(string $table, string $column): bool { /* Implementation */ return true; }
    private function findRedundantIndexes(string $table): array { /* Implementation */ return []; }
    private function findUnusedIndexes(string $table): array { /* Implementation */ return []; }
    private function hasPrimaryKey(string $table): bool { /* Implementation */ return true; }
    private function validateReferentialIntegrity(string $table): void { /* Implementation */ }
    private function getColumnDetails(string $table): array { /* Implementation */ return []; }
    private function validateColumnDataType(string $table, array $column): void { /* Implementation */ }
    private function validateNullability(string $table, array $column): void { /* Implementation */ }
    private function validateDefaultValues(string $table, array $column): void { /* Implementation */ }
    private function validateDatabaseUsers(): void { /* Implementation */ }
    private function validateSSLConfiguration(): void { /* Implementation */ }
    private function validateAuditLogging(): void { /* Implementation */ }
    private function validateBackupConfiguration(): void { /* Implementation */ }
}

// Comando Artisan para ejecutar validación
// app/Console/Commands/ValidateSchema.php
class ValidateSchemaCommand extends Command
{
    protected $signature = 'db:validate-schema {--fix} {--report=}';
    protected $description = 'Validate database schema against best practices';

    public function handle(): int
    {
        $this->info('🔍 Validating database schema...');

        $validator = new SchemaValidator();
        $results = $validator->validateSchema();

        $this->displayResults($results);

        if ($this->option('report')) {
            $this->generateReport($results);
        }

        return $results['status'] === 'PASS' ? 0 : 1;
    }

    private function displayResults(array $results): void
    {
        if (empty($results['violations']) && empty($results['warnings'])) {
            $this->info('✅ Schema validation passed! No issues found.');
            return;
        }

        if (!empty($results['violations'])) {
            $this->error('❌ Schema validation failed with violations:');
            foreach ($results['violations'] as $violation) {
                $this->line("  • {$violation}");
            }
        }

        if (!empty($results['warnings'])) {
            $this->warn('⚠️  Warnings found:');
            foreach ($results['warnings'] as $warning) {
                $this->line("  • {$warning}");
            }
        }
    }

    private function generateReport(array $results): void
    {
        $reportPath = $this->option('report');
        $report = $this->buildHtmlReport($results);

        file_put_contents($reportPath, $report);
        $this->info("📄 Report generated: {$reportPath}");
    }

    private function buildHtmlReport(array $results): string
    {
        // Generar reporte HTML detallado
        return "<!DOCTYPE html>..."; // Implementation
    }
}
```

### 3. Performance Checker

#### Analizador de Performance

```php
<?php
// tools/database-performance-checker.php

namespace Tools\Database;

class PerformanceChecker
{
    public function runPerformanceCheck(): array
    {
        return [
            'slow_queries' => $this->analyzeSlowQueries(),
            'index_usage' => $this->analyzeIndexUsage(),
            'table_sizes' => $this->analyzeTableSizes(),
            'query_patterns' => $this->analyzeQueryPatterns(),
            'recommendations' => $this->generateRecommendations(),
        ];
    }

    private function analyzeSlowQueries(): array
    {
        $slowQueries = DB::select("
            SELECT
                sql_text,
                exec_count,
                avg_timer_wait/1000000000 as avg_time_seconds,
                sum_timer_wait/1000000000 as total_time_seconds,
                sum_rows_examined,
                sum_rows_sent,
                first_seen,
                last_seen
            FROM performance_schema.events_statements_summary_by_digest
            WHERE schema_name = DATABASE()
            AND avg_timer_wait > 1000000000  -- > 1 second
            ORDER BY avg_timer_wait DESC
            LIMIT 20
        ");

        return array_map(function($query) {
            return [
                'query' => $this->sanitizeQuery($query->sql_text),
                'avg_time' => round($query->avg_time_seconds, 3),
                'total_time' => round($query->total_time_seconds, 3),
                'executions' => $query->exec_count,
                'rows_examined' => $query->sum_rows_examined,
                'rows_sent' => $query->sum_rows_sent,
                'efficiency' => $query->sum_rows_sent > 0 ?
                    round($query->sum_rows_examined / $query->sum_rows_sent, 2) : 0,
                'priority' => $this->calculatePriority($query)
            ];
        }, $slowQueries);
    }

    private function analyzeIndexUsage(): array
    {
        // Analizar índices no utilizados
        $unusedIndexes = DB::select("
            SELECT
                object_schema,
                object_name,
                index_name
            FROM performance_schema.table_io_waits_summary_by_index_usage
            WHERE index_name IS NOT NULL
            AND index_name != 'PRIMARY'
            AND count_star = 0
            AND object_schema = DATABASE()
            ORDER BY object_name, index_name
        ");

        // Analizar índices más utilizados
        $mostUsedIndexes = DB::select("
            SELECT
                object_name,
                index_name,
                count_star as usage_count,
                sum_timer_wait/1000000000 as total_time_seconds
            FROM performance_schema.table_io_waits_summary_by_index_usage
            WHERE index_name IS NOT NULL
            AND object_schema = DATABASE()
            ORDER BY count_star DESC
            LIMIT 20
        ");

        return [
            'unused' => $unusedIndexes,
            'most_used' => $mostUsedIndexes,
            'recommendations' => $this->generateIndexRecommendations($unusedIndexes)
        ];
    }

    private function analyzeTableSizes(): array
    {
        $tableSizes = DB::select("
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
        ");

        return array_map(function($table) {
            return [
                'table' => $table->table_name,
                'rows' => number_format($table->table_rows),
                'total_size_mb' => $table->size_mb,
                'data_size_mb' => $table->data_mb,
                'index_size_mb' => $table->index_mb,
                'index_ratio' => $table->index_ratio,
                'status' => $this->getTableSizeStatus($table)
            ];
        }, $tableSizes);
    }

    private function getTableSizeStatus(object $table): string
    {
        if ($table->size_mb > 1000) return 'LARGE';
        if ($table->size_mb > 100) return 'MEDIUM';
        if ($table->index_ratio > 50) return 'OVER_INDEXED';
        if ($table->index_ratio < 5 && $table->table_rows > 1000) return 'UNDER_INDEXED';
        return 'OK';
    }

    private function calculatePriority(object $query): string
    {
        $impact = $query->avg_time_seconds * $query->exec_count;

        if ($impact > 100) return 'CRITICAL';
        if ($impact > 10) return 'HIGH';
        if ($impact > 1) return 'MEDIUM';
        return 'LOW';
    }

    private function sanitizeQuery(string $query): string
    {
        // Remover datos específicos para agrupar queries similares
        $query = preg_replace('/\b\d+\b/', '?', $query);
        $query = preg_replace("/('[^']*')/", '?', $query);
        return substr($query, 0, 200) . (strlen($query) > 200 ? '...' : '');
    }
}
```

### 4. Security Auditor

#### Auditor de Seguridad

```php
<?php
// tools/database-security-auditor.php

namespace Tools\Database;

class SecurityAuditor
{
    public function runSecurityAudit(): array
    {
        return [
            'user_permissions' => $this->auditUserPermissions(),
            'sensitive_data' => $this->auditSensitiveData(),
            'encryption_status' => $this->auditEncryption(),
            'audit_logging' => $this->auditLogging(),
            'backup_security' => $this->auditBackupSecurity(),
            'compliance' => $this->auditCompliance(),
        ];
    }

    private function auditUserPermissions(): array
    {
        // Verificar usuarios con permisos excesivos
        $users = DB::select("
            SELECT
                User,
                Host,
                Select_priv,
                Insert_priv,
                Update_priv,
                Delete_priv,
                Create_priv,
                Drop_priv,
                Grant_priv,
                Super_priv
            FROM mysql.user
            WHERE User != 'root'
        ");

        $violations = [];
        foreach ($users as $user) {
            if ($user->Grant_priv === 'Y') {
                $violations[] = "User '{$user->User}@{$user->Host}' has GRANT privilege";
            }
            if ($user->Super_priv === 'Y') {
                $violations[] = "User '{$user->User}@{$user->Host}' has SUPER privilege";
            }
        }

        return [
            'users' => $users,
            'violations' => $violations,
            'recommendations' => $this->generateUserRecommendations($violations)
        ];
    }

    private function auditSensitiveData(): array
    {
        $sensitiveColumns = [
            'password', 'email', 'phone', 'ssn', 'credit_card',
            'bank_account', 'address', 'birth_date', 'salary'
        ];

        $findings = [];
        $tables = $this->getAllTables();

        foreach ($tables as $table) {
            $columns = Schema::getColumnListing($table);

            foreach ($columns as $column) {
                if ($this->isSensitiveColumn($column, $sensitiveColumns)) {
                    $protection = $this->checkColumnProtection($table, $column);

                    if (!$protection['encrypted'] && !$protection['hashed']) {
                        $findings[] = [
                            'table' => $table,
                            'column' => $column,
                            'type' => $this->getSensitiveDataType($column),
                            'protection' => 'NONE',
                            'recommendation' => $this->getProtectionRecommendation($column)
                        ];
                    }
                }
            }
        }

        return [
            'sensitive_columns' => $findings,
            'total_unprotected' => count($findings),
            'severity' => count($findings) > 0 ? 'HIGH' : 'LOW'
        ];
    }

    private function auditEncryption(): array
    {
        // Verificar configuración de encriptación
        $sslStatus = DB::select("SHOW STATUS LIKE 'Ssl_cipher'");
        $tlsVersion = DB::select("SHOW STATUS LIKE 'Tls_version'");

        return [
            'connection_encryption' => !empty($sslStatus[0]->Value),
            'tls_version' => $tlsVersion[0]->Value ?? 'Not configured',
            'data_at_rest' => $this->checkDataAtRestEncryption(),
            'recommendations' => $this->generateEncryptionRecommendations()
        ];
    }

    private function auditLogging(): array
    {
        $auditStatus = [
            'general_log' => $this->getSystemVariable('general_log'),
            'slow_query_log' => $this->getSystemVariable('slow_query_log'),
            'log_bin' => $this->getSystemVariable('log_bin'),
            'audit_log_policy' => $this->getSystemVariable('audit_log_policy'),
        ];

        $violations = [];
        if ($auditStatus['general_log'] !== 'ON') {
            $violations[] = 'General query logging is disabled';
        }
        if ($auditStatus['slow_query_log'] !== 'ON') {
            $violations[] = 'Slow query logging is disabled';
        }

        return [
            'status' => $auditStatus,
            'violations' => $violations,
            'audit_tables' => $this->findAuditTables()
        ];
    }

    private function auditCompliance(): array
    {
        return [
            'gdpr' => $this->auditGDPRCompliance(),
            'pci_dss' => $this->auditPCIDSSCompliance(),
            'data_retention' => $this->auditDataRetention(),
        ];
    }

    private function auditGDPRCompliance(): array
    {
        $issues = [];
        $tables = $this->getAllTables();

        foreach ($tables as $table) {
            // Verificar soft deletes para derecho al olvido
            if (!Schema::hasColumn($table, 'deleted_at')) {
                if ($this->hasPersonalData($table)) {
                    $issues[] = "Table '{$table}' contains personal data but lacks soft delete capability";
                }
            }

            // Verificar campos de consentimiento
            if ($this->hasPersonalData($table) && !$this->hasConsentTracking($table)) {
                $issues[] = "Table '{$table}' should track user consent for data processing";
            }
        }

        return [
            'compliant' => empty($issues),
            'issues' => $issues,
            'recommendations' => $this->generateGDPRRecommendations($issues)
        ];
    }

    // Métodos auxiliares
    private function isSensitiveColumn(string $column, array $sensitiveColumns): bool
    {
        foreach ($sensitiveColumns as $sensitive) {
            if (strpos(strtolower($column), $sensitive) !== false) {
                return true;
            }
        }
        return false;
    }

    private function checkColumnProtection(string $table, string $column): array
    {
        // Verificar si la columna está encriptada o hasheada
        // Esto dependería de la implementación específica
        return [
            'encrypted' => false, // Implementar lógica de detección
            'hashed' => strpos($column, 'password') !== false
        ];
    }

    private function getSystemVariable(string $variable): string
    {
        $result = DB::select("SHOW VARIABLES LIKE '{$variable}'");
        return $result[0]->Value ?? 'Not set';
    }

    private function hasPersonalData(string $table): bool
    {
        $personalDataColumns = ['email', 'name', 'phone', 'address', 'birth_date'];
        $columns = Schema::getColumnListing($table);

        foreach ($personalDataColumns as $personalColumn) {
            foreach ($columns as $column) {
                if (strpos(strtolower($column), $personalColumn) !== false) {
                    return true;
                }
            }
        }

        return false;
    }

    private function hasConsentTracking(string $table): bool
    {
        $consentColumns = ['consent_given', 'privacy_accepted', 'terms_accepted'];
        $columns = Schema::getColumnListing($table);

        foreach ($consentColumns as $consentColumn) {
            if (in_array($consentColumn, $columns)) {
                return true;
            }
        }

        return false;
    }
}
```

## Tips

### Automatización del Checklist

- **Integración CI/CD**: Incorporar validaciones en pipeline de despliegue
- **Scripts de validación**: Automatizar verificaciones técnicas
- **Reports automáticos**: Generar reportes de revisión automáticamente
- **Alertas proactivas**: Notificar violaciones antes del despliegue
- **Métricas de calidad**: Trackear tendencias de calidad de BD

### Herramientas de Apoyo

- **Database migration tools**: Laravel migrations, Flyway, Liquibase
- **Schema comparison**: MySQL Workbench, Adminer
- **Performance monitoring**: Percona Toolkit, MySQL Enterprise Monitor
- **Security scanners**: SQLMap (para testing), Nessus
- **Documentation tools**: SchemaSpy, dbdocs.io

### Mejores Prácticas

- **Revisión por pares**: Nunca deployar sin review de otro desarrollador
- **Entornos de staging**: Probar todos los cambios en ambiente similar a
  producción
- **Rollback planning**: Siempre tener plan de reversión probado
- **Documentation first**: Documentar antes de implementar
- **Security by design**: Considerar seguridad desde el diseño inicial
- **Performance testing**: Benchmark antes y después de cambios

## Ejemplos

### Checklist Específico para E-commerce

```markdown
# E-commerce Database Review Checklist

## Específico para Comercio Electrónico

### Compliance PCI DSS

- [ ] **Credit card data**: Nunca almacenar datos completos de tarjetas
- [ ] **CVV storage**: CVV nunca debe persistirse
- [ ] **Tokenization**: Usar tokens para referencias de pago
- [ ] **Encryption**: Datos sensibles encriptados con AES-256
- [ ] **Access logging**: Todos los accesos a datos de pago loggeados

### Performance para Catálogo

- [ ] **Product search**: Índices full-text para búsqueda
- [ ] **Category browsing**: Índices compuestos category_id + status
- [ ] **Price filtering**: Índices en campos de precio
- [ ] **Inventory queries**: Índices en stock_quantity
- [ ] **Cache strategy**: Estrategia de cache para productos frecuentes

### Integridad de Pedidos

- [ ] **Order consistency**: Transacciones ACID para creación de pedidos
- [ ] **Inventory tracking**: Triggers para actualización de stock
- [ ] **Financial reconciliation**: Constraints para integridad financiera
- [ ] **Audit trail**: Logging completo de cambios en pedidos
- [ ] **Status transitions**: Validaciones de estados de pedido

### Escalabilidad

- [ ] **Read replicas**: Configuración para lectura de catálogo
- [ ] **Partitioning**: Particionado por fecha para pedidos históricos
- [ ] **Archival strategy**: Estrategia de archivo para datos antiguos
- [ ] **Connection pooling**: Pool de conexiones configurado
- [ ] **Query optimization**: Todas las queries críticas optimizadas
```

### Script de Pre-deployment

```bash
#!/bin/bash
# scripts/pre-deployment-check.sh

echo "🔍 Starting pre-deployment database check..."

# Ejecutar validaciones automáticas
php artisan db:validate-schema --report=/tmp/schema-report.html
SCHEMA_STATUS=$?

php artisan db:check-performance --report=/tmp/performance-report.html
PERFORMANCE_STATUS=$?

php artisan db:security-audit --report=/tmp/security-report.html
SECURITY_STATUS=$?

# Verificar backups recientes
LATEST_BACKUP=$(find /var/backups/mysql -name "*.sql.gz" -mtime -1 | wc -l)
if [ $LATEST_BACKUP -eq 0 ]; then
    echo "❌ No recent backup found (last 24h)"
    BACKUP_STATUS=1
else
    echo "✅ Recent backup found"
    BACKUP_STATUS=0
fi

# Consolidar resultados
TOTAL_ERRORS=$((SCHEMA_STATUS + PERFORMANCE_STATUS + SECURITY_STATUS + BACKUP_STATUS))

if [ $TOTAL_ERRORS -eq 0 ]; then
    echo "✅ All database checks passed - Ready for deployment"
    exit 0
else
    echo "❌ Database checks failed with $TOTAL_ERRORS error(s)"
    echo "📄 Check reports in /tmp/*-report.html"
    exit 1
fi
```

## Navegación

[⬅️ Estrategias de Caché](./estrategias-cache.md) |
[🏠 README Principal](../../README.md) |
[Revisión General del Código ➡️](./revision-general-codigo.md)
