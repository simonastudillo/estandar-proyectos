# Gestión de Nuevas Funcionalidades

## ¿Qué es?

La gestión de nuevas funcionalidades es el proceso estructurado de planificar,
desarrollar e implementar nuevas características en el sistema existente. En el
contexto de Clean Architecture + DDD, implica extender el dominio con nuevas
entidades, casos de uso y reglas de negocio, manteniendo la integridad
arquitectónica y la independencia de las capas.

Este proceso abarca desde la captura de requerimientos hasta la implementación,
testing y despliegue de nuevas features, asegurando que se integren de manera
coherente con la funcionalidad existente y respeten los principios de diseño
establecidos.

## ¿Por qué es importante?

- **Evolución del negocio**: Permite que el software se adapte a nuevos
  requerimientos
- **Ventaja competitiva**: Mantiene el producto relevante en el mercado
- **Satisfacción del usuario**: Responde a necesidades y feedback de usuarios
- **ROI sostenido**: Maximiza el valor del software a lo largo del tiempo
- **Escalabilidad del dominio**: Expande las capacidades del sistema de forma
  controlada
- **Mantenimiento arquitectónico**: Preserva la calidad y estructura del código
- **Innovation delivery**: Facilita la entrega continua de valor al negocio

## ¿Qué debe incluir?

### Planificación de Features

- Feature roadmap y prioritización
- Análisis de impacto en arquitectura
- Estimación de esfuerzo y recursos
- Definición de acceptance criteria
- Análisis de dependencias

### Diseño Arquitectónico

- Modeling del dominio extendido
- Nuevos casos de uso y entidades
- APIs y contratos de integración
- Impacto en capas existentes
- Estrategias de migración

### Implementación Controlada

- Feature flags para despliegue gradual
- Desarrollo incremental por capas
- Testing comprehensivo
- Code review riguroso
- Documentación actualizada

### Validación y Entrega

- User acceptance testing
- Performance impact analysis
- Rollback strategies
- Monitoring de nuevas features
- Feedback collection

## ¿Qué debo hacer?

### 1. Establecer Proceso de Feature Management

**Feature request workflow:**

```php
<?php
// app/Domain/Features/Entities/FeatureRequest.php
namespace App\Domain\Features\Entities;

use App\Domain\Features\ValueObjects\FeaturePriority;
use App\Domain\Features\ValueObjects\FeatureStatus;
use App\Domain\Features\ValueObjects\FeatureComplexity;

class FeatureRequest
{
    private function __construct(
        private string $id,
        private string $title,
        private string $description,
        private FeaturePriority $priority,
        private FeatureComplexity $complexity,
        private FeatureStatus $status,
        private string $requesterId,
        private array $acceptanceCriteria,
        private ?\DateTimeImmutable $targetDate,
        private \DateTimeImmutable $createdAt
    ) {}

    public static function create(
        string $title,
        string $description,
        FeaturePriority $priority,
        string $requesterId,
        array $acceptanceCriteria,
        ?\DateTimeImmutable $targetDate = null
    ): self {
        return new self(
            id: uniqid('feat_'),
            title: $title,
            description: $description,
            priority: $priority,
            complexity: FeatureComplexity::UNKNOWN,
            status: FeatureStatus::REQUESTED,
            requesterId: $requesterId,
            acceptanceCriteria: $acceptanceCriteria,
            targetDate: $targetDate,
            createdAt: new \DateTimeImmutable()
        );
    }

    public function analyze(FeatureComplexity $complexity, int $estimatedHours): void
    {
        $this->complexity = $complexity;
        $this->status = FeatureStatus::ANALYZED;
        $this->metadata['estimated_hours'] = $estimatedHours;
    }

    public function approve(): void
    {
        if ($this->status !== FeatureStatus::ANALYZED) {
            throw new \DomainException('Feature must be analyzed before approval');
        }
        
        $this->status = FeatureStatus::APPROVED;
    }

    public function startDevelopment(string $developerId): void
    {
        if ($this->status !== FeatureStatus::APPROVED) {
            throw new \DomainException('Feature must be approved before development');
        }
        
        $this->status = FeatureStatus::IN_DEVELOPMENT;
        $this->metadata['developer_id'] = $developerId;
        $this->metadata['development_started_at'] = new \DateTimeImmutable();
    }

    public function complete(): void
    {
        $this->status = FeatureStatus::COMPLETED;
        $this->metadata['completed_at'] = new \DateTimeImmutable();
    }

    // Getters y métodos adicionales...
}
```

### 2. Implementar Feature Flags

**Feature flag system:**

```php
<?php
// app/Infrastructure/FeatureFlags/FeatureFlagService.php
namespace App\Infrastructure\FeatureFlags;

class FeatureFlagService
{
    public function __construct(
        private FeatureFlagRepository $repository,
        private CacheManager $cache
    ) {}

    public function isEnabled(string $featureName, ?string $userId = null): bool
    {
        $cacheKey = "feature_flag:{$featureName}:{$userId}";
        
        return $this->cache->remember($cacheKey, 300, function() use ($featureName, $userId) {
            $flag = $this->repository->findByName($featureName);
            
            if (!$flag || !$flag->isActive()) {
                return false;
            }

            // Global enable/disable
            if ($flag->isGloballyEnabled()) {
                return true;
            }

            // User-specific checks
            if ($userId && $flag->isEnabledForUser($userId)) {
                return true;
            }

            // Percentage rollout
            if ($flag->hasPercentageRollout()) {
                return $this->isUserInPercentage($userId, $flag->getPercentage());
            }

            return false;
        });
    }

    public function enableForUser(string $featureName, string $userId): void
    {
        $flag = $this->repository->findByName($featureName);
        $flag->enableForUser($userId);
        $this->repository->save($flag);
        
        // Invalidar cache
        $this->cache->forget("feature_flag:{$featureName}:{$userId}");
    }

    public function setPercentageRollout(string $featureName, int $percentage): void
    {
        $flag = $this->repository->findByName($featureName);
        $flag->setPercentageRollout($percentage);
        $this->repository->save($flag);
        
        // Invalidar toda la cache del feature
        $this->cache->tags(["feature_flag:{$featureName}"])->flush();
    }

    private function isUserInPercentage(?string $userId, int $percentage): bool
    {
        if (!$userId) {
            return false;
        }

        // Usar hash del userId para deterministic rollout
        $hash = crc32($userId);
        return ($hash % 100) < $percentage;
    }
}
```

**Middleware para feature flags:**

```php
<?php
// app/Infrastructure/Http/Middleware/FeatureFlagMiddleware.php
namespace App\Infrastructure\Http\Middleware;

use App\Infrastructure\FeatureFlags\FeatureFlagService;

class FeatureFlagMiddleware
{
    public function __construct(
        private FeatureFlagService $featureFlags
    ) {}

    public function handle(Request $request, Closure $next, string $featureName)
    {
        $userId = auth()->id();
        
        if (!$this->featureFlags->isEnabled($featureName, $userId)) {
            return response()->json([
                'error' => 'Feature not available',
                'code' => 'FEATURE_DISABLED'
            ], 404);
        }

        // Agregar flag al request para uso en controladores
        $request->attributes->set('feature_enabled', true);
        $request->attributes->set('feature_name', $featureName);

        return $next($request);
    }
}
```

### 3. Workflow de Desarrollo de Features

**GitHub Actions para feature development:**

```yaml
# .github/workflows/feature-development.yml
name: Feature Development Workflow

on:
   push:
      branches: [feature/*]
   pull_request:
      branches: [develop]
      types: [opened, synchronize]

jobs:
   feature-analysis:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
           with:
              fetch-depth: 0

         - name: Extract Feature Info
           id: feature_info
           run: |
              BRANCH_NAME=${GITHUB_HEAD_REF:-${GITHUB_REF#refs/heads/}}
              FEATURE_NAME=$(echo $BRANCH_NAME | sed 's/feature\///')
              echo "feature_name=$FEATURE_NAME" >> $GITHUB_OUTPUT

         - name: Analyze Code Changes
           run: |
              # Analizar impacto en arquitectura
              ./scripts/analyze-architecture-impact.sh "${{ steps.feature_info.outputs.feature_name }}"

         - name: Check Domain Layer Changes
           run: |
              # Verificar que cambios en Domain sigan DDD principles
              ./scripts/validate-domain-changes.sh

         - name: Validate Clean Architecture
           run: |
              # Verificar que no se violen principios de Clean Architecture
              ./scripts/validate-clean-architecture.sh

   comprehensive-testing:
      needs: feature-analysis
      runs-on: ubuntu-latest
      strategy:
         matrix:
            test-suite: [unit, integration, feature, browser]
      steps:
         - uses: actions/checkout@v3

         - name: Setup Environment
           uses: ./.github/actions/setup-full-environment

         - name: Run ${{ matrix.test-suite }} Tests
           run: |
              case "${{ matrix.test-suite }}" in
                "unit")
                  ./vendor/bin/phpunit --testsuite=Unit
                  npm run test:unit
                  ;;
                "integration")
                  ./vendor/bin/phpunit --testsuite=Integration
                  npm run test:integration
                  ;;
                "feature")
                  ./vendor/bin/phpunit --testsuite=Feature
                  npm run test:e2e:headless
                  ;;
                "browser")
                  php artisan dusk --env=testing
                  ;;
              esac

         - name: Feature-specific Tests
           run: |
              # Ejecutar tests específicos de la nueva feature
              FEATURE_NAME=$(echo ${GITHUB_HEAD_REF:-${GITHUB_REF#refs/heads/}} | sed 's/feature\///')
              ./scripts/run-feature-tests.sh "$FEATURE_NAME"

   performance-impact:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
           with:
              fetch-depth: 0

         - name: Setup Environment
           uses: ./.github/actions/setup-full-environment

         - name: Baseline Performance Test
           run: |
              git checkout ${{ github.event.pull_request.base.sha }}
              ./scripts/performance-baseline.sh

         - name: Feature Performance Test
           run: |
              git checkout ${{ github.event.pull_request.head.sha }}
              ./scripts/performance-test.sh

         - name: Compare Performance
           run: |
              ./scripts/compare-performance.sh

         - name: Comment Performance Results
           uses: actions/github-script@v6
           with:
              script: |
                 const fs = require('fs');
                 const performanceReport = fs.readFileSync('performance-report.md', 'utf8');

                 github.rest.issues.createComment({
                   issue_number: context.issue.number,
                   owner: context.repo.owner,
                   repo: context.repo.repo,
                   body: performanceReport
                 });
```

### 4. Service para Gestión de Features

**Feature management service:**

```php
<?php
// app/Application/Services/FeatureManagementService.php
namespace App\Application\Services;

use App\Domain\Features\Entities\FeatureRequest;
use App\Domain\Features\ValueObjects\FeaturePriority;
use App\Domain\Features\Repositories\FeatureRequestRepositoryInterface;

class FeatureManagementService
{
    public function __construct(
        private FeatureRequestRepositoryInterface $featureRepository,
        private ArchitectureAnalysisService $architectureAnalysis,
        private EstimationService $estimationService,
        private NotificationService $notificationService
    ) {}

    public function submitFeatureRequest(
        string $title,
        string $description,
        FeaturePriority $priority,
        string $requesterId,
        array $acceptanceCriteria
    ): FeatureRequest {
        $featureRequest = FeatureRequest::create(
            title: $title,
            description: $description,
            priority: $priority,
            requesterId: $requesterId,
            acceptanceCriteria: $acceptanceCriteria
        );

        $this->featureRepository->save($featureRequest);
        
        // Notificar al equipo de producto
        $this->notificationService->notifyNewFeatureRequest($featureRequest);
        
        return $featureRequest;
    }

    public function analyzeFeatureRequest(string $featureId): array
    {
        $feature = $this->featureRepository->findById($featureId);
        
        // Análisis de impacto arquitectónico
        $architecturalImpact = $this->architectureAnalysis->analyzeImpact(
            $feature->getDescription(),
            $feature->getAcceptanceCriteria()
        );
        
        // Estimación de esfuerzo
        $estimation = $this->estimationService->estimateFeature($feature);
        
        // Análisis de dependencias
        $dependencies = $this->analyzeDependencies($feature);
        
        // Análisis de riesgos
        $risks = $this->analyzeRisks($feature, $architecturalImpact);
        
        $analysis = [
            'architectural_impact' => $architecturalImpact,
            'estimation' => $estimation,
            'dependencies' => $dependencies,
            'risks' => $risks,
            'recommendations' => $this->generateRecommendations($feature, $architecturalImpact, $risks)
        ];
        
        // Actualizar feature con análisis
        $feature->analyze($estimation['complexity'], $estimation['hours']);
        $this->featureRepository->save($feature);
        
        return $analysis;
    }

    public function createFeatureBranch(string $featureId): string
    {
        $feature = $this->featureRepository->findById($featureId);
        
        if (!$feature->isApproved()) {
            throw new \DomainException('Feature must be approved before development');
        }
        
        $branchName = 'feature/' . $this->slugify($feature->getTitle());
        
        // Crear branch usando Git API
        $this->gitService->createBranch($branchName, 'develop');
        
        // Crear estructura inicial de archivos
        $this->scaffoldFeature($feature, $branchName);
        
        return $branchName;
    }

    private function analyzeDependencies(FeatureRequest $feature): array
    {
        // Analizar dependencias con otras features
        $allFeatures = $this->featureRepository->findActive();
        $dependencies = [];
        
        foreach ($allFeatures as $existingFeature) {
            if ($this->hasDependency($feature, $existingFeature)) {
                $dependencies[] = [
                    'feature_id' => $existingFeature->getId(),
                    'title' => $existingFeature->getTitle(),
                    'type' => $this->getDependencyType($feature, $existingFeature)
                ];
            }
        }
        
        return $dependencies;
    }

    private function scaffoldFeature(FeatureRequest $feature, string $branchName): void
    {
        $featureName = $this->slugify($feature->getTitle());
        
        // Crear estructura de archivos para la feature
        $scaffoldStructure = [
            "app/Domain/{$featureName}/",
            "app/Application/UseCases/{$featureName}/",
            "app/Infrastructure/Http/Controllers/Api/V1/{$featureName}/",
            "tests/Unit/Domain/{$featureName}/",
            "tests/Feature/{$featureName}/",
            "database/migrations/",
            "resources/js/pages/{$featureName}/",
            "resources/js/components/{$featureName}/"
        ];
        
        foreach ($scaffoldStructure as $path) {
            $this->gitService->createDirectory($branchName, $path);
        }
        
        // Generar archivos boilerplate
        $this->generateBoilerplateFiles($feature, $branchName);
    }
}
```

## Tips

### Planificación de Features

- **MVP first**: Comienza con la versión mínima viable
- **Incremental delivery**: Entrega valor en incrementos pequeños
- **User feedback**: Incorpora feedback de usuarios desde etapas tempranas
- **Technical debt**: Considera el impacto en deuda técnica

### Desarrollo Arquitectónico

- **Domain first**: Comienza por el dominio y las reglas de negocio
- **Layer isolation**: Mantén la independencia entre capas
- **Interface segregation**: Define interfaces claras entre componentes
- **Test-driven**: Usa TDD para guiar el diseño

### Feature Flags Best Practices

- **Gradual rollout**: Usa rollout gradual para mitigar riesgos
- **Kill switches**: Implementa capacidad de desactivar features rápidamente
- **Cleanup**: Remueve flags obsoletos regularmente
- **Monitoring**: Monitorea el impacto de nuevas features

### Gestión de Riesgos

- **Impact analysis**: Analiza el impacto en funcionalidad existente
- **Rollback strategy**: Siempre ten un plan de rollback
- **Performance monitoring**: Monitorea el impacto en performance
- **Canary deployments**: Usa despliegues canary para features grandes

## Ejemplos

### 1. Feature Flag en Frontend React

```typescript
// src/hooks/useFeatureFlag.ts
import { useContext, useEffect, useState } from "react";
import { FeatureFlagContext } from "../contexts/FeatureFlagContext";

export const useFeatureFlag = (flagName: string): boolean => {
   const { isEnabled, loading } = useContext(FeatureFlagContext);
   const [enabled, setEnabled] = useState<boolean>(false);

   useEffect(() => {
      if (!loading) {
         setEnabled(isEnabled(flagName));
      }
   }, [flagName, isEnabled, loading]);

   return enabled;
};

// src/contexts/FeatureFlagContext.tsx
interface FeatureFlagContextType {
   isEnabled: (flagName: string) => boolean;
   loading: boolean;
   refresh: () => Promise<void>;
}

export const FeatureFlagContext = createContext<FeatureFlagContextType>({
   isEnabled: () => false,
   loading: true,
   refresh: async () => {},
});

export const FeatureFlagProvider: React.FC<{ children: ReactNode }> = (
   { children },
) => {
   const [flags, setFlags] = useState<Record<string, boolean>>({});
   const [loading, setLoading] = useState(true);

   const loadFlags = useCallback(async () => {
      try {
         const response = await fetch("/api/feature-flags");
         const flagData = await response.json();
         setFlags(flagData);
      } catch (error) {
         console.error("Failed to load feature flags:", error);
      } finally {
         setLoading(false);
      }
   }, []);

   useEffect(() => {
      loadFlags();
   }, [loadFlags]);

   const isEnabled = useCallback((flagName: string): boolean => {
      return flags[flagName] ?? false;
   }, [flags]);

   return (
      <FeatureFlagContext.Provider
         value={{ isEnabled, loading, refresh: loadFlags }}
      >
         {children}
      </FeatureFlagContext.Provider>
   );
};

// Ejemplo de uso en componente
const NewFeatureComponent: React.FC = () => {
   const isNewDashboardEnabled = useFeatureFlag("new_dashboard_ui");
   const isAdvancedFiltersEnabled = useFeatureFlag("advanced_filters");

   if (!isNewDashboardEnabled) {
      return <LegacyDashboard />;
   }

   return (
      <div className="new-dashboard">
         <DashboardHeader />
         <DashboardContent />
         {isAdvancedFiltersEnabled && <AdvancedFilters />}
      </div>
   );
};
```

### 2. Test de Feature con Feature Flags

```php
<?php
// tests/Feature/FeatureFlagTest.php
namespace Tests\Feature;

use Tests\TestCase;
use App\Infrastructure\FeatureFlags\FeatureFlagService;

class FeatureFlagTest extends TestCase
{
    private FeatureFlagService $featureFlags;

    protected function setUp(): void
    {
        parent::setUp();
        $this->featureFlags = app(FeatureFlagService::class);
    }

    /** @test */
    public function new_user_profile_feature_is_disabled_by_default(): void
    {
        $response = $this->getJson('/api/user/profile-new');
        
        $response->assertStatus(404)
                ->assertJson(['error' => 'Feature not available']);
    }

    /** @test */
    public function new_user_profile_feature_works_when_enabled(): void
    {
        // Habilitar feature para este test
        $this->featureFlags->enableGlobally('new_user_profile');
        
        $user = User::factory()->create();
        $this->actingAs($user);
        
        $response = $this->getJson('/api/user/profile-new');
        
        $response->assertStatus(200)
                ->assertJsonStructure([
                    'data' => [
                        'id',
                        'name',
                        'email',
                        'new_profile_features' // Nueva estructura
                    ]
                ]);
    }

    /** @test */
    public function feature_rollout_works_for_percentage_of_users(): void
    {
        // Configurar 50% rollout
        $this->featureFlags->setPercentageRollout('new_user_profile', 50);
        
        $enabledUsers = 0;
        $totalUsers = 100;
        
        for ($i = 0; $i < $totalUsers; $i++) {
            $user = User::factory()->create();
            
            if ($this->featureFlags->isEnabled('new_user_profile', $user->id)) {
                $enabledUsers++;
            }
        }
        
        // Debe estar cerca del 50% (con margen de error)
        $this->assertGreaterThan(40, $enabledUsers);
        $this->assertLessThan(60, $enabledUsers);
    }
}
```

### 3. Pipeline de CI/CD para Features

```yaml
# .github/workflows/feature-release.yml
name: Feature Release Pipeline

on:
   push:
      tags: ["feature-*"]

jobs:
   validate-feature:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Extract Feature Name
           id: feature
           run: |
              FEATURE_NAME=$(echo ${GITHUB_REF#refs/tags/feature-} | tr '[:upper:]' '[:lower:]')
              echo "name=$FEATURE_NAME" >> $GITHUB_OUTPUT

         - name: Validate Feature Implementation
           run: |
              ./scripts/validate-feature-completeness.sh "${{ steps.feature.outputs.name }}"

         - name: Check Feature Flag Configuration
           run: |
              ./scripts/check-feature-flag-config.sh "${{ steps.feature.outputs.name }}"

   deploy-with-feature-flag:
      needs: validate-feature
      runs-on: ubuntu-latest
      environment: production
      steps:
         - uses: actions/checkout@v3

         - name: Deploy Application
           run: |
              # Deploy normal application
              ./scripts/deploy-production.sh

         - name: Configure Feature Flag
           run: |
              # Inicialmente deshabilitado en producción
              FEATURE_NAME="${{ needs.validate-feature.outputs.feature.name }}"
              curl -X POST ${{ secrets.FEATURE_FLAG_API_URL }}/flags \
                -H "Authorization: Bearer ${{ secrets.FEATURE_FLAG_TOKEN }}" \
                -H "Content-Type: application/json" \
                -d "{
                  \"name\": \"$FEATURE_NAME\",
                  \"enabled\": false,
                  \"environment\": \"production\"
                }"

         - name: Gradual Rollout
           run: |
              # Habilitar para 1% de usuarios inicialmente
              FEATURE_NAME="${{ needs.validate-feature.outputs.feature.name }}"
              ./scripts/gradual-rollout.sh "$FEATURE_NAME" 1

         - name: Monitor Initial Rollout
           run: |
              # Monitorear por 30 minutos
              ./scripts/monitor-feature-rollout.sh "${{ needs.validate-feature.outputs.feature.name }}" 30

         - name: Increase Rollout on Success
           if: success()
           run: |
              # Si todo va bien, aumentar a 10%
              ./scripts/gradual-rollout.sh "${{ needs.validate-feature.outputs.feature.name }}" 10

   post-deployment-validation:
      needs: deploy-with-feature-flag
      runs-on: ubuntu-latest
      steps:
         - name: Run Smoke Tests
           run: |
              ./scripts/smoke-tests-production.sh

         - name: Validate Feature Metrics
           run: |
              ./scripts/validate-feature-metrics.sh "${{ needs.validate-feature.outputs.feature.name }}"

         - name: Alert on Issues
           if: failure()
           run: |
              curl -X POST ${{ secrets.SLACK_WEBHOOK }} \
                -H 'Content-type: application/json' \
                --data "{
                  \"text\": \"🚨 Feature deployment issue: ${{ needs.validate-feature.outputs.feature.name }}\",
                  \"channel\": \"#alerts\"
                }"
```

## Navegación

**Progreso en Mantenimiento y Evolución:**

- ✅ [Mantenimiento y Evolución](./mantenimiento-evolucion.md)
- ✅ [Monitoreo Post-Deployment](./monitoreo-post-deployment.md)
- ✅ [Bug Fixes y Hotfixes](./bug-fixes-hotfixes.md)
- ✅ [Actualización de Dependencias](./actualizacion-dependencias.md)
- ✅ **Gestión de Nuevas Funcionalidades** ← Estás aquí
- ⏭️ [Documentación y Knowledge Transfer](./documentacion-knowledge-transfer.md)
- ⏭️ [Métricas y Analytics de Uso](./metricas-analytics-uso.md)

---

### Siguiente Paso

Continúa con
[**Documentación y Knowledge Transfer**](./documentacion-knowledge-transfer.md)

[⬅️ Actualización de Dependencias](./actualizacion-dependencias.md) |
[🏠 README Principal](../../README.md) |
[➡️ Documentación y Knowledge Transfer](./documentacion-knowledge-transfer.md)
