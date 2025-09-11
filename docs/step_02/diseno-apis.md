# Diseño de APIs

## ¿Qué es?

El diseño de APIs (Application Programming Interface) es el proceso de definir y
estructurar las interfaces que permiten la comunicación entre diferentes
componentes de software. En el contexto de aplicaciones web, las APIs RESTful
actúan como puente entre el frontend y el backend, definiendo cómo se
intercambian los datos y qué operaciones están disponibles.

## ¿Por qué es importante?

- **Comunicación eficiente**: Establece un protocolo claro para el intercambio
  de datos entre cliente y servidor
- **Escalabilidad**: Permite que múltiples aplicaciones consuman los mismos
  servicios
- **Mantenibilidad**: Un diseño bien estructurado facilita futuras
  modificaciones y extensiones
- **Separación de responsabilidades**: Permite desarrollar frontend y backend de
  forma independiente
- **Reutilización**: Las APIs bien diseñadas pueden ser consumidas por
  diferentes tipos de clientes (web, móvil, terceros)
- **Documentación clara**: Facilita el trabajo en equipo y la integración con
  sistemas externos

## ¿Qué debe incluir?

### Estructura de Endpoints

- Rutas consistentes siguiendo convenciones RESTful
- Uso correcto de métodos HTTP (GET, POST, PUT, PATCH, DELETE)
- Versionado de API
- Parámetros de consulta y filtrado

### Formato de Respuestas

- Estructura JSON consistente
- Códigos de estado HTTP apropiados
- Mensajes de error descriptivos
- Metadatos de paginación

### Autenticación y Autorización

- Implementación de JWT tokens
- Middleware de autenticación
- Control de acceso basado en roles

### Validación y Sanitización

- Validación de datos de entrada
- Sanitización para prevenir ataques
- Manejo de errores de validación

## ¿Qué debo hacer?

### 1. Definir la Estructura de Endpoints

```
# Usuarios
GET    /api/v1/users              # Listar usuarios
POST   /api/v1/users              # Crear usuario
GET    /api/v1/users/{uuid}       # Obtener usuario específico
PUT    /api/v1/users/{uuid}       # Actualizar usuario completo
PATCH  /api/v1/users/{uuid}       # Actualizar usuario parcial
DELETE /api/v1/users/{uuid}       # Eliminar usuario (soft delete)

# Autenticación
POST   /api/v1/auth/login         # Iniciar sesión
POST   /api/v1/auth/logout        # Cerrar sesión
POST   /api/v1/auth/refresh       # Renovar token
```

### 2. Establecer Formato de Respuestas

```json
{
   "success": true,
   "data": {
      "user": {
         "uuid": "550e8400-e29b-41d4-a716-446655440000",
         "name": "Juan Pérez",
         "email": "juan@example.com"
      }
   },
   "message": "Usuario creado exitosamente",
   "meta": {
      "timestamp": "2024-01-15T10:30:00Z",
      "version": "v1"
   }
}
```

### 3. Implementar Manejo de Errores

```json
{
   "success": false,
   "error": {
      "code": "VALIDATION_ERROR",
      "message": "Los datos proporcionados no son válidos",
      "details": [
         {
            "field": "email",
            "message": "El email ya está registrado"
         }
      ]
   },
   "meta": {
      "timestamp": "2024-01-15T10:30:00Z",
      "version": "v1"
   }
}
```

### 4. Configurar Middleware de Autenticación

```php
// En Laravel
Route::middleware(['auth:api'])->group(function () {
    Route::apiResource('users', UserController::class);
});
```

### 5. Implementar Paginación

```json
{
   "success": true,
   "data": {
      "users": [
         // ... array de usuarios
      ]
   },
   "pagination": {
      "current_page": 1,
      "last_page": 10,
      "per_page": 15,
      "total": 150,
      "from": 1,
      "to": 15
   }
}
```

## Tips

### Convenciones de Naming

- Usar nombres en plural para recursos (`/users` no `/user`)
- Usar kebab-case para URLs (`/user-profiles` no `/userProfiles`)
- Ser consistente con el formato de fechas (ISO 8601)

### Códigos de Estado HTTP

- **200**: Operación exitosa
- **201**: Recurso creado
- **204**: Operación exitosa sin contenido
- **400**: Error de validación
- **401**: No autenticado
- **403**: Sin permisos
- **404**: Recurso no encontrado
- **422**: Error de validación de entidad
- **500**: Error interno del servidor

### Optimización

- Implementar caché para endpoints frecuentemente consultados
- Usar compresión GZIP
- Implementar rate limiting
- Documentar límites de la API

### Seguridad

- Validar todos los inputs
- Implementar CORS apropiadamente
- Usar HTTPS en producción
- No exponer información sensible en respuestas

## Ejemplos

### Controlador Base para API

```php
<?php

namespace App\Http\Controllers\Api\V1;

use App\Http\Controllers\Controller;
use Illuminate\Http\JsonResponse;

abstract class BaseApiController extends Controller
{
    protected function successResponse(
        $data = null,
        string $message = 'Operación exitosa',
        int $statusCode = 200
    ): JsonResponse {
        return response()->json([
            'success' => true,
            'data' => $data,
            'message' => $message,
            'meta' => [
                'timestamp' => now()->toISOString(),
                'version' => 'v1'
            ]
        ], $statusCode);
    }

    protected function errorResponse(
        string $message = 'Error en la operación',
        int $statusCode = 400,
        array $details = []
    ): JsonResponse {
        return response()->json([
            'success' => false,
            'error' => [
                'message' => $message,
                'details' => $details
            ],
            'meta' => [
                'timestamp' => now()->toISOString(),
                'version' => 'v1'
            ]
        ], $statusCode);
    }
}
```

### Controlador de Usuarios

```php
<?php

namespace App\Http\Controllers\Api\V1;

use App\Http\Requests\CreateUserRequest;
use App\Http\Requests\UpdateUserRequest;
use App\Http\Resources\UserResource;
use App\Services\UserService;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class UserController extends BaseApiController
{
    public function __construct(
        private UserService $userService
    ) {}

    public function index(Request $request): JsonResponse
    {
        $users = $this->userService->getAllUsers($request->all());

        return $this->successResponse(
            UserResource::collection($users),
            'Usuarios obtenidos exitosamente'
        );
    }

    public function store(CreateUserRequest $request): JsonResponse
    {
        $user = $this->userService->createUser($request->validated());

        return $this->successResponse(
            new UserResource($user),
            'Usuario creado exitosamente',
            201
        );
    }

    public function show(string $uuid): JsonResponse
    {
        $user = $this->userService->getUserByUuid($uuid);

        if (!$user) {
            return $this->errorResponse('Usuario no encontrado', 404);
        }

        return $this->successResponse(
            new UserResource($user),
            'Usuario obtenido exitosamente'
        );
    }
}
```

### Resource para Transformar Datos

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'uuid' => $this->uuid,
            'name' => $this->name,
            'email' => $this->email,
            'email_verified_at' => $this->email_verified_at?->toISOString(),
            'created_at' => $this->created_at->toISOString(),
            'updated_at' => $this->updated_at->toISOString(),
        ];
    }
}
```

### Middleware de Autenticación Personalizado

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Tymon\JWTAuth\Facades\JWTAuth;
use Tymon\JWTAuth\Exceptions\JWTException;

class AuthenticateApi
{
    public function handle(Request $request, Closure $next)
    {
        try {
            $user = JWTAuth::parseToken()->authenticate();

            if (!$user) {
                return response()->json([
                    'success' => false,
                    'error' => [
                        'message' => 'Token inválido'
                    ]
                ], 401);
            }

        } catch (JWTException $e) {
            return response()->json([
                'success' => false,
                'error' => [
                    'message' => 'Token requerido'
                ]
            ], 401);
        }

        return $next($request);
    }
}
```

### Request de Validación

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Http\Exceptions\HttpResponseException;
use Illuminate\Contracts\Validation\Validator;

class CreateUserRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|string|min:8|confirmed',
        ];
    }

    public function messages(): array
    {
        return [
            'name.required' => 'El nombre es obligatorio',
            'email.required' => 'El email es obligatorio',
            'email.email' => 'El email debe tener un formato válido',
            'email.unique' => 'Este email ya está registrado',
            'password.required' => 'La contraseña es obligatoria',
            'password.min' => 'La contraseña debe tener al menos 8 caracteres',
            'password.confirmed' => 'Las contraseñas no coinciden',
        ];
    }

    protected function failedValidation(Validator $validator)
    {
        throw new HttpResponseException(
            response()->json([
                'success' => false,
                'error' => [
                    'message' => 'Error de validación',
                    'details' => $validator->errors()
                ]
            ], 422)
        );
    }
}
```

## Navegación

**Progreso en Diseño y Arquitectura:**

- ✅ [Diseño y Arquitectura - Introducción](./diseno-arquitectura.md)
- ✅ [Stack Tecnológico](./stack-tecnologico.md)
- ✅ [Elección de Herramientas](./eleccion-herramientas.md)
- ✅ [Patrones de Diseño](./patrones-diseno.md)
- ✅ [Arquitectura del Sistema](./arquitectura-sistema.md)
- ✅ [Diagrama de Arquitectura General](./diagrama-arquitectura-general.md)
- ✅ [Estructura de Carpetas](./estructura-carpetas.md)
- ✅ [Diseño de Base de Datos](./diseno-base-datos.md)
- ✅ **Diseño de APIs** ← Estás aquí
- ⏭️ [Diagramas de Flujo](./diagramas-flujo.md)
- ⏭️ [Casos de Uso y User Stories](./casos-uso-user-stories.md)
- ⏭️ [Prototipos y Wireframes](./prototipos-wireframes.md)
- ⏭️ [Especificaciones Técnicas](./especificaciones-tecnicas.md)

---

### Siguiente Paso

Continúa con los [**Diagramas de Flujo**](./diagramas-flujo.md).

[⬅️ Diseño de Base de Datos](./diseno-base-datos.md) |
[🏠 README Principal](../../README.md) |
[➡️ Diagramas de Flujo](./diagramas-flujo.md)
