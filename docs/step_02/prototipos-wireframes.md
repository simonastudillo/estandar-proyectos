# Prototipos y Wireframes

## Conocimientos involucrados
- Diseño de interfaces de usuario (UI/UX)
- Herramientas de prototipado
- Validación de conceptos con stakeholders

## Responsable
- Equipo de diseño y desarrollo

## ¿Qué es?

Los prototipos y wireframes son representaciones visuales tempranas del sistema
que permiten conceptualizar, validar y comunicar ideas de diseño antes de la
implementación. Los wireframes son esquemas básicos de bajo nivel que muestran
la estructura y layout, mientras que los prototipos son versiones más
interactivas que simulan la funcionalidad del sistema.

## ¿Por qué es importante?

- **Validación temprana**: Permite identificar problemas de usabilidad y diseño
  antes del desarrollo
- **Comunicación efectiva**: Facilita la comunicación entre stakeholders,
  diseñadores y desarrolladores
- **Reducción de costos**: Evita cambios costosos durante la fase de desarrollo
- **Alineación de expectativas**: Asegura que todos los involucrados tengan la
  misma visión del producto
- **Iteración rápida**: Permite realizar cambios y mejoras de forma ágil
- **Pruebas de concepto**: Valida ideas y conceptos con usuarios reales

## ¿Qué debe incluir?

### Wireframes de Baja Fidelidad

- Estructura básica de páginas/pantallas
- Disposición de elementos principales
- Navegación entre secciones
- Jerarquía de información
- Flujos de usuario básicos

### Wireframes de Media Fidelidad

- Definición de componentes UI
- Espaciado y proporciones más precisas
- Contenido representativo
- Estados de elementos interactivos
- Responsive design considerations

### Prototipos de Alta Fidelidad

- Diseño visual completo
- Interacciones y animaciones
- Flujos de usuario completos
- Micro-interacciones
- Casos de error y estados especiales

### Documentación de Diseño

- Guía de estilos (Style Guide)
- Sistema de componentes
- Especificaciones técnicas
- Casos de uso visuales
- Criterios de aceptación de UI/UX

## ¿Qué debo hacer?

### 1. Análisis y Preparación

```
├── Revisar casos de uso y user stories
├── Definir dispositivos objetivo (mobile, tablet, desktop)
├── Establecer principios de diseño
├── Crear user personas y journey maps
└── Definir métricas de UX
```

### 2. Wireframes de Baja Fidelidad

```
├── Sketches en papel o herramientas básicas
├── Mapeo de información y contenido
├── Definición de navegación principal
├── Estructuras de páginas clave
└── Validación con stakeholders
```

### 3. Wireframes de Media Fidelidad

```
├── Digitalización de sketches
├── Definición de grids y layouts
├── Especificación de componentes
├── Navegación detallada
└── Versiones responsive
```

### 4. Prototipos Interactivos

```
├── Implementación de flujos principales
├── Definición de estados e interacciones
├── Pruebas de usabilidad
├── Iteración basada en feedback
└── Documentación de interacciones
```

### 5. Sistema de Diseño

```
├── Definición de colores y tipografías
├── Biblioteca de componentes
├── Iconografía y elementos gráficos
├── Guías de espaciado y medidas
└── Documentación técnica para desarrollo
```

## Tips

### Herramientas Recomendadas

- **Wireframing**: Balsamiq, Whimsical, Draw.io
- **Prototyping**: Figma, Adobe XD, Sketch, InVision
- **Colaboración**: Miro, FigJam, Conceptboard
- **Testing**: Maze, UserTesting, Hotjar

### Mejores Prácticas

- Comenzar siempre con baja fidelidad antes de avanzar
- Involucrar a usuarios reales en las validaciones
- Mantener consistencia en patrones de diseño
- Documentar decisiones de diseño y su justificación
- Iterar frecuentemente basado en feedback
- Considerar accesibilidad desde el inicio

### Metodología de Trabajo

- **Design Thinking**: Empatizar, definir, idear, prototipar, testear
- **Lean UX**: Build-Measure-Learn cycles
- **Atomic Design**: Átomos, moléculas, organismos, templates, páginas
- **Design System First**: Crear sistemas escalables y mantenibles

### Consideraciones Técnicas

- Limitaciones de la plataforma objetivo
- Performance y tiempo de carga
- Capacidades del stack tecnológico
- Integración con APIs y servicios externos
- Escalabilidad del diseño

## Ejemplos

## Wireframes Iniciales del Proyecto

### 1. Página de Inicio (Landing Page)

```
┌─────────────────────────────────────────────────────────┐
│ [Logo] Nombre Proyecto   [Inicio][Productos][Blog][👤] │
├─────────────────────────────────────────────────────────┤
│                                                         │
│              HERO SECTION                               │
│   ┌─────────────────┐    ┌─────────────────────────┐   │
│   │                 │    │ Título Principal        │   │
│   │   Hero Image    │    │ Subtítulo descriptivo   │   │
│   │     o Video     │    │                         │   │
│   │                 │    │ [  COMENZAR AHORA  ]    │   │
│   └─────────────────┘    │ [   Saber más      ]    │   │
│                          └─────────────────────────┘   │
│                                                         │
│              CARACTERÍSTICAS PRINCIPALES                │
│   ┌─────────┐    ┌─────────┐    ┌─────────┐           │
│   │   🚀    │    │   🛡️    │    │   📱    │           │
│   │ Rápido  │    │ Seguro  │    │ Móvil   │           │
│   │Descripción│  │Descripción│  │Descripción│          │
│   └─────────┘    └─────────┘    └─────────┘           │
│                                                         │
│              LLAMADA A LA ACCIÓN                        │
│   ┌─────────────────────────────────────────────────┐   │
│   │         "¿Listo para comenzar?"                │   │
│   │    [    REGISTRARSE GRATIS    ]                │   │
│   └─────────────────────────────────────────────────┘   │
│                                                         │
│ Footer: Links | Contacto | Social | © 2025            │
└─────────────────────────────────────────────────────────┘
```

### 2. Dashboard Principal (Post-login)

```
┌─────────────────────────────────────────────────────────┐
│ [☰] Dashboard | Proyecto    [🔔3] [🔍] [👤 Usuario] [⚙️] │
├─┬───────────────────────────────────────────────────────┤
│ │ SIDEBAR                   MAIN CONTENT                │
│ │ ┌─────────────┐          ┌─────────────────────────┐  │
│ │ │📊 Dashboard │          │    Resumen General      │  │
│ │ │👥 Usuarios  │          │ ┌───────┐ ┌───────┐     │  │
│ │ │📦 Productos │          │ │ Total │ │ Activos│     │  │
│ │ │📈 Reportes  │          │ │1,234  │ │  987   │     │  │
│ │ │⚙️ Config.   │          │ └───────┘ └───────┘     │  │
│ │ │📝 Blog      │          │                         │  │
│ │ │💬 Mensajes  │          │ ┌─────────────────────┐  │  │
│ │ │🗂️ Archivos  │          │ │ Gráfico de Actividad│  │  │
│ │ │               │          │ │      📊           │  │  │
│ │ │               │          │ │                   │  │  │
│ │ │[+ Nuevo]     │          │ └─────────────────────┘  │  │
│ │ └─────────────┘          │                         │  │
│ │                          │ Actividad Reciente      │  │
│ │                          │ ┌─────────────────────┐  │  │
│ │                          │ │ ○ Usuario creado    │  │  │
│ │                          │ │ ○ Producto editado  │  │  │
│ │                          │ │ ○ Reporte generado  │  │  │
│ │                          │ └─────────────────────┘  │  │
│ │                          └─────────────────────────┘  │
└─┴───────────────────────────────────────────────────────┘
```

### 3. Formulario de Registro

```
┌─────────────────────────────────────────────────────────┐
│ [← Volver]           CREAR CUENTA              [🔒SSL] │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │               CREAR CUENTA                      │    │
│  │                                                 │    │
│  │ Información Personal                            │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ * Nombre completo                           │ │    │
│  │ │ [_____________________________]             │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ * Email                                     │ │    │
│  │ │ [_____________________________]             │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │ ┌───────────────┐ ┌─────────────────────────┐   │    │
│  │ │ * Contraseña  │ │ * Confirmar contraseña  │   │    │
│  │ │ [___________] │ │ [_________________]     │   │    │
│  │ └───────────────┘ └─────────────────────────┘   │    │
│  │                                                 │    │
│  │ Configuración de cuenta                         │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ Tipo de cuenta: [Básica ▼]                  │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │ ☐ Acepto los términos y condiciones            │    │
│  │ ☐ Deseo recibir noticias por email             │    │
│  │                                                 │    │
│  │     [      CREAR CUENTA      ]                  │    │
│  │                                                 │    │
│  │ ¿Ya tienes cuenta? [Iniciar sesión]            │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 4. Gestión de Usuarios (CRUD)

```
┌─────────────────────────────────────────────────────────┐
│ [☰] Dashboard > Usuarios                [+ Nuevo Usuario]│
├─────────────────────────────────────────────────────────┤
│                                                         │
│ FILTROS Y BÚSQUEDA                                      │
│ ┌─────────────────┐ ┌──────────┐ ┌─────────┐ [Filtrar] │
│ │🔍 Buscar usuario│ │Estado: ▼ │ │Rol: ▼  │            │
│ └─────────────────┘ └──────────┘ └─────────┘            │
│                                                         │
│ LISTA DE USUARIOS                            [⚙️ Exportar]│
│ ┌─────────────────────────────────────────────────────┐ │
│ │☐│Avatar│Nombre      │Email        │Rol    │Estado│📝│ │
│ ├─┼─────┼───────────┼─────────────┼──────┼─────┼─┤ │
│ │☐│ 👤  │Juan Pérez  │juan@email   │Admin │🟢   │✏️│ │
│ │☐│ 👤  │Ana García  │ana@email    │User  │🟢   │✏️│ │
│ │☐│ 👤  │Luis Torres │luis@email   │User  │🔴   │✏️│ │
│ │☐│ 👤  │María López │maria@email  │Mod.  │🟡   │✏️│ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ ACCIONES MASIVAS                                        │
│ [Activar] [Desactivar] [Eliminar] [Enviar Email]       │
│                                                         │
│ PAGINACIÓN                                              │
│ [← Anterior] Página 1 de 15 (147 usuarios) [Siguiente →]│
└─────────────────────────────────────────────────────────┘
```

### 5. Vista Móvil Responsive

```
MOBILE (360px)              TABLET (768px)
┌─────────────────┐         ┌──────────────────────────┐
│[☰][Logo][🔔][👤]│         │[Logo] [Nav Items] [👤]   │
├─────────────────┤         ├──────────────────────────┤
│                 │         │                          │
│ MENU HAMBURGER  │         │  ┌─────────┐ ┌─────────┐ │
│ ┌─────────────┐ │         │  │ Card 1  │ │ Card 2  │ │
│ │📊 Dashboard │ │         │  │Content  │ │Content  │ │
│ │👥 Usuarios  │ │         │  └─────────┘ └─────────┘ │
│ │📦 Productos │ │         │                          │
│ │📈 Reportes  │ │         │  ┌─────────┐ ┌─────────┐ │
│ │⚙️ Config.   │ │         │  │ Card 3  │ │ Card 4  │ │
│ └─────────────┘ │         │  │Content  │ │Content  │ │
│                 │         │  └─────────┘ └─────────┘ │
│ CONTENT AREA    │         │                          │
│ ┌─────────────┐ │         │  [Load More]             │
│ │             │ │         │                          │
│ │   Cards     │ │         │                          │
│ │   Stack     │ │         │                          │
│ │   Vertical  │ │         │                          │
│ │             │ │         │                          │
│ └─────────────┘ │         │                          │
│                 │         │                          │
│ [Bottom Nav]    │         │                          │
│ [🏠][📊][👥][⚙️] │         │                          │
└─────────────────┘         └──────────────────────────┘
```

### 6. Modal/Popup de Confirmación

```
┌─────────────────────────────────────────────────────────┐
│                    [Overlay Oscuro]                     │
│                                                         │
│       ┌─────────────────────────────────────────┐       │
│       │  ⚠️  CONFIRMAR ACCIÓN            [✕]   │       │
│       ├─────────────────────────────────────────┤       │
│       │                                         │       │
│       │   ¿Estás seguro que deseas eliminar     │       │
│       │   el usuario "Juan Pérez"?              │       │
│       │                                         │       │
│       │   Esta acción no se puede deshacer.     │       │
│       │                                         │       │
│       │         [  Cancelar  ] [ Eliminar ]     │       │
│       │                         ^^^ Danger      │       │
│       └─────────────────────────────────────────┘       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 7. Estados de Carga y Error

```
ESTADO DE CARGA                    ESTADO DE ERROR
┌─────────────────────┐           ┌─────────────────────┐
│    ⏳ CARGANDO...   │           │      ❌ ERROR       │
│                     │           │                     │
│ ┌─────────────────┐ │           │  No se pudieron     │
│ │ ████████████░░░ │ │           │  cargar los datos   │
│ │    Cargando...  │ │           │                     │
│ └─────────────────┘ │           │  [  Reintentar  ]   │
│                     │           │                     │
│ O usar skeleton:    │           │  [Ir al inicio]     │
│ ┌─────────────────┐ │           └─────────────────────┘
│ │ ████████░░░░░░░ │ │           
│ │ ██░░░░░░░░░░░░░ │ │           ESTADO VACÍO
│ │ ██████░░░░░░░░░ │ │           ┌─────────────────────┐
│ └─────────────────┘ │           │      📭 VACÍO       │
└─────────────────────┘           │                     │
                                  │  No hay elementos   │
                                  │     para mostrar    │
                                  │                     │
                                  │  [  Crear nuevo  ]  │
                                  │                     │
                                  └─────────────────────┘
```

## Flujos de Usuario del Proyecto

### Flujo de Onboarding Completo

```
Landing Page → Registro → Verificación Email → Tour Inicial → Dashboard
      ↓             ↓           ↓                ↓            ↓
   [Ver demo]   [Configurar] [Reenviar]    [Saltar]    [Comenzar]
      ↓          Perfil        Email         Tour         Usar App
   Modal Demo      ↓             ↓            ↓            ↓
      ↓        [Guardar]    [Verificado]  [Finalizar]  [Explorar]
   [Registrar]      ↓             ↓            ↓            ↓
      ↓         Dashboard   → Dashboard → Dashboard → Dashboard
   Formulario        ↓             ↓            ↓            ↓
      ↓         Estado:     Estado:      Estado:      Estado:
   [Enviar]     Incompleto  Verificado   Configurado  Activo
```

### Flujo de Gestión de Contenido

```
Dashboard → Lista Items → [Ver/Editar/Eliminar] → Confirmación → Refresh
    ↓           ↓              ↓                      ↓           ↓
[+ Nuevo]   [Filtrar]      Modal Edit            [Confirmar]  Lista
    ↓           ↓              ↓                      ↓       Actualizada
Formulario  Resultados    [Guardar]              Acción          ↓
    ↓           ↓              ↓                  Ejecutada   [Notificación]
[Guardar]   [Paginación]   Validación               ↓            ↓
    ↓           ↓              ↓                 [Toast/Alert] [Cerrar]
Validación  [Cargar más]   [Success/Error]          ↓            ↓
    ↓           ↓              ↓                 Auto-close   Dashboard
[Crear Item]→ Lista      → Dashboard                ↓         o Lista
```

### Flujo de Autenticación Completa

```
Landing → Login → [Credenciales] → Dashboard
   ↓        ↓           ↓              ↓
[Login]  [Email]   [Validar]      [Bienvenido]
   ↓        ↓           ↓              ↓
Modal    [Pass]    [Correcto] → Set Session
   ↓        ↓           ↓              ↓
[¿Nuevo?] [Submit] [Incorrecto] → Redirect
   ↓        ↓           ↓              ↓
Registro [Loading]  [Error]       Dashboard
   ↓        ↓           ↓              ↓
[Form]   [Spinner]  [Retry]      [Tour/Skip]
```

### Navegación Responsiva del Sistema

```
DESKTOP                    TABLET                    MOBILE
┌─────────────────┐       ┌─────────────────┐       ┌─────────┐
│Header Completo  │       │Header Reducido  │       │Top Bar  │
├─────────────────┤       ├─────────────────┤       ├─────────┤
│Sidebar│Content  │       │Content Full     │       │Content  │
│Fixed  │Fluid    │  →    │Width           │  →    │Full     │
│       │         │       │                 │       │Width    │
│       │         │       │[Sidebar Toggle] │       │         │
└─────────────────┘       └─────────────────┘       └─────────┘

Breakpoints:
- Mobile: 320px - 767px
- Tablet: 768px - 1023px  
- Desktop: 1024px+
```

## Wireframes Detallados por Módulo

### Módulo de Autenticación

#### Login Avanzado

```
┌─────────────────────────────────────────────────────────┐
│              [Logo] Nombre del Proyecto                 │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │                INICIAR SESIÓN                   │    │
│  │                                                 │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ 📧 Email o nombre de usuario                │ │    │
│  │ │ [_____________________________]             │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ 🔒 Contraseña                 [👁️ Mostrar] │ │    │
│  │ │ [_____________________________]             │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │ ☐ Mantenerme conectado                          │    │
│  │                                                 │    │
│  │       [      INICIAR SESIÓN      ]              │    │
│  │                                                 │    │
│  │ ──────────── O ────────────                    │    │
│  │                                                 │    │
│  │ [🔵 Google] [📘 Facebook] [🐙 GitHub]         │    │
│  │                                                 │    │
│  │ [¿Olvidaste tu contraseña?]                     │    │
│  │                                                 │    │
│  │ ¿No tienes cuenta? [Crear cuenta gratis]       │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
│                [🔒 Conexión segura SSL]                │
└─────────────────────────────────────────────────────────┘
```

#### Recuperación de Contraseña

```
┌─────────────────────────────────────────────────────────┐
│ [← Volver al login]    RECUPERAR CONTRASEÑA              │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │           RECUPERAR CONTRASEÑA                  │    │
│  │                                                 │    │
│  │  Ingresa tu email y te enviaremos               │    │
│  │  instrucciones para recuperar tu cuenta.        │    │
│  │                                                 │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ 📧 Email registrado                         │ │    │
│  │ │ [_____________________________]             │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │       [    ENVIAR INSTRUCCIONES    ]            │    │
│  │                                                 │    │
│  │ ¿Recordaste tu contraseña? [Iniciar sesión]    │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Módulo de Gestión de Datos

#### Formulario de Creación/Edición

```
┌─────────────────────────────────────────────────────────┐
│ [← Volver] Crear Nuevo Item                    [💾 Guardar] │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ INFORMACIÓN BÁSICA                              * Requerido │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ * Título                                            │ │
│ │ [___________________________________]               │ │
│ │                                                     │ │
│ │ * Descripción                                       │ │
│ │ [___________________________________]               │ │
│ │ [___________________________________]               │ │
│ │ [___________________________________]               │ │
│ │                                                     │ │
│ │ Categoría          Estado                           │ │
│ │ [Seleccionar ▼]    [Activo ▼]                      │ │
│ │                                                     │ │
│ │ Tags (separados por comas)                          │ │
│ │ [tag1, tag2, tag3________________]                  │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ CONFIGURACIÓN AVANZADA                                  │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ ☐ Publicar inmediatamente                           │ │
│ │ ☐ Permitir comentarios                              │ │
│ │ ☐ Enviar notificaciones                             │ │
│ │                                                     │ │
│ │ Fecha de publicación: [📅 Seleccionar fecha]       │ │
│ │ Autor: [Usuario actual ▼]                          │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│         [Cancelar]  [Guardar borrador]  [Publicar]     │
└─────────────────────────────────────────────────────────┘
```

## Sistema de Componentes Base

### Diseño Atómico

#### Átomos (Elementos básicos)

```
BOTONES:
┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐
│Primary  │ │Secondary│ │ Success │ │ Danger  │
└─────────┘ └─────────┘ └─────────┘ └─────────┘

INPUTS:
┌─────────────────────────┐  ┌─────────────────┐
│ Text Input              │  │ Password    [👁️]│
└─────────────────────────┘  └─────────────────┘

┌─────────────────────────┐  ┌─────────────────┐
│ Email Input         [✓] │  │ Select Option ▼ │
└─────────────────────────┘  └─────────────────┘

ICONOS:
🏠 📊 👥 📦 ⚙️ 🔔 🔍 ❤️ 💬 📝 📷 🗑️ ✏️ ⬇️ ⬆️

COLORES:
■ Primary (#007bff)    ■ Success (#28a745)
■ Secondary (#6c757d)  ■ Warning (#ffc107)
■ Info (#17a2b8)       ■ Danger (#dc3545)
```

#### Moléculas (Combinaciones)

```
CAMPO DE FORMULARIO:
┌─────────────────────────────────────────┐
│ Label del campo *                       │
│ ┌─────────────────────────────────────┐ │
│ │ Input field                         │ │
│ └─────────────────────────────────────┘ │
│ ⚠️ Mensaje de error aquí                │
└─────────────────────────────────────────┘

TARJETA SIMPLE:
┌─────────────────────────────────────────┐
│ 📊 Título de la tarjeta                 │
├─────────────────────────────────────────┤
│ Contenido de la tarjeta                 │
│ con información relevante               │
├─────────────────────────────────────────┤
│              [Acción]                   │
└─────────────────────────────────────────┘

BARRA DE BÚSQUEDA:
┌─────────────────────────────────────────┐
│ [🔍] Buscar...                [Filtros] │
└─────────────────────────────────────────┘
```

#### Organismos (Secciones completas)

```
HEADER PRINCIPAL:
┌─────────────────────────────────────────────────────────┐
│ [Logo] Navegación        [🔍][🔔][👤][⚙️]            │
└─────────────────────────────────────────────────────────┘

SIDEBAR DE NAVEGACIÓN:
┌─────────────────┐
│ 📊 Dashboard    │
│ 👥 Usuarios     │
│ 📦 Productos    │
│ 📈 Reportes     │
│ ──────────────  │
│ ⚙️ Configuración│
│ 📝 Documentación│
│ 💬 Soporte      │
│ ──────────────  │
│ [+ Nuevo]       │
└─────────────────┘

TABLA DE DATOS:
┌─────────────────────────────────────────────────────────┐
│ [🔍 Buscar] [Filtros ▼] [Exportar]          [+ Nuevo] │
├─────────────────────────────────────────────────────────┤
│☐│ID │Nombre     │Email        │Estado│Fecha  │Acciones│
├─┼───┼──────────┼─────────────┼─────┼───────┼────────┤
│☐│001│Juan Pérez │juan@email   │🟢   │Ayer   │[✏️][🗑️] │
│☐│002│Ana García │ana@email    │🟢   │Hoy    │[✏️][🗑️] │
└─────────────────────────────────────────────────────────┘
```

## Guía de Implementación Específica

### Breakpoints y Responsive Design

```css
/* Mobile First Approach */
/* Extra Small: 320px - 575px */
.container {
   max-width: 100%;
   padding: 0 16px;
}

/* Small: 576px - 767px */
@media (min-width: 576px) {
   .container {
      max-width: 540px;
   }
}

/* Medium: 768px - 991px */
@media (min-width: 768px) {
   .container {
      max-width: 720px;
   }
   .sidebar {
      display: block;
   }
}

/* Large: 992px - 1199px */
@media (min-width: 992px) {
   .container {
      max-width: 960px;
   }
}

/* Extra Large: 1200px+ */
@media (min-width: 1200px) {
   .container {
      max-width: 1140px;
   }
}
```

### Estados de Componentes

```
BOTÓN PRIMARIO:
┌─────────────────────────────────────────────┐
│ Estados:                                    │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐        │
│ │ Normal  │ │ Hover   │ │ Active  │        │
│ │ #007bff │ │ #0056b3 │ │ #004085 │        │
│ └─────────┘ └─────────┘ └─────────┘        │
│                                             │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐        │
│ │ Focus   │ │Disabled │ │ Loading │        │
│ │ Border  │ │ #6c757d │ │ Spinner │        │
│ │ #80bdff │ │ Cursor  │ │ Animated│        │
│ └─────────┘ └─────────┘ └─────────┘        │
└─────────────────────────────────────────────┘

INPUT FIELD:
┌─────────────────────────────────────────────┐
│ Estados:                                    │
│ ┌───────────────────────────────────────┐   │
│ │ Default: border #ced4da               │   │
│ └───────────────────────────────────────┘   │
│ ┌───────────────────────────────────────┐   │
│ │ Focus: border #007bff, shadow         │   │
│ └───────────────────────────────────────┘   │
│ ┌───────────────────────────────────────┐   │
│ │ Error: border #dc3545, text rojo      │   │
│ └───────────────────────────────────────┘   │
│ ┌───────────────────────────────────────┐   │
│ │ Success: border #28a745, checkmark    │   │
│ └───────────────────────────────────────┘   │
│ ┌───────────────────────────────────────┐   │
│ │ Disabled: background #e9ecef          │   │
│ └───────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

### Animaciones y Transiciones

```
FADE IN/OUT:
┌─────────────────────────────────────────────┐
│ Modal/Toast:                                │
│ opacity: 0 → 1 (300ms ease-in-out)         │
│                                             │
│ Hover effects:                              │
│ transform: scale(1) → scale(1.05) (200ms)   │
│                                             │
│ Loading states:                             │
│ Skeleton: shimmer animation (1.5s infinite) │
│ Spinner: rotate 360deg (1s linear infinite) │
└─────────────────────────────────────────────┘

SLIDE ANIMATIONS:
┌─────────────────────────────────────────────┐
│ Sidebar toggle:                             │
│ transform: translateX(-100%) → translateX(0) │
│                                             │
│ Dropdown:                                   │
│ max-height: 0 → auto (250ms ease-out)      │
│                                             │
│ Notification:                               │
│ transform: translateY(-100%) → translateY(0) │
└─────────────────────────────────────────────┘
```

### Accesibilidad (WCAG 2.1)

```
COLOR CONTRAST:
┌─────────────────────────────────────────────┐
│ Mínimo AA: 4.5:1 para texto normal          │
│ Mínimo AA: 3:1 para texto grande            │
│ Mínimo AAA: 7:1 para texto normal           │
│                                             │
│ ✅ Texto negro (#000) sobre blanco (#fff)   │
│    Ratio: 21:1                              │
│ ✅ Primary (#007bff) sobre blanco (#fff)    │
│    Ratio: 5.5:1                             │
│ ❌ Gris claro (#6c757d) sobre blanco        │
│    Ratio: 4.2:1 (Borderline)                │
└─────────────────────────────────────────────┘

NAVEGACIÓN POR TECLADO:
┌─────────────────────────────────────────────┐
│ Tab order lógico: header → nav → main → footer │
│ Focus visible: outline, border o background     │
│ Skip links: "Saltar al contenido principal"    │
│ Escape key: cerrar modales y dropdowns         │
│ Enter/Space: activar botones y enlaces         │
│ Arrow keys: navegación en menus                │
└─────────────────────────────────────────────┘

ARIA LABELS:
┌─────────────────────────────────────────────┐
│ Botones: aria-label="Descripción clara"    │
│ Formularios: aria-describedby="help-text"  │
│ Estados: aria-expanded, aria-selected       │
│ Live regions: aria-live="polite"            │
│ Landmarks: role="navigation", "main"        │
└─────────────────────────────────────────────┘
```

### Métricas y Medidas Estándar

```
SPACING SYSTEM (8px base):
┌─────────────────────────────────────────────┐
│ xs: 4px   │ sm: 8px   │ md: 16px │ lg: 24px │
│ xl: 32px  │ 2xl: 48px │ 3xl: 64px│ 4xl: 96px│
└─────────────────────────────────────────────┘

TYPOGRAPHY SCALE:
┌─────────────────────────────────────────────┐
│ h1: 32px/40px (2rem/2.5rem)                │
│ h2: 28px/36px (1.75rem/2.25rem)            │
│ h3: 24px/32px (1.5rem/2rem)                │
│ h4: 20px/28px (1.25rem/1.75rem)            │
│ h5: 18px/26px (1.125rem/1.625rem)          │
│ h6: 16px/24px (1rem/1.5rem)                │
│ body: 16px/24px (1rem/1.5rem)              │
│ small: 14px/20px (0.875rem/1.25rem)        │
│ xs: 12px/16px (0.75rem/1rem)               │
└─────────────────────────────────────────────┘

COMPONENT SIZES:
┌─────────────────────────────────────────────┐
│ Button heights: 32px, 40px, 48px            │
│ Input heights: 36px, 44px, 52px             │
│ Icon sizes: 16px, 20px, 24px, 32px          │
│ Avatar sizes: 24px, 32px, 40px, 64px        │
│ Card border-radius: 8px                     │
│ Button border-radius: 6px                   │
│ Input border-radius: 4px                    │
└─────────────────────────────────────────────┘
```

### Checklist de Validación

```
WIREFRAME REVIEW CHECKLIST:
┌─────────────────────────────────────────────┐
│ ☐ Todos los casos de uso están cubiertos   │
│ ☐ Navegación es intuitiva y consistente    │
│ ☐ Responsive design para todos los tamaños │
│ ☐ Estados de error y carga están definidos │
│ ☐ Accesibilidad considerada desde el inicio│
│ ☐ CTAs (Call to Actions) son claros        │
│ ☐ Jerarquía visual es apropiada            │
│ ☐ Contenido placeholder es realista        │
│ ☐ Performance considerations incluidas      │
│ ☐ Feedback del usuario está contemplado    │
└─────────────────────────────────────────────┘

PROTOTYPE TESTING:
┌─────────────────────────────────────────────┐
│ ☐ Flujos principales funcionan              │
│ ☐ Micro-interacciones son smooth           │
│ ☐ Tiempo de carga es aceptable              │
│ ☐ Formularios validan correctamente         │
│ ☐ Navegación por teclado funciona           │
│ ☐ Responsive comporta como esperado         │
│ ☐ Cross-browser compatibility               │
│ ☐ Touch interactions en móvil               │
│ ☐ Accessibility features funcionan          │
│ ☐ Error handling es user-friendly           │
└─────────────────────────────────────────────┘
```

### Estructura de Wireframe (Página de Login)

```
┌─────────────────────────────────────────────────────────┐
│              [Logo] Nombre del Proyecto                 │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │                INICIAR SESIÓN                   │    │
│  │                                                 │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ 📧 Email o nombre de usuario                │ │    │
│  │ │ [_____________________________]             │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │ ┌─────────────────────────────────────────────┐ │    │
│  │ │ 🔒 Contraseña                 [👁️ Mostrar] │ │    │
│  │ │ [_____________________________]             │ │    │
│  │ └─────────────────────────────────────────────┘ │    │
│  │                                                 │    │
│  │ ☐ Mantenerme conectado                          │    │
│  │                                                 │    │
│  │       [      INICIAR SESIÓN      ]              │    │
│  │                                                 │    │
│  │ ──────────── O ────────────                    │    │
│  │                                                 │    │
│  │ [🔵 Google] [📘 Facebook] [🐙 GitHub]         │    │
│  │                                                 │    │
│  │ [¿Olvidaste tu contraseña?]                     │    │
│  │                                                 │    │
│  │ ¿No tienes cuenta? [Crear cuenta gratis]       │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
│                [🔒 Conexión segura SSL]                │
└─────────────────────────────────────────────────────────┘
```

### Flujo de Prototipo (E-commerce)

```
Home → Catálogo → Producto → Carrito → Checkout → Confirmación
  ↓       ↓         ↓         ↓         ↓          ↓
Login   Filtros   Reviews   Editar   Pago     Email/SMS
  ↓       ↓         ↓         ↓         ↓          ↓
Perfil  Búsqueda  Comprar  Eliminar  Dirección  Tracking
```

### Componentes del Sistema de Diseño

```
Átomos:
├── Botones (primary, secondary, danger)
├── Inputs (text, email, password, select)
├── Icons (SVG library)
├── Typography (headings, body, captions)
└── Colors (primary, secondary, semantic)

Moléculas:
├── Form fields (label + input + error)
├── Search bar (input + button + suggestions)
├── Navigation items (icon + text + badge)
└── Cards (image + title + description + actions)

Organismos:
├── Header (logo + navigation + user menu)
├── Footer (links + social + contact)
├── Product grid (filters + cards + pagination)
└── Forms (login, register, checkout)
```

### Casos de Uso Visuales

#### Dashboard de Administración

```
┌─────────────────────────────────────────────────────────┐
│ [☰] Dashboard Admin           [🔔] [👤] Admin [⚙️]     │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │
│ │Usuarios │ │Ventas   │ │Productos│ │Reportes │        │
│ │  1,234  │ │ $45,678 │ │   567   │ │   📊    │        │
│ └─────────┘ └─────────┘ └─────────┘ └─────────┘        │
│                                                         │
│ Gráfico de Ventas (Últimos 30 días)                    │
│ ┌─────────────────────────────────────────────────────┐ │
│ │      📈                                             │ │
│ │                                                     │ │
│ │                                                     │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ Últimas Transacciones                    [Ver todas]   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Usuario A     │ Producto X    │ $123.45  │ Hoy     │ │
│ │ Usuario B     │ Producto Y    │ $67.89   │ Ayer    │ │
│ │ Usuario C     │ Producto Z    │ $234.56  │ 2d      │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

#### Aplicación Móvil (Responsive)

```
Mobile (320px)           Tablet (768px)           Desktop (1200px)
┌─────────────┐         ┌──────────────────────┐   ┌──────────────────────────────┐
│ [☰] [🔍] [👤]│         │ [Logo] [Nav] [🔍] [👤] │   │ [Logo] [Nav Items] [🔍] [👤]  │
├─────────────┤         ├──────────────────────┤   ├──────────────────────────────┤
│             │         │                      │   │ [Sidebar]  [Main Content]    │
│   Content   │         │     Content Grid     │   │            [Secondary]       │
│   Stack     │         │     (2 columns)      │   │                              │
│             │         │                      │   │                              │
│             │         │                      │   │                              │
│             │         │                      │   │                              │
└─────────────┘         └──────────────────────┘   └──────────────────────────────┘
```

### Especificaciones de Componentes

#### Botón Primario

```
Propiedades:
├── Background: #007bff
├── Color: #ffffff
├── Padding: 12px 24px
├── Border-radius: 6px
├── Font-weight: 600
├── Min-height: 44px (accesibilidad táctil)
└── States:
    ├── Hover: #0056b3
    ├── Active: #004085
    ├── Disabled: #6c757d
    └── Focus: border 2px #80bdff
```

#### Formulario de Contacto

```
Validaciones:
├── Nombre: Requerido, min 2 caracteres
├── Email: Requerido, formato válido
├── Teléfono: Opcional, formato válido
├── Mensaje: Requerido, min 10 caracteres
└── Estados:
    ├── Default: Border gris
    ├── Focus: Border azul
    ├── Error: Border rojo + mensaje
    ├── Success: Border verde + checkmark
    └── Disabled: Background gris
```

## Navegación

[⬅️ Casos de Uso y User Stories](./casos-uso-user-stories.md) |
[🏠 README Principal](../../README.md) |
[Especificaciones Técnicas ➡️](./especificaciones-tecnicas.md)
