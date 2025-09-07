# Estimación de Tiempos y Recursos

## ¿Qué es?

La **estimación de tiempos y recursos** es el proceso de calcular de manera
fundamentada el esfuerzo, tiempo y recursos necesarios para completar cada
componente del proyecto. Incluye la evaluación de la complejidad técnica, la
identificación de dependencias, la asignación de recursos humanos especializados
y la definición de cronogramas realistas que consideren riesgos y contingencias.

Este proceso utiliza **story points** como unidad de medida relativa para
estimar la complejidad y esfuerzo, permitiendo comparaciones consistentes entre
diferentes tareas y facilitando la planificación iterativa típica de
metodologías ágiles.

## ¿Por qué es Importante?

### 📅 **Planificación Realista**

- Establece expectativas alcanzables con stakeholders y equipo de desarrollo
- Permite la identificación temprana de cuellos de botella y dependencias
  críticas
- Facilita la toma de decisiones sobre priorización y asignación de recursos

### 💰 **Control Presupuestal**

- Proporciona base sólida para cotizaciones y presupuestos precisos
- Permite monitoreo continuo del progreso vs. estimaciones originales
- Facilita la justificación de inversiones en recursos adicionales

### 🎯 **Gestión de Expectativas**

- Comunica claramente los tiempos de entrega esperados
- Proporciona visibilidad sobre el avance del proyecto
- Permite negociación informada sobre alcance vs. tiempo vs. calidad

### 📊 **Mejora Continua**

- Genera datos históricos para mejorar estimaciones futuras
- Identifica patrones de productividad del equipo
- Facilita la calibración de la velocidad de desarrollo

### ⚖️ **Equilibrio de Cargas de Trabajo**

- Identifica sobrecarga de recursos específicos
- Permite redistribución de tareas según especialización
- Facilita decisiones sobre contratación o subcontratación

## ¿Qué Debe Incluir?

### 📊 **Sistema de Story Points**

La estimación se basa en **story points** que representan la complejidad
relativa de cada tarea, considerando esfuerzo, complejidad técnica y riesgo:

| **Story Points** | **Complejidad** | **Descripción**                                                                                                                   |
| ---------------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **1**            | Muy Baja        | Tareas triviales, conocidas, sin riesgo. Cambios menores en CSS, corrección de typos, configuraciones básicas.                    |
| **2**            | Baja            | Tareas simples con implementación clara. Componentes básicos de UI, CRUD simple, validaciones estándar.                           |
| **3**            | Baja-Media      | Funcionalidades estándar con alguna lógica. Formularios con validaciones complejas, reportes básicos.                             |
| **5**            | Media           | Funcionalidades que requieren diseño e integración. APIs con lógica de negocio, componentes complejos, integraciones simples.     |
| **8**            | Media-Alta      | Funcionalidades complejas con múltiples componentes. Módulos completos, integraciones complejas, optimizaciones de rendimiento.   |
| **13**           | Alta            | Funcionalidades muy complejas o inciertas. Arquitectura nueva, investigación técnica, integraciones críticas con sistemas legacy. |

### 🎯 **Desglose por Tecnología**

#### **Frontend (React + TypeScript)**

- Setup inicial del proyecto y configuración
- Componentes de UI reutilizables
- Páginas y flujos de navegación
- Integración con APIs del backend
- Testing de componentes y e2e

#### **Backend (Laravel)**

- Configuración de proyecto y arquitectura
- Modelos, migraciones y seeders
- Controladores y middleware
- APIs REST con documentación
- Testing unitario e integración

#### **Móvil (React Native)**

- Setup del proyecto cross-platform
- Pantallas y navegación nativa
- Integración con APIs
- Testing en dispositivos
- Preparación para stores

#### **Base de Datos (MySQL)**

- Diseño de esquema y relaciones
- Optimización de consultas
- Índices y performance tuning
- Scripts de migración y backup

### 📋 **Factores de Ajuste**

#### **Complejidad Técnica**

- Nuevas tecnologías para el equipo (+20-50%)
- Integraciones complejas (+30-40%)
- Requerimientos de performance estrictos (+25-35%)
- Consideraciones de seguridad avanzadas (+20-30%)

#### **Factores del Equipo**

- Experiencia del equipo con el stack tecnológico
- Disponibilidad de recursos senior vs junior
- Distribución geográfica del equipo
- Overlapping de responsabilidades

#### **Factores Externos**

- Dependencias de terceros
- Aprobaciones y validaciones de stakeholders
- Disponibilidad de ambientes de testing
- Restricciones de calendario (vacaciones, feriados)

## ¿Qué Debo Hacer?

### 📋 **1. Backlog Estimado con Story Points**

- **Herramienta**: Jira, Azure DevOps, o Trello con plugins de estimación
- **Contenido**: Cada user story con su estimación en story points
- **Metodología**: Planning poker para estimaciones colaborativas
- **Refinamiento**: Revisión semanal de estimaciones pendientes

### ⏱️ **2. Cronograma Detallado del Proyecto**

- **Herramienta**: Microsoft Project, Asana, o Monday.com
- **Estructura**: Fases, sprints, hitos y dependencias críticas
- **Baseline**: Versión aprobada para control de avance
- **Actualización**: Semanal con progreso real vs estimado

### 👥 **3. Plan de Asignación de Recursos**

- **Formato**: Matriz de recursos por período de tiempo
- **Contenido**: Roles requeridos, nivel de experiencia, % de dedicación
- **Consideraciones**: Vacaciones, capacitación, otros proyectos
- **Contingencias**: Plan B para recursos críticos no disponibles

### 📊 **4. Métricas de Velocidad del Equipo**

- **Tracking**: Story points completados por sprint
- **Herramienta**: Burndown charts y velocity charts
- **Calibración**: Ajuste de estimaciones basado en velocidad histórica
- **Predictibilidad**: Forecasting de entregas futuras

## Tips

### 🎯 **Para Estimación con Story Points**

- **Usa referencia base**: Establece una tarea de 3 puntos como referencia para
  comparar
- **Estima en equipo**: Las estimaciones colaborativas son más precisas que
  individuales
- **No conviertas a horas**: Los story points representan complejidad, no tiempo
  directo
- **Re-estima cuando aprendas**: Ajusta estimaciones cuando tengas más
  información

### ⏱️ **Para Planificación Temporal**

- **Incluye buffers**: Agrega 20-30% de tiempo adicional para imprevistos
- **Considera la regla 80/20**: 80% del tiempo se va en 20% de las tareas más
  complejas
- **Planifica por fases**: Divide el proyecto en hitos manejables de 2-4 semanas
- **Valida dependencias**: Identifica y mapea todas las dependencias críticas

### 👥 **Para Gestión de Recursos**

- **Considera curva de aprendizaje**: Desarrolladores nuevos son menos
  productivos inicialmente
- **Planifica overlapping**: Asegura transferencia de conocimiento entre
  recursos
- **Identifica especialistas**: Algunas tareas requieren conocimiento específico
- **Mantén flexibilidad**: Permite reasignación de recursos según prioridades

### 📊 **Para Seguimiento y Ajustes**

- **Mide velocidad real**: Tracking de story points completados por sprint
- **Analiza variaciones**: Investiga diferencias significativas entre estimado
  vs real
- **Documenta lecciones aprendidas**: Mejora estimaciones futuras con
  experiencia
- **Comunica cambios**: Notifica impactos en cronograma a stakeholders
  tempranamente

## Ejemplos

### 📋 **Ejemplo de Estimación por Módulo**

**Módulo: Sistema de Autenticación**

| **User Story**           | **Descripción**                        | **Story Points** | **Justificación**                                      |
| ------------------------ | -------------------------------------- | ---------------- | ------------------------------------------------------ |
| Registro de usuarios     | Formulario con validaciones básicas    | **3**            | Funcionalidad estándar, validaciones conocidas         |
| Login con email/password | Autenticación básica con Laravel       | **2**            | Implementación directa con herramientas estándar       |
| Reset de contraseña      | Envío de email y token de recuperación | **5**            | Requiere integración email + validaciones de seguridad |
| Login con redes sociales | OAuth con Google y Facebook            | **8**            | Integración compleja con APIs externas                 |
| Autenticación 2FA        | SMS o authenticator app                | **13**           | Funcionalidad compleja, investigación de librerías     |

**Total del Módulo: 31 Story Points**

### ⏱️ **Ejemplo de Cronograma por Sprint**

**Sprint 1 (2 semanas) - Capacidad: 25 story points**

- Setup proyecto React + TypeScript (3 pts)
- Setup proyecto Laravel (3 pts)
- Configuración base de datos (2 pts)
- Componentes básicos UI (5 pts)
- Modelos básicos de usuario (3 pts)
- APIs de autenticación básica (8 pts)
- **Total Sprint 1: 24 puntos**

**Sprint 2 (2 semanas) - Capacidad: 25 story points**

- Sistema de registro completo (8 pts)
- Reset de contraseña (5 pts)
- Middleware de autenticación (3 pts)
- Testing unitario backend (5 pts)
- Testing componentes frontend (3 pts)
- **Total Sprint 2: 24 puntos**

### 👥 **Ejemplo de Asignación de Recursos**

| **Rol**          | **Nivel** | **Dedicación** | **Sprint 1**       | **Sprint 2**            | **Sprint 3**             |
| ---------------- | --------- | -------------- | ------------------ | ----------------------- | ------------------------ |
| **Tech Lead**    | Senior    | 80%            | Setup arquitectura | Code review + mentoring | Performance optimization |
| **Frontend Dev** | Mid-level | 100%           | Componentes base   | Pantallas auth          | Integración APIs         |
| **Backend Dev**  | Senior    | 100%           | APIs core          | APIs avanzadas          | Testing + deployment     |
| **Mobile Dev**   | Mid-level | 50%            | -                  | Setup RN project        | Pantallas móviles        |
| **QA Engineer**  | Mid-level | 60%            | Plan de testing    | Testing manual          | Automatización           |

### 📊 **Ejemplo de Tracking de Velocidad**

**Velocidad del Equipo:**

- **Sprint 1**: 24 puntos planificados → 22 puntos completados (92%)
- **Sprint 2**: 25 puntos planificados → 26 puntos completados (104%)
- **Sprint 3**: 25 puntos planificados → 23 puntos completados (92%)
- **Velocidad promedio**: 23.7 story points por sprint
- **Predictibilidad**: ±8% de variación

## Navegación

**Progreso en Planificación y Análisis:**

- ✅ [Planificación y Análisis - Introducción](./planificacion-analisis.md)
- ✅
  [Requerimientos Funcionales y No Funcionales](./requerimientos-funcionales-no-funcionales.md)
- ✅ [Análisis de Stakeholders](./analisis-stakeholders.md)
- ✅ [Roadmap del Proyecto](./roadmap-proyecto.md)
- ✅ [Metodología de Trabajo](./metodologia-trabajo.md)
- ✅ [Definición del Alcance del Proyecto](./definicion-alcance.md)
- ✅ **Estimación de Tiempos y Recursos** ← Estás aquí
- ⏭️ [Análisis de Riesgos](./analisis-riesgos.md)

---

### Siguiente Paso

Continúa con el [**Análisis de Riesgos**](./analisis-riesgos.md) para
identificar y mitigar los posibles obstáculos que podrían afectar el éxito de tu
proyecto.

[⬅️ Definición del Alcance](./definicion-alcance.md) |
[🏠 README Principal](../../README.md) |
[➡️ Análisis de Riesgos](./analisis-riesgos.md)
