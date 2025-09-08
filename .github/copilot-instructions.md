# GitHub Copilot Instructions

# General

Este repositorio será una guía paso a paso para la creación de un proyecto, siguiendo un estándar para mis proyectos. Este estándar aplica a cada etapa y capa del proyecto.

# Stack tecnológico

## Front-end

- Framework: React
- Lenguaje: TypeScript
- Bundle: Vite
- Tipado: Mantener un tipado fuerte.
- Estado: Redux toolkit

## Back-end

- Framework: Laravel
- Importante usar arquitectura por capas y clean architecture.

## Mobile

- Framework: React-native para aplicaciones móviles.

## Base de datos

- Framework: MySQL

Es importante que se sigan los principios solid, STUPID, DRY, clean code, clean architecture, que se revisen patrones de diseño creacionales, estructurales y de comportamiento.

Cada sub paso debe tener las siguientes secciones:

- Título
- ¿Que es?
- ¿Porque es importante?
- ¿Que debe de incluir?
- ¿Que debo hacer?
- Tips
- Ejemplos
- Navegación a siguiente documento y home (README.md)

# Estimación de tiempo

Para la estimación de tiempo se hará uso de Story Points.

# Metodología de trabajo

Para la metodología de trabajo se propone utilizar Scrumban, una combinación de Scrum y Kanban que permite flexibilidad y estructura en el desarrollo de proyectos.

# Convenciones

# Backend (Laravel)

- Nombres de clases en inglés, PascalCase y singular.
- Nombres de métodos en inglés, camelCase y verbos.
- Nombres de variables en inglés, camelCase y descriptivos.
- Nombres de interfaces en inglés, PascalCase y prefijados con "I".
- Nombres de traits en inglés, PascalCase y descriptivos.
- Nombres de excepciones en inglés, PascalCase y terminados en "Exception".
- Nombres de controladores en inglés, PascalCase y terminados en "Controller".
- Nombres de middlewares en inglés, PascalCase y terminados en "Middleware".
- Nombres de servicios en inglés, PascalCase y terminados en "Service".
- Nombres de repositorios en inglés, PascalCase y terminados en "Repository".
- Nombres de migraciones en inglés, snake_case y descriptivos.
- Nombres de seeders en inglés, PascalCase y terminados en "Seeder".
- Nombres de factories en inglés, PascalCase y terminados en "Factory".
- Nombres de rutas en inglés, kebab-case y descriptivos.

## Mysql

- Tablas: Nombres en ingles, plural y snake_case
- Los id se deben definir usando el nombre de la tabla en singular seguido de `_id`, ejemplo `user_id`
- Columnas: Nombres en ingles, singular y snake_case
- Triggers: Nombres en ingles, snake_case
- Indices: Nombres en ingles, snake*case, prefijados con "idx*", usando la siguiente estructura: `idx_tabla_columna`.
- UUID como identificador público pero deben ser generados desde PHP con `bin2hex(random_bytes(16))` para mayor seguridad.
- Todas las tablas deben tener soft deletes implementados.
- Todas las tablas deben tener timestamps (`created_at`, `updated_at`, `deleted_at`).
- Se deben de crear claves foráneas para todas las relaciones entre tablas.
