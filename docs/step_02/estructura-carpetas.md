# Estructura de Carpetas

## Conocimientos involucrados
- Organización de proyectos
- Separación de responsabilidades
- Estándares de estructura

## Responsable
- Equipo de desarrollo

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

**Estructura Progresiva - UI Components:**

```
src/
├── components/            # Componentes - Evolución por fases
│   ├── ui/               # Fase 1: Shadcn/ui components
│   │   ├── button.tsx    # Copy-paste components de Shadcn
│   │   ├── input.tsx     # Customizables al 100%
│   │   ├── card.tsx      # Base: Radix UI primitives
│   │   └── modal.tsx     # Tailwind CSS styling
│   │
│   ├── atoms/            # Fase 2: Atomic Design
│   │   ├── Button/       # Componentes básicos
│   │   │   ├── Button.tsx
│   │   │   ├── Button.stories.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   ├── Input/
│   │   └── Typography/
│   │
│   ├── molecules/        # Fase 2: Combinaciones simples
│   │   ├── SearchBox/    # Input + Button
│   │   ├── FormField/    # Label + Input + Error
│   │   └── Card/         # Header + Content + Footer
│   │
│   ├── organisms/        # Fase 2: Componentes complejos
│   │   ├── DataTable/    # Tabla con paginación
│   │   ├── Navigation/   # Menu de navegación
│   │   └── UserForm/     # Formulario completo
│   │
│   └── templates/        # Fase 2: Layout templates
│       ├── PageLayout/   # Layout base de páginas
│       └── DashboardLayout/
│
├── pages/                # Páginas/vistas principales
├── hooks/                # Custom hooks de React
├── services/             # Servicios y API calls
├── store/                # Estado global
│   ├── slices/          # Redux Toolkit slices (proyectos grandes)
│   └── stores/          # Zustand stores (proyectos pequeños)
├── lib/                  # Utilidades específicas
│   ├── utils.ts         # cn(), clsx helpers
│   ├── validations.ts   # Zod schemas
│   └── api.ts           # Axios configuration
├── types/                # Definiciones de TypeScript
├── constants/            # Constantes de la aplicación
├── styles/               # Estilos globales y Tailwind config
├── assets/               # Imágenes, iconos, etc.
├── stories/              # Fase 2: Storybook stories
│   ├── atoms/
│   ├── molecules/
│   └── organisms/
└── __tests__/            # Pruebas específicas de componentes
```

### Para Proyectos Back-end (Laravel + Clean Architecture)

```
app/
├── Domain/               # Núcleo: Reglas de negocio puras
│   ├── Entities/        # Objetos del dominio (POPOs)
│   │   ├── User.php
│   │   └── Order.php
│   ├── ValueObjects/    # Email, Money, RUT, etc.
│   │   ├── Email.php
│   │   └── Money.php
│   ├── Repositories/    # Interfaces de persistencia
│   │   ├── UserRepositoryInterface.php
│   │   └── OrderRepositoryInterface.php
│   ├── Services/        # Reglas complejas que combinan entidades
│   │   ├── UserDomainService.php
│   │   └── OrderDomainService.php
│   ├── Events/          # Eventos de dominio
│   │   ├── UserCreated.php
│   │   └── OrderCompleted.php
│   └── Exceptions/      # Excepciones propias del dominio
│       ├── UserNotFoundException.php
│       └── InvalidOrderStateException.php
│
├── Application/         # Capa de casos de uso
│   ├── UseCases/       # Casos de uso organizados por módulo
│   │   ├── User/
│   │   │   ├── CreateUserUseCase.php
│   │   │   ├── UpdateUserUseCase.php
│   │   │   └── DeleteUserUseCase.php
│   │   └── Order/
│   ├── DTOs/           # Data Transfer Objects
│   │   ├── CreateUserDTO.php
│   │   └── UpdateOrderDTO.php
│   ├── Commands/       # Commands para operaciones
│   │   ├── CreateUserCommand.php
│   │   └── ProcessOrderCommand.php
│   └── Queries/        # Queries para consultas específicas
│       ├── GetUserQuery.php
│       └── GetOrdersQuery.php
│
├── Infrastructure/     # Implementaciones técnicas
│   ├── Persistence/
│   │   └── Eloquent/   # Models y repositorios concretos
│   │       ├── Models/
│   │       │   ├── User.php
│   │       │   └── Order.php
│   │       └── Repositories/
│   │           ├── EloquentUserRepository.php
│   │           └── EloquentOrderRepository.php
│   ├── Http/
│   │   ├── Controllers/
│   │   │   └── Api/V1/ # Controladores API versionados
│   │   │       ├── UserController.php
│   │   │       └── OrderController.php
│   │   ├── Requests/   # Form Requests para validaciones
│   │   │   ├── CreateUserRequest.php
│   │   │   └── UpdateOrderRequest.php
│   │   ├── Resources/  # API Resources
│   │   │   ├── UserResource.php
│   │   │   └── OrderResource.php
│   │   └── Middleware/ # Middleware personalizado
│   ├── Events/         # Event Handlers (infraestructura)
│   ├── Console/        # Comandos de consola
│   └── Providers/      # Service Providers
│
└── Shared/             # Código compartido
    ├── Utils/          # Helpers y utilidades
    └── Traits/         # Traits reutilizables
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

**Frontend con Features + Atomic Design:**

```
src/
├── features/              # Características del negocio
│   ├── auth/
│   │   ├── components/   # Organisms específicos de auth
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   ├── services/
│   │   │   └── authService.ts
│   │   ├── store/        # Estado específico
│   │   │   └── authSlice.ts (Redux) o authStore.ts (Zustand)
│   │   ├── types/
│   │   │   └── auth.types.ts
│   │   └── index.ts
│   ├── dashboard/
│   │   ├── components/
│   │   ├── services/
│   │   └── store/
│   └── users/
├── components/           # Design System compartido
│   ├── ui/              # Fase 1: Shadcn components
│   ├── atoms/           # Fase 2: Atomic Design
│   ├── molecules/
│   ├── organisms/
│   └── templates/
└── shared/              # Código compartido
    ├── hooks/
    ├── utils/
    ├── types/
    └── constants/
```

**Backend con Features + Clean Architecture:**

```
app/
├── Features/             # Bounded Contexts por dominio
│   ├── User/
│   │   ├── Domain/
│   │   │   ├── Entities/
│   │   │   ├── ValueObjects/
│   │   │   └── Repositories/
│   │   ├── Application/
│   │   │   ├── UseCases/
│   │   │   └── DTOs/
│   │   └── Infrastructure/
│   │       ├── Persistence/
│   │       └── Http/
│   └── Order/
├── Shared/              # Código compartido entre features
│   ├── Domain/
│   ├── Application/
│   └── Infrastructure/
```

### 4. Configurar herramientas de desarrollo

- Crear scripts de automatización en `/scripts`
- Configurar linters y formatters (ESLint + Prettier)
- Establecer alias de importación (@/, @components, @lib)
- **Shadcn/ui**: Configurar `components.json` para path mapping
- **Storybook**: Setup para Fase 2 con .storybook/ config
- **Tailwind**: Configurar `tailwind.config.js` con design tokens

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
- **Atomic Design**: Componentes más específicos importan de más genéricos
- **Shadcn/ui**: Mantener components copiados en `/ui` sin modificar estructura
  interna
- **Clean Architecture**: Dependencias siempre hacia adentro (Domain ←
  Application ← Infrastructure)

### Herramientas Útiles

- **Path mapping**: Configurar alias como `@/components`, `@/lib`
- **Barrel exports**: Usar `index.ts` para exportaciones centralizadas
- **Auto-importación**: Configurar IDE para imports automáticos
- **Shadcn CLI**: `npx shadcn-ui@latest add button` para agregar components
- **Storybook**: Auto-discovery de stories con `*.stories.tsx`
- **Tailwind IntelliSense**: Autocomplete para clases CSS

## Ejemplos

### Estructura React + TypeScript + Vite + Shadcn/ui

```typescript
// src/components/ui/index.ts - Barrel export para Shadcn
export { Button } from "./button";
export { Input } from "./input";
export { Card, CardHeader, CardContent, CardFooter } from "./card";

// src/lib/utils.ts - Utility functions para Shadcn
import { type ClassValue, clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

// vite.config.ts - Path mapping
import { defineConfig } from "vite";
import path from "path";

export default defineConfig({
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
      "@/components": path.resolve(__dirname, "./src/components"),
      "@/lib": path.resolve(__dirname, "./src/lib"),
      "@/ui": path.resolve(__dirname, "./src/components/ui"),
    },
  },
});

// components.json - Configuración Shadcn/ui
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "src/app/globals.css",
    "baseColor": "slate",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

### Estructura Laravel con Clean Architecture

```php
<?php
// app/Application/UseCases/User/CreateUserUseCase.php
namespace App\Application\UseCases\User;

use App\Domain\Repositories\UserRepositoryInterface;
use App\Application\DTOs\CreateUserDTO;
use App\Domain\Entities\User;

class CreateUserUseCase
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function execute(CreateUserDTO $userData): User
    {
        // Lógica de caso de uso
        $user = new User(
            name: $userData->name,
            email: $userData->email
        );
        
        return $this->userRepository->save($user);
    }
}

// app/Infrastructure/Http/Controllers/Api/V1/UserController.php
namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\UseCases\User\CreateUserUseCase;
use App\Infrastructure\Http\Requests\CreateUserRequest;
use App\Infrastructure\Http\Resources\UserResource;

class UserController extends Controller
{
    public function __construct(
        private CreateUserUseCase $createUserUseCase
    ) {}

    public function store(CreateUserRequest $request): UserResource
    {
        $dto = CreateUserDTO::fromRequest($request);
        $user = $this->createUserUseCase->execute($dto);
        
        return new UserResource($user);
    }
}
```

### Feature-based Structure + Atomic Design

```
src/features/auth/
├── components/          # Organisms específicos de auth
│   ├── LoginForm.tsx   # Usa molecules y atoms del design system
│   └── RegisterForm.tsx
├── services/
│   └── authService.ts
├── store/
│   └── authSlice.ts    # Redux Toolkit slice
│   # O authStore.ts    # Zustand store (proyectos pequeños)
├── types/
│   └── auth.types.ts
└── index.ts            # Exportaciones públicas del feature

# Design System global
src/components/ui/       # Fase 1: Shadcn components
├── button.tsx
├── input.tsx
├── form.tsx
└── card.tsx

src/components/atoms/    # Fase 2: Atomic Design
├── Button/
│   ├── Button.tsx
│   ├── Button.stories.tsx  # Storybook story
│   ├── Button.test.tsx     # Unit tests
│   └── index.ts

# Uso en feature:
// src/features/auth/components/LoginForm.tsx
import { Button } from "@/components/ui/button";  // Fase 1
// import { Button } from "@/components/atoms";    // Fase 2
```

## Navegación

[⬅️ Convenciones y Estándares](./convenciones-estandares.md) |
[🏠 README Principal](../../README.md) |
[Manejo de Imágenes ➡️](./manejo-imagenes.md)
