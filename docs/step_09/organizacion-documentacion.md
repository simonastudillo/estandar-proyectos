# Organización de la Documentación

## ¿Qué es?

La **organización de la documentación** es el sistema estructurado para
categorizar, jerarquizar y mantener toda la información técnica y de negocio del
proyecto de manera que sea fácilmente navegable, encontrable y mantenible. En el
contexto de Clean Architecture + DDD, esto implica organizar la documentación
reflejando las capas arquitectónicas, bounded contexts, y flujos de negocio del
sistema.

Esta organización actúa como la **arquitectura de la información** del proyecto,
permitiendo que cualquier stakeholder encuentre rápidamente la información que
necesita, desde especificaciones técnicas hasta guías de usuario, manteniendo
coherencia y evitando duplicación.

## ¿Por qué es importante?

### 🎯 **Findabilidad y Accesibilidad**

- Permite encontrar información específica rápidamente
- Reduce tiempo perdido buscando documentación dispersa
- Facilita el acceso a información relevante para cada rol

### 📚 **Mantenimiento Eficiente**

- Evita duplicación de información en múltiples lugares
- Facilita actualizaciones coordinadas de documentación relacionada
- Reduce inconsistencias entre documentos relacionados

### 🧭 **Navegación Intuitiva**

- Estructura lógica que refleja el flujo de trabajo del proyecto
- Facilita el onboarding de nuevos miembros del equipo
- Permite escalamiento de la documentación sin perder coherencia

### 🔄 **Sincronización con Arquitectura**

- La estructura de documentación refleja la arquitectura del sistema
- Facilita la comprensión del sistema a través de su documentación
- Mantiene trazabilidad entre código y documentación

### 👥 **Colaboración Mejorada**

- Cada equipo sabe dónde documentar y encontrar información
- Facilita reviews y contribuciones a la documentación
- Establece ownership claro de diferentes secciones

## ¿Qué debe incluir?

### Estructura Jerárquica

- Taxonomía clara por tipo de contenido
- Niveles de información (overview → detalles)
- Agrupación lógica por dominio/módulo
- Separación por audiencia objetivo

### Sistema de Navegación

- Índices y tablas de contenido
- Enlaces cruzados entre documentos relacionados
- Breadcrumbs y rutas de navegación
- Sistema de etiquetas y categorización

### Metadatos de Documentos

- Fechas de creación y última actualización
- Autores y responsables de mantenimiento
- Versiones y historial de cambios
- Estado de la documentación (draft, review, approved)

### Convenciones de Naming

- Nomenclatura consistente para archivos y carpetas
- Prefijos y sufijos estandarizados
- Convenciones de URL/path amigables
- Sistema de versionado coherente

## ¿Qué debo hacer?

### 1. **Definir Taxonomía Base**

Establecer categorías principales y subcategorías:

```bash
# Crear estructura base de documentación
mkdir -p docs/{
architecture,
api,
business,
deployment,
development,
operations,
user-guides,
processes,
decisions
}
```

### 2. **Implementar Sistema de Navegación**

Crear índices y sistema de enlaces entre documentos.

### 3. **Establecer Convenciones**

Definir y documentar estándares de naming, estructura y formato.

## Estructura de Organización

## 📁 **Arquitectura Principal**

### Estructura Base Recomendada

```
docs/
├── README.md                     # Punto de entrada principal
├── INDEX.md                      # Índice maestro de toda la documentación
├── CONTRIBUTING.md               # Guía para contribuir a la documentación
│
├── 01-overview/                  # Visión general del proyecto
│   ├── project-summary.md
│   ├── architecture-overview.md
│   ├── technology-stack.md
│   └── getting-started.md
│
├── 02-architecture/              # Documentación arquitectónica
│   ├── clean-architecture.md
│   ├── domain-design.md
│   ├── bounded-contexts.md
│   ├── system-design.md
│   ├── diagrams/
│   │   ├── system-architecture.png
│   │   ├── domain-model.png
│   │   └── deployment-diagram.png
│   └── decisions/                # Architecture Decision Records (ADRs)
│       ├── 001-architecture-style.md
│       ├── 002-database-choice.md
│       └── template-adr.md
│
├── 03-business/                  # Documentación de negocio
│   ├── domain-model/
│   │   ├── entities.md
│   │   ├── value-objects.md
│   │   ├── aggregates.md
│   │   └── business-rules.md
│   ├── use-cases/
│   │   ├── user-management.md
│   │   ├── payment-processing.md
│   │   └── reporting.md
│   ├── workflows/
│   │   ├── order-fulfillment.md
│   │   └── user-onboarding.md
│   └── glossary.md               # Glosario de términos de negocio
│
├── 04-development/               # Guías de desarrollo
│   ├── setup/
│   │   ├── local-environment.md
│   │   ├── docker-setup.md
│   │   └── dependencies.md
│   ├── coding-standards/
│   │   ├── php-standards.md
│   │   ├── typescript-standards.md
│   │   ├── testing-standards.md
│   │   └── code-review-guide.md
│   ├── tools/
│   │   ├── ide-configuration.md
│   │   ├── debugging.md
│   │   └── profiling.md
│   └── workflows/
│       ├── git-workflow.md
│       ├── feature-development.md
│       └── bug-fixing.md
│
├── 05-api/                       # Documentación de APIs
│   ├── README.md                 # Overview de todas las APIs
│   ├── openapi/                  # Especificaciones OpenAPI
│   │   ├── v1/
│   │   │   ├── users.yaml
│   │   │   ├── orders.yaml
│   │   │   └── payments.yaml
│   │   └── v2/
│   │       └── users.yaml
│   ├── authentication.md         # Guías de autenticación
│   ├── rate-limiting.md
│   ├── versioning.md
│   └── examples/                 # Ejemplos de uso
│       ├── curl-examples.md
│       ├── postman-collection.json
│       └── sdk-examples/
│
├── 06-testing/                   # Documentación de testing
│   ├── testing-strategy.md
│   ├── unit-testing.md
│   ├── integration-testing.md
│   ├── e2e-testing.md
│   ├── performance-testing.md
│   └── test-data/
│       ├── fixtures/
│       └── factories/
│
├── 07-deployment/                # Documentación de deployment
│   ├── environments/
│   │   ├── development.md
│   │   ├── staging.md
│   │   └── production.md
│   ├── infrastructure/
│   │   ├── aws-setup.md
│   │   ├── kubernetes.md
│   │   └── monitoring.md
│   ├── ci-cd/
│   │   ├── github-actions.md
│   │   ├── deployment-pipeline.md
│   │   └── rollback-procedures.md
│   └── database/
│       ├── migrations.md
│       ├── backups.md
│       └── performance-tuning.md
│
├── 08-operations/                # Documentación operacional
│   ├── runbooks/
│   │   ├── incident-response.md
│   │   ├── backup-restore.md
│   │   └── scaling-procedures.md
│   ├── monitoring/
│   │   ├── metrics.md
│   │   ├── alerts.md
│   │   └── dashboards.md
│   ├── maintenance/
│   │   ├── dependency-updates.md
│   │   ├── security-patches.md
│   │   └── performance-optimization.md
│   └── troubleshooting/
│       ├── common-issues.md
│       ├── performance-issues.md
│       └── database-issues.md
│
├── 09-user-guides/               # Documentación para usuarios finales
│   ├── admin/
│   │   ├── user-management.md
│   │   ├── system-configuration.md
│   │   └── reporting.md
│   ├── end-user/
│   │   ├── getting-started.md
│   │   ├── feature-guides/
│   │   └── faq.md
│   └── api-consumers/
│       ├── quick-start.md
│       ├── integration-guide.md
│       └── best-practices.md
│
├── 10-processes/                 # Documentación de procesos
│   ├── development-process.md
│   ├── release-process.md
│   ├── incident-management.md
│   ├── change-management.md
│   └── quality-assurance.md
│
└── assets/                       # Recursos compartidos
    ├── images/
    ├── diagrams/
    ├── templates/
    └── styles/
```

## 🗂️ **Sistema de Categorización**

### Por Audiencia

```markdown
## Audiencias y Sus Documentos

### 👩‍💻 Desarrolladores

- `/development/` - Setup, coding standards, herramientas
- `/architecture/` - Decisiones técnicas, diagramas
- `/api/` - Especificaciones técnicas de APIs
- `/testing/` - Estrategias y guías de testing

### 🚀 DevOps/SRE

- `/deployment/` - Infraestructura, CI/CD
- `/operations/` - Runbooks, monitoreo
- `/processes/` - Procedimientos operacionales

### 📊 Product/Business

- `/business/` - Reglas de negocio, workflows
- `/user-guides/admin/` - Guías de administración
- `/overview/` - Visión general del producto

### 👥 End Users

- `/user-guides/end-user/` - Guías de usuario final
- `/user-guides/api-consumers/` - Para integradores

### 🏛️ Stakeholders/Management

- `/overview/` - Resúmenes ejecutivos
- `/architecture/decisions/` - Decisiones importantes
- `/business/` - Impacto de negocio
```

### Por Tipo de Contenido

```markdown
## Tipos de Contenido

### 📋 Especificaciones

- **Ubicación**: `/architecture/`, `/api/`, `/business/`
- **Formato**: Markdown con diagramas embebidos
- **Naming**: `specification-[nombre].md`

### 📖 Guías y Tutoriales

- **Ubicación**: `/development/`, `/user-guides/`
- **Formato**: Markdown con ejemplos de código
- **Naming**: `guide-[topic].md`, `tutorial-[topic].md`

### 🔧 Procedimientos

- **Ubicación**: `/operations/`, `/processes/`
- **Formato**: Markdown con checklists
- **Naming**: `procedure-[action].md`

### 🎯 Referencias

- **Ubicación**: `/api/`, `/development/tools/`
- **Formato**: Markdown o YAML (OpenAPI)
- **Naming**: `reference-[component].md`

### 🏗️ Diagramas

- **Ubicación**: `/assets/diagrams/`, `/architecture/diagrams/`
- **Formato**: PNG, SVG, Mermaid
- **Naming**: `[type]-[description].png`
```

## 📋 **Convenciones de Naming**

### Archivos y Carpetas

```bash
# Convenciones de nombres
docs/
├── kebab-case-for-folders/
├── kebab-case-for-files.md
├── UPPERCASE-for-root-files.md        # README, CONTRIBUTING, etc.
└── numbered-prefixes/                 # Para orden específico
    ├── 01-first-topic.md
    ├── 02-second-topic.md
    └── 99-appendix.md

# Ejemplos específicos:
# ✅ Buenos nombres
api-authentication.md
user-management-guide.md
deployment-procedure-production.md
architecture-decision-database.md

# ❌ Malos nombres
api_auth.md
UserMgmt.md
deploy.md
decision1.md
```

### Estructura de URLs

```markdown
# URLs amigables para documentación web

https://docs.project.com/ ├── overview/ ├── development/setup/local-environment
├── api/v1/users ├── deployment/environments/production └──
operations/runbooks/incident-response

# Estructura que refleja la organización de archivos
```

## 🔗 **Sistema de Navegación**

### Navegación Principal (README.md)

```markdown
# Documentación del Proyecto

## 🎯 Quick Start

- [Getting Started](./01-overview/getting-started.md)
- [Local Setup](./04-development/setup/local-environment.md)
- [API Overview](./05-api/README.md)

## 📚 Por Audiencia

### 👩‍💻 Desarrolladores

- [Development Guide](./04-development/)
- [Architecture](./02-architecture/)
- [Testing](./06-testing/)

### 🚀 DevOps

- [Deployment](./07-deployment/)
- [Operations](./08-operations/)

### 📊 Product/Business

- [Business Logic](./03-business/)
- [User Guides](./09-user-guides/)

## 📖 Por Tipo

- [📋 Specifications](./INDEX.md#specifications)
- [📖 Guides](./INDEX.md#guides)
- [🔧 Procedures](./INDEX.md#procedures)
- [🎯 References](./INDEX.md#references)

## 🔍 [Índice Completo](./INDEX.md)
```

### Índice Maestro (INDEX.md)

```markdown
# Índice Completo de Documentación

## 📊 Overview

| Documento                                                        | Audiencia  | Tipo          | Última Actualización |
| ---------------------------------------------------------------- | ---------- | ------------- | -------------------- |
| [Project Summary](./01-overview/project-summary.md)              | All        | Overview      | 2023-12-01           |
| [Architecture Overview](./02-architecture/clean-architecture.md) | Developers | Specification | 2023-11-28           |
| [API Reference](./05-api/README.md)                              | Developers | Reference     | 2023-12-01           |

## 🏗️ Architecture

- [Clean Architecture](./02-architecture/clean-architecture.md)
- [Domain Design](./02-architecture/domain-design.md)
- [System Design](./02-architecture/system-design.md)
- [ADRs](./02-architecture/decisions/)

## 💼 Business

- [Domain Model](./03-business/domain-model/)
- [Use Cases](./03-business/use-cases/)
- [Business Rules](./03-business/domain-model/business-rules.md)

## 👩‍💻 Development

- [Setup Guides](./04-development/setup/)
- [Coding Standards](./04-development/coding-standards/)
- [Development Workflows](./04-development/workflows/)

## 🔌 API

- [Authentication](./05-api/authentication.md)
- [Rate Limiting](./05-api/rate-limiting.md)
- [OpenAPI Specs](./05-api/openapi/)

## 🧪 Testing

- [Testing Strategy](./06-testing/testing-strategy.md)
- [Unit Testing](./06-testing/unit-testing.md)
- [Integration Testing](./06-testing/integration-testing.md)

## 🚀 Deployment

- [Environments](./07-deployment/environments/)
- [CI/CD](./07-deployment/ci-cd/)
- [Infrastructure](./07-deployment/infrastructure/)

## ⚙️ Operations

- [Runbooks](./08-operations/runbooks/)
- [Monitoring](./08-operations/monitoring/)
- [Troubleshooting](./08-operations/troubleshooting/)

## 👥 User Guides

- [Admin Guides](./09-user-guides/admin/)
- [End User Guides](./09-user-guides/end-user/)
- [API Consumer Guides](./09-user-guides/api-consumers/)

## 🔄 Processes

- [Development Process](./10-processes/development-process.md)
- [Release Process](./10-processes/release-process.md)
- [Incident Management](./10-processes/incident-management.md)
```

### Navegación en Documentos (Template)

```markdown
# [Título del Documento]

## Navegación

🏠 [Home](../../README.md) | 📚 [Índice](../../INDEX.md) | ⬅️
[Anterior](../previous-doc.md) | ➡️ [Siguiente](./next-doc.md)

---

[Contenido del documento]

---

## Related Documents

- [Related Doc 1](./related-doc-1.md)
- [Related Doc 2](../other-section/related-doc-2.md)

## Navegación

⬅️ [Anterior](../previous-doc.md) | 🏠 [Home](../../README.md) | ➡️
[Siguiente](./next-doc.md)
```

## 🏷️ **Sistema de Metadatos**

### Template de Metadatos

```markdown
---
title: "Título del Documento"
description: "Descripción breve del contenido"
author: "Nombre del Autor"
created: "2023-12-01"
updated: "2023-12-01"
version: "1.0"
status: "approved" # draft, review, approved, obsolete
audience: ["developers", "devops"] # developers, devops, business, users
type: "guide" # guide, reference, specification, procedure
tags: ["setup", "backend", "php"]
related: ["local-environment.md", "docker-setup.md"]
---

# [Título del Documento]
```

### Script para Gestión de Metadatos

```bash
#!/bin/bash
# scripts/update-doc-metadata.sh

DOC_FILE=$1

if [ ! -f "$DOC_FILE" ]; then
    echo "❌ Archivo no encontrado: $DOC_FILE"
    exit 1
fi

# Actualizar fecha de modificación
CURRENT_DATE=$(date +"%Y-%m-%d")

# Verificar si ya tiene metadatos
if grep -q "^---" "$DOC_FILE"; then
    # Actualizar fecha existente
    sed -i "s/updated: \".*\"/updated: \"$CURRENT_DATE\"/" "$DOC_FILE"
    echo "✅ Metadatos actualizados en $DOC_FILE"
else
    # Agregar metadatos al inicio
    TEMP_FILE=$(mktemp)
    cat > "$TEMP_FILE" << EOF
---
title: "$(basename "$DOC_FILE" .md | tr '-' ' ' | tr '[:lower:]' '[:upper:]')"
author: "$(git config user.name)"
created: "$CURRENT_DATE"
updated: "$CURRENT_DATE"
version: "1.0"
status: "draft"
---

EOF
    cat "$DOC_FILE" >> "$TEMP_FILE"
    mv "$TEMP_FILE" "$DOC_FILE"
    echo "✅ Metadatos agregados a $DOC_FILE"
fi
```

## 🔍 **Herramientas de Búsqueda y Navegación**

### Generador de Índice Automático

```bash
#!/bin/bash
# scripts/generate-index.sh

echo "📚 Generando índice automático de documentación..."

INDEX_FILE="docs/INDEX.md"
DOCS_DIR="docs"

# Crear cabecera del índice
cat > "$INDEX_FILE" << 'EOF'
# Índice Completo de Documentación

*Generado automáticamente el: $(date)*

## 📊 Resumen
EOF

# Contar documentos por categoría
for dir in "$DOCS_DIR"/*/; do
    if [ -d "$dir" ]; then
        category=$(basename "$dir")
        count=$(find "$dir" -name "*.md" | wc -l)
        echo "- **$category**: $count documentos" >> "$INDEX_FILE"
    fi
done

echo "" >> "$INDEX_FILE"

# Generar tabla de contenidos
echo "## 📖 Tabla de Contenidos" >> "$INDEX_FILE"
echo "" >> "$INDEX_FILE"

find "$DOCS_DIR" -name "*.md" -not -path "*/node_modules/*" | sort | while read -r file; do
    # Extraer título del archivo
    title=$(head -n 20 "$file" | grep -m 1 "^# " | sed 's/^# //')
    if [ -z "$title" ]; then
        title=$(basename "$file" .md | tr '-' ' ')
    fi
    
    # Crear enlace relativo
    rel_path=$(realpath --relative-to="$DOCS_DIR" "$file")
    
    # Extraer metadatos si existen
    audience=""
    if grep -q "audience:" "$file"; then
        audience=$(grep "audience:" "$file" | sed 's/.*: \[\(.*\)\]/\1/')
    fi
    
    echo "- [$title](./$rel_path) ${audience:+($audience)}" >> "$INDEX_FILE"
done

echo "✅ Índice generado en $INDEX_FILE"
```

### Verificador de Enlaces

```bash
#!/bin/bash
# scripts/check-broken-links.sh

echo "🔗 Verificando enlaces en documentación..."

DOCS_DIR="docs"
BROKEN_LINKS=0

find "$DOCS_DIR" -name "*.md" | while read -r file; do
    echo "📄 Verificando: $file"
    
    # Extraer enlaces markdown
    grep -o '\[.*\]([^)]*\.md[^)]*)' "$file" | while read -r link; do
        # Extraer path del enlace
        link_path=$(echo "$link" | sed 's/.*(\([^)]*\)).*/\1/')
        
        # Resolver path relativo
        dir=$(dirname "$file")
        full_path="$dir/$link_path"
        
        if [ ! -f "$full_path" ]; then
            echo "❌ Enlace roto en $file: $link_path"
            BROKEN_LINKS=$((BROKEN_LINKS + 1))
        fi
    done
done

if [ $BROKEN_LINKS -eq 0 ]; then
    echo "✅ Todos los enlaces están funcionando"
else
    echo "❌ Se encontraron $BROKEN_LINKS enlaces rotos"
    exit 1
fi
```

## 📱 **Documentación Responsive**

### Template HTML para Documentación Web

```html
<!DOCTYPE html>
<html lang="es">
   <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>{{title}} - Documentación del Proyecto</title>
      <link rel="stylesheet" href="/assets/styles/docs.css">
   </head>
   <body>
      <nav class="sidebar">
         <div class="logo">
            <h2>📚 Docs</h2>
         </div>
         <ul class="nav-menu">
            <li><a href="/overview/">🎯 Overview</a></li>
            <li><a href="/architecture/">🏗️ Architecture</a></li>
            <li><a href="/development/">👩‍💻 Development</a></li>
            <li><a href="/api/">🔌 API</a></li>
            <li><a href="/operations/">⚙️ Operations</a></li>
         </ul>
      </nav>

      <main class="content">
         <header class="breadcrumb">
            {{breadcrumb}}
         </header>

         <article class="doc-content">
            {{content}}
         </article>

         <footer class="doc-footer">
            <div class="pagination">
               {{#previous}}<a href="{{url}}" class="btn-prev"
               >⬅️ {{title}}</a>{{/previous}} {{#next}}<a
                  href="{{url}}"
                  class="btn-next"
               >{{title}} ➡️</a>{{/next}}
            </div>
            <div class="metadata">
               <p>Última actualización: {{updated}} por {{author}}</p>
            </div>
         </footer>
      </main>

      <script src="/assets/scripts/docs.js"></script>
   </body>
</html>
```

## Tips

### 💡 **Mejores Prácticas**

1. **Estructura Escalable**
   - Diseña para crecimiento futuro
   - Mantén profundidad máxima de 3-4 niveles
   - Usa convenciones consistentes

2. **Navegación Intuitiva**
   - Múltiples puntos de entrada
   - Enlaces bidireccionales
   - Breadcrumbs claros

3. **Mantenimiento**
   - Automatiza la generación de índices
   - Verifica enlaces regularmente
   - Mantén metadatos actualizados

4. **Accesibilidad**
   - Múltiples formatos (web, PDF, markdown)
   - Búsqueda eficiente
   - URLs amigables

### ⚠️ **Errores Comunes a Evitar**

- Estructura muy profunda (>4 niveles)
- Nomenclatura inconsistente
- Enlaces rotos no detectados
- Duplicación de información
- Falta de punto de entrada claro

## Ejemplos

### Configuración de MkDocs

```yaml
# mkdocs.yml
site_name: "Documentación del Proyecto"
site_description: "Documentación técnica completa"

nav:
   - Inicio: "index.md"
   - Overview:
        - "Resumen del Proyecto": "01-overview/project-summary.md"
        - "Arquitectura": "01-overview/architecture-overview.md"
        - "Getting Started": "01-overview/getting-started.md"
   - Arquitectura:
        - "Clean Architecture": "02-architecture/clean-architecture.md"
        - "Domain Design": "02-architecture/domain-design.md"
        - "Decisiones": "02-architecture/decisions/"
   - Desarrollo:
        - "Setup Local": "04-development/setup/local-environment.md"
        - "Coding Standards": "04-development/coding-standards/"
        - "Workflows": "04-development/workflows/"

theme:
   name: "material"
   palette:
      primary: "blue"
      accent: "light blue"
   features:
      - navigation.tabs
      - navigation.sections
      - toc.integrate
      - search.suggest

plugins:
   - search
   - git-revision-date-localized
   - awesome-pages
```

## Navegación

[⬅️ Cómo Documentar Procesos](./como-documentar-procesos.md) |
[🏠 README Principal](../../README.md) |
[Retroalimentación del Equipo ➡️](./retroalimentacion-equipo.md)
