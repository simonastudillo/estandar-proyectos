# Metodología de Trabajo

## Conocimientos Involucrados

- Metodologías ágiles
- Gestión de proyectos

## Responsable

- Equipo de Gestión de Proyectos

## ¿Qué es?

**Scrumban** es una metodología híbrida específicamente adaptada para proyectos
con **React + TypeScript (Shadcn/ui → Atomic Design)** y **Laravel Clean
Architecture**, que combina la estructura y disciplina de **Scrum** con la
flexibilidad y flujo continuo de **Kanban**. Esta metodología toma las mejores
prácticas de ambos frameworks: las ceremonias y estimaciones de Scrum junto con
el sistema de flujo visual y límites de trabajo en progreso (WIP) de Kanban.

Para nuestro estándar de proyectos, Scrumban proporciona la **flexibilidad
necesaria** para adaptarse tanto a proyectos pequeños (que requieren más
agilidad) como a proyectos grandes (que necesitan más estructura), manteniendo
siempre la **disciplina de estimación con story points** y la **mejora
continua** a través de retrospectivas regulares, con **quality gates específicos
por stack tecnológico**.

## ¿Por qué es Importante?

### 🔄 **Adaptabilidad según Contexto del Proyecto y Stack Tecnológico**

Scrumban se adapta perfectamente a la **evolución progresiva** de nuestro stack:

- **Fase 1** (Shadcn/ui + Tailwind): Ceremonias ligeras, focus en setup rápido
- **Fase 2** (Atomic Design + Storybook): Ceremonias más estructuradas, quality
  gates por componente
- **Backend Clean Architecture**: Review específico por capa (Domain,
  Application, Infrastructure)
- **Equipos distribuidos**: Herramientas async-first, documentación como código

- Para proyectos pequeños: Opera como Kanban con flujo continuo y menos
  ceremonias
- Para proyectos complejos: Incorpora sprints y ceremonias de Scrum para mayor
  control
- Se ajusta automáticamente a las necesidades del equipo y stakeholders

### 📊 **Compatibilidad Perfecta con Story Points**

- Mantiene la estimación relativa de complejidad característica de Scrum
- Permite tracking de velocity para mejorar estimaciones futuras
- Facilita refinamiento continuo del backlog basado en capacity del equipo

### 🎯 **Optimización para nuestro Stack Tecnológico Específico**

- **React + TypeScript + Shadcn/ui**: Ideal para desarrollo incremental con
  feedback rápido y componentes tipados
- **Atomic Design progression**: Ceremonias adaptadas para evolucionar de UI
  components a design system
- **Laravel Clean Architecture**: Desarrollo por capas con review gates
  específicos (Domain → Application → Infrastructure)
- **Zustand vs Redux Toolkit**: Quality gates diferenciados según complejidad
  del proyecto (<50 vs +50 components)
- **MySQL 8.0+ con soft deletes**: Permite iteraciones de base de datos
  alineadas con sprints, historial completo

### 🏗️ **Alineación con Principios de Calidad y Stack**

- **SOLID + Clean Architecture**: Iteraciones cortas favorecen refactoring por
  capas
- **WIP Limits por tecnología**: Frontend (Shadcn components), Backend (Use
  Cases), Database (migrations)
- **Code Reviews específicos**: TypeScript strict mode, ESLint + Prettier,
  PHPStan Level 9
- **Testing integrado**: Jest + RTL (frontend), PHPUnit (backend), coverage
  mínimo 80%
- **Performance gates**: Lighthouse >90, API response <200ms, bundle size
  monitoring

### 📈 **Mejora Continua con Métricas Stack-Específicas**

- **Retrospectivas técnicas**: Quality gates por tecnología, performance
  metrics, architecture debt
- **Métricas visuales por capa**: Frontend (component reusability), Backend (use
  case complexity), Database (query performance)
- **Stack evolution tracking**: Progreso Shadcn/ui → Atomic Design, Clean
  Architecture maturity
- **Developer experience metrics**: Build time, hot reload performance, test
  execution speed
- Adaptación basada en datos del desempeño del equipo

## ¿Qué Debe Incluir?

### 🏃‍♂️ **Elementos de Scrum Adoptados**

#### **Ceremonias Clave**

- **Sprint Planning**: Definición de objetivos y selección de user stories
- **Daily Standups**: Sincronización diaria del equipo (máximo 15 minutos)
- **Sprint Review**: Demo de funcionalidades completadas a stakeholders
- **Retrospectivas**: Análisis de qué funcionó y qué mejorar

#### **Artefactos**

- **Product Backlog**: Lista priorizada de user stories con story points
- **Sprint Backlog**: Subset de user stories seleccionadas para el sprint actual
- **Definition of Done**: Criterios claros para considerar una story completada

### 🌊 **Elementos de Kanban Adoptados**

#### **Tablero Visual**

- **Columnas de Flujo**: To Do → In Progress → Code Review → Testing → Done
- **WIP Limits**: Límites máximos de items en cada columna
- **Swimlanes**: Separación por tipo (Feature, Bug, Technical Debt)

#### **Métricas de Flujo**

- **Lead Time**: Tiempo desde creación hasta entrega
- **Cycle Time**: Tiempo desde inicio de desarrollo hasta entrega
- **Throughput**: Número de items completados por período

### 🎯 **Configuración Específica para nuestro Stack**

#### **Columnas Especializadas**

- **Backend Ready**: Stories listas para desarrollo de APIs Laravel
- **Frontend Ready**: Stories listas para desarrollo React/TypeScript
- **Mobile Ready**: Stories listas para desarrollo React Native
- **Integration**: Stories que requieren integración entre capas

#### **Definition of Done por Tecnología**

- **Backend**: API documentada, tests unitarios, code review aprobado
- **Frontend**: Componente testeado, responsive, integrado con API
- **Mobile**: Funcionalidad testeada en iOS/Android, navegación correcta
- **Base de Datos**: Migración versionada, índices optimizados, backup tested

### 📊 **Sistema de Estimación y Métricas**

#### **Story Points Relativos**

- Escala: 1, 2, 3, 5, 8, 13 (Fibonacci modificado)
- Planning Poker para estimaciones colaborativas
- Re-estimación basada en velocity histórica

#### **Métricas de Desempeño**

- **Velocity**: Story points completados por sprint
- **Burndown Chart**: Progreso diario del sprint
- **Cumulative Flow Diagram**: Visualización de bottlenecks

## ¿Qué Debo Hacer?

### 🛠️ **1. Setup del Tablero Scrumban con Stack Integration**

- **Herramienta**: GitHub Projects (integration con repos), Linear
  (tech-focused), o Jira (enterprise)
- **Configuración**: Columnas de flujo con WIP limits por tecnología y capa
  arquitectónica
- **Personalización**: Swimlanes por stack (Frontend React, Backend Laravel,
  Mobile React Native, Infrastructure)
- **Automatización**: CI/CD integration, automated testing gates, performance
  monitoring triggers
- **Labels específicas**: `shadcn-component`, `atomic-design`, `use-case`,
  `domain-entity`, `infrastructure`, `migration`

### 📋 **2. Definición de Ceremonias Adaptadas con Quality Gates**

- **Sprint Planning**: Cada 2 semanas, duración máxima 2 horas, incluye
  architecture review y performance targets
- **Daily Standups**: Diarios a las 9:00 AM, máximo 15 minutos, format:
  ayer/hoy/blockers + quality metrics check
- **Sprint Review**: Cada 2 semanas, demo funcional + architecture evolution +
  performance validation
- **Retrospectiva**: Cada 2 semanas, format Start/Stop/Continue + stack-specific
  improvements (build time, DX, code quality)
- **Architecture Review**: Monthly, Clean Architecture compliance, design system
  evolution, technical debt assessment

### 🎯 **3. Templates y Estándares Stack-Específicos**

- **User Story Template**: Como [usuario] quiero [funcionalidad] para
  [beneficio] + acceptance criteria técnicos específicos
- **Definition of Done Checklist**:
  - Frontend: TypeScript strict, ESLint clean, Jest tests >80%, Lighthouse >90
  - Backend: PHPStan Level 9, PHPUnit tests >90%, API docs updated, use case
    documented
  - Database: Migration + rollback tested, indexes optimized, soft deletes
    implemented
- **Bug Report Template**: Pasos para reproducir, ambiente, stack trace,
  performance impact, severidad
- **Technical Debt Template**: Layer affected
  (Domain/Application/Infrastructure), refactoring effort, impact on velocity
- **Component Story Template**: Shadcn/ui → Atomic Design migration plan,
  reusability assessment, Storybook docs

### 📊 **4. Métricas y Reporting con Stack Integration**

- **Dashboard de Métricas**: Velocity, lead time, cycle time, code quality,
  performance metrics, bundle size
- **Sprint Report**: Burndown, completed vs planned, retrospective insights,
  technical debt evolution
- **Stack Health Report**: Build times, test coverage, performance regression,
  dependency updates needed
- **Monthly Review**: Velocity trends, architecture evolution (Shadcn → Atomic),
  performance improvements, DX satisfaction
- **Quarterly Planning**: Roadmap alignment (UI evolution Q1-Q3), architecture
  maturity assessment, stack upgrades planning

## Tips

### 🎯 **Para Implementación Inicial con Stack Específico**

- **Comienza simple**: Tablero básico con columnas por stack
  (Frontend/Backend/Database), agrega complejidad gradualmente
- **Calibra WIP limits por tecnología**: React components (2-3), Laravel use
  cases (2-3), migrations (1-2)
- **Entrena al equipo**: TypeScript best practices, Clean Architecture
  principles, testing strategies específicas
- **Stack evolution**: Planifica transición Shadcn/ui → Atomic Design con
  ceremonias específicas de validación

### 🔄 **Para Adaptación según Proyecto y Stack Maturity**

- **Proyectos pequeños** (1-3 devs): Shadcn/ui focus, ceremonias ligeras,
  sprints de 1 semana, Zustand state management
- **Proyectos medianos** (4-7 devs): Balance Shadcn→Atomic, sprints de 2
  semanas, Redux Toolkit consideration
- **Proyectos grandes** (8+ devs): Full Atomic Design, ceremonias completas,
  múltiples equipos, Storybook mandatory
- **Proyectos enterprise**: Clean Architecture strict compliance, performance
  SLAs, multi-tenant considerations

### 📊 **Para Estimación Efectiva Stack-Aware**

- **Establece referencia técnica**: Define user story de 3 puntos incluyendo
  Frontend + Backend + Testing
- **Estima por complejidad de stack**: Componente Shadcn (1-2pts), Atomic
  molecule (3-5pts), Use case simple (2-3pts), Use case complex (5-8pts)
- **Re-calibra con métricas técnicas**: Velocity ajustado por build time, test
  coverage, performance compliance
- **Incluye todo el trabajo técnico**: Stories, bugs, tech debt, Shadcn→Atomic
  migration, dependency updates, performance optimization

### 🚫 **Errores Comunes a Evitar con Stack Específico**

- **No respetar WIP limits por tecnología**: Permite que el tablero se sature en
  una capa específica (frontend vs backend)
- **Ceremonias muy largas sin focus técnico**: Mantén timeboxing estricto y
  agenda stack-specific
- **Ignorar métricas técnicas**: Build time, test coverage, performance deben
  guiar mejoras del proceso
- **Resistencia a evolution gradual**: Shadcn→Atomic migration debe ser
  iterativa, no big-bang
- **Saltarse quality gates**: TypeScript strict mode, ESLint rules, performance
  thresholds son NO negociables

## Ejemplos

### 🏃‍♂️ **Ejemplo de Tablero Scrumban Stack-Específico**

#### **Columnas Principales con Quality Gates**

```
📝 Backlog → 🔍 Ready → 👨‍💻 In Progress → 🧪 Testing → 👀 Code Review → 🚀 Deploy Ready → ✅ Done
     (∞)        (8)         (5)              (3)          (3)             (2)            (∞)
```

#### **Swimlanes por Stack + Fase**

- **🎨 Frontend (Shadcn Phase)**: Componentes UI básicos, setup inicial
- **🎭 Frontend (Atomic Phase)**: Atoms, Molecules, Organisms, Templates
- **⚡ Backend (Domain Layer)**: Entities, Value Objects, Domain Services
- **🔧 Backend (Application Layer)**: Use Cases, DTOs, Commands/Queries
- **🏗️ Backend (Infrastructure)**: Controllers, Repositories, External APIs
- **💾 Database**: Migrations, Seeders, Indexes, Performance tuning

#### **Labels por Stack y Evolución**

- **🚀 Features**: Nuevas funcionalidades con stack completo (Frontend +
  Backend + DB)
- **🐛 Bugs**: Correcciones por capa específica (UI bug, Use Case bug, Database
  performance)
- **🔧 Tech Debt**: Refactoring por stack (Shadcn→Atomic migration, Clean
  Architecture compliance)
- **📚 Research**: Spikes técnicos (new dependencies, performance optimization,
  architecture patterns)
- **🎨 UI Evolution**: Transición progresiva Shadcn/ui → Atomic Design
- **🏗️ Architecture**: Clean Architecture implementation y compliance validation

### 📋 **Ejemplo de Ceremonias Adaptadas**

#### **Sprint Planning Stack-Integrated (2 horas cada 2 semanas)**

```
⏰ Agenda:
1. Stack health review (build time, coverage, performance) (15 min)
2. Architecture evolution checkpoint (Shadcn→Atomic progress) (20 min)
3. Refinamiento de top stories con criterios técnicos (45 min)
4. Planning poker con complexity stack-aware (30 min)
5. Sprint goal + quality targets (performance, coverage) (10 min)
```

#### **Daily Standup Stack-Aware (15 minutos)**

```
📊 Formato por persona:
- ✅ Ayer completé: [tasks] + quality gates passed (tests, linting, performance)
- 🎯 Hoy trabajaré en: [tasks] + stack focus (Frontend/Backend/DB)
- 🚧 Blockers: [impediments] + technical dependencies (APIs, components, migrations)
- 📊 Board update + build status + test coverage check
- 🎯 Performance impact assessment (bundle size, API response time)
```

#### **Retrospectiva Stack-Focused (1 hora cada 2 semanas)**

```
🔄 Formato Start/Stop/Continue + Stack Evolution:
- 🚀 Start: ¿Qué debemos empezar? (nuevas prácticas, tools, stack evolution)
- 🛑 Stop: ¿Qué debemos dejar? (deprecated practices, performance bottlenecks)
- ✅ Continue: ¿Qué funciona? (quality gates, development flow, stack choices)
- 📊 Stack metrics review: build time trends, test coverage evolution, performance regression
- 🎯 Action items stack-specific: architecture improvements, tooling upgrades, process optimization
- 🗺️ Evolution roadmap: Shadcn→Atomic progress, Clean Architecture maturity, performance goals
```

### 📊 **Ejemplo de User Story Stack-Completa con Quality Gates**

#### **Story: Sistema de Autenticación JWT**

```
📝 Como usuario del sistema
   Quiero poder iniciar sesión con email y contraseña válidos
   Para acceder a las funcionalidades protegidas con token JWT seguro

🎯 Story Points: 8 (Frontend: 3pts, Backend: 5pts)

✅ Criterios de Aceptación:
- Usuario puede login con email/password válidos usando componente Shadcn/ui
- Sistema retorna JWT token con expiración configurada (8 horas)
- Frontend almacena token en httpOnly cookie con refresh automático
- Usuario es redirigido al dashboard tras login exitoso con loading states
- Session expira y redirige a login tras 8 horas de inactividad

🔧 Definition of Done por Stack:

Backend (Laravel Clean Architecture):
- ✅ Domain: User Entity con email value object, Authentication Domain Service
- ✅ Application: LoginUserUseCase con validación de credenciales
- ✅ Infrastructure: AuthController con JWT token generation (Laravel Sanctum)
- ✅ API endpoint POST /api/v1/auth/login documentado en OpenAPI
- ✅ PHPUnit tests >90% coverage, PHPStan Level 9 compliance
- ✅ Response time <200ms, rate limiting implemented

Frontend (React + TypeScript + Shadcn/ui):
- ✅ LoginForm component con Shadcn Input y Button components
- ✅ TypeScript strict mode, proper error types definition
- ✅ Form validation con React Hook Form + Zod schema
- ✅ Zustand store para auth state management con persist
- ✅ Jest + RTL tests >80% coverage, user interaction testing
- ✅ Responsive design validado (mobile-first), accessibility compliant
- ✅ Performance: Lighthouse >90, bundle impact analysis

Database:
- ✅ Migration para users table con soft deletes, created_at/updated_at
- ✅ Indexes optimizados para email lookup performance  
- ✅ Rollback testing completado, backup strategy defined

🏷️ Labels: authentication, domain-layer, shadcn-ui, jwt-security, user-management
📊 Performance Targets: API <200ms, Frontend FCP <1.5s, Bundle +5KB max
```

### 📈 **Ejemplo de Métricas de Sprint Stack-Integrated**

#### **Sprint 3 - Métricas Técnicas Completas**

```
📊 Sprint Metrics:
- Velocity: 42 story points (target: 35)
- Sprint Goal: "JWT Authentication + Shadcn Form Components"
- Completion Rate: 93% (39/42 points completados)
- Stack distribution: Frontend 18pts, Backend 21pts, Database 3pts

⏱️ Tiempos Promedio por Stack:
- Frontend Lead Time: 3.1 días (Shadcn component + testing)
- Backend Lead Time: 4.8 días (Clean Architecture compliance)
- Database Lead Time: 1.2 días (migration + validation)
- Cross-stack integration: 0.8 días (API + Frontend sync)

🎯 Quality Metrics por Tecnología:
Frontend:
- Test Coverage: 87% (target: >80%)
- Lighthouse Performance: 94 (target: >90)
- ESLint violations: 0 (strict mode)
- Bundle size impact: +3.2KB (target: <5KB)

Backend:
- PHPUnit Coverage: 94% (target: >90%)
- PHPStan Level: 9/9 (strict compliance)
- API Response Time: 156ms avg (target: <200ms)
- Use Cases documented: 100%

Database:
- Migration rollback: ✅ tested
- Query performance: <50ms avg
- Indexes optimized: ✅ all tables

📈 Tendencias Stack Evolution:
- Velocity estabilizándose (+7% vs sprint anterior)
- Build time mejorando (-12% desde setup Vite)
- Shadcn→Atomic readiness: 15% (components catalogued)
- Clean Architecture maturity: 78% (domain layer complete)
```

### 🛠️ **Ejemplo de WIP Limits Stack-Específicos por Tamaño de Equipo**

#### **Equipo Pequeño (3 desarrolladores) - Shadcn Phase**

```
📝 Ready: 6 stories (4 Frontend Shadcn, 2 Backend Use Cases)
👨‍💻 In Progress: 3 stories (2 Frontend, 1 Backend)
🧪 Testing: 2 stories (integrated testing Frontend+Backend)
👀 Code Review: 2 stories (TypeScript + PHP review)
```

#### **Equipo Mediano (5 desarrolladores) - Shadcn→Atomic Transition**

```
📝 Ready: 10 stories (5 Frontend evolution, 4 Backend, 1 Database)
👨‍💻 In Progress: 5 stories (3 Frontend, 2 Backend)
🧪 Testing: 3 stories (unit + integration)
👀 Code Review: 3 stories (architecture compliance review)
```

#### **Equipo Grande (8 desarrolladores) - Full Atomic Design**

```
📝 Ready: 16 stories (8 Atomic components, 6 Use Cases, 2 Infrastructure)
👨‍💻 In Progress: 8 stories (4 Frontend atoms/molecules, 3 Backend, 1 DevOps)
🧪 Testing: 4 stories (Storybook + Jest + PHPUnit)
👀 Code Review: 4 stories (design system + architecture compliance)
```

#### **Performance Targets por Equipo Size**

```
Pequeño: Build <30s, Tests <2min, Deploy <5min
Mediano: Build <45s, Tests <4min, Deploy <8min  
Grande: Build <60s, Tests <6min, Deploy <12min
```

## Navegación

[⬅️ Roadmap del Proyecto](./roadmap-proyecto.md) |
[🏠 README Principal](../../README.md) |
[Definición del Alcance ➡️](./definicion-alcance.md)
