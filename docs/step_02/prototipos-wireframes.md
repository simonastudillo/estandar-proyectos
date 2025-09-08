# Prototipos y Wireframes

## ¿Qué es?

Los prototipos y wireframes son representaciones visuales tempranas del sistema que permiten conceptualizar, validar y comunicar ideas de diseño antes de la implementación. Los wireframes son esquemas básicos de bajo nivel que muestran la estructura y layout, mientras que los prototipos son versiones más interactivas que simulan la funcionalidad del sistema.

## ¿Por qué es importante?

- **Validación temprana**: Permite identificar problemas de usabilidad y diseño antes del desarrollo
- **Comunicación efectiva**: Facilita la comunicación entre stakeholders, diseñadores y desarrolladores
- **Reducción de costos**: Evita cambios costosos durante la fase de desarrollo
- **Alineación de expectativas**: Asegura que todos los involucrados tengan la misma visión del producto
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

### Estructura de Wireframe (Página de Login)

```
┌─────────────────────────────────┐
│           LOGO/BRAND            │
├─────────────────────────────────┤
│                                 │
│        Iniciar Sesión           │
│                                 │
│  [Email________________]        │
│  [Password_____________]        │
│                                 │
│  □ Recordarme                   │
│                                 │
│  [    INICIAR SESIÓN    ]       │
│                                 │
│  ¿Olvidaste tu contraseña?      │
│                                 │
│  ¿No tienes cuenta? Regístrate  │
│                                 │
└─────────────────────────────────┘
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

---

## Navegación

← [Casos de Uso y User Stories](./casos-uso-user-stories.md) | [Inicio](../../README.md) | [Diseño de Base de Datos](./diseno-base-datos.md) →
