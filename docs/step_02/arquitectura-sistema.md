# Arquitectura del Sistema

## Conocimientos involucrados
- Diseño de software
- Arquitectura de sistemas
- Patrones de diseño (Clean Architecture, Layered Architecture)
- Herramientas de desarrollo frontend y backend

## Responsable
- Equipo de desarrollo

## ¿Qué es?

La arquitectura del sistema es el diseño estructural de alto nivel que define
cómo los diferentes componentes de una aplicación interactúan entre sí.
Establece las capas, módulos, patrones de comunicación y flujo de datos que
garantizan que el sistema sea escalable, mantenible y robusto.

En nuestro estándar, utilizamos una **arquitectura por capas (layered
architecture)** combinada con **clean architecture**, lo que nos permite separar
responsabilidades y mantener un bajo acoplamiento entre componentes.

## ¿Por qué es importante?

- **Escalabilidad**: Permite que el sistema crezca de manera ordenada sin
  comprometer la performance
- **Mantenibilidad**: Facilita la modificación y extensión del código
- **Testabilidad**: Cada capa puede ser probada de forma independiente
- **Reutilización**: Los componentes pueden ser reutilizados en diferentes
  contextos
- **Separación de responsabilidades**: Cada capa tiene una función específica y
  bien definida
- **Flexibilidad**: Permite cambios en una capa sin afectar las demás

## ¿Qué debe incluir?

### 1. Capas de la Arquitectura

#### **Capa de Presentación (Frontend)**

- **React + TypeScript + Vite**
- Componentes UI reutilizables
- Gestión de estado con Redux Toolkit
- Routing y navegación
- Validación de formularios
- Manejo de errores de UI

#### **Capa de Lógica de Negocio (Backend)**

- **Laravel**
- Controllers para endpoints API
- Services para lógica de negocio
- Repositories para acceso a datos
- Middleware para autenticación y autorización
- Validadores de request

#### **Capa de Datos**

- **MySQL**
- Modelos de base de datos
- Migraciones y seeders
- Relaciones entre entidades
- Índices y optimizaciones

#### **Capa de Infraestructura**

- Configuración de servidores
- Variables de entorno
- Logs y monitoreo
- Caching (Redis)
- Queue management

### 2. Patrones de Diseño a Implementar

#### **Patrones Creacionales**

- **Factory Pattern**: Para crear instancias de servicios
- **Builder Pattern**: Para construcción compleja de objetos
- **Singleton Pattern**: Para configuraciones globales

#### **Patrones Estructurales**

- **Repository Pattern**: Para abstracción de acceso a datos
- **Adapter Pattern**: Para integración con APIs externas
- **Decorator Pattern**: Para middleware y funcionalidades adicionales

#### **Patrones de Comportamiento**

- **Observer Pattern**: Para notificaciones y eventos
- **Strategy Pattern**: Para algoritmos intercambiables
- **Command Pattern**: Para operaciones reversibles

### 3. Principios SOLID

- **S** - Single Responsibility Principle
- **O** - Open/Closed Principle
- **L** - Liskov Substitution Principle
- **I** - Interface Segregation Principle
- **D** - Dependency Inversion Principle

## ¿Qué debo hacer?

### 1. Definir la Estructura de Carpetas

```
proyecto/
├── frontend/                 # React + TypeScript + Vite
│   ├── src/
│   │   ├── components/      # Componentes reutilizables
│   │   ├── pages/           # Páginas de la aplicación
│   │   ├── hooks/           # Custom hooks
│   │   ├── store/           # Redux store y slices
│   │   ├── services/        # Servicios API
│   │   ├── types/           # Definiciones TypeScript
│   │   ├── utils/           # Utilidades y helpers
│   │   └── constants/       # Constantes de la app
│   ├── public/
│   └── package.json
├── backend/                  # Laravel - Clean Architecture
│   ├── app/
│   │   ├── Domain/          # Núcleo: reglas de negocio puras
│   │   │   ├── Entities/    # Objetos del dominio (POPOs)
│   │   │   ├── ValueObjects/ # Email, Dinero, RUT, etc.
│   │   │   ├── Repositories/ # Interfaces para persistencia
│   │   │   ├── Services/    # Reglas complejas que involucran varias entidades
│   │   │   ├── Events/      # Eventos del dominio
│   │   │   └── Exceptions/  # Excepciones del dominio
│   │   ├── Application/     # Capa de casos de uso
│   │   │   ├── UseCases/    # Casos de uso organizados por módulo
│   │   │   ├── DTOs/        # Data Transfer Objects
│   │   │   ├── Commands/    # Commands para operaciones
│   │   │   └── Queries/     # Queries para consultas
│   │   ├── Infrastructure/  # Implementaciones técnicas
│   │   │   ├── Persistence/
│   │   │   │   └── Eloquent/ # Repositorios y Models
│   │   │   ├── Http/
│   │   │   │   ├── Controllers/
│   │   │   │   │   └── Api/V1/ # Controladores API versionados
│   │   │   │   ├── Requests/ # Form requests
│   │   │   │   ├── Resources/ # API Resources
│   │   │   │   └── Middleware/ # Middleware personalizado
│   │   │   ├── Events/      # Event Handlers
│   │   │   ├── Console/     # Commands de consola
│   │   │   └── Providers/   # Service Providers
│   │   └── Shared/          # Código compartido
│   │       ├── Utils/       # Utilidades
│   │       └── Traits/      # Traits reutilizables
│   ├── database/
│   │   ├── migrations/      # Migraciones
│   │   └── seeders/         # Seeders
│   └── routes/
├── mobile/                   # React Native (futuro)
├── database/                 # Scripts y documentación DB
├── docs/                     # Documentación del proyecto
└── docker/                   # Configuración Docker
```

### 2. Implementar Comunicación Entre Capas

#### **Frontend ↔ Backend**

- API RESTful con endpoints bien definidos
- Autenticación JWT
- Manejo de errores HTTP estandarizado
- Paginación y filtrado consistente

#### **Backend ↔ Base de Datos**

- Uso de Eloquent ORM
- Query builders optimizados
- Transacciones para operaciones críticas
- Soft deletes para datos sensibles

### 3. Definir Flujos de Datos

#### **Flujo de Lectura (Clean Architecture)**

1. Frontend solicita datos via API
2. Controller (Infrastructure) recibe request y crea Query/Command
3. UseCase (Application) procesa la lógica del caso de uso
4. Repository Interface (Domain) define contrato de datos
5. Repository Implementation (Infrastructure) consulta base de datos
6. Datos retornan como DTOs por las mismas capas
7. Frontend actualiza estado y UI

#### **Flujo de Escritura (Clean Architecture)**

1. Frontend envía datos via API
2. Validation Request (Infrastructure) valida entrada
3. Controller crea Command y llama UseCase
4. UseCase ejecuta lógica de negocio usando Domain Services
5. Domain Entities aplican reglas de negocio
6. Repository persiste entidades en base de datos
7. Eventos del dominio se disparan si es necesario
8. Response DTO confirma operación
9. Frontend actualiza estado local

## Tips

### 🎯 **Arquitectura**

- Mantén las capas desacopladas usando interfaces
- Implementa inyección de dependencias
- Usa eventos para comunicación asíncrona
- Documenta las decisiones arquitectónicas

### 🔧 **Implementación**

- Comienza con una estructura simple y evoluciona
- Implementa logging en todas las capas
- Usa caché estratégicamente
- Planifica para testing desde el inicio

### 📊 **Performance**

- Implementa lazy loading en frontend
- Usa índices apropiados en base de datos
- Considera paginación para listas grandes
- Optimiza queries N+1

### 🛡️ **Seguridad**

- Valida datos en todas las capas
- Implementa rate limiting
- Usa HTTPS en producción
- Sanitiza inputs de usuario

## Ejemplos

### Ejemplo 1: Domain Entity (Clean Architecture)

```php
<?php

namespace App\Domain\Entities;

use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\Password;

class Usuario
{
    public function __construct(
        private int $id,
        private string $nombre,
        private Email $email,
        private Password $password,
        private \DateTimeImmutable $fechaCreacion
    ) {}

    public function cambiarEmail(Email $nuevoEmail): void
    {
        // Lógica de validación del dominio
        if ($this->email->equals($nuevoEmail)) {
            throw new \App\Domain\Exceptions\EmailYaExiste('El nuevo email debe ser diferente al actual');
        }

        $this->email = $nuevoEmail;
    }

    public function getId(): int
    {
        return $this->id;
    }

    public function getNombre(): string
    {
        return $this->nombre;
    }

    public function getEmail(): Email
    {
        return $this->email;
    }
}
```

### Ejemplo 2: UseCase (Application Layer)

```php
<?php

namespace App\Application\UseCases\Usuarios;

use App\Domain\Entities\Usuario;
use App\Domain\Repositories\UsuarioRepositoryInterface;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\Password;
use App\Application\Commands\RegistrarUsuarioCommand;
use App\Application\DTOs\UsuarioDTO;

class RegistrarUsuarioUseCase
{
    public function __construct(
        private UsuarioRepositoryInterface $usuarioRepository
    ) {}

    public function ejecutar(RegistrarUsuarioCommand $command): UsuarioDTO
    {
        // Validaciones del dominio
        $email = new Email($command->email);
        $password = new Password($command->password);

        // Verificar que no existe usuario con ese email
        if ($this->usuarioRepository->existeConEmail($email)) {
            throw new \App\Domain\Exceptions\EmailYaExiste($email->valor());
        }

        // Crear entidad del dominio
        $usuario = new Usuario(
            id: 0, // Se asignará en persistencia
            nombre: $command->nombre,
            email: $email,
            password: $password,
            fechaCreacion: new \DateTimeImmutable()
        );

        // Persistir
        $usuarioGuardado = $this->usuarioRepository->guardar($usuario);

        // Retornar DTO
        return new UsuarioDTO(
            id: $usuarioGuardado->getId(),
            nombre: $usuarioGuardado->getNombre(),
            email: $usuarioGuardado->getEmail()->valor()
        );
    }
}
```

### Ejemplo 3: Controller (Infrastructure Layer)

```php
<?php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\UseCases\Usuarios\RegistrarUsuarioUseCase;
use App\Application\Commands\RegistrarUsuarioCommand;
use App\Infrastructure\Http\Requests\RegistrarUsuarioRequest;
use App\Infrastructure\Http\Resources\UsuarioResource;
use Illuminate\Http\JsonResponse;

class UsuarioController extends \App\Http\Controllers\Controller
{
    public function __construct(
        private RegistrarUsuarioUseCase $registrarUsuarioUseCase
    ) {}

    public function store(RegistrarUsuarioRequest $request): JsonResponse
    {
        try {
            $command = new RegistrarUsuarioCommand(
                nombre: $request->validated('nombre'),
                email: $request->validated('email'),
                password: $request->validated('password')
            );

            $usuarioDTO = $this->registrarUsuarioUseCase->ejecutar($command);

            return response()->json([
                'success' => true,
                'data' => new UsuarioResource($usuarioDTO),
                'message' => 'Usuario registrado exitosamente'
            ], 201);

        } catch (\App\Domain\Exceptions\EmailYaExiste $e) {
            return response()->json([
                'success' => false,
                'message' => 'El email ya está registrado',
                'errors' => ['email' => [$e->getMessage()]]
            ], 422);
        }
    }
}
```

### Ejemplo 4: Store Slice (Frontend)

```typescript
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";
import { userService } from "../services/userService";
import { User } from "../types/User";

interface UserState {
   users: User[];
   loading: boolean;
   error: string | null;
}

const initialState: UserState = {
   users: [],
   loading: false,
   error: null,
};

export const fetchUsers = createAsyncThunk("users/fetchUsers", async () => {
   const response = await userService.getUsers();
   return response.data;
});

const userSlice = createSlice({
   name: "users",
   initialState,
   reducers: {},
   extraReducers: (builder) => {
      builder
         .addCase(fetchUsers.pending, (state) => {
            state.loading = true;
            state.error = null;
         })
         .addCase(fetchUsers.fulfilled, (state, action) => {
            state.loading = false;
            state.users = action.payload;
         })
         .addCase(fetchUsers.rejected, (state, action) => {
            state.loading = false;
            state.error = action.error.message || "Error fetching users";
         });
   },
});

export default userSlice.reducer;
```

### Ejemplo 5: Repository Pattern (Clean Architecture)

```php
<?php

namespace App\Infrastructure\Persistence\Eloquent;

use App\Domain\Entities\Usuario;
use App\Domain\Repositories\UsuarioRepositoryInterface;
use App\Domain\ValueObjects\Email;
use App\Infrastructure\Persistence\Eloquent\Models\Usuario as UsuarioModel;

class EloquentUsuarioRepository implements UsuarioRepositoryInterface
{
    public function existeConEmail(Email $email): bool
    {
        return UsuarioModel::where('email', $email->valor())->exists();
    }

    public function guardar(Usuario $usuario): Usuario
    {
        $usuarioModel = UsuarioModel::create([
            'nombre' => $usuario->getNombre(),
            'email' => $usuario->getEmail()->valor(),
            'password' => $usuario->getPassword()->valorHasheado(),
            'created_at' => $usuario->getFechaCreacion(),
        ]);

        return new Usuario(
            id: $usuarioModel->id,
            nombre: $usuarioModel->nombre,
            email: new Email($usuarioModel->email),
            password: $usuario->getPassword(),
            fechaCreacion: $usuarioModel->created_at->toDateTimeImmutable()
        );
    }

    public function encontrarPorId(int $id): ?Usuario
    {
        $usuarioModel = UsuarioModel::find($id);

        if (!$usuarioModel) {
            return null;
        }

        return new Usuario(
            id: $usuarioModel->id,
            nombre: $usuarioModel->nombre,
            email: new Email($usuarioModel->email),
            password: new Password($usuarioModel->password, true), // Ya hasheado
            fechaCreacion: $usuarioModel->created_at->toDateTimeImmutable()
        );
    }
}
```

### Ejemplo 6: API Service (Frontend)

```typescript
import axios from "axios";
import { CreateUserRequest, UpdateUserRequest, User } from "../types/User";

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL;

const apiClient = axios.create({
   baseURL: API_BASE_URL,
   headers: {
      "Content-Type": "application/json",
   },
});

// Interceptor para agregar token de autenticación
apiClient.interceptors.request.use((config) => {
   const token = localStorage.getItem("authToken");
   if (token) {
      config.headers.Authorization = `Bearer ${token}`;
   }
   return config;
});

export const userService = {
   async getUsers(): Promise<{ data: User[] }> {
      const response = await apiClient.get("/users");
      return response.data;
   },

   async getUserById(id: number): Promise<{ data: User }> {
      const response = await apiClient.get(`/users/${id}`);
      return response.data;
   },

   async createUser(userData: CreateUserRequest): Promise<{ data: User }> {
      const response = await apiClient.post("/users", userData);
      return response.data;
   },

   async updateUser(
      id: number,
      userData: UpdateUserRequest,
   ): Promise<{ data: User }> {
      const response = await apiClient.put(`/users/${id}`, userData);
      return response.data;
   },

   async deleteUser(id: number): Promise<void> {
      await apiClient.delete(`/users/${id}`);
   },
};
```

## Navegación

[⬅️ Patrones de Diseño](./patrones-diseno.md) |
[🏠 README Principal](../../README.md) |
[Diagrama de Arquitectura General ➡️](./diagrama-arquitectura-general.md)
