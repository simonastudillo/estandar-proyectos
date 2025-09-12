# Casos de Uso y User Stories

## ¿Qué es?

Los casos de uso y user stories son técnicas complementarias para capturar y
documentar los requisitos funcionales del sistema desde la perspectiva del
usuario. Los casos de uso proporcionan una descripción detallada de las
interacciones entre los actores y el sistema, mientras que las user stories
ofrecen una perspectiva más ágil y centrada en el valor de negocio.

### Casos de Uso

Son descripciones narrativas de las interacciones entre un actor (usuario,
sistema externo) y el sistema para lograr un objetivo específico. Incluyen
flujos principales, alternativos y de excepción.

### User Stories

Son descripciones cortas y simples de una funcionalidad desde la perspectiva del
usuario final, siguiendo el formato: "Como [tipo de usuario], quiero
[funcionalidad] para [beneficio/valor]".

## ¿Por qué es importante?

1. **Claridad de Requisitos**: Proporcionan una comprensión clara de lo que el
   sistema debe hacer
2. **Comunicación Efectiva**: Facilitan la comunicación entre stakeholders,
   desarrolladores y usuarios
3. **Planificación Ágil**: Las user stories permiten una planificación flexible
   y iterativa
4. **Trazabilidad**: Permiten rastrear desde los requisitos hasta la
   implementación
5. **Validación**: Sirven como base para pruebas de aceptación y validación del
   sistema
6. **Estimación**: Facilitan la estimación de esfuerzo y tiempo de desarrollo

## ¿Qué debe incluir?

### Para Casos de Uso:

- **Identificador único** y nombre descriptivo
- **Actores primarios y secundarios**
- **Precondiciones** que deben cumplirse
- **Postcondiciones** resultado esperado
- **Flujo principal** paso a paso
- **Flujos alternativos** para variaciones
- **Flujos de excepción** para errores
- **Criterios de aceptación**

### Para User Stories:

- **Formato estándar**: Como [rol] quiero [funcionalidad] para [beneficio]
- **Criterios de aceptación** específicos y medibles
- **Prioridad** (Alta, Media, Baja)
- **Story Points** para estimación
- **Definición de Hecho** (Definition of Done)
- **Dependencias** con otras stories

## ¿Qué debo hacer?

### 1. Identificar Actores y Roles

```
Actores del Sistema:
├── Primarios (inician casos de uso)
│   ├── Usuario Final
│   ├── Administrador
│   ├── Operador
│   └── Cliente
└── Secundarios (sistemas externos)
    ├── Sistema de Pagos
    ├── API Externa
    └── Servicio de Notificaciones
```

### 2. Documentar Casos de Uso

#### Plantilla de Caso de Uso

```
UC-001: [Nombre del Caso de Uso]

Actor Primario: [Actor principal]
Actores Secundarios: [Otros actores involucrados]

Precondiciones:
- [Condición 1]
- [Condición 2]

Flujo Principal:
1. [Paso 1]
2. [Paso 2]
3. [Paso 3]
...

Flujos Alternativos:
A1. [Condición alternativa]
  A1.1. [Paso alternativo]
  A1.2. [Retorno al flujo principal]

Flujos de Excepción:
E1. [Condición de error]
  E1.1. [Manejo del error]
  E1.2. [Acción de recuperación]

Postcondiciones:
- [Resultado esperado]

Criterios de Aceptación:
- [Criterio 1]
- [Criterio 2]
```

### 3. Crear User Stories

#### Plantilla de User Story

```
US-001: [Título de la User Story]

Como [tipo de usuario]
Quiero [funcionalidad deseada]
Para [beneficio o valor]

Criterios de Aceptación:
- Dado [contexto]
  Cuando [acción]
  Entonces [resultado esperado]

Prioridad: [Alta/Media/Baja]
Story Points: [1, 2, 3, 5, 8, 13]
Sprint: [Número de sprint]

Definición de Hecho:
☐ Código implementado
☐ Pruebas unitarias
☐ Pruebas de integración
☐ Documentación actualizada
☐ Revisión de código
☐ Validación con stakeholder
```

### 4. Mapear Casos de Uso a User Stories

```
Mapeo UC ↔ US:
UC-001 → US-001, US-002, US-003
UC-002 → US-004, US-005
```

### 5. Priorizar y Estimar

#### Matriz de Priorización

| User Story | Valor Negocio | Complejidad | Prioridad | Story Points |
| ---------- | ------------- | ----------- | --------- | ------------ |
| US-001     | Alto          | Baja        | Alta      | 3            |
| US-002     | Alto          | Alta        | Alta      | 8            |
| US-003     | Medio         | Media       | Media     | 5            |

## Tips

### Para Casos de Uso:

1. **Mantén el foco**: Un caso de uso = un objetivo específico
2. **Usa lenguaje del dominio**: Utiliza términos que entienda el negocio
3. **Sé específico**: Evita ambigüedades en los pasos
4. **Considera todos los escenarios**: Incluye flujos happy path y edge cases
5. **Valida con stakeholders**: Revisa regularmente con usuarios finales

### Para User Stories:

1. **INVEST**: Independent, Negotiable, Valuable, Estimable, Small, Testable
2. **3 C's**: Card (historia), Conversation (discusión), Confirmation
   (criterios)
3. **Evita soluciones técnicas**: Enfócate en el "qué" no en el "cómo"
4. **Historias épicas**: Divide historias grandes en más pequeñas
5. **Criterios SMART**: Specific, Measurable, Achievable, Relevant, Time-bound

### Herramientas Recomendadas:

- **Diagramas**: Lucidchart, Draw.io, PlantUML
- **Gestión**: Jira, Azure DevOps, Trello
- **Documentación**: Confluence, Notion, GitBook
- **Colaboración**: Miro, Mural, FigJam

## Ejemplos

### Ejemplo de Caso de Uso

```
UC-001: Autenticación de Usuario

Actor Primario: Usuario
Actores Secundarios: Sistema de Autenticación

Precondiciones:
- El usuario tiene una cuenta registrada
- El sistema está disponible

Flujo Principal:
1. El usuario accede a la página de login
2. El sistema muestra el formulario de autenticación
3. El usuario ingresa email y contraseña
4. El usuario hace clic en "Iniciar Sesión"
5. El sistema valida las credenciales
6. El sistema autentica al usuario
7. El sistema redirige al dashboard principal

Flujos Alternativos:
A1. Usuario olvida contraseña (paso 3)
  A1.1. El usuario hace clic en "Olvidé mi contraseña"
  A1.2. El sistema muestra formulario de recuperación
  A1.3. El usuario ingresa su email
  A1.4. El sistema envía email de recuperación
  A1.5. Fin del caso de uso

Flujos de Excepción:
E1. Credenciales incorrectas (paso 5)
  E1.1. El sistema muestra mensaje de error
  E1.2. El sistema regresa al paso 2
  E1.3. Se incrementa contador de intentos fallidos

E2. Cuenta bloqueada (paso 5)
  E2.1. El sistema muestra mensaje de cuenta bloqueada
  E2.2. El sistema sugiere recuperación de cuenta
  E2.3. Fin del caso de uso

Postcondiciones:
- El usuario está autenticado en el sistema
- Se registra la sesión del usuario
- Se actualiza la fecha de último acceso

Criterios de Aceptación:
- Las credenciales válidas permiten el acceso
- Las credenciales inválidas muestran error claro
- La cuenta se bloquea después de 5 intentos fallidos
- El sistema redirecciona correctamente después del login
```

### Ejemplo de User Stories

```
US-001: Login de Usuario

Como usuario registrado
Quiero poder iniciar sesión en el sistema
Para acceder a mis datos y funcionalidades personalizadas

Criterios de Aceptación:
- Dado que soy un usuario registrado
  Cuando ingreso email y contraseña correctos
  Entonces puedo acceder al dashboard principal

- Dado que soy un usuario registrado
  Cuando ingreso credenciales incorrectas
  Entonces veo un mensaje de error claro

- Dado que he fallado el login 5 veces
  Cuando intento ingresar nuevamente
  Entonces mi cuenta se bloquea temporalmente

Prioridad: Alta
Story Points: 5
Sprint: 1

Definición de Hecho:
☐ Formulario de login implementado
☐ Validación de credenciales
☐ Manejo de errores
☐ Bloqueo de cuenta por intentos fallidos
☐ Redirección post-login
☐ Pruebas automatizadas
☐ Documentación de API
```

```
US-002: Recuperación de Contraseña

Como usuario que olvidó su contraseña
Quiero poder recuperar el acceso a mi cuenta
Para no perder mis datos y configuraciones

Criterios de Aceptación:
- Dado que olvidé mi contraseña
  Cuando hago clic en "Olvidé mi contraseña"
  Entonces puedo ingresar mi email para recuperación

- Dado que ingresé un email válido
  Cuando solicito recuperación
  Entonces recibo un email con enlace de recuperación

- Dado que recibí el email de recuperación
  Cuando hago clic en el enlace (válido por 24h)
  Entonces puedo establecer una nueva contraseña

Prioridad: Alta
Story Points: 3
Sprint: 1
```

```
US-003: Dashboard de Usuario

Como usuario autenticado
Quiero ver un dashboard personalizado
Para tener acceso rápido a mi información más importante

Criterios de Aceptación:
- Dado que estoy autenticado
  Cuando accedo al dashboard
  Entonces veo mis datos personales resumidos

- Dado que estoy en el dashboard
  Cuando veo las métricas principales
  Entonces los datos están actualizados y son precisos

- Dado que uso dispositivos móviles
  Cuando accedo al dashboard
  Entonces la interfaz es responsive y usable

Prioridad: Media
Story Points: 8
Sprint: 2
```

### Ejemplo de Epic y Breakdown

```
EPIC: Sistema de Gestión de Usuarios

Descripción: Como administrador del sistema, necesito gestionar usuarios para mantener la seguridad y organización del sistema.

User Stories:
├── US-001: Login de Usuario (5 pts)
├── US-002: Recuperación de Contraseña (3 pts)
├── US-003: Dashboard de Usuario (8 pts)
├── US-004: Registro de Usuario (5 pts)
├── US-005: Perfil de Usuario (3 pts)
├── US-006: Cambio de Contraseña (2 pts)
├── US-007: Gestión de Roles (13 pts)
└── US-008: Auditoria de Accesos (8 pts)

Total Story Points: 47
Sprints estimados: 3-4 sprints
```

## Navegación

[⬅️ Diagramas de Flujo](./diagramas-flujo.md) |
[🏠 README Principal](../../README.md) |
[Prototipos y Wireframes ➡️](./prototipos-wireframes.md)
