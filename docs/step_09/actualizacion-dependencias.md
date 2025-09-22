# Actualización de Dependencias

## Conocimientos Involucrados
- Gestión de dependencias (Composer, npm, etc.)
- Seguridad y auditoría de librerías
- Evaluación de compatibilidad y pruebas

## Responsable
- Equipo de desarrollo

## ¿Qué es?

La actualización de dependencias es el proceso sistemático de mantener
actualizadas las librerías, frameworks y herramientas externas que utiliza
nuestro proyecto. Incluye tanto actualizaciones de seguridad críticas como
mejoras de funcionalidad y performance. En el contexto de Clean Architecture +
DDD, es crucial que las actualizaciones no comprometan la independencia de las
capas ni las reglas de negocio del dominio.

Este proceso abarca dependencias del backend (Composer/PHP), frontend
(npm/Node.js), base de datos, herramientas de desarrollo y servicios de
infraestructura, manteniendo la estabilidad y seguridad del sistema.

## ¿Por qué es importante?

- **Seguridad**: Corrige vulnerabilidades conocidas en librerías de terceros
- **Performance**: Aprovecha optimizaciones y mejoras de rendimiento
- **Compatibilidad**: Mantiene compatibilidad con ecosistemas en evolución
- **Nuevas funcionalidades**: Accede a nuevas características y APIs
- **Soporte técnico**: Asegura que las librerías mantengan soporte activo
- **Estabilidad**: Reduce bugs y problemas conocidos en versiones anteriores
- **Compliance**: Cumple con requisitos de auditoría y seguridad corporativa

## ¿Qué debe incluir?

### Categorización de Dependencias

- **Críticas**: Framework core, librerías de seguridad
- **Importantes**: ORM, HTTP clients, utilidades principales
- **Opcionales**: Herramientas de desarrollo, testing, linting
- **Desarrollo**: DevDependencies que no afectan producción

### Tipos de Actualizaciones

- **Patch**: Fixes de bugs (1.0.1 → 1.0.2)
- **Minor**: Nuevas features compatibles (1.0.0 → 1.1.0)
- **Major**: Breaking changes (1.0.0 → 2.0.0)
- **Security**: Parches de seguridad urgentes

### Proceso de Actualización

- Análisis de changelog y breaking changes
- Testing en ambiente de desarrollo
- Validación de regresiones
- Despliegue gradual por ambientes
- Rollback plan preparado

### Automatización

- Dependabot/Renovate para PRs automáticos
- Security scanning automatizado
- Testing automático post-actualización
- Notificaciones de vulnerabilidades

## ¿Qué debo hacer?

### 1. Configurar Dependabot

**GitHub Dependabot configuration:**

```yaml
# .github/dependabot.yml
version: 2
updates:
   # Backend dependencies (Composer)
   - package-ecosystem: "composer"
     directory: "/backend"
     schedule:
        interval: "weekly"
        day: "monday"
        time: "09:00"
     open-pull-requests-limit: 5
     reviewers:
        - "backend-team"
     assignees:
        - "tech-lead"
     commit-message:
        prefix: "deps"
        include: "scope"

   # Frontend dependencies (npm)
   - package-ecosystem: "npm"
     directory: "/frontend"
     schedule:
        interval: "weekly"
        day: "tuesday"
        time: "09:00"
     open-pull-requests-limit: 5
     ignore:
        # Ignorar actualizaciones major de React temporalmente
        - dependency-name: "react"
          update-types: ["version-update:semver-major"]
     reviewers:
        - "frontend-team"
     commit-message:
        prefix: "deps"

   # Docker dependencies
   - package-ecosystem: "docker"
     directory: "/"
     schedule:
        interval: "monthly"
     reviewers:
        - "devops-team"

   # GitHub Actions
   - package-ecosystem: "github-actions"
     directory: "/"
     schedule:
        interval: "weekly"
```

### 2. Script de Actualización Automatizada

**Script para actualización controlada:**

```bash
#!/bin/bash
# scripts/update-dependencies.sh

set -e

echo "🔄 Iniciando actualización de dependencias..."

# Función para logging con timestamp
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

# Función para backup
backup_lockfiles() {
    log "📦 Creando backup de lock files..."
    cp backend/composer.lock backend/composer.lock.backup
    cp frontend/package-lock.json frontend/package-lock.json.backup
}

# Función para restaurar backup
restore_lockfiles() {
    log "⏪ Restaurando lock files..."
    mv backend/composer.lock.backup backend/composer.lock
    mv frontend/package-lock.json.backup frontend/package-lock.json
}

# Función para actualizar backend
update_backend() {
    log "🐘 Actualizando dependencias de backend..."
    cd backend
    
    # Actualizar dependencias de seguridad
    composer audit --format=json > security-audit.json
    
    # Actualizar solo patches por defecto
    composer update --with-dependencies --no-dev --optimize-autoloader
    
    # Limpiar cache
    php artisan config:clear
    php artisan cache:clear
    php artisan route:clear
    
    cd ..
}

# Función para actualizar frontend
update_frontend() {
    log "⚛️ Actualizando dependencias de frontend..."
    cd frontend
    
    # Audit de seguridad
    npm audit --audit-level=high
    
    # Actualizar dependencias
    npm update
    
    # Audit fix automático para vulnerabilidades
    npm audit fix
    
    # Rebuild
    npm run build
    
    cd ..
}

# Función para ejecutar tests
run_tests() {
    log "🧪 Ejecutando tests..."
    
    # Backend tests
    cd backend
    php artisan test --coverage
    cd ..
    
    # Frontend tests
    cd frontend
    npm run test:coverage
    cd ..
    
    # Integration tests
    ./scripts/integration-tests.sh
}

# Función para verificar security
security_check() {
    log "🔒 Verificando seguridad..."
    
    # Backend security audit
    cd backend
    composer audit --format=table
    
    # Frontend security audit
    cd ../frontend
    npm audit --audit-level=moderate
    
    # Security scanning con herramientas adicionales
    cd ..
    ./scripts/security-scan.sh
}

# Main execution
main() {
    # Verificar que estamos en la rama correcta
    CURRENT_BRANCH=$(git branch --show-current)
    if [ "$CURRENT_BRANCH" != "develop" ]; then
        log "❌ Error: Debe estar en la rama 'develop' para actualizar dependencias"
        exit 1
    fi
    
    # Crear branch para la actualización
    UPDATE_BRANCH="deps/update-$(date +%Y%m%d-%H%M%S)"
    git checkout -b "$UPDATE_BRANCH"
    
    # Backup de archivos lock
    backup_lockfiles
    
    # Intentar actualización
    if update_backend && update_frontend; then
        log "✅ Dependencias actualizadas exitosamente"
        
        # Ejecutar tests
        if run_tests; then
            log "✅ Tests pasaron exitosamente"
            
            # Security check
            if security_check; then
                log "✅ Security check pasó exitosamente"
                
                # Commit cambios
                git add .
                git commit -m "deps: update dependencies $(date +%Y-%m-%d)"
                
                # Push y crear PR
                git push origin "$UPDATE_BRANCH"
                
                log "🎉 Actualización completada. PR creado: $UPDATE_BRANCH"
            else
                log "❌ Security check falló"
                restore_lockfiles
                exit 1
            fi
        else
            log "❌ Tests fallaron"
            restore_lockfiles
            exit 1
        fi
    else
        log "❌ Error en actualización de dependencias"
        restore_lockfiles
        exit 1
    fi
}

# Ejecutar función principal
main "$@"
```

### 3. Servicio de Gestión de Dependencias

**Service para tracking de dependencias:**

```php
<?php
// app/Application/Services/DependencyManagementService.php
namespace App\Application\Services;

use App\Domain\Dependencies\Entities\Dependency;
use App\Domain\Dependencies\ValueObjects\DependencyType;
use App\Domain\Dependencies\ValueObjects\UpdateSeverity;

class DependencyManagementService
{
    public function __construct(
        private DependencyRepositoryInterface $dependencyRepository,
        private SecurityScannerService $securityScanner,
        private NotificationService $notificationService
    ) {}

    public function scanForUpdates(): array
    {
        $updates = [];
        
        // Escanear dependencias de Composer
        $composerUpdates = $this->scanComposerDependencies();
        $updates = array_merge($updates, $composerUpdates);
        
        // Escanear dependencias de NPM
        $npmUpdates = $this->scanNpmDependencies();
        $updates = array_merge($updates, $npmUpdates);
        
        // Analizar severidad y crear recomendaciones
        foreach ($updates as $update) {
            $this->analyzeUpdateSeverity($update);
        }
        
        return $updates;
    }

    public function checkSecurityVulnerabilities(): array
    {
        $vulnerabilities = [];
        
        // Composer audit
        $composerVulns = $this->securityScanner->scanComposer();
        $vulnerabilities = array_merge($vulnerabilities, $composerVulns);
        
        // NPM audit
        $npmVulns = $this->securityScanner->scanNpm();
        $vulnerabilities = array_merge($vulnerabilities, $npmVulns);
        
        // Notificar vulnerabilidades críticas
        $criticalVulns = array_filter(
            $vulnerabilities, 
            fn($vuln) => $vuln['severity'] === 'critical'
        );
        
        if (!empty($criticalVulns)) {
            $this->notificationService->sendSecurityAlert($criticalVulns);
        }
        
        return $vulnerabilities;
    }

    public function createUpdatePlan(array $dependencies): array
    {
        $plan = [
            'immediate' => [],  // Security patches
            'scheduled' => [],  // Regular updates
            'deferred' => [],   // Major updates requiring planning
        ];
        
        foreach ($dependencies as $dependency) {
            $updateSeverity = $this->calculateUpdateSeverity($dependency);
            
            switch ($updateSeverity) {
                case UpdateSeverity::CRITICAL:
                    $plan['immediate'][] = $dependency;
                    break;
                case UpdateSeverity::HIGH:
                    $plan['scheduled'][] = $dependency;
                    break;
                default:
                    $plan['deferred'][] = $dependency;
                    break;
            }
        }
        
        return $plan;
    }

    private function analyzeUpdateSeverity(Dependency $dependency): UpdateSeverity
    {
        // Factors to consider:
        // 1. Security vulnerabilities
        // 2. Breaking changes
        // 3. Dependency criticality
        // 4. Last update date
        
        if ($dependency->hasSecurityVulnerabilities()) {
            return UpdateSeverity::CRITICAL;
        }
        
        if ($dependency->hasBreakingChanges()) {
            return UpdateSeverity::LOW; // Require manual review
        }
        
        if ($dependency->isCriticalDependency()) {
            return UpdateSeverity::HIGH;
        }
        
        return UpdateSeverity::MEDIUM;
    }
}
```

### 4. Configurar Renovate Bot

**Renovate configuration:**

```json
{
   "extends": [
      "config:base",
      "security:openssf-scorecard",
      ":dependencyDashboard",
      ":semanticCommits",
      ":automergeDigest"
   ],
   "timezone": "America/Santiago",
   "schedule": [
      "before 10am on monday"
   ],
   "packageRules": [
      {
         "matchPackagePatterns": ["^laravel/"],
         "groupName": "Laravel framework",
         "reviewers": ["backend-team"],
         "schedule": ["before 10am on the first day of the month"]
      },
      {
         "matchPackagePatterns": ["^react", "^@types/react"],
         "groupName": "React ecosystem",
         "reviewers": ["frontend-team"]
      },
      {
         "matchDepTypes": ["devDependencies"],
         "automerge": true,
         "schedule": ["before 5am on monday"]
      },
      {
         "matchUpdateTypes": ["patch"],
         "automerge": true,
         "schedule": ["before 5am on monday"]
      },
      {
         "matchUpdateTypes": ["major"],
         "dependencyDashboardApproval": true,
         "schedule": ["before 10am on the first day of the month"]
      }
   ],
   "vulnerabilityAlerts": {
      "enabled": true,
      "schedule": ["at any time"]
   },
   "osvVulnerabilityAlerts": true,
   "prHourlyLimit": 2,
   "prConcurrentLimit": 5,
   "rebaseWhen": "conflicted"
}
```

## Tips

### Estrategia de Actualización

- **Pequeños incrementos**: Actualiza frecuentemente en pequeños lotes
- **Testing exhaustivo**: Nunca omitas testing después de actualizaciones
- **Staged rollout**: Despliega actualizaciones gradualmente por ambientes
- **Rollback ready**: Siempre ten un plan de rollback preparado

### Manejo de Breaking Changes

- **Read changelogs**: Siempre lee los changelogs completos
- **Feature flags**: Usa feature flags para changes riesgosos
- **Backward compatibility**: Mantén compatibilidad cuando sea posible
- **Migration guides**: Sigue las guías de migración oficiales

### Security First

- **Security patches**: Prioriza actualizaciones de seguridad
- **Vulnerability scanning**: Escanea regularmente por vulnerabilidades
- **Zero-day response**: Ten proceso para respuesta rápida a vulnerabilidades
- **Audit trails**: Mantén registro de todas las actualizaciones

### Automatización Inteligente

- **Smart grouping**: Agrupa dependencias relacionadas
- **Conditional automerge**: Automerge solo para cambios seguros
- **Conflict resolution**: Configura resolución automática de conflictos
- **Notification tuning**: Ajusta notificaciones para evitar spam

## Ejemplos

### 1. Workflow de Testing Post-Actualización

```yaml
# .github/workflows/dependency-update-test.yml
name: Dependency Update Testing

on:
   pull_request:
      branches: [develop]
      paths:
         - "backend/composer.lock"
         - "frontend/package-lock.json"

jobs:
   dependency-analysis:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
           with:
              fetch-depth: 0

         - name: Analyze Dependency Changes
           run: |
              # Detectar qué dependencias cambiaron
              git diff HEAD~1 backend/composer.lock > composer-changes.txt
              git diff HEAD~1 frontend/package-lock.json > package-changes.txt

              # Extraer nombres de dependencias actualizadas
              ./scripts/extract-dependency-changes.sh

         - name: Check for Breaking Changes
           run: |
              ./scripts/check-breaking-changes.sh

         - name: Security Audit
           run: |
              cd backend && composer audit
              cd ../frontend && npm audit --audit-level=high

   comprehensive-testing:
      needs: dependency-analysis
      runs-on: ubuntu-latest
      strategy:
         matrix:
            php-version: [8.1, 8.2]
            node-version: [18, 20]
      steps:
         - uses: actions/checkout@v3

         - name: Setup PHP ${{ matrix.php-version }}
           uses: shivammathur/setup-php@v2
           with:
              php-version: ${{ matrix.php-version }}

         - name: Setup Node ${{ matrix.node-version }}
           uses: actions/setup-node@v3
           with:
              node-version: ${{ matrix.node-version }}

         - name: Install Dependencies
           run: |
              cd backend && composer install --no-dev
              cd ../frontend && npm ci

         - name: Run Backend Tests
           run: |
              cd backend
              php artisan test --parallel --coverage

         - name: Run Frontend Tests
           run: |
              cd frontend
              npm run test:coverage

         - name: Integration Tests
           run: |
              ./scripts/start-test-environment.sh
              ./scripts/run-integration-tests.sh

         - name: Performance Regression Test
           run: |
              ./scripts/performance-baseline.sh
              ./scripts/compare-performance.sh

   security-scan:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Run Trivy vulnerability scanner
           uses: aquasecurity/trivy-action@master
           with:
              scan-type: "fs"
              scan-ref: "."

         - name: Run Snyk security scan
           uses: snyk/actions/php@master
           with:
              args: --severity-threshold=high
           env:
              SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
```

### 2. Script de Análisis de Breaking Changes

```bash
#!/bin/bash
# scripts/check-breaking-changes.sh

echo "🔍 Analizando breaking changes..."

# Función para verificar breaking changes en Composer
check_composer_breaking_changes() {
    echo "Verificando breaking changes en dependencias PHP..."
    
    # Extraer dependencias actualizadas
    UPDATED_PACKAGES=$(git diff HEAD~1 backend/composer.lock | grep -E '^\+.*"name"' | sed 's/.*"name": "\(.*\)",/\1/')
    
    for package in $UPDATED_PACKAGES; do
        echo "Verificando $package..."
        
        # Obtener versión anterior y nueva
        OLD_VERSION=$(git show HEAD~1:backend/composer.lock | jq -r ".packages[] | select(.name==\"$package\") | .version")
        NEW_VERSION=$(jq -r ".packages[] | select(.name==\"$package\") | .version" backend/composer.lock)
        
        if [ "$OLD_VERSION" != "$NEW_VERSION" ]; then
            # Verificar si es un major update
            OLD_MAJOR=$(echo $OLD_VERSION | cut -d. -f1 | tr -d 'v')
            NEW_MAJOR=$(echo $NEW_VERSION | cut -d. -f1 | tr -d 'v')
            
            if [ "$OLD_MAJOR" != "$NEW_MAJOR" ]; then
                echo "⚠️ BREAKING CHANGE: $package actualizado de $OLD_VERSION a $NEW_VERSION"
                echo "Verificando changelog..."
                
                # Buscar changelog en repositorio de la dependencia
                ./scripts/fetch-changelog.sh "$package" "$OLD_VERSION" "$NEW_VERSION"
            fi
        fi
    done
}

# Función para verificar breaking changes en NPM
check_npm_breaking_changes() {
    echo "Verificando breaking changes en dependencias Node..."
    
    # Usar npm outdated para verificar updates disponibles
    cd frontend
    npm outdated --json > outdated.json
    
    # Procesar packages con major updates
    jq -r 'to_entries[] | select(.value.type == "major") | "\(.key) \(.value.current) \(.value.wanted)"' outdated.json | while read package current wanted; do
        echo "⚠️ MAJOR UPDATE: $package de $current a $wanted"
        
        # Verificar changelog
        ../scripts/fetch-npm-changelog.sh "$package" "$current" "$wanted"
    done
    
    cd ..
}

# Función principal
main() {
    check_composer_breaking_changes
    check_npm_breaking_changes
    
    echo "✅ Análisis de breaking changes completado"
}

main "$@"
```

### 3. Servicio de Monitoreo de Dependencias

```typescript
// src/services/dependency-monitor.service.ts
interface DependencyVulnerability {
   package: string;
   version: string;
   severity: "low" | "moderate" | "high" | "critical";
   description: string;
   recommendation: string;
   cve?: string;
}

interface DependencyUpdate {
   package: string;
   currentVersion: string;
   latestVersion: string;
   updateType: "patch" | "minor" | "major";
   changelog?: string;
   breakingChanges: boolean;
}

export class DependencyMonitorService {
   async scanForVulnerabilities(): Promise<DependencyVulnerability[]> {
      const vulnerabilities: DependencyVulnerability[] = [];

      // Scan backend dependencies
      const backendVulns = await this.scanBackendVulnerabilities();
      vulnerabilities.push(...backendVulns);

      // Scan frontend dependencies
      const frontendVulns = await this.scanFrontendVulnerabilities();
      vulnerabilities.push(...frontendVulns);

      return vulnerabilities;
   }

   async checkForUpdates(): Promise<DependencyUpdate[]> {
      const updates: DependencyUpdate[] = [];

      // Check Composer updates
      const composerUpdates = await this.checkComposerUpdates();
      updates.push(...composerUpdates);

      // Check NPM updates
      const npmUpdates = await this.checkNpmUpdates();
      updates.push(...npmUpdates);

      return updates;
   }

   async generateUpdateReport(): Promise<{
      summary: {
         total: number;
         security: number;
         major: number;
         minor: number;
         patch: number;
      };
      recommendations: string[];
      details: DependencyUpdate[];
   }> {
      const updates = await this.checkForUpdates();
      const vulnerabilities = await this.scanForVulnerabilities();

      const summary = {
         total: updates.length,
         security: vulnerabilities.length,
         major: updates.filter((u) => u.updateType === "major").length,
         minor: updates.filter((u) => u.updateType === "minor").length,
         patch: updates.filter((u) => u.updateType === "patch").length,
      };

      const recommendations = this.generateRecommendations(
         updates,
         vulnerabilities,
      );

      return {
         summary,
         recommendations,
         details: updates,
      };
   }

   private async scanBackendVulnerabilities(): Promise<
      DependencyVulnerability[]
   > {
      try {
         const response = await fetch("/api/dependencies/security-scan", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ type: "composer" }),
         });

         return await response.json();
      } catch (error) {
         console.error("Error scanning backend vulnerabilities:", error);
         return [];
      }
   }

   private generateRecommendations(
      updates: DependencyUpdate[],
      vulnerabilities: DependencyVulnerability[],
   ): string[] {
      const recommendations: string[] = [];

      // Critical security updates
      const criticalVulns = vulnerabilities.filter((v) =>
         v.severity === "critical"
      );
      if (criticalVulns.length > 0) {
         recommendations.push(
            `🚨 URGENT: ${criticalVulns.length} critical security vulnerabilities found. Update immediately.`,
         );
      }

      // Major updates requiring attention
      const majorUpdates = updates.filter((u) => u.updateType === "major");
      if (majorUpdates.length > 0) {
         recommendations.push(
            `⚠️ ${majorUpdates.length} major updates available. Review breaking changes before updating.`,
         );
      }

      // Safe updates
      const safeUpdates = updates.filter((u) =>
         u.updateType === "patch" && !u.breakingChanges
      );
      if (safeUpdates.length > 0) {
         recommendations.push(
            `✅ ${safeUpdates.length} safe patch updates available for automatic update.`,
         );
      }

      return recommendations;
   }
}
```

## Navegación

[⬅️ Bug Fixes y Hotfixes](./bug-fixes-hotfixes.md) |
[🏠 README Principal](../../README.md) |
[Gestión de Nuevas Funcionalidades ➡️](./gestion-nuevas-funcionalidades.md)
