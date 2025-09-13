# Estándar de Proyectos - Guía de Desarrollo

## Descripción

Esta guía proporciona un estándar paso a paso para la creación de proyectos
siguiendo las mejores prácticas de la industria y arquitectura limpia. El
objetivo es tener una referencia completa que garantice la calidad,
mantenibilidad y escalabilidad de los proyectos desde su concepción hasta su
despliegue y mantenimiento.

**Toda la documentación sigue principios de Clean Architecture + DDD
(Domain-Driven Design) y está diseñada para ser utilizada con GitHub Copilot
siguiendo las instrucciones específicas del repositorio.**

### Decisiones Arquitectónicas Definidas

El estándar incluye decisiones específicas y no ambiguas sobre:

- **UI Components**: Estrategia progresiva Shadcn/ui → Atomic Design con roadmap
  definido
- **Estado Global**: Redux Toolkit como estándar único obligatorio
- **Base de Datos**: MySQL vs PostgreSQL con criterios técnicos claros
- **Cloud Provider**: DigitalOcean vs AWS según tamaño y compliance
- **Testing**: Frameworks específicos y coverage mínimo por layer
- **Performance**: Métricas específicas y thresholds obligatorios

Actualmente la documentación es teórica pero implementable, diseñada para ser
aplicada en proyectos reales siguiendo un roadmap de evolución técnica definido.

## Estado Actual

### Etapas Completadas

- **Step 1**: Planificación y Análisis
- **Step 2**: Diseño y Arquitectura
- **Step 3**: Configuración del Entorno de Desarrollo
- **Step 4**: Desarrollo del Backend
- **Step 5**: Desarrollo del Frontend Web
- **Step 6**: Desarrollo de Aplicación Móvil
- **Step 7**: Testing y Quality Assurance
- **Step 8**: Despliegue y DevOps
- **Step 9**: Mantenimiento y Evolución

## Stack Tecnológico Estándar

### Frontend - Estrategia Progresiva

- **Framework**: React 18+ con TypeScript 5+ (tipado fuerte obligatorio)
- **Bundler**: Vite (estándar único, CRA deprecated)
- **UI Components**:
  - **Fase 1 (Inmediata)**: Shadcn/ui + Tailwind CSS + Radix UI primitives
  - **Fase 2 (3-6 meses)**: Atomic Design + Storybook + Design System
- **Estado Global**: Redux Toolkit (estándar único obligatorio)
- **Routing**: React Router v6+
- **HTTP Client**: Axios con interceptors
- **Testing**: Jest + React Testing Library + Storybook (Fase 2)
- **Linting**: ESLint + Prettier + Tailwind plugin

### Backend (Clean Architecture + DDD)

- **Framework**: Laravel 10+ con Clean Architecture
- **Lenguaje**: PHP 8.2+ (strict types obligatorio)
- **Arquitectura**: Domain-Driven Design (DDD) + Clean Architecture
- **API**: RESTful con versionado URL-based (`/api/v1/`)
- **Autenticación**: Laravel Sanctum + MFA para admin panels
- **Testing**: PHPUnit (estándar de facto, mejor integración IDE)
- **Code Quality**: PHP CS Fixer + PHPStan + Larastan

### Base de Datos

- **Principal**: MySQL 8.0+ (proyectos estándar)
- **Alternativa**: PostgreSQL 15+ (JSON avanzado, full-text search, análisis
  complejos)
- **Identificadores**: UUID públicos generados en PHP:
  `bin2hex(random_bytes(16))`
- **Convenciones**: Tablas en inglés, plural, snake_case
- **Obligatorio**: Soft deletes, timestamps, foreign keys

### Infraestructura y DevOps

- **Cloud Provider**:
  - **Principal**: DigitalOcean (simplicidad, costo-beneficio para
    startups/SMEs)
  - **Empresarial**: AWS (proyectos >$10k/mes, compliance específico)
- **CI/CD**: GitHub Actions con templates específicos
- **Containerización**: Docker + Docker Compose
- **Monitoreo**: New Relic Basic + DigitalOcean Monitoring (básico)
- **Package Manager**: npm (estándar único, viene con Node.js)

### Móvil (Fase Futura)

- **Framework**: React Native 0.72+ con TypeScript
- **CLI**: React Native CLI (mayor control que Expo)
- **Estado**: Sincronizado con web (Redux Toolkit + Redux Persist)
- **Navegación**: React Navigation 6+
- **Testing**: Jest + React Native Testing Library + Detox

### Metodología y Procesos

- **Gestión**: Scrumban (Scrum + Kanban)
- **Estimación**: Story Points
- **Tableros**: GitHub Projects (preferido), Jira, Trello
- **Git Workflow**: GitHub Flow con protected main + staging branch
- **Branching**: feature/_, bugfix/_, hotfix/*
- **Code Review**: Mínimo 1 reviewer, tests passing obligatorio

### Calidad y Testing

- **Coverage Mínimo**:
  - Domain Layer: 90%
  - Application Layer: 85%
  - Infrastructure Layer: 70%
  - Frontend Components: 80%
- **Performance**:
  - Bundle frontend: <500KB inicial
  - API response time: <200ms p95
  - First Contentful Paint: <1.5s
- **Security**: TLS 1.3, headers obligatorios, rate limiting por endpoint

### Principios Fundamentales

- **SOLID**: Single Responsibility, Open/Closed, Liskov Substitution, Interface
  Segregation, Dependency Inversion
- **Clean Architecture**: Separación de capas, independencia del framework
- **DRY**: Don't Repeat Yourself
- **Clean Code**: Código legible, mantenible y testeable

## Etapas del Desarrollo

### [1. Planificación y Análisis](./docs/step_01/planificacion-analisis.md)

- [Definición de requerimientos funcionales y no funcionales](./docs/step_01/requerimientos-funcionales-no-funcionales.md)
- [Análisis de stakeholders](./docs/step_01/analisis-stakeholders.md)
- [Roadmap del proyecto](./docs/step_01/roadmap-proyecto.md)
- [Metodología de trabajo](./docs/step_01/metodologia-trabajo.md)
- [Definición del alcance del proyecto](./docs/step_01/definicion-alcance.md)
- [Estimación de tiempos y recursos](./docs/step_01/estimacion-tiempos-recursos.md)
- [Análisis de riesgos](./docs/step_01/analisis-riesgos.md)

### [2. Diseño y Arquitectura](./docs/step_02/diseno-arquitectura.md)

- [Stack tecnológico inicial](./docs/step_02/stack-tecnologico.md)
- [Elección de herramientas](./docs/step_02/eleccion-herramientas.md)
- [Herramientas por Lenguaje](./docs/step_02/herramientas-por-lenguaje.md)
- [Checklist Mínima por Proyecto](./docs/step_02/checklist-minima-proyecto.md)
- [Estilo y Formato de Documentación](./docs/step_02/estilo-formato-documentacion.md)
- [Generación de Diagramas](./docs/step_02/generacion-diagramas.md)
- [Buenas Prácticas de Desarrollo](./docs/step_02/buenas-practicas-desarrollo.md)
- [Patrones de diseño del proyecto](./docs/step_02/patrones-diseno.md)
- [Arquitectura del sistema](./docs/step_02/arquitectura-sistema.md)
- [Diagrama de arquitectura general](./docs/step_02/diagrama-arquitectura-general.md)
- [Convenciones y estándares](./docs/step_02/convenciones-estandares.md)
- [Estructura de carpetas base](./docs/step_02/estructura-carpetas.md)
- [Manejo de Imágenes](./docs/step_02/manejo-imagenes.md)
- [Modelado de datos](./docs/step_02/modelado-datos.md)
- [Buenas prácticas de base de datos](./docs/step_02/buenas-practicas-base-datos.md)
- [Diseño de base de datos (ERD)](./docs/step_02/diseno-base-datos.md)
- [Diseño de APIs (OpenAPI/Swagger)](./docs/step_02/diseno-apis.md)
- [Diagramas de flujo](./docs/step_02/diagramas-flujo.md)
- [Casos de uso y User Stories](./docs/step_02/casos-uso-user-stories.md)
- [Prototipos y wireframes](./docs/step_02/prototipos-wireframes.md)
- [Especificaciones técnicas](./docs/step_02/especificaciones-tecnicas.md)

### [3. Configuración del Entorno de Desarrollo](./docs/step_03/configuracion-entorno-desarrollo.md)

- [Requisitos mínimos del sistema](./docs/step_03/requisitos-sistema.md)
- [Instalación de dependencias iniciales](./docs/step_03/instalacion-dependencias-iniciales.md)
- [Configuración de repositorios Git y GitHub](./docs/step_03/configuracion-repositorios-git.md)
- [Setup de entornos de desarrollo local con Docker](./docs/step_03/setup-entorno-local-docker.md)
- [Configuración de herramientas de CI/CD con GitHub Actions](./docs/step_03/configuracion-cicd-github-actions.md)
- [Setup de ambientes (desarrollo, testing, staging, producción)](./docs/step_03/setup-ambientes-multiples.md)
- [Configuración de herramientas de calidad de código](./docs/step_03/configuracion-herramientas-calidad.md)
- [Setup de Laravel con Clean Architecture](./docs/step_03/setup-laravel-clean-architecture.md)
- [Configuración de React + TypeScript + Vite](./docs/step_03/configuracion-react-typescript-vite.md)

### [4. Desarrollo del Backend (Laravel + Clean Architecture)](./docs/step_04/desarrollo-backend.md)

- [Implementación Domain Layer](./docs/step_04/implementacion-domain-layer.md)
- [Implementación Application Layer](./docs/step_04/implementacion-application-layer.md)
- [Implementación Infrastructure Layer](./docs/step_04/implementacion-infrastructure-layer.md)
- [APIs RESTful Versionadas](./docs/step_04/apis-restful-versionadas.md)
- [Validaciones de Seguridad](./docs/step_04/validaciones-seguridad.md)
- [Manejo Seguro de Archivos](./docs/step_04/manejo-archivos-seguro.md)
- [Autenticación Laravel Sanctum](./docs/step_04/autenticacion-laravel-sanctum.md)
- [Migraciones y Seeders](./docs/step_04/migraciones-seeders.md)
- [Testing TDD Backend](./docs/step_04/testing-tdd-backend.md)

### [5. Desarrollo del Frontend Web (React + TypeScript)](./docs/step_05/desarrollo-frontend-web.md)

- [Configuración React + TypeScript + Vite](./docs/step_05/configuracion-react-typescript-vite.md)
- [Componentes Reutilizables con Tipado](./docs/step_05/componentes-reutilizables-tipado.md)
- [Redux Toolkit - Estado Global](./docs/step_05/redux-toolkit-estado-global.md)
- [Integración APIs con Axios](./docs/step_05/integracion-apis-axios.md)
- [React Router - Navegación](./docs/step_05/react-router-navegacion.md)
- [Testing Componentes con Jest](./docs/step_05/testing-componentes-jest.md)

### [6. Desarrollo de Aplicación Móvil (React Native)](./docs/step_06/desarrollo-movil.md)

- [Configuración React Native + TypeScript](./docs/step_06/configuracion-react-native-typescript.md)
- [Pantallas y React Navigation](./docs/step_06/pantallas-react-navigation.md)
- [Sincronización Estado Redux](./docs/step_06/sincronizacion-estado-redux.md)
- [Integración APIs Móvil](./docs/step_06/integracion-apis-movil.md)
- [Testing Dispositivos y Simuladores](./docs/step_06/testing-dispositivos-simuladores.md)
- [Preparación App Stores](./docs/step_06/preparacion-app-stores.md)

### [7. Testing y Quality Assurance](./docs/step_07/testing-qa.md)

- [Tipos de Pruebas](./docs/step_07/tipos-pruebas.md)
- [Configuración de Herramientas de Testing](./docs/step_07/configuracion-herramientas-testing.md)
- [Ejemplos de Testing por Lenguaje](./docs/step_07/ejemplos-testing-por-lenguaje.md)
- [Cobertura de Pruebas (Coverage)](./docs/step_07/cobertura-pruebas.md)
- [Testing por Entorno](./docs/step_07/testing-por-entorno.md)
- [Checklist de Testing por Tipo de Proyecto](./docs/step_07/checklist-testing-proyectos.md)
- [Testing Funcional Automatizado](./docs/step_07/testing-funcional-automatizado.md)
- [Testing de Regresión](./docs/step_07/testing-regresion.md)
- [Checklists QA](./docs/step_07/checklists-qa.md)
- [Pruebas de Aceptación del Usuario](./docs/step_07/pruebas-aceptacion-usuario.md)
- [Gestión de Reportes de Errores](./docs/step_07/gestion-reportes-errores.md)
- [Testing de Performance y Carga](./docs/step_07/testing-performance-carga.md)
- [Testing de Seguridad OWASP](./docs/step_07/testing-seguridad-owasp.md)
- [Testing de Usabilidad](./docs/step_07/testing-usabilidad.md)
- [Code Review y Refactoring](./docs/step_07/code-review-refactoring.md)
- [Auditoría de Calidad de Código](./docs/step_07/auditoria-calidad-codigo.md)
- [Checklist de Performance](./docs/step_07/checklist-performance.md)
- [Checklist de File System](./docs/step_07/checklist-file-system.md)
- [Optimización del Frontend](./docs/step_07/optimizacion-frontend.md)
- [Estrategias de Caché](./docs/step_07/estrategias-cache.md)
- [Checklist de Revisión de Base de Datos](./docs/step_07/checklist-revision-base-datos.md)
- [Revisión General del Código](./docs/step_07/revision-general-codigo.md)
- [Optimización de Recursos](./docs/step_07/optimizacion-recursos.md)

### [8. Despliegue y DevOps](./docs/step_08/despliegue-devops.md)

- [Configuración de Servidores de Producción](./docs/step_08/configuracion-servidores-produccion.md)
- [CI/CD Pipelines con GitHub Actions](./docs/step_08/ci-cd-pipelines-github-actions.md)
- [Docker y Contenedores](./docs/step_08/docker-contenedores.md)
- [Monitoreo y Logging](./docs/step_08/monitoreo-logging.md)
- [Optimización base de datos](./docs/step_08/optimizacion-base-datos.md)
- [Backup y Recovery](./docs/step_08/backup-recovery-strategies.md)
- [Mantenimiento del File System](./docs/step_08/file-system-maintenance.md)
- [SSL y Configuraciones de Seguridad](./docs/step_08/ssl-configuraciones-seguridad.md)

### [9. Mantenimiento y Evolución](./docs/step_09/mantenimiento-evolucion.md)

- [Monitoreo Post-Deployment](./docs/step_09/monitoreo-post-deployment.md)
- [Bug Fixes y Hotfixes](./docs/step_09/bug-fixes-hotfixes.md)
- [Actualización de Dependencias](./docs/step_09/actualizacion-dependencias.md)
- [Gestión de Nuevas Funcionalidades](./docs/step_09/gestion-nuevas-funcionalidades.md)
- [Mejoras Continuas Programadas](./docs/step_09/mejoras-continuas-programadas.md)
- [Documentación y Knowledge Transfer](./docs/step_09/documentacion-knowledge-transfer.md)
- [Cómo Documentar Procesos](./docs/step_09/como-documentar-procesos.md)
- [Organización de la Documentación](./docs/step_09/organizacion-documentacion.md)
- [Retroalimentación del Equipo](./docs/step_09/retroalimentacion-equipo.md)
- [Evaluación del Cumplimiento del Estándar](./docs/step_09/evaluacion-cumplimiento-estandar.md)
- [Lecciones Aprendidas](./docs/step_09/lecciones-aprendidas.md)
- [Evaluación de Herramientas](./docs/step_09/evaluacion-herramientas.md)
- [Cierre de Proyecto](./docs/step_09/cierre-proyecto.md)
- [Métricas y Analytics de Uso](./docs/step_09/metricas-analytics-uso.md)

## Estructura del Repositorio

```
estandar-proyectos/
├── .github/
│   └── copilot-instructions.md     # Instrucciones para GitHub Copilot
├── docs/                           # Contenedor de la guía paso a paso
│   ├── step_01/                    # Planificación y Análisis
│   ├── step_02/                    # Diseño y Arquitectura
│   ├── step_03/                    # Configuración del Entorno
│   ├── step_04/                    # Desarrollo Backend
│   ├── step_05/                    # Desarrollo Frontend (Completado)
│   ├── step_06/                    # Desarrollo Móvil (Completado)
│   ├── step_07/                    # Testing y QA (Completado)
│   ├── step_08/                    # Despliegue y DevOps (Completado)
│   └── step_09/                    # Mantenimiento y Evolución (Completado)
├── ignore/                         # Archivos de prueba y borradores
├── README.md                       # Este archivo
├── LICENSE                         # Licencia MIT
└── .gitignore                      # Archivos ignorados por Git
```

## Contribución

Esta guía está en constante evolución. Si tienes sugerencias o mejoras:

1. **Crea un Issue**: Para reportar problemas o sugerir mejoras
2. **Fork y Pull Request**: Para contribuir con documentación o correcciones
3. **Sigue los estándares**: Mantén el formato establecido en cada documento
4. **Actualiza la navegación**: Si agregas nuevos documentos, actualiza los
   enlaces

### Reglas para Contribuciones

- Mantén el formato estándar de documentación
- Incluye ejemplos prácticos cuando sea posible
- Asegúrate de que el contenido esté alineado con las instrucciones de Copilot
- Actualiza la navegación entre documentos
- Prueba todos los enlaces antes de hacer commit

## Licencia

Este proyecto está bajo la [Licencia MIT](./LICENSE).

---

**Desarrollado con ❤️ siguiendo principios de Clean Architecture y DDD**

**📚 Documentación 100% completa - Guía lista para usar**

Última actualización: Septiembre 2025
