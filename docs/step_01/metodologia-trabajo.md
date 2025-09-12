# Metodología de Trabajo

## ¿Qué es?

**Scrumban** es una metodología híbrida que combina la estructura y disciplina
de **Scrum** con la flexibilidad y flujo continuo de **Kanban**. Esta
metodología toma las mejores prácticas de ambos frameworks: las ceremonias y
estimaciones de Scrum junto con el sistema de flujo visual y límites de trabajo
en progreso (WIP) de Kanban.

Para nuestro estándar de proyectos, Scrumban proporciona la **flexibilidad
necesaria** para adaptarse tanto a proyectos pequeños (que requieren más
agilidad) como a proyectos grandes (que necesitan más estructura), manteniendo
siempre la **disciplina de estimación con story points** y la **mejora
continua** a través de retrospectivas regulares.

## ¿Por qué es Importante?

### 🔄 **Adaptabilidad según Contexto del Proyecto**

- Para proyectos pequeños: Opera como Kanban con flujo continuo y menos
  ceremonias
- Para proyectos complejos: Incorpora sprints y ceremonias de Scrum para mayor
  control
- Se ajusta automáticamente a las necesidades del equipo y stakeholders

### 📊 **Compatibilidad Perfecta con Story Points**

- Mantiene la estimación relativa de complejidad característica de Scrum
- Permite tracking de velocity para mejorar estimaciones futuras
- Facilita refinamiento continuo del backlog basado en capacity del equipo

### 🎯 **Optimización para nuestro Stack Tecnológico**

- **React + TypeScript**: Ideal para desarrollo incremental con feedback rápido
- **Laravel APIs**: Permite desarrollo de endpoints bajo demanda según
  prioridades del tablero
- **React Native**: Facilita sincronización entre equipos web y móvil
- **MySQL**: Permite iteraciones de base de datos alineadas con sprints

### 🏗️ **Alineación con Principios de Calidad**

- **SOLID + Clean Code**: Iteraciones cortas favorecen refactoring continuo
- **WIP Limits**: Evitan código apresurado que compromete principios de calidad
- **Code Reviews**: Integradas naturalmente en el flujo de trabajo
- **Testing**: TDD/BDD se integra perfectamente con user stories

### 📈 **Mejora Continua Estructurada**

- Retrospectivas regulares para optimizar procesos de desarrollo
- Métricas visuales que muestran bottlenecks en tiempo real
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

### 🛠️ **1. Setup del Tablero Scrumban**

- **Herramienta**: Jira, Trello con Power-Ups, o Azure DevOps
- **Configuración**: Crear columnas de flujo con WIP limits apropiados
- **Personalización**: Agregar swimlanes por tipo de trabajo y etiquetas por
  tecnología
- **Automatización**: Configurar transiciones automáticas y notificaciones

### 📋 **2. Definición de Ceremonias Adaptadas**

- **Sprint Planning**: Cada 2 semanas, duración máxima 2 horas
- **Daily Standups**: Diarios a las 9:00 AM, máximo 15 minutos, format:
  ayer/hoy/blockers
- **Sprint Review**: Cada 2 semanas, demo + feedback de stakeholders
- **Retrospectiva**: Cada 2 semanas, format Start/Stop/Continue

### 🎯 **3. Templates y Estándares**

- **User Story Template**: Como [usuario] quiero [funcionalidad] para
  [beneficio]
- **Definition of Done Checklist**: Lista verificable por tecnología
- **Bug Report Template**: Pasos para reproducir, ambiente, severidad
- **Technical Debt Template**: Descripción técnica, impacto, esfuerzo estimado

### 📊 **4. Métricas y Reporting**

- **Dashboard de Métricas**: Velocity, lead time, cycle time, quality metrics
- **Sprint Report**: Burndown, completed vs planned, retrospective insights
- **Monthly Review**: Tendencias de velocity, predictibilidad, satisfaction
- **Quarterly Planning**: Ajustes de proceso basados en datos históricos

## Tips

### 🎯 **Para Implementación Inicial**

- **Comienza simple**: Inicia con tablero básico y agrega complejidad
  gradualmente
- **Calibra WIP limits**: Empieza conservador (2-3 items por columna) y ajusta
  según flow
- **Entrena al equipo**: Asegura que todos entiendan los principios de ambas
  metodologías
- **Iterative improvement**: Usa retrospectivas para optimizar el proceso
  específicamente

### 🔄 **Para Adaptación según Proyecto**

- **Proyectos pequeños** (1-3 devs): Más Kanban, menos ceremonias, sprints de 1
  semana
- **Proyectos medianos** (4-6 devs): Balance 50/50, sprints de 2 semanas
- **Proyectos grandes** (7+ devs): Más Scrum, ceremonias completas, múltiples
  equipos
- **Proyectos críticos**: Agregar ceremonias adicionales de quality gate

### 📊 **Para Estimación Efectiva**

- **Establece referencia**: Define una story de 3 puntos como baseline para
  comparación
- **Estima en equipo**: Planning poker involucra a todo el equipo técnico
- **Re-calibra regularmente**: Ajusta estimaciones basado en velocity real del
  equipo
- **Incluye todo el trabajo**: Stories, bugs, technical debt, research spikes

### 🚫 **Errores Comunes a Evitar**

- **No respetar WIP limits**: Permite que el tablero se llene y se pierda el
  flujo
- **Ceremonias muy largas**: Mantén focus y timeboxing estricto
- **Ignorar métricas**: Los datos deben guiar mejoras del proceso
- **Resistencia al cambio**: El proceso debe evolucionar basado en feedback del
  equipo

## Ejemplos

### 🏃‍♂️ **Ejemplo de Tablero Scrumban**

#### **Columnas Principales**

```
📝 Backlog → 🔍 Ready → 👨‍💻 In Progress → 👀 Code Review → 🧪 Testing → ✅ Done
     (∞)          (8)          (4)              (3)             (3)         (∞)
```

#### **Swimlanes por Tipo**

- **🚀 Features**: Nuevas funcionalidades del roadmap
- **🐛 Bugs**: Correcciones de defectos reportados
- **🔧 Tech Debt**: Mejoras técnicas y refactoring
- **📚 Research**: Spikes de investigación técnica

### 📋 **Ejemplo de Ceremonias Adaptadas**

#### **Sprint Planning (2 horas cada 2 semanas)**

```
⏰ Agenda:
1. Review de velocity anterior (15 min)
2. Refinamiento de top stories (30 min)
3. Planning poker para nuevas stories (45 min)
4. Selección de sprint backlog (20 min)
5. Definición de sprint goal (10 min)
```

#### **Daily Standup (15 minutos)**

```
📊 Formato por persona:
- ✅ Ayer completé: [tasks]
- 🎯 Hoy trabajaré en: [tasks]  
- 🚧 Blockers: [impediments]
- 📊 Actualización del tablero en vivo
```

#### **Retrospectiva (1 hora cada 2 semanas)**

```
🔄 Formato Start/Stop/Continue:
- 🚀 Start: ¿Qué debemos empezar a hacer?
- 🛑 Stop: ¿Qué debemos dejar de hacer?
- ✅ Continue: ¿Qué está funcionando bien?
- 📊 Review de métricas del sprint
- 🎯 Action items con owner y deadline
```

### 📊 **Ejemplo de User Story con Criterios**

#### **Story: Autenticación de Usuario**

```
📝 Como usuario del sistema
   Quiero poder iniciar sesión con email y contraseña
   Para acceder a las funcionalidades del sistema

🎯 Story Points: 5

✅ Criterios de Aceptación:
- Usuario puede login con email/password válidos
- Sistema muestra error para credenciales inválidas
- Usuario es redirigido al dashboard tras login exitoso
- Session expira después de 8 horas de inactividad

🔧 Definition of Done:
Backend:
- ✅ API endpoint POST /auth/login implementado
- ✅ Validación de email y password
- ✅ JWT token generation
- ✅ Tests unitarios > 90% coverage
- ✅ Documentación API actualizada

Frontend:
- ✅ Componente Login implementado
- ✅ Validación de formulario
- ✅ Manejo de estados de loading/error
- ✅ Tests de componente
- ✅ Responsive design validado

🏷️ Labels: authentication, backend, frontend, security
```

### 📈 **Ejemplo de Métricas de Sprint**

#### **Sprint 3 - Métricas de Desempeño**

```
📊 Velocity: 34 story points (target: 30)
📅 Sprint Goal: Sistema de autenticación completo
🎯 Completion Rate: 85% (29/34 points completados)

⏱️ Tiempos Promedio:
- Lead Time: 4.2 días
- Cycle Time: 2.8 días  
- Code Review Time: 0.5 días
- Testing Time: 0.8 días

🎯 Quality Metrics:
- Bugs encontrados en testing: 2
- Code coverage: 94%
- User stories con rework: 1

📈 Tendencias:
- Velocity estable (+13% vs sprint anterior)
- Lead time mejorando (-15% vs sprint anterior)
- Quality metrics dentro de target
```

### 🛠️ **Ejemplo de WIP Limits por Tamaño de Equipo**

#### **Equipo Pequeño (3 desarrolladores)**

```
📝 Ready: 6 stories
👨‍💻 In Progress: 3 stories  
👀 Code Review: 2 stories
🧪 Testing: 2 stories
```

#### **Equipo Mediano (5 desarrolladores)**

```
📝 Ready: 10 stories
👨‍💻 In Progress: 5 stories
👀 Code Review: 3 stories  
🧪 Testing: 3 stories
```

#### **Equipo Grande (8 desarrolladores)**

```
📝 Ready: 16 stories
👨‍💻 In Progress: 8 stories
👀 Code Review: 4 stories
🧪 Testing: 4 stories
```

## Navegación

[⬅️ Roadmap del Proyecto](./roadmap-proyecto.md) |
[🏠 README Principal](../../README.md) |
[Definición del Alcance ➡️](./definicion-alcance.md)
