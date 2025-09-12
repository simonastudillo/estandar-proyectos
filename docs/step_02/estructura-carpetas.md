# Estructura de Carpetas

## ¿Qué es?

La estructura de carpetas es la organización jerárquica de directorios y
archivos en un proyecto de software. Define cómo se agrupan, categorizan y
distribuyen los diferentes componentes del código fuente, recursos,
documentación y configuraciones para mantener el proyecto ordenado y fácil de
navegar.

## ¿Por qué es importante?

- **Mantenibilidad**: Facilita la localización y modificación de archivos
  específicos
- **Escalabilidad**: Permite que el proyecto crezca de manera organizada
- **Colaboración**: Los equipos pueden entender rápidamente la estructura del
  proyecto
- **Productividad**: Reduce el tiempo de búsqueda de archivos y componentes
- **Estándares**: Mantiene consistencia entre proyectos
- **Separación de responsabilidades**: Agrupa funcionalidades relacionadas

## ¿Qué debe incluir?

### Estructura Base del Proyecto

```
proyecto/
├── docs/                   # Documentación del proyecto
├── src/                    # Código fuente principal
├── tests/                  # Pruebas unitarias e integración
├── config/                 # Archivos de configuración
├── scripts/                # Scripts de automatización
├── assets/                 # Recursos estáticos
├── .github/                # Configuración de GitHub
├── .gitignore             # Archivos ignorados por Git
├── README.md              # Documentación principal
└── package.json           # Dependencias y scripts (Node.js)
```

### Para Proyectos Front-end (React + TypeScript + Vite)

```
src/
├── components/            # Componentes reutilizables
│   ├── common/           # Componentes comunes
│   ├── ui/               # Componentes de interfaz
│   └── forms/            # Componentes de formularios
├── pages/                # Páginas/vistas principales
├── hooks/                # Custom hooks de React
├── services/             # Servicios y API calls
├── store/                # Estado global (Redux Toolkit)
│   ├── slices/          # Redux slices
│   └── middleware/      # Middleware personalizado
├── types/                # Definiciones de TypeScript
├── utils/                # Funciones utilitarias
├── constants/            # Constantes de la aplicación
├── styles/               # Estilos globales
├── assets/               # Imágenes, iconos, etc.
└── __tests__/            # Pruebas específicas de componentes
```

### Para Proyectos Back-end (Laravel)

```
app/
├── Http/
│   ├── Controllers/      # Controladores
│   ├── Middleware/       # Middleware personalizado
│   ├── Requests/         # Form requests para validación
│   └── Resources/        # API resources
├── Models/               # Modelos Eloquent
├── Services/             # Lógica de negocio
├── Repositories/         # Patrón Repository
├── Interfaces/           # Contratos e interfaces
├── DTOs/                 # Data Transfer Objects
├── Events/               # Eventos del sistema
├── Listeners/            # Manejadores de eventos
├── Jobs/                 # Jobs para colas
├── Mail/                 # Clases de email
└── Exceptions/           # Excepciones personalizadas
```

## ¿Qué debo hacer?

### 1. Definir la estructura base

- Crear los directorios principales según el tipo de proyecto
- Establecer convenciones de nomenclatura claras
- Documentar la estructura en el README.md

### 2. Implementar separación por capas

- **Presentación**: Componentes UI, páginas, controladores
- **Lógica de negocio**: Services, casos de uso
- **Datos**: Repositories, modelos, DTOs

### 3. Organizar por características (Feature-based)

```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   ├── services/
│   │   ├── types/
│   │   └── index.ts
│   ├── dashboard/
│   └── users/
└── shared/              # Código compartido
    ├── components/
    ├── utils/
    └── types/
```

### 4. Configurar herramientas de desarrollo

- Crear scripts de automatización en `/scripts`
- Configurar linters y formatters
- Establecer alias de importación

### 5. Mantener consistencia

- Usar convenciones de nomenclatura uniformes
- Agrupar archivos relacionados
- Evitar anidamiento excesivo (máximo 3-4 niveles)

## Tips

### Convenciones de Nomenclatura

- **Carpetas**: kebab-case o camelCase consistente
- **Archivos**: PascalCase para componentes, camelCase para utilitarios
- **Constantes**: UPPER_SNAKE_CASE

### Buenas Prácticas

- Máximo 7±2 elementos por carpeta (regla de Miller)
- Usar archivos `index.ts` para exportaciones limpias
- Agrupar por dominio antes que por tipo técnico
- Mantener archivos relacionados cerca físicamente

### Herramientas Útiles

- **Path mapping**: Configurar alias como `@/components`
- **Barrel exports**: Usar `index.ts` para exportaciones centralizadas
- **Auto-importación**: Configurar IDE para imports automáticos

## Ejemplos

### Estructura React + TypeScript + Vite

```typescript
// src/components/index.ts - Barrel export
export { Button } from "./ui/Button";
export { Modal } from "./ui/Modal";
export { UserForm } from "./forms/UserForm";

// vite.config.ts - Path mapping
import { defineConfig } from "vite";
import path from "path";

export default defineConfig({
   resolve: {
      alias: {
         "@": path.resolve(__dirname, "./src"),
         "@components": path.resolve(__dirname, "./src/components"),
         "@services": path.resolve(__dirname, "./src/services"),
      },
   },
});
```

### Estructura Laravel con Clean Architecture

```php
<?php
// app/Services/UserService.php
namespace App\Services;

use App\Interfaces\UserRepositoryInterface;
use App\DTOs\CreateUserDTO;

class UserService
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function createUser(CreateUserDTO $userData): User
    {
        // Lógica de negocio
        return $this->userRepository->create($userData->toArray());
    }
}
```

### Feature-based Structure

```
src/features/auth/
├── components/
│   ├── LoginForm.tsx
│   └── RegisterForm.tsx
├── services/
│   └── authService.ts
├── store/
│   └── authSlice.ts
├── types/
│   └── auth.types.ts
├── utils/
│   └── validation.ts
└── index.ts              # Exportaciones públicas
```

## Navegación

[⬅️ Convenciones y Estándares](./convenciones-estandares.md) |
[🏠 README Principal](../../README.md) |
[Diseño de Base de Datos ➡️](./diseno-base-datos.md)
