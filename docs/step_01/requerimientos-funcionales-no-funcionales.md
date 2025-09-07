# Definición de Requerimientos Funcionales y No Funcionales

## ¿Qué es?

Los **requerimientos funcionales** definen **qué** debe hacer el sistema - las
funcionalidades específicas, características y servicios que el software debe
proporcionar. Describen el comportamiento del sistema bajo condiciones
específicas.

Los **requerimientos no funcionales** definen **cómo** debe comportarse el
sistema - los atributos de calidad como rendimiento, seguridad, usabilidad,
escalabilidad y mantenibilidad. Establecen las restricciones y criterios de
calidad que el sistema debe cumplir.

Juntos forman la base contractual entre stakeholders y el equipo de desarrollo,
definiendo tanto las capacidades como las características de calidad del
sistema.

## ¿Por qué es Importante?

### 🎯 **Claridad en el Desarrollo**

- Elimina ambigüedades sobre lo que debe construirse
- Proporciona criterios objetivos para validar si el sistema está completo
- Facilita la estimación precisa de esfuerzo y tiempo

### ⚖️ **Base Legal y Contractual**

- Establece compromisos claros entre cliente y proveedor
- Define criterios de aceptación verificables
- Protege tanto al cliente como al equipo de desarrollo

### 🏗️ **Fundamento Arquitectónico**

- Los requerimientos no funcionales determinan decisiones arquitectónicas
  críticas
- Influyen en la selección de tecnologías y patrones de diseño
- Definen las métricas de rendimiento que debe alcanzar el sistema

### 💰 **Control de Costos**

- Previene el scope creep al tener límites claramente definidos
- Permite identificar cambios y su impacto en tiempo/costo
- Facilita la priorización de funcionalidades según valor de negocio

## ¿Qué Debe Incluir?

### 📋 **Requerimientos Funcionales**

#### **Casos de Uso Detallados**

- Actor principal y secundarios
- Precondiciones y postcondiciones
- Flujo principal y flujos alternativos
- Manejo de excepciones

#### **Reglas de Negocio**

- Validaciones de datos específicas
- Lógica de cálculos y algoritmos
- Flujos de trabajo (workflows)
- Permisos y autorizaciones por rol

#### **Interfaces y Integraciones**

- APIs que debe exponer el sistema
- Servicios externos que debe consumir
- Formatos de intercambio de datos
- Protocolos de comunicación

### ⚡ **Requerimientos No Funcionales**

#### **Rendimiento**

- Tiempo de respuesta máximo por funcionalidad
- Throughput esperado (transacciones por segundo)
- Número de usuarios concurrentes soportados
- Tiempo de arranque del sistema

#### **Seguridad**

- Métodos de autenticación y autorización
- Cifrado de datos en tránsito y reposo
- Políticas de contraseñas
- Auditoría y logging de seguridad

#### **Usabilidad**

- Tiempo máximo para completar tareas comunes
- Curva de aprendizaje esperada
- Accesibilidad (WCAG compliance)
- Compatibilidad con navegadores/dispositivos

#### **Escalabilidad y Disponibilidad**

- Capacidad de crecimiento esperada
- Tiempo de inactividad máximo permitido (SLA)
- Estrategias de backup y recuperación
- Distribución geográfica

## ¿Qué Debo Hacer?

### 📝 **1. Documento de Especificación de Requerimientos (SRS)**

- **Herramienta**: Microsoft Word, Google Docs o Confluence
- **Estructura**:
  - Introducción y objetivos
  - Requerimientos funcionales organizados por módulos
  - Requerimientos no funcionales categorizados
  - Casos de uso con diagramas UML
  - Mockups de interfaces principales

### 🎯 **2. Matriz de Trazabilidad**

- **Herramienta**: Excel o herramientas especializadas como Jira
- **Contenido**: Vincular cada requerimiento con casos de prueba, diseño y
  código
- **Propósito**: Asegurar que todos los requerimientos sean implementados y
  probados

### 📊 **3. Priorización de Requerimientos**

- **Método**: MoSCoW (Must have, Should have, Could have, Won't have)
- **Herramienta**: Excel con clasificación por colores
- **Involucrados**: Product Owner y stakeholders clave

### ✅ **4. Criterios de Aceptación**

- **Formato**: Given-When-Then para cada requerimiento funcional
- **Herramienta**: Gherkin syntax en herramientas como Cucumber
- **Validación**: Revisión con stakeholders antes de desarrollo

## Tips

### 💡 **Para Requerimientos Funcionales**

- **Usa verbos de acción específicos**: "El sistema debe calcular..." en lugar
  de "El sistema debe manejar..."
- **Sé específico con los datos**: Define formatos, rangos y validaciones
  exactas
- **Incluye flujos de excepción**: No solo el camino feliz, sino qué pasa cuando
  algo falla
- **Vincula con objetivos de negocio**: Cada requerimiento debe tener un "por
  qué"

### ⚡ **Para Requerimientos No Funcionales**

- **Usa métricas cuantificables**: "Tiempo de respuesta < 2 segundos" no
  "Respuesta rápida"
- **Define condiciones de prueba**: Especifica bajo qué condiciones se medirán
  las métricas
- **Considera el contexto de uso**: Los requerimientos varían según el tipo de
  usuario y situación
- **Evalúa trade-offs**: Algunos requerimientos pueden ser conflictivos
  (seguridad vs usabilidad)

### 🔍 **Técnicas de Validación**

- **Revisiones con stakeholders**: Sesiones estructuradas de validación
- **Prototipos rápidos**: Para validar interfaces y flujos de usuario
- **Casos de prueba tempranos**: Escribir tests antes del desarrollo
- **Análisis de impacto**: Evaluar cómo cada cambio afecta otros requerimientos

## Ejemplos

### 📋 **Ejemplo de Requerimiento Funcional**

**RF-001: Registro de Usuario**

- **Descripción**: El sistema debe permitir a nuevos usuarios registrarse
  proporcionando información básica
- **Actor**: Usuario no registrado
- **Precondiciones**: El usuario accede a la página de registro
- **Flujo Principal**:
  1. El usuario ingresa email, contraseña, nombre completo
  2. El sistema valida que el email no esté registrado
  3. El sistema valida que la contraseña cumpla políticas de seguridad
  4. El sistema envía email de confirmación
  5. El usuario confirma su cuenta mediante el enlace recibido
- **Postcondiciones**: Usuario creado y activado en el sistema
- **Reglas de Negocio**:
  - Email debe ser único en el sistema
  - Contraseña mínimo 8 caracteres, incluir mayúscula, minúscula y número

### ⚡ **Ejemplo de Requerimiento No Funcional**

**RNF-001: Rendimiento de Búsqueda**

- **Categoría**: Rendimiento
- **Descripción**: Las búsquedas de productos deben completarse en menos de 2
  segundos
- **Condiciones de Medición**:
  - Base de datos con 100,000 productos
  - 500 usuarios concurrentes
  - Consulta desde navegador web estándar
- **Criterio de Aceptación**: 95% de las búsquedas deben completarse en < 2
  segundos
- **Método de Prueba**: JMeter con escenarios de carga definidos

## Navegación

**Progreso en Planificación y Análisis:**

- ✅ [Planificación y Análisis - Introducción](./planificacion-analisis.md)
- ✅ **Requerimientos Funcionales y No Funcionales** ← Estás aquí
- ⏭️ [Análisis de Stakeholders](./analisis-stakeholders.md)
- ⏭️ [Definición del Alcance del Proyecto](./definicion-alcance.md)
- ⏭️ [Estimación de Tiempos y Recursos](./estimacion-tiempos-recursos.md)
- ⏭️ [Análisis de Riesgos](./analisis-riesgos.md)

---

### Siguiente Paso

Continúa con el [**Análisis de Stakeholders**](./analisis-stakeholders.md) para
identificar y gestionar a todas las personas que influirán en el éxito de tu
proyecto.

[⬅️ Volver a Planificación](./planificacion-analisis.md) |
[➡️ Análisis de Stakeholders](./analisis-stakeholders.md)
