# Stack Tecnológico

## ¿Qué es?

Un stack tecnológico es el conjunto de tecnologías, frameworks, librerías,
herramientas y lenguajes de programación que se utilizan para desarrollar una
aplicación o sistema completo. Incluye tanto las tecnologías del frontend (lado
del cliente) como del backend (lado del servidor), base de datos, y herramientas
de desarrollo.

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

- **Framework/Librería**: React 18+ con TypeScript 5+
- **Bundler**: Vite (estándar único, CRA deprecated)
- **Routing**: React Router v6+
- **Estado global**:
  - **Zustand**: Proyectos pequeños-medianos (<50 componentes conectados)
  - **Redux Toolkit**: Proyectos grandes, múltiples equipos, debugging complejo
- **UI Components - Estrategia Progresiva**:
  - **Fase 1 (Inmediata)**: Shadcn/ui + Tailwind CSS + Radix UI primitives
  - **Fase 2 (3-6 meses)**: Atomic Design + Storybook + Design System
- **Testing**: Jest + React Testing Library + Storybook (Fase 2)

### Backend

- **Framework**: Laravel 10+ (PHP 8.2+)
- **Architecture**: Clean Architecture basada en DDD
- **API**: RESTful APIs con Laravel Sanctum para autenticación
- **Validación**: Form Request Validation
- **ORM**: Eloquent ORM (solo en Infrastructure layer)
- **Testing**: PHPUnit (estándar de facto, mejor integración IDE)

### Base de Datos

- **Principal**: MySQL 8.0+ (proyectos estándar)
- **Alternativa**: PostgreSQL 15+ (JSON avanzado, full-text search, análisis
  complejos)
- **Migraciones**: Laravel Migrations
- **Seeders**: Laravel Database Seeders
- **Convenciones**: Inglés, snake_case, soft deletes, timestamps obligatorios

### Mobile

- **Framework**: React Native 0.72+ con TypeScript
- **CLI**: React Native CLI (mayor control que Expo)
- **Navigation**: React Navigation 6+
- **Estado**: Sincronizado con web (Redux Toolkit/Zustand + Redux Persist)
- **Testing**: Jest + React Native Testing Library + Detox

### DevOps y Herramientas

- **Control de versiones**: Git (GitHub Flow + protected branches)
- **CI/CD**: GitHub Actions con templates específicos
- **Containerización**: Docker + Docker Compose
- **Servidor web**: Nginx
- **Cloud Provider**:
  - **Principal**: DigitalOcean (simplicidad, costo-beneficio)
  - **Empresarial**: AWS (proyectos >$10k/mes, compliance específico)
- **Monitoreo**: New Relic Basic + DigitalOcean Monitoring (básico)

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

## ¿Qué debo hacer?

1. **Evaluar requisitos del proyecto**

   - Identificar las necesidades específicas
   - Considerar la escalabilidad requerida
   - Evaluar el tiempo de desarrollo disponible

2. **Seleccionar tecnologías base (decisiones tomadas)**

   - Frontend: React 18+ + TypeScript 5+ + Vite
   - UI Components: Shadcn/ui + Tailwind CSS (Fase 1)
   - Estado: Zustand (proyectos pequeños) / Redux Toolkit (grandes)
   - Backend: Laravel 10+ + Clean Architecture + DDD
   - Base de datos: MySQL 8.0+ (default) / PostgreSQL 15+ (casos específicos)
   - Mobile: React Native 0.72+ (si aplica)

3. **Configurar herramientas de desarrollo**

   - Instalar y configurar el entorno de desarrollo
   - Configurar linting y formatting
   - Establecer estructura de carpetas estándar

4. **Documentar decisiones**

   - Justificar la elección de cada tecnología
   - Documentar configuraciones específicas
   - Mantener un registro de versiones utilizadas

5. **Establecer estándares de código**
   - Configurar reglas de ESLint y PHP CS Fixer
   - Definir convenciones de nomenclatura
   - Establecer patrones de arquitectura

## Tips

- **Mantén la consistencia**: Una vez elegido el stack, mantente fiel a él
  durante todo el proyecto
- **Considera el futuro**: Elige tecnologías con buen soporte a largo plazo
- **Evalúa la curva de aprendizaje**: Considera el conocimiento del equipo
- **Documenta todo**: Mantén un registro detallado de versiones y
  configuraciones
- **Usa herramientas de automatización**: Configura linting, testing y CI/CD
  desde el inicio
- **Principios SOLID**: Asegúrate de que el stack elegido permita aplicar estos
  principios
- **Clean Architecture**: El stack debe facilitar una arquitectura limpia y
  mantenible

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
