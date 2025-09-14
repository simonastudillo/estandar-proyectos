# Stack Tecnológico Estándar

> **IMPORTANTE**: Este documento define decisiones arquitectónicas **NO
> NEGOCIABLES** para mantener consistencia en todos los proyectos. Las
> tecnologías aquí especificadas son **estándares únicos** sin alternativas,
> eliminando ambigüedades en la toma de decisiones.

## ¿Qué es?

Un stack tecnológico es el conjunto de tecnologías, frameworks, librerías,
herramientas y lenguajes de programación que se utilizan para desarrollar una
aplicación o sistema completo. Este documento establece las **decisiones
definitivas** para nuestro stack, eliminando opciones múltiples y garantizando
consistencia entre proyectos.

## ¿Por qué es importante?

- **Coherencia**: Mantener un stack consistente facilita el mantenimiento y
  escalabilidad del proyecto
- **Eficiencia**: Permite aprovechar las sinergias entre tecnologías compatibles
- **Expertise del equipo**: Facilita el desarrollo al trabajar con tecnologías
  conocidas
- **Comunidad y soporte**: Las tecnologías populares tienen mejor documentación
  y comunidad
- **Escalabilidad**: Un stack bien elegido permite el crecimiento del proyecto
- **Estándares**: Facilita la incorporación de nuevos desarrolladores al
  proyecto

## ¿Qué debe incluir?

### Frontend

- **Framework/Librería**: React 18+ con TypeScript 5+ (tipado fuerte
  obligatorio)
- **Bundler**: **Vite** (estándar único - CRA, Webpack, Parcel PROHIBIDOS)
- **Routing**: React Router v6+ (única opción permitida)
- **Estado global**: **Redux Toolkit** (estándar único obligatorio - Zustand,
  Context API prohibidos para estado global)
- **HTTP Client**: Axios con interceptors (única opción permitida)
- **Package Manager**: **npm** (estándar único - Yarn, pnpm prohibidos)
- **UI Components - Estrategia Progresiva NO OPCIONAL**:
  - **Fase 1 (Inmediata)**: Shadcn/ui + Tailwind CSS + Radix UI primitives
  - **Fase 2 (3-6 meses)**: Atomic Design + Storybook + Design System
- **Testing**: Jest + React Testing Library + Storybook (Fase 2) - única
  configuración permitida
- **Linting**: ESLint + Prettier + Tailwind plugin (configuración estándar
  obligatoria)

### Backend

- **Framework**: Laravel 10+ (PHP 8.2+ con strict types obligatorio)
- **Architecture**: Clean Architecture basada en DDD (única arquitectura
  permitida)
- **API**: RESTful APIs con versionado URL-based (`/api/v1/`) - único estilo
  permitido
- **Autenticación**: Laravel Sanctum + MFA para admin panels (única opción)
- **Validación**: Form Request Validation (estándar obligatorio)
- **ORM**: Eloquent ORM (solo en Infrastructure layer - estricto)
- **Testing**: **PHPUnit** (estándar único - Pest prohibido por simplicidad y
  compatibilidad IDE)
- **Code Quality**: PHP CS Fixer + PHPStan + Larastan (configuración estándar
  obligatoria)

### Base de Datos

- **Principal**: **MySQL 8.0+** (única base de datos permitida en todos los
  proyectos)
- **Identificadores**: UUID públicos generados en PHP:
  `bin2hex(random_bytes(16))`
- **Migraciones**: Laravel Migrations (única herramienta permitida)
- **Seeders**: Laravel Database Seeders (única herramienta permitida)
- **Convenciones NO NEGOCIABLES**: Inglés, snake_case, soft deletes, timestamps
  obligatorios, foreign keys obligatorias

### Mobile

- **Framework**: React Native 0.72+ con TypeScript
- **CLI**: React Native CLI (mayor control que Expo)
- **Navigation**: React Navigation 6+
- **Estado**: Sincronizado con web (Redux Toolkit + Redux Persist)
- **Testing**: Jest + React Native Testing Library + Detox

### DevOps y Herramientas

- **Control de versiones**: Git con **GitHub Flow + protected branches** (única
  metodología)
- **CI/CD**: **GitHub Actions** con templates específicos (única plataforma
  permitida)
- **Containerización**: Docker + Docker Compose (estándar obligatorio)
- **Servidor web**: Nginx (única opción para producción)
- **Cloud Provider - Política**:
  - **Estándar (obligatorio)**: **DigitalOcean** — todos los proyectos se
    desplegarán por defecto en DigitalOcean. Esta decisión reduce la
    complejidad operativa y permite plantillas y scripts reutilizables.
  - **Excepción (AWS)**: Uso de **AWS** sólo mediante aprobación
    arquitectónica documentada cuando existan requisitos empresariales o de
    compliance que DigitalOcean no soporte (p.ej. servicios gestionados
    específicos, requisitos regulatorios o cargas extremas). La excepción debe
    incluir justificación técnica, estimación de coste y plan operativo.
  - **PROHIBIDOS**: Azure, GCP, Heroku, Vercel para backend (salvo
    excepciones extraordinarias con aprobación explícita).
- **Monitoreo Específico**:
  - **Básico**: **DigitalOcean Monitoring** (obligatorio) — integrar métricas
    de infraestructura y alertas básicas. New Relic puede usarse como
    complemento si el proyecto lo requiere.
  - **Avanzado**: DataDog + ELK Stack (solo mediante aprobación para
    proyectos enterprise con requerimientos de observabilidad avanzados)

### Desarrollo

- **IDE**: Visual Studio Code
- **Package Manager**: npm (estándar único, viene con Node.js)
- **Linting**: ESLint + PHP CS Fixer + configuraciones específicas
- **Code Formatting**: Prettier
- **Pre-commit hooks**: Husky + lint-staged
- **Extensiones VS Code obligatorias**:
  - ES7+ React/Redux/React-Native snippets
  - Tailwind CSS IntelliSense
  - PHP Intelephense

## ⚠️ Herramientas y Tecnologías PROHIBIDAS

Para mantener consistencia y evitar fragmentación del conocimiento del equipo:

### ❌ Frontend - Bundlers y Build Tools

- **Create React App (CRA)**: Deprecado por Meta, usar **Vite**
- **Webpack directo**: Complejidad innecesaria, usar **Vite**
- **Parcel**: No alineado con stack, usar **Vite**
- **Next.js**: Para SPA usar React + Vite, no SSR framework
- **Gatsby**: No compatible con backend separado

### ❌ Estado Global Alternativo

- **Zustand**: Usar **Redux Toolkit** únicamente
- **Jotai**: Usar **Redux Toolkit** únicamente
- **Context API**: Solo para datos de UI local, NO para estado global
- **MobX**: Usar **Redux Toolkit** únicamente

### ❌ Package Managers Alternativos

- **Yarn**: Usar **npm** únicamente
- **pnpm**: Usar **npm** únicamente
- **Bun**: Demasiado nuevo, usar **npm**

### ❌ Testing Frameworks Alternativos

- **Vitest**: Usar **Jest** para consistencia
- **Pest (PHP)**: Usar **PHPUnit** para mejor IDE support
- **Cypress**: Usar React Testing Library + Jest

### ❌ CSS Frameworks Alternativos

- **Bootstrap**: Usar **Tailwind CSS** únicamente
- **Material-UI standalone**: Usar **Shadcn/ui + Tailwind**
- **Chakra UI**: Usar **Shadcn/ui + Tailwind**
- **Styled Components**: Usar **Tailwind CSS**

### ❌ Backend Frameworks Alternativos

- **Express.js/Node.js**: Usar **Laravel** únicamente
- **Django/Python**: Usar **Laravel** únicamente
- **Spring Boot/Java**: Usar **Laravel** únicamente

### ❌ Base de Datos NoSQL (Sin Casos de Uso Específicos)

- **MongoDB**: Usar **MySQL/PostgreSQL**
- **Redis** como DB principal: Solo para cache/sessions
- **DynamoDB**: Usar **MySQL/PostgreSQL**

### ✅ Excepciones Permitidas

- **Redis**: SOLO para cache y sessions
- **Elasticsearch**: SOLO para search avanzado (casos específicos)
- **S3/Object Storage**: Para archivos y assets

## ¿Qué debo hacer?

1. **Evaluar requisitos del proyecto**

   - Identificar las necesidades específicas
   - Considerar la escalabilidad requerida
   - Evaluar el tiempo de desarrollo disponible

2. **Seleccionar tecnologías base (decisiones tomadas)**

   - ✅ **Frontend**: React 18+ + TypeScript 5+ + **Vite** (único bundler
     permitido)
   - ✅ **UI**: Shadcn/ui + Tailwind CSS (Fase 1) → Atomic Design (Fase 2)
   - ✅ **Estado**: Redux Toolkit (único estándar permitido)
   - ✅ **Backend**: Laravel 10+ + Clean Architecture + DDD
   - ✅ **DB**: MySQL 8.0+ (default) / PostgreSQL 15+ (casos específicos
     documentados)
   - ✅ **Mobile**: React Native 0.72+ (si aplica)

3. **Configurar herramientas de desarrollo**

   - Configurar **Vite** con configuración estándar específica
   - Instalar y configurar ESLint + Prettier con reglas obligatorias
   - Establecer estructura de carpetas estándar (no negociable)
   - Configurar **GitHub Actions** con templates específicos

4. **Implementar estándares de calidad no negociables**

   - Coverage mínimo: Domain 90%, Application 85%, Infrastructure 70%
   - Performance: Bundle <500KB, API <200ms p95
   - Security: TLS 1.3, headers obligatorios, MFA para admin

5. **Documentar configuraciones específicas (obligatorio)**

   - Configuraciones exactas de Vite, ESLint, PHPUnit
   - Justificar SOLO si se requiere PostgreSQL sobre MySQL
   - Mantener registro de versiones específicas utilizadas

6. **Establecer proceso de compliance**
   - Auditorías de stack compliance regulares
   - Code reviews verificando adherencia al estándar
   - CI/CD failfast en caso de tecnologías no permitidas

## Tips para Implementación

- **No hay opciones**: El stack está definido, enfócate en implementar
  correctamente
- **Vite es obligatorio**: Toda optimización debe usar herramientas del
  ecosistema Vite/Rollup
- **Redux Toolkit únicamente**: No evaluar alternativas de estado global
- **Consulta antes de desviar**: Cualquier desviación requiere aprobación
  arquitectónica
- **Automatiza compliance**: Configura CI/CD para detectar tecnologías no
  permitidas
- **Prioriza configuración estándar**: Usa templates y configuraciones
  predefinidas
- **Documenta excepciones**: Justifica técnicamente cualquier caso especial
- **Mantén versiones actualizadas**: Sigue las versiones mínimas especificadas

## Ejemplos

### Stack completo específico:

```
Frontend: React 18+ + TypeScript 5+ + Vite + Shadcn/ui + Tailwind
Backend: Laravel 10+ + PHP 8.2+ + MySQL 8.0+ + Clean Architecture
Mobile: React Native 0.72+ + TypeScript + React Navigation 6+
DevOps: Docker + GitHub Actions + DigitalOcean
```

### Estructura de proyecto:

```
proyecto/
├── frontend/          # React + TypeScript + Vite
│   ├── src/
│   │   ├── components/
│   │   │   ├── ui/           # Shadcn components (Fase 1)
│   │   │   ├── atoms/        # Atomic Design (Fase 2)
│   │   │   ├── molecules/    # Combinaciones simples (Fase 2)
│   │   │   └── organisms/    # Componentes complejos (Fase 2)
│   │   └── lib/
│   │       └── utils.ts      # cn(), etc.
├── backend/           # Laravel + Clean Architecture
│   ├── app/
│   │   ├── Domain/           # Entidades, Value Objects
│   │   ├── Application/      # Casos de uso, DTOs
│   │   └── Infrastructure/   # Controllers, Repositories
├── mobile/            # React Native (opcional)
├── docs/              # Documentación
├── docker/            # Configuración Docker
└── .github/           # GitHub Actions workflows
```

### Configuración de dependencias específicas:

**Frontend (package.json) - Fase 1:**

```json
{
   "dependencies": {
      "react": "^18.0.0",
      "typescript": "^5.0.0",
      "tailwindcss": "^3.0.0",
      "@radix-ui/react-slot": "^1.0.0",
      "@radix-ui/react-dialog": "^1.0.0",
      "class-variance-authority": "^0.7.0",
      "clsx": "^2.0.0",
      "tailwind-merge": "^1.0.0",
      "react-router-dom": "^6.0.0"
   },
   "devDependencies": {
      "@storybook/react-vite": "^7.0.0"
   }
}
```

**Backend (composer.json):**

```json
{
   "require": {
      "laravel/framework": "^10.0",
      "laravel/sanctum": "^3.0",
      "php": "^8.2"
   }
}
```

## Roadmap de Evolución UI Components

### Q1: Implementación Shadcn/ui (Fase 1)

- ✅ Setup inicial: Vite + React + TypeScript + Tailwind
- ✅ Instalación de Shadcn/ui con componentes básicos
- ✅ Configuración de design tokens básicos
- ✅ Guidelines de uso y patterns documentados

### Q2: Migración a Atomic Design (Fase 2)

- 🔄 Restructuración gradual usando metodología Atomic Design
- 🔄 Setup completo de Storybook + documentación automática
- 🔄 Design System consolidado con tokens avanzados
- 🔄 Visual regression testing con Chromatic

### Q3: Mobile + Advanced Features

- 📋 React Native setup con state synchronization
- 📋 Advanced monitoring y analytics implementado
- 📋 Performance optimization completada

## Estándares de Calidad Técnicos

### Performance Requirements

- **Frontend**:
  - Bundle inicial <500KB
  - First Contentful Paint <1.5s
  - Largest Contentful Paint <2.5s
- **Backend**:
  - API CRUD <200ms p95
  - Database queries <100ms p95

### Security Standards

- **TLS**: 1.3 mínimo obligatorio
- **Headers**: HSTS, CSP, X-Frame-Options configurados
- **Rate Limiting**: Implementado por endpoint type
- **Authentication**: Laravel Sanctum + MFA para admin panels

### Testing Coverage Requirements

- **Domain Layer**: 90% mínimo
- **Application Layer**: 85% mínimo
- **Infrastructure Layer**: 70% mínimo
- **Frontend Components**: 80% mínimo

## Navegación

[⬅️ Diseño y Arquitectura](./diseno-arquitectura.md) |
[🏠 README Principal](../../README.md) |
[Elección de Herramientas ➡️](./eleccion-herramientas.md)
