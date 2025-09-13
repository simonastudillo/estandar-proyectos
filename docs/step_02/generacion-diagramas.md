# Generación de Diagramas

## ¿Qué es?

La generación de diagramas es el proceso de crear representaciones visuales del
sistema, arquitectura, flujos de datos y procesos de negocio utilizando
herramientas estandarizadas. Estas representaciones gráficas sirven como
documentación técnica, herramienta de comunicación y referencia para el
desarrollo del proyecto.

Un sistema estandarizado de generación de diagramas garantiza consistencia
visual, facilita la colaboración y mantiene la documentación actualizada a lo
largo del ciclo de vida del proyecto.

## ¿Por qué es importante?

- **Comunicación visual**: Facilita la comprensión de conceptos complejos
  mediante representaciones gráficas
- **Documentación técnica**: Proporciona documentación visual que complementa la
  documentación escrita
- **Alineación del equipo**: Asegura que todos los miembros del equipo tengan la
  misma visión del sistema
- **Onboarding**: Acelera la comprensión del proyecto para nuevos
  desarrolladores
- **Toma de decisiones**: Ayuda a identificar problemas y oportunidades de
  mejora
- **Mantenimiento**: Facilita la evolución y mantenimiento del sistema
- **Versionado**: Permite rastrear cambios en la arquitectura a lo largo del
  tiempo

## ¿Qué debe incluir?

### Herramienta Estándar

**Draw.io (app.diagrams.net)** como herramienta principal:

- **Gratuita** y de código abierto
- **Integración con GitHub** para versionado
- **Colaboración** en tiempo real
- **Exportación** a múltiples formatos (PNG, SVG, PDF)
- **Plantillas** predefinidas para diferentes tipos de diagramas
- **Sin instalación** requerida (funciona en navegador)

### Almacenamiento y Organización

```
proyecto/
├── diagrams/                    # Carpeta principal de diagramas
│   ├── architecture/           # Diagramas de arquitectura
│   │   ├── system-overview.drawio
│   │   ├── component-diagram.drawio
│   │   └── deployment-diagram.drawio
│   ├── database/               # Diagramas de base de datos
│   │   ├── erd-main.drawio
│   │   └── schema-evolution.drawio
│   ├── flows/                  # Diagramas de flujo
│   │   ├── user-authentication.drawio
│   │   ├── payment-process.drawio
│   │   └── data-processing.drawio
│   ├── wireframes/             # Wireframes y prototipos
│   │   ├── dashboard-layout.drawio
│   │   └── mobile-screens.drawio
│   ├── exports/                # Versiones exportadas (PNG/PDF)
│   │   ├── architecture/
│   │   ├── database/
│   │   ├── flows/
│   │   └── wireframes/
│   └── templates/              # Plantillas reutilizables
│       ├── standard-flowchart.drawio
│       └── architecture-template.drawio
```

### Convenciones de Nomenclatura

#### Archivos de Diagramas

**Formato**: `{tipo}-{descripcion}-{version}.drawio`

**Ejemplos**:

```
# Arquitectura
arch-system-overview-v1.drawio
arch-microservices-detail-v2.drawio
arch-deployment-production-v1.drawio

# Base de datos
db-erd-users-v1.drawio
db-relationships-complete-v3.drawio
db-migration-strategy-v1.drawio

# Flujos
flow-user-registration-v2.drawio
flow-payment-gateway-v1.drawio
flow-error-handling-v1.drawio

# Wireframes
wf-dashboard-admin-v1.drawio
wf-mobile-profile-v2.drawio
wf-checkout-process-v1.drawio
```

#### Versiones Exportadas

**Formato**: `{nombre-archivo}.{formato}`

**Ejemplos**:

```
exports/architecture/arch-system-overview-v1.png
exports/flows/flow-user-registration-v2.pdf
exports/database/db-erd-users-v1.svg
```

### Convenciones Visuales

#### Paleta de Colores Estándar

```
# Capas de Arquitectura
Frontend:       #E3F2FD (Azul claro)   | Borde: #1976D2 (Azul)
Backend:        #F3E5F5 (Morado claro) | Borde: #7B1FA2 (Morado)
Domain:         #E8F5E8 (Verde claro)  | Borde: #388E3C (Verde)
Infrastructure: #FFF3E0 (Naranja claro)| Borde: #F57C00 (Naranja)
External:       #FCE4EC (Rosa claro)   | Borde: #C2185B (Rosa)
Database:       #F1F8E9 (Verde lima)   | Borde: #689F38 (Verde lima)

# Estados y Procesos
Proceso Normal: #E8F5E8 (Verde claro)  | Borde: #4CAF50 (Verde)
Proceso Error:  #FFEBEE (Rojo claro)   | Borde: #F44336 (Rojo)
Decisión:       #FFF3E0 (Naranja claro)| Borde: #FF9800 (Naranja)
Datos:          #E1F5FE (Azul claro)   | Borde: #03A9F4 (Azul)
Usuario:        #F3E5F5 (Morado claro) | Borde: #9C27B0 (Morado)
```

#### Estilos de Elementos

**Formas estándar**:

- **Rectángulos**: Procesos, componentes, servicios
- **Rectángulos redondeados**: Inicio/fin de procesos
- **Rombos**: Decisiones, validaciones
- **Círculos**: Conectores, puntos de referencia
- **Elipses**: Actores externos, usuarios
- **Cilindros**: Bases de datos, almacenamiento

**Estilos de líneas**:

- **Sólida**: Flujo normal, relaciones directas
- **Punteada**: Flujo condicional, dependencias opcionales
- **Gruesa**: Flujo crítico, relaciones principales

#### Tipografía

```
Títulos:        Arial, 14pt, Negrita
Elementos:      Arial, 11pt, Normal
Anotaciones:    Arial, 9pt, Cursiva
Etiquetas:      Arial, 10pt, Normal
```

## ¿Qué debo hacer?

### 1. Configurar el entorno de trabajo

- **Acceder a Draw.io**: https://app.diagrams.net/
- **Conectar con GitHub**: Configurar integración para guardar directamente en
  el repositorio
- **Crear carpeta diagrams**: En la raíz del proyecto según la estructura
  definida
- **Importar plantillas**: Crear plantillas base con los estilos estándar

### 2. Establecer el flujo de trabajo

```bash
# Crear estructura de carpetas
mkdir -p diagrams/{architecture,database,flows,wireframes,exports,templates}
mkdir -p diagrams/exports/{architecture,database,flows,wireframes}

# Crear archivo .gitignore específico si es necesario
echo "# Archivos temporales de Draw.io" > diagrams/.gitignore
echo "*.tmp" >> diagrams/.gitignore
echo "*.bak" >> diagrams/.gitignore
```

### 3. Crear plantillas estándar

#### Plantilla de Arquitectura

```
Elementos base:
- Rectángulo Frontend (estilo definido)
- Rectángulo Backend (estilo definido)
- Rectángulo Database (estilo definido)
- Flechas de conexión estándar
- Leyenda de colores
- Título y metadata
```

#### Plantilla de Flujo de Proceso

```
Elementos base:
- Óvalo de inicio (verde claro)
- Rectángulos de proceso (azul claro)
- Rombos de decisión (naranja claro)
- Óvalo de fin (verde claro)
- Conectores estándar
- Leyenda de símbolos
```

### 4. Documentar decisiones

Para cada diagrama creado, documentar:

- **Propósito**: ¿Qué representa el diagrama?
- **Audiencia**: ¿Para quién está dirigido?
- **Última actualización**: Fecha de la versión actual
- **Responsable**: Quién mantiene el diagrama
- **Dependencias**: Qué otros diagramas se relacionan

### 5. Proceso de versionado

```bash
# Al crear/modificar un diagrama:
1. Trabajar en Draw.io conectado al repositorio
2. Guardar con nombre versionado
3. Exportar a PNG/PDF en carpeta exports/
4. Commit con mensaje descriptivo
5. Actualizar referencias en documentación
```

## Tips

### 🎨 **Diseño Efectivo**

- **Simplicidad**: Un diagrama = una idea principal
- **Consistencia**: Usar siempre los mismos colores y formas para conceptos
  similares
- **Legibilidad**: Texto grande enough para leer en pantalla y al imprimir
- **Espacio en blanco**: No saturar el diagrama, dejar respirar los elementos
- **Flujo lógico**: De izquierda a derecha, de arriba a abajo

### 🔄 **Mantenimiento**

- **Actualizar junto con el código**: Los diagramas deben evolucionar con el
  proyecto
- **Revisar periodicidad**: Establecer revisiones mensuales de diagramas
  críticos
- **Eliminar obsoletos**: Archivar o eliminar diagramas que ya no son relevantes
- **Vincular con issues**: Referenciar diagramas en issues y PRs cuando sea
  relevante

### 👥 **Colaboración**

- **Comentarios**: Usar la función de comentarios de Draw.io para feedback
- **Revisiones**: Incluir revisión de diagramas en el proceso de code review
- **Acceso compartido**: Asegurar que el equipo tenga acceso a los diagramas
- **Capacitación**: Entrenar al equipo en las convenciones establecidas

### 📱 **Accesibilidad**

- **Múltiples formatos**: Exportar a PNG para visualización rápida, PDF para
  impresión
- **Texto alternativo**: Incluir descripción textual en la documentación
- **Colores**: Asegurar suficiente contraste para accesibilidad
- **Tamaño**: Crear versiones en diferentes tamaños según el uso

## Ejemplos

### Estructura de Diagrama de Arquitectura

```
[TÍTULO: Arquitectura del Sistema - v2.1]

┌─────────────────────────────────────────────────────────┐
│                    FRONTEND LAYER                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │  React App  │  │ Redux Store │  │ Router      │    │
│  │ TypeScript  │  │   Global    │  │ Navigation  │    │
│  └─────────────┘  └─────────────┘  └─────────────┘    │
└─────────────────────────────────────────────────────────┘
                              ↓ HTTP/REST
┌─────────────────────────────────────────────────────────┐
│                    BACKEND LAYER                       │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │ API Gateway │  │ Controllers │  │ Middleware  │    │
│  │  Laravel    │  │  REST/JSON  │  │   Auth      │    │
│  └─────────────┘  └─────────────┘  └─────────────┘    │
└─────────────────────────────────────────────────────────┘

[LEYENDA]
🔵 Frontend Components    🟣 Backend Services    🟢 Database
```

### Ejemplo de Flujo de Usuario

```
┌─────────────┐
│   INICIO    │
│ Usuario Web │
└─────────────┘
      ↓
┌─────────────┐
│ Completar   │
│ Formulario  │
└─────────────┘
      ↓
 ┌─────────────┐
 │ ¿Válido?    │ ──No──→ ┌─────────────┐
 │ Validación  │         │ Mostrar     │
 └─────────────┘         │ Error       │
      ↓ Sí               └─────────────┘
┌─────────────┐
│ Procesar    │
│ Datos       │
└─────────────┘
      ↓
┌─────────────┐
│    FIN      │
│ Confirmación│
└─────────────┘
```

### Checklist de Calidad

Antes de finalizar un diagrama, verificar:

- [ ] ✅ **Título claro y versión definida**
- [ ] 🎨 **Colores consistentes con la paleta estándar**
- [ ] 📝 **Texto legible (min 10pt)**
- [ ] 🔄 **Flujo lógico y direcciones claras**
- [ ] 🏷️ **Leyenda incluida si es necesario**
- [ ] 📐 **Elementos alineados y espaciados correctamente**
- [ ] 💾 **Guardado con nomenclatura correcta**
- [ ] 📤 **Exportado a formatos apropiados**
- [ ] 📚 **Documentación actualizada con referencias**

## Navegación

[⬅️ Elección de Herramientas](./eleccion-herramientas.md) |
[🏠 README Principal](../../README.md) |
[Buenas Prácticas de Desarrollo ➡️](./buenas-practicas-desarrollo.md)
