GitHub Copilot Instructions

Objetivo

Este repositorio es una guía estándar para la creación de proyectos, definiendo un stack tecnológico, convenciones de código y estructura de carpetas. Estas reglas deben aplicarse en todos los proyectos y deben ser respetadas por Copilot y por cualquier colaborador.

Copilot debe:
• Seguir esta arquitectura y convenciones en todo momento.
• Priorizar código limpio, mantenible y con tipado fuerte.
• Proponer ejemplos consistentes con estas reglas.
• Evitar atajos o soluciones rápidas que rompan la arquitectura definida.
• Generar código que sea consistente con las etapas 1 y 2 ya documentadas.

⸻

Stack Tecnológico

Front-end
• Framework: React 18+
• Lenguaje: TypeScript 5+
• Bundler: Vite
• Estado: Redux Toolkit
• Testing: Jest + React Testing Library
• Linting: ESLint + Prettier
• Tipado: Mantener un tipado fuerte siempre.

Sugerencias adicionales:
• Librerías estándar: React Router, Axios, ESLint, Prettier, Testing Library.
• Estructura base de carpetas:

src/
├── components/     # Componentes reutilizables
├── hooks/         # Custom hooks
├── pages/         # Páginas principales
├── services/      # API calls y servicios
├── store/         # Redux store y slices
├── types/         # Definiciones TypeScript
└── utils/         # Utilidades y helpers

⸻

Backend (Laravel) — Clean Architecture + DDD

El backend seguirá Clean Architecture basada en DDD (Domain-Driven Design), desacoplando las reglas de negocio del framework Laravel.

Estructura de carpetas

backend/ # Laravel - Clean Architecture
├── app/
│ ├── Domain/ # Núcleo: reglas de negocio puras
│ │ ├── Entities/ # Objetos del dominio (POPOs)
│ │ ├── ValueObjects/ # Email, Dinero, RUT, etc.
│ │ ├── Repositories/ # Interfaces de persistencia (sin implementación)
│ │ ├── Services/ # Reglas complejas que combinan entidades
│ │ ├── Events/ # Eventos de dominio
│ │ └── Exceptions/ # Excepciones propias del dominio
│ │
│ ├── Application/ # Capa de casos de uso
│ │ ├── UseCases/ # Casos de uso organizados por módulo
│ │ ├── DTOs/ # Data Transfer Objects
│ │ ├── Commands/ # Commands para operaciones
│ │ └── Queries/ # Queries para consultas específicas
│ │
│ ├── Infrastructure/ # Implementaciones técnicas
│ │ ├── Persistence/
│ │ │ └── Eloquent/ # Models y repositorios concretos
│ │ ├── Http/
│ │ │ ├── Controllers/
│ │ │ │ └── Api/V1/ # Controladores API versionados
│ │ │ ├── Requests/ # Form Requests para validaciones
│ │ │ ├── Resources/ # API Resources
│ │ │ └── Middleware/ # Middleware personalizado
│ │ ├── Events/ # Event Handlers (infraestructura)
│ │ ├── Console/ # Comandos de consola
│ │ └── Providers/ # Service Providers
│ │
│ └── Shared/ # Código compartido
│ ├── Utils/ # Helpers y utilidades
│ └── Traits/ # Traits reutilizables
│
├── database/
│ ├── migrations/ # Migraciones
│ └── seeders/ # Seeders
│
└── routes/ # Rutas agrupadas y versionadas

Reglas clave
• Separación total entre reglas de negocio y Laravel.
• No usar Eloquent dentro de Domain ni Application.
• Los repositorios concretos van en Infrastructure/Persistence/Eloquent/.
• Los casos de uso son la única vía para modificar el estado del dominio.
• Los controladores sólo orquestan: reciben requests, llaman casos de uso, devuelven respuestas.
• Los DTOs se usan para la comunicación entre capas.
• Las rutas deben versionarse:

routes/api/v1/user.php
routes/api/v2/product.php

Flujo de ejemplo
• Controller → recibe un request.
• Form Request → valida datos.
• UseCase → ejecuta la lógica principal usando Entities + Repositories.
• Repository → implementado en Infrastructure (ej. Eloquent).
• Resource → formatea la respuesta final.

Convenciones de nombres
• Entidades → singular, PascalCase → User, Order.
• Casos de uso → verbo + recurso, PascalCase → CreateUserUseCase.
• DTOs → PascalCase + DTO → UserDTO.
• Controladores → PascalCase + Controller → UserController.
• Requests → PascalCase + Request → CreateUserRequest.
• Repositorios → PascalCase + Repository → UserRepository.

⸻

Móvil (React Native) — Fase Futura

• Framework: React Native con TypeScript
• Navegación: React Navigation 6+
• Estado: Redux Toolkit (sincronizado con web)
• Testing: Jest + Detox
• Estructura base:

src/
├── components/    # Componentes reutilizables
├── screens/      # Pantallas de la app
├── navigation/   # Configuración de navegación
├── hooks/        # Custom hooks compartidos
├── services/     # API calls compartidas con web
├── store/        # Estado compartido con web
├── types/        # Tipos TypeScript compartidos
└── utils/        # Utilidades compartidas

⸻

Base de datos — MySQL
• Tablas: nombres en inglés, plural, snake_case.
• IDs: usar el nombre de la tabla en singular + \_id → user_id.
• Columnas: nombres en inglés, singular, snake_case.
• Índices: idx_tabla_columna → idx_users_email.
• Triggers: nombres en inglés, snake_case.
• UUID como identificador público, generados en PHP:

bin2hex(random_bytes(16));

    •	Todas las tablas deben tener:
    •	Soft deletes.
    •	created_at, updated_at, deleted_at.
    •	Claves foráneas obligatorias para todas las relaciones.

⸻

Principios y buenas prácticas
• Aplicar SOLID, DRY, Clean Code y Clean Architecture.
• Usar interfaces sobre implementaciones.
• Evitar dependencias circulares.
• Usar inyección de dependencias sobre llamadas estáticas.
• Revisar patrones de diseño creacionales, estructurales y de comportamiento.
• Mantener tipado fuerte en TypeScript.
• Escribir tests unitarios para casos de uso y componentes.

⸻

Metodología de trabajo
• Metodología: Scrumban (combinación de Scrum y Kanban).
• Estimación: usar Story Points para medir el esfuerzo.
• Tablero: definir explícitamente en el proyecto (Jira, Trello, GitHub Projects).

⸻

Estado actual del repositorio

Documentación completada:
• ✅ Step 1: Planificación y Análisis (8 documentos)
• ✅ Step 2: Diseño y Arquitectura (11 documentos)
• 🚧 Step 3-9: Pendientes de documentar

Cuando generes código o documentación, asegúrate de que sea consistente con las etapas ya completadas.

⸻

Documentación de pasos

Cada sub-paso de esta guía debe tener un archivo .md con las siguientes secciones:
• Título
• ¿Qué es?
• ¿Por qué es importante?
• ¿Qué debe incluir?
• ¿Qué debo hacer?
• Tips
• Ejemplos
• Navegación: Mostrar el progreso y enlaces a pasos relacionados. Navegación entre pasos. Al siguiente paso, al README y al paso anterior.
