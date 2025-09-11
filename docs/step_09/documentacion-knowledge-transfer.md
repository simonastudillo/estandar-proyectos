# Documentación y Knowledge Transfer

## ¿Qué es?

La documentación y knowledge transfer es el proceso sistemático de capturar,
organizar y transferir el conocimiento técnico y de negocio del sistema. En el
contexto de Clean Architecture + DDD, implica documentar no solo el código y
APIs, sino también las decisiones arquitectónicas, las reglas de dominio, los
bounded contexts y los patrones de diseño implementados.

Este proceso asegura que el conocimiento crítico del sistema no se pierda con la
rotación del equipo y facilita el onboarding de nuevos desarrolladores,
manteniendo la continuidad del proyecto a lo largo del tiempo.

## ¿Por qué es importante?

- **Continuidad del proyecto**: Preserva el conocimiento crítico ante cambios de
  equipo
- **Onboarding eficiente**: Acelera la incorporación de nuevos desarrolladores
- **Mantenibilidad**: Facilita el mantenimiento y evolución del sistema
- **Decisiones informadas**: Documenta el contexto de decisiones arquitectónicas
- **Compliance**: Cumple con requisitos de auditoría y governance
- **Reducción de riesgos**: Mitiga el riesgo de pérdida de conocimiento crítico
- **Calidad del código**: Promueve mejores prácticas de desarrollo

## ¿Qué debe incluir?

### Documentación Técnica

- Architecture Decision Records (ADRs)
- Documentación de APIs (OpenAPI/Swagger)
- Diagramas de arquitectura actualizados
- Guías de desarrollo y coding standards
- Documentación de base de datos y migraciones

### Documentación de Dominio

- Bounded contexts y context maps
- Entidades y value objects del dominio
- Reglas de negocio y casos de uso
- Eventos de dominio y workflows
- Glosario de términos del negocio

### Procesos y Workflows

- Procesos de desarrollo y despliegue
- Workflows de CI/CD
- Procedimientos de hotfix y release
- Guías de troubleshooting
- Runbooks operacionales

### Knowledge Transfer

- Sesiones de transferencia estructuradas
- Pair programming y shadowing
- Documentación de componentes críticos
- Videos explicativos y demos
- Q&A sessions y workshops

## ¿Qué debo hacer?

### 1. Implementar Architecture Decision Records

**Estructura de ADR:**

````markdown
# ADR-001: Implementación de Clean Architecture

## Status

Accepted

## Context

Necesitamos una arquitectura que permita independencia del framework,
testabilidad y mantenibilidad a largo plazo.

## Decision

Implementaremos Clean Architecture con Domain-Driven Design, organizando el
código en capas claramente separadas.

## Consequences

### Positive

- Mayor testabilidad
- Independencia del framework
- Código más mantenible
- Separación clara de responsabilidades

### Negative

- Mayor complejidad inicial
- Curva de aprendizaje para el equipo
- Más archivos y estructura

## Implementation

```bash
app/
├── Domain/          # Entidades y reglas de negocio
├── Application/     # Casos de uso
└── Infrastructure/  # Implementaciones técnicas
```
````

## References

- [Clean Architecture by Robert Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Domain-Driven Design by Eric Evans](https://domainlanguage.com/ddd/)

````
**Template para nuevos ADRs:**
```markdown
# scripts/create-adr.sh
#!/bin/bash

ADR_NUMBER=$1
TITLE=$2

if [ -z "$ADR_NUMBER" ] || [ -z "$TITLE" ]; then
    echo "Usage: ./create-adr.sh <number> <title>"
    exit 1
fi

ADR_FILE="docs/architecture/adr-$(printf "%03d" $ADR_NUMBER)-$(echo $TITLE | tr '[:upper:]' '[:lower:]' | tr ' ' '-').md"

cat > "$ADR_FILE" << EOF
# ADR-$(printf "%03d" $ADR_NUMBER): $TITLE

## Status
Proposed

## Context
[Describe the architectural challenge or problem]

## Decision
[Describe the architectural decision made]

## Consequences
### Positive
- [List positive consequences]

### Negative
- [List negative consequences]

## Implementation
[Describe implementation details]

## References
- [Links to relevant documentation or resources]

---
Created: $(date +%Y-%m-%d)
Authors: [Author names]
EOF

echo "ADR created: $ADR_FILE"
````

### 2. Documentación Automática de APIs

**Laravel API documentation:**

```php
<?php
// app/Infrastructure/Http/Controllers/Api/V1/UserController.php
namespace App\Infrastructure\Http\Controllers\Api\V1;

/**
 * @OA\Tag(
 *     name="Users",
 *     description="User management operations"
 * )
 */
class UserController extends Controller
{
    /**
     * @OA\Get(
     *     path="/api/v1/users",
     *     summary="Get users list",
     *     tags={"Users"},
     *     @OA\Parameter(
     *         name="page",
     *         in="query",
     *         description="Page number",
     *         required=false,
     *         @OA\Schema(type="integer", minimum=1, default=1)
     *     ),
     *     @OA\Parameter(
     *         name="per_page",
     *         in="query",
     *         description="Items per page",
     *         required=false,
     *         @OA\Schema(type="integer", minimum=1, maximum=100, default=15)
     *     ),
     *     @OA\Response(
     *         response=200,
     *         description="Users retrieved successfully",
     *         @OA\JsonContent(
     *             type="object",
     *             @OA\Property(
     *                 property="data",
     *                 type="array",
     *                 @OA\Items(ref="#/components/schemas/User")
     *             ),
     *             @OA\Property(
     *                 property="meta",
     *                 ref="#/components/schemas/PaginationMeta"
     *             )
     *         )
     *     ),
     *     @OA\Response(
     *         response=401,
     *         description="Unauthenticated",
     *         @OA\JsonContent(ref="#/components/schemas/Error")
     *     )
     * )
     */
    public function index(ListUsersRequest $request): JsonResponse
    {
        $users = $this->listUsersUseCase->execute(
            $request->validated()
        );

        return UserResource::collection($users)
            ->response()
            ->setStatusCode(200);
    }
}

/**
 * @OA\Schema(
 *     schema="User",
 *     type="object",
 *     title="User",
 *     description="User model",
 *     @OA\Property(property="id", type="string", format="uuid", description="User ID"),
 *     @OA\Property(property="name", type="string", description="User full name"),
 *     @OA\Property(property="email", type="string", format="email", description="User email"),
 *     @OA\Property(property="created_at", type="string", format="date-time", description="Creation timestamp"),
 *     @OA\Property(property="updated_at", type="string", format="date-time", description="Last update timestamp")
 * )
 */
```

### 3. Sistema de Documentación Living

**Generación automática de documentación:**

```bash
#!/bin/bash
# scripts/generate-docs.sh

echo "📚 Generando documentación del proyecto..."

# Crear directorio de documentación
mkdir -p docs/generated

# Generar documentación de APIs
echo "🔌 Generando documentación de APIs..."
cd backend
php artisan l5-swagger:generate
cp storage/api-docs/api-docs.json ../docs/generated/api-docs.json
cd ..

# Generar documentación de base de datos
echo "🗄️ Generando documentación de base de datos..."
./scripts/generate-db-docs.sh

# Generar documentación de componentes React
echo "⚛️ Generando documentación de componentes..."
cd frontend
npm run build-storybook
npm run docs:generate
cp -r docs/* ../docs/generated/frontend/
cd ..

# Generar métricas de código
echo "📊 Generando métricas de código..."
./scripts/generate-code-metrics.sh

# Generar documentación de arquitectura
echo "🏗️ Actualizando diagramas de arquitectura..."
./scripts/generate-architecture-diagrams.sh

# Compilar documentación final
echo "📖 Compilando documentación final..."
./scripts/compile-documentation.sh

echo "✅ Documentación generada exitosamente en docs/generated/"
```

**Documentation service:**

```php
<?php
// app/Application/Services/DocumentationService.php
namespace App\Application\Services;

class DocumentationService
{
    public function __construct(
        private CodeAnalyzer $codeAnalyzer,
        private ArchitectureDiagramGenerator $diagramGenerator,
        private ChangelogGenerator $changelogGenerator
    ) {}

    public function generateProjectDocumentation(): array
    {
        return [
            'architecture' => $this->generateArchitectureDocumentation(),
            'apis' => $this->generateApiDocumentation(),
            'database' => $this->generateDatabaseDocumentation(),
            'business_rules' => $this->generateBusinessRulesDocumentation(),
            'deployment' => $this->generateDeploymentDocumentation(),
        ];
    }

    public function generateOnboardingGuide(string $role): string
    {
        $sections = [];
        
        switch ($role) {
            case 'backend_developer':
                $sections = [
                    'architecture_overview',
                    'domain_model',
                    'development_setup',
                    'coding_standards',
                    'testing_guidelines',
                    'deployment_process'
                ];
                break;
                
            case 'frontend_developer':
                $sections = [
                    'frontend_architecture',
                    'component_library',
                    'state_management',
                    'development_setup',
                    'design_system',
                    'testing_guidelines'
                ];
                break;
                
            case 'devops_engineer':
                $sections = [
                    'infrastructure_overview',
                    'deployment_pipeline',
                    'monitoring_setup',
                    'security_guidelines',
                    'troubleshooting_guides'
                ];
                break;
        }
        
        return $this->compileOnboardingGuide($sections);
    }

    private function generateArchitectureDocumentation(): array
    {
        return [
            'overview' => $this->getArchitectureOverview(),
            'layers' => $this->getLayerDocumentation(),
            'bounded_contexts' => $this->getBoundedContexts(),
            'design_patterns' => $this->getDesignPatterns(),
            'decisions' => $this->getArchitectureDecisions()
        ];
    }

    private function generateBusinessRulesDocumentation(): array
    {
        $domainServices = $this->codeAnalyzer->findDomainServices();
        $entities = $this->codeAnalyzer->findDomainEntities();
        $valueObjects = $this->codeAnalyzer->findValueObjects();
        $useCases = $this->codeAnalyzer->findUseCases();
        
        return [
            'domain_model' => [
                'entities' => $this->documentEntities($entities),
                'value_objects' => $this->documentValueObjects($valueObjects),
                'domain_services' => $this->documentDomainServices($domainServices)
            ],
            'use_cases' => $this->documentUseCases($useCases),
            'business_rules' => $this->extractBusinessRules($domainServices, $entities),
            'workflows' => $this->extractWorkflows($useCases)
        ];
    }
}
```

### 4. Knowledge Transfer Framework

**Structured knowledge transfer process:**

```php
<?php
// app/Application/Services/KnowledgeTransferService.php
namespace App\Application\Services;

class KnowledgeTransferService
{
    public function createTransferPlan(
        string $departingMemberId,
        string $receivingMemberId,
        array $knowledgeAreas
    ): KnowledgeTransferPlan {
        $plan = KnowledgeTransferPlan::create([
            'departing_member_id' => $departingMemberId,
            'receiving_member_id' => $receivingMemberId,
            'knowledge_areas' => $knowledgeAreas,
            'estimated_duration' => $this->estimateTransferDuration($knowledgeAreas),
            'sessions' => $this->planTransferSessions($knowledgeAreas)
        ]);

        return $plan;
    }

    public function generateTransferChecklist(array $knowledgeAreas): array
    {
        $checklist = [];
        
        foreach ($knowledgeAreas as $area) {
            $checklist[$area] = [
                'documentation_review' => false,
                'code_walkthrough' => false,
                'hands_on_session' => false,
                'qa_session' => false,
                'independent_task' => false,
                'knowledge_validation' => false
            ];
        }
        
        return $checklist;
    }

    public function scheduleTransferSessions(
        KnowledgeTransferPlan $plan
    ): array {
        $sessions = [];
        
        foreach ($plan->getSessions() as $sessionConfig) {
            $session = TransferSession::create([
                'plan_id' => $plan->getId(),
                'type' => $sessionConfig['type'],
                'duration' => $sessionConfig['duration'],
                'topics' => $sessionConfig['topics'],
                'materials' => $this->prepareSessionMaterials($sessionConfig['topics']),
                'scheduled_date' => $this->findAvailableSlot($plan->getParticipants())
            ]);
            
            $sessions[] = $session;
        }
        
        return $sessions;
    }

    private function prepareSessionMaterials(array $topics): array
    {
        $materials = [];
        
        foreach ($topics as $topic) {
            $materials[$topic] = [
                'documentation' => $this->findRelevantDocumentation($topic),
                'code_examples' => $this->findCodeExamples($topic),
                'diagrams' => $this->findRelevantDiagrams($topic),
                'exercises' => $this->createHandsOnExercises($topic)
            ];
        }
        
        return $materials;
    }
}
```

## Tips

### Documentación Efectiva

- **Living documentation**: Mantén la documentación sincronizada con el código
- **Audience-specific**: Adapta la documentación a diferentes audiencias
- **Visual aids**: Usa diagramas y ejemplos para clarificar conceptos
- **Searchable**: Organiza la documentación para fácil búsqueda

### Knowledge Transfer

- **Gradual transfer**: Transfiere conocimiento gradualmente, no todo de una vez
- **Hands-on learning**: Combina teoría con práctica
- **Documentation first**: Documenta antes de transferir
- **Validation**: Valida que el conocimiento fue transferido efectivamente

### Mantenimiento de Documentación

- **Automated updates**: Automatiza la generación de documentación cuando sea
  posible
- **Review cycles**: Establece ciclos regulares de revisión
- **Ownership**: Asigna ownership de documentación a equipos específicos
- **Feedback loops**: Recolecta feedback sobre utilidad de la documentación

### Herramientas y Estándares

- **Standard formats**: Usa formatos estándar (Markdown, OpenAPI, etc.)
- **Version control**: Versiona la documentación junto con el código
- **Centralized location**: Mantén toda la documentación en ubicación central
- **Access control**: Controla acceso según sensibilidad de la información

## Ejemplos

### 1. Template de Onboarding

```markdown
# Onboarding Guide - Backend Developer

## Welcome to the Team! 🎉

Esta guía te ayudará a familiarizarte con nuestro proyecto y comenzar a
contribuir efectivamente.

## Week 1: Foundation

### Day 1-2: Environment Setup

- [ ] Configurar entorno de desarrollo local
- [ ] Clonar repositorios y configurar accesos
- [ ] Ejecutar tests y verificar setup
- [ ] Revisar documentación de arquitectura

**Resources:**

- [Development Setup Guide](./development-setup.md)
- [Architecture Overview](./architecture/overview.md)

### Day 3-5: Architecture Deep Dive

- [ ] Estudiar Clean Architecture principles
- [ ] Entender Domain-Driven Design concepts
- [ ] Revisar bounded contexts del proyecto
- [ ] Analizar estructura de carpetas

**Activities:**

- Pair programming session con tech lead
- Code walkthrough de componentes principales
- Q&A session sobre decisiones arquitectónicas

## Week 2: Domain Understanding

### Day 1-3: Business Domain

- [ ] Estudiar reglas de negocio principales
- [ ] Entender entidades y value objects
- [ ] Revisar casos de uso implementados
- [ ] Analizar eventos de dominio

**Resources:**

- [Domain Model Documentation](./domain/model.md)
- [Business Rules Catalog](./domain/business-rules.md)

### Day 4-5: First Contribution

- [ ] Pick up starter ticket
- [ ] Implement feature following standards
- [ ] Write comprehensive tests
- [ ] Create pull request

**Mentorship:**

- Daily check-ins con mentor asignado
- Code review detallado de primera contribución

## Week 3-4: Independence

### Goals:

- [ ] Contribute independently to features
- [ ] Participate in code reviews
- [ ] Help with bug fixes
- [ ] Understand deployment process

### Success Criteria:

- Can implement small features without guidance
- Understands testing strategies
- Follows coding standards consistently
- Contributes to team discussions

## Resources

### Documentation

- [API Documentation](./api/docs.html)
- [Database Schema](./database/schema.md)
- [Coding Standards](./standards/coding.md)
- [Testing Guidelines](./standards/testing.md)

### Tools

- [Local Development](./tools/local-dev.md)
- [Debugging Guide](./tools/debugging.md)
- [Performance Profiling](./tools/profiling.md)

### Contacts

- **Tech Lead**: John Doe (@johndoe)
- **Mentor**: Jane Smith (@janesmith)
- **DevOps**: Bob Wilson (@bobwilson)

## Feedback

Please provide feedback on this onboarding process to help us improve it for
future team members.
```

### 2. API Documentation Generator

```php
<?php
// scripts/generate-api-docs.php

use L5Swagger\L5SwaggerServiceProvider;
use OpenApi\Generator;

class ApiDocumentationGenerator
{
    public function generateDocumentation(): void
    {
        echo "Generating API documentation...\n";
        
        // Generate OpenAPI documentation
        $this->generateOpenApiDocs();
        
        // Generate Postman collection
        $this->generatePostmanCollection();
        
        // Generate API client examples
        $this->generateClientExamples();
        
        // Generate API changelog
        $this->generateApiChangelog();
        
        echo "API documentation generated successfully!\n";
    }
    
    private function generateOpenApiDocs(): void
    {
        $openapi = Generator::scan([
            app_path('Infrastructure/Http/Controllers/Api'),
            app_path('Infrastructure/Http/Resources'),
            app_path('Infrastructure/Http/Requests')
        ]);
        
        file_put_contents(
            storage_path('api-docs/api-docs.json'),
            $openapi->toJson()
        );
        
        // Generate HTML documentation
        $this->generateHtmlDocs($openapi);
    }
    
    private function generatePostmanCollection(): void
    {
        $openApiSpec = json_decode(
            file_get_contents(storage_path('api-docs/api-docs.json')),
            true
        );
        
        $postmanCollection = [
            'info' => [
                'name' => $openApiSpec['info']['title'],
                'description' => $openApiSpec['info']['description'],
                'version' => $openApiSpec['info']['version']
            ],
            'item' => $this->convertPathsToPostmanItems($openApiSpec['paths'])
        ];
        
        file_put_contents(
            storage_path('api-docs/postman-collection.json'),
            json_encode($postmanCollection, JSON_PRETTY_PRINT)
        );
    }
    
    private function generateClientExamples(): void
    {
        $languages = ['javascript', 'php', 'python', 'curl'];
        
        foreach ($languages as $language) {
            $this->generateClientExample($language);
        }
    }
    
    private function generateClientExample(string $language): void
    {
        $examples = [];
        $openApiSpec = json_decode(
            file_get_contents(storage_path('api-docs/api-docs.json')),
            true
        );
        
        foreach ($openApiSpec['paths'] as $path => $methods) {
            foreach ($methods as $method => $spec) {
                $examples[] = $this->generateMethodExample($path, $method, $spec, $language);
            }
        }
        
        file_put_contents(
            storage_path("api-docs/examples-{$language}.md"),
            implode("\n\n", $examples)
        );
    }
}
```

### 3. Automated Documentation Pipeline

```yaml
# .github/workflows/documentation.yml
name: Documentation Generation

on:
   push:
      branches: [main, develop]
      paths:
         - "backend/app/**"
         - "frontend/src/**"
         - "docs/**"
   schedule:
      - cron: "0 2 * * *" # Daily at 2 AM

jobs:
   generate-docs:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3
           with:
              fetch-depth: 0

         - name: Setup Environment
           uses: ./.github/actions/setup-full-environment

         - name: Generate API Documentation
           run: |
              cd backend
              php artisan l5-swagger:generate
              cd ..

         - name: Generate Database Documentation
           run: |
              ./scripts/generate-db-docs.sh

         - name: Generate Frontend Component Docs
           run: |
              cd frontend
              npm run build-storybook
              npm run docs:generate
              cd ..

         - name: Generate Architecture Diagrams
           run: |
              pip install diagrams
              python scripts/generate-architecture-diagrams.py

         - name: Generate Code Metrics
           run: |
              cd backend
              ./vendor/bin/phpmetrics --report-html=../docs/generated/metrics/backend .
              cd ../frontend
              npm run analyze
              cd ..

         - name: Compile Documentation Site
           run: |
              ./scripts/build-documentation-site.sh

         - name: Deploy to GitHub Pages
           uses: peaceiris/actions-gh-pages@v3
           with:
              github_token: ${{ secrets.GITHUB_TOKEN }}
              publish_dir: ./docs/site

         - name: Update Documentation Index
           run: |
              ./scripts/update-doc-search-index.sh

         - name: Notify Team
           if: always()
           run: |
              STATUS="${{ job.status }}"
              curl -X POST ${{ secrets.SLACK_WEBHOOK }} \
                -H 'Content-type: application/json' \
                --data "{
                  \"text\": \"📚 Documentation update $STATUS\",
                  \"attachments\": [{
                    \"color\": \"$([ '$STATUS' = 'success' ] && echo 'good' || echo 'danger')\",
                    \"fields\": [{
                      \"title\": \"Commit\",
                      \"value\": \"${{ github.event.head_commit.message }}\",
                      \"short\": false
                    }]
                  }]
                }"

   validate-docs:
      runs-on: ubuntu-latest
      steps:
         - uses: actions/checkout@v3

         - name: Check Documentation Links
           run: |
              npm install -g markdown-link-check
              find docs -name "*.md" -exec markdown-link-check {} \;

         - name: Validate API Documentation
           run: |
              npx swagger-cli validate docs/generated/api-docs.json

         - name: Check Documentation Coverage
           run: |
              ./scripts/check-documentation-coverage.sh

         - name: Spell Check
           uses: streetsidesoftware/cspell-action@v2
           with:
              files: "docs/**/*.md"
              config: ".cspell.json"
```

## Navegación

**Progreso en Mantenimiento y Evolución:**

- ✅ [Mantenimiento y Evolución](./mantenimiento-evolucion.md)
- ✅ [Monitoreo Post-Deployment](./monitoreo-post-deployment.md)
- ✅ [Bug Fixes y Hotfixes](./bug-fixes-hotfixes.md)
- ✅ [Actualización de Dependencias](./actualizacion-dependencias.md)
- ✅ [Gestión de Nuevas Funcionalidades](./gestion-nuevas-funcionalidades.md)
- ✅ **Documentación y Knowledge Transfer** ← Estás aquí
- ⏭️ [Métricas y Analytics de Uso](./metricas-analytics-uso.md)

---

### Siguiente Paso

Continúa con [**Métricas y Analytics de Uso**](./metricas-analytics-uso.md)

[⬅️ Gestión de Nuevas Funcionalidades](./gestion-nuevas-funcionalidades.md) |
[🏠 README Principal](../../README.md) |
[➡️ Métricas y Analytics de Uso](./metricas-analytics-uso.md)
