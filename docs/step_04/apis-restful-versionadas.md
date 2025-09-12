# APIs RESTful Versionadas

## ¿Qué es?

Las APIs RESTful versionadas son interfaces de programación de aplicaciones que
siguen los principios REST (Representational State Transfer) y incluyen un
sistema de versionado que permite evolucionar la API sin romper la
compatibilidad con clientes existentes. Este enfoque garantiza que diferentes
versiones de la API puedan coexistir y que los cambios se introduzcan de manera
controlada.

## ¿Por qué es importante?

- **Compatibilidad hacia atrás**: Permite mantener versiones anteriores
  funcionando mientras se desarrollan nuevas funcionalidades
- **Evolución controlada**: Facilita la introducción de cambios sin afectar
  clientes existentes
- **Documentación clara**: Cada versión tiene su propia documentación y
  comportamiento definido
- **Migración gradual**: Los clientes pueden migrar a nuevas versiones a su
  propio ritmo
- **Mantenimiento**: Permite deprecar versiones antiguas de forma planificada
- **Estabilidad**: Garantiza que los cambios no rompan aplicaciones en
  producción

## ¿Qué debe incluir?

### Estructura de Versionado

```
routes/api/
├── v1/
│   ├── auth.php      # Autenticación v1
│   ├── users.php     # Gestión de usuarios v1
│   ├── orders.php    # Gestión de órdenes v1
│   └── products.php  # Gestión de productos v1
├── v2/
│   ├── auth.php      # Autenticación v2 (con mejoras)
│   ├── users.php     # Usuarios v2 (campos adicionales)
│   └── orders.php    # Órdenes v2 (nueva funcionalidad)
└── api.php          # Rutas principales y redirects
```

### Componentes de las APIs

1. **Versionado por URL**: `/api/v1/users`, `/api/v2/users`
2. **Controladores específicos por versión**: `V1\UserController`,
   `V2\UserController`
3. **Resources versionados**: `V1\UserResource`, `V2\UserResource`
4. **Documentación separada**: Swagger/OpenAPI por versión
5. **Middleware de validación**: Verificación de versión y formato
6. **Deprecation headers**: Notificación de versiones obsoletas

## ¿Qué debo hacer?

### 1. Configurar la Estructura de Rutas

```php
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;

// Redirect default API calls to latest version
Route::prefix('api')->group(function () {
    Route::redirect('/', '/api/v2');

    // Include versioned routes
    Route::prefix('v1')->group(base_path('routes/api/v1/api.php'));
    Route::prefix('v2')->group(base_path('routes/api/v2/api.php'));
});
```

```php
<?php
// routes/api/v1/api.php

use Illuminate\Support\Facades\Route;

Route::middleware(['api', 'api.version:v1'])->group(function () {
    require base_path('routes/api/v1/auth.php');
    require base_path('routes/api/v1/users.php');
    require base_path('routes/api/v1/orders.php');
    require base_path('routes/api/v1/products.php');
});
```

### 2. Crear Middleware de Versionado

```php
<?php
// app/Infrastructure/Http/Middleware/ApiVersionMiddleware.php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class ApiVersionMiddleware
{
    public function handle(Request $request, Closure $next, string $version): Response
    {
        // Set the API version in the request
        $request->attributes->set('api_version', $version);

        // Add version header to response
        $response = $next($request);
        $response->headers->set('API-Version', $version);

        // Add deprecation warning for old versions
        if ($this->isDeprecatedVersion($version)) {
            $response->headers->set('Warning', '299 - "API version deprecated"');
            $response->headers->set('Deprecation', 'true');
            $response->headers->set('Sunset', '2024-12-31'); // Example sunset date
        }

        return $response;
    }

    private function isDeprecatedVersion(string $version): bool
    {
        $deprecatedVersions = ['v1']; // Configure deprecated versions
        return in_array($version, $deprecatedVersions);
    }
}
```

### 3. Implementar Controladores Versionados

```php
<?php
// app/Infrastructure/Http/Controllers/Api/V1/UserController.php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\UseCases\User\CreateUserUseCase;
use App\Application\DTOs\User\CreateUserDTO;
use App\Infrastructure\Http\Controllers\Controller;
use App\Infrastructure\Http\Requests\V1\CreateUserRequest;
use App\Infrastructure\Http\Resources\V1\UserResource;
use Illuminate\Http\JsonResponse;

class UserController extends Controller
{
    public function __construct(
        private CreateUserUseCase $createUserUseCase
    ) {}

    public function store(CreateUserRequest $request): JsonResponse
    {
        $dto = CreateUserDTO::fromArray($request->validated());
        $userResponse = $this->createUserUseCase->execute($dto);

        return response()->json([
            'message' => 'User created successfully',
            'data' => new UserResource($userResponse),
            'version' => 'v1'
        ], 201);
    }

    public function show(string $id): JsonResponse
    {
        // V1 implementation - basic user data only
        $query = new GetUserQuery($id);
        $userResponse = $this->getUserUseCase->execute($query);

        return response()->json([
            'data' => new UserResource($userResponse),
            'version' => 'v1'
        ]);
    }
}
```

```php
<?php
// app/Infrastructure/Http/Controllers/Api/V2/UserController.php

namespace App\Infrastructure\Http\Controllers\Api\V2;

use App\Application\UseCases\User\CreateUserUseCase;
use App\Application\UseCases\User\GetUserWithProfileUseCase; // New use case
use App\Infrastructure\Http\Resources\V2\UserResource;

class UserController extends Controller
{
    public function show(string $id): JsonResponse
    {
        // V2 implementation - includes profile data and settings
        $query = new GetUserWithProfileQuery($id);
        $userResponse = $this->getUserWithProfileUseCase->execute($query);

        return response()->json([
            'data' => new UserResource($userResponse),
            'version' => 'v2',
            'meta' => [
                'includes' => ['profile', 'settings', 'preferences']
            ]
        ]);
    }
}
```

### 4. Crear Resources Versionados

```php
<?php
// app/Infrastructure/Http/Resources/V1/UserResource.php

namespace App\Infrastructure\Http\Resources\V1;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->resource->id,
            'name' => $this->resource->name,
            'email' => $this->resource->email,
            'created_at' => $this->resource->createdAt,
            // V1 only includes basic fields
        ];
    }
}
```

```php
<?php
// app/Infrastructure/Http/Resources/V2/UserResource.php

namespace App\Infrastructure\Http\Resources\V2;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->resource->id,
            'name' => $this->resource->name,
            'email' => $this->resource->email,
            'email_verified' => $this->resource->emailVerified,
            'created_at' => $this->resource->createdAt,
            'updated_at' => $this->resource->updatedAt,
            // V2 includes additional fields
            'profile' => $this->whenLoaded('profile', function() {
                return new ProfileResource($this->resource->profile);
            }),
            'settings' => $this->whenLoaded('settings', function() {
                return new SettingsResource($this->resource->settings);
            }),
            'api_version' => 'v2'
        ];
    }
}
```

### 5. Implementar Form Requests Versionados

```php
<?php
// app/Infrastructure/Http/Requests/V1/CreateUserRequest.php

namespace App\Infrastructure\Http\Requests\V1;

use Illuminate\Foundation\Http\FormRequest;

class CreateUserRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|string|min:8',
        ];
    }
}
```

```php
<?php
// app/Infrastructure/Http/Requests/V2/CreateUserRequest.php

namespace App\Infrastructure\Http\Requests\V2;

use Illuminate\Foundation\Http\FormRequest;

class CreateUserRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|string|min:8|confirmed', // V2 requires confirmation
            'phone' => 'nullable|string|max:20', // V2 adds phone field
            'preferences' => 'nullable|array', // V2 adds preferences
            'preferences.language' => 'nullable|string|in:en,es,fr',
            'preferences.timezone' => 'nullable|string',
        ];
    }
}
```

### 6. Configurar Rutas Específicas por Versión

```php
<?php
// routes/api/v1/users.php

use App\Infrastructure\Http\Controllers\Api\V1\UserController;
use Illuminate\Support\Facades\Route;

Route::middleware(['auth:sanctum'])->group(function () {
    Route::apiResource('users', UserController::class);

    // V1 specific routes
    Route::get('users/{user}/basic', [UserController::class, 'showBasic']);
});
```

```php
<?php
// routes/api/v2/users.php

use App\Infrastructure\Http\Controllers\Api\V2\UserController;
use Illuminate\Support\Facades\Route;

Route::middleware(['auth:sanctum'])->group(function () {
    Route::apiResource('users', UserController::class);

    // V2 specific routes
    Route::get('users/{user}/profile', [UserController::class, 'showWithProfile']);
    Route::patch('users/{user}/preferences', [UserController::class, 'updatePreferences']);
    Route::get('users/{user}/activity', [UserController::class, 'getActivity']);
});
```

### 7. Implementar Documentación por Versión

```php
<?php
// app/Infrastructure/Http/Controllers/Api/V1/DocsController.php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Infrastructure\Http\Controllers\Controller;
use Illuminate\Http\JsonResponse;

class DocsController extends Controller
{
    public function swagger(): JsonResponse
    {
        $swagger = [
            'openapi' => '3.0.0',
            'info' => [
                'title' => 'API Documentation',
                'version' => '1.0.0',
                'description' => 'API Version 1 - Basic functionality'
            ],
            'paths' => [
                '/api/v1/users' => [
                    'get' => [
                        'summary' => 'Get users list',
                        'responses' => [
                            '200' => [
                                'description' => 'Users list retrieved successfully'
                            ]
                        ]
                    ]
                ]
            ]
        ];

        return response()->json($swagger);
    }
}
```

### 8. Crear Traits para Funcionalidad Común

```php
<?php
// app/Infrastructure/Http/Traits/ApiResponseTrait.php

namespace App\Infrastructure\Http\Traits;

use Illuminate\Http\JsonResponse;

trait ApiResponseTrait
{
    protected function successResponse(
        $data = null,
        string $message = 'Success',
        int $code = 200
    ): JsonResponse {
        return response()->json([
            'success' => true,
            'message' => $message,
            'data' => $data,
            'version' => request()->attributes->get('api_version', 'unknown')
        ], $code);
    }

    protected function errorResponse(
        string $message = 'Error',
        int $code = 400,
        $errors = null
    ): JsonResponse {
        return response()->json([
            'success' => false,
            'message' => $message,
            'errors' => $errors,
            'version' => request()->attributes->get('api_version', 'unknown')
        ], $code);
    }
}
```

## Tips

### Mejores Prácticas de Versionado

1. **Estrategias de Versionado**:

   - **URL Path**: `/api/v1/users` (Recomendado)
   - **Query Parameter**: `/api/users?version=v1`
   - **Header**: `Accept: application/vnd.api+json;version=1`

2. **Política de Deprecación**:

   ```php
   // Configure in config/api.php
   'versions' => [
       'v1' => [
           'status' => 'deprecated',
           'sunset_date' => '2024-12-31',
           'migration_guide' => '/docs/migration/v1-to-v2'
       ],
       'v2' => [
           'status' => 'stable',
           'sunset_date' => null
       ]
   ]
   ```

3. **Backward Compatibility**:

   - Solo agregar campos, nunca remover
   - Mantener el mismo tipo de dato
   - Valores por defecto para nuevos campos obligatorios
   - Documentar todos los cambios

4. **Semantic Versioning**:
   - Major: Cambios incompatibles (v1 → v2)
   - Minor: Funcionalidad nueva compatible (v2.1)
   - Patch: Bug fixes (v2.1.1)

### Evitar Estos Errores

- ❌ No cambiar el formato de respuesta en la misma versión
- ❌ No remover endpoints sin deprecación previa
- ❌ No cambiar tipos de datos en campos existentes
- ❌ No mantener múltiples versiones indefinidamente
- ❌ No versionar sin documentación clara

## Ejemplos

### Endpoint con Múltiples Versiones

```php
<?php
// V1: Respuesta básica
{
    "data": {
        "id": "123",
        "name": "John Doe",
        "email": "john@example.com"
    },
    "version": "v1"
}

// V2: Respuesta extendida
{
    "data": {
        "id": "123",
        "name": "John Doe",
        "email": "john@example.com",
        "email_verified": true,
        "profile": {
            "avatar": "https://example.com/avatar.jpg",
            "bio": "Software developer"
        },
        "settings": {
            "language": "es",
            "timezone": "America/Santiago"
        }
    },
    "meta": {
        "includes": ["profile", "settings"]
    },
    "version": "v2"
}
```

### Migración Gradual de Endpoints

```php
<?php
// app/Infrastructure/Http/Controllers/Api/BaseController.php

namespace App\Infrastructure\Http\Controllers\Api;

use App\Infrastructure\Http\Controllers\Controller;
use App\Infrastructure\Http\Traits\ApiResponseTrait;

abstract class BaseController extends Controller
{
    use ApiResponseTrait;

    protected function getApiVersion(): string
    {
        return request()->attributes->get('api_version', 'v1');
    }

    protected function isVersion(string $version): bool
    {
        return $this->getApiVersion() === $version;
    }

    protected function versionedResponse($data, string $message = 'Success'): JsonResponse
    {
        $version = $this->getApiVersion();

        // Apply version-specific transformations
        if ($version === 'v1') {
            $data = $this->transformForV1($data);
        } elseif ($version === 'v2') {
            $data = $this->transformForV2($data);
        }

        return $this->successResponse($data, $message);
    }

    abstract protected function transformForV1($data);
    abstract protected function transformForV2($data);
}
```

### Testing de APIs Versionadas

```php
<?php
// tests/Feature/Api/V1/UserApiTest.php

namespace Tests\Feature\Api\V1;

use Tests\TestCase;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;

class UserApiTest extends TestCase
{
    /** @test */
    public function can_create_user_v1()
    {
        $userData = [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password123'
        ];

        $response = $this->postJson('/api/v1/users', $userData);

        $response->assertStatus(201)
                ->assertJsonStructure([
                    'message',
                    'data' => ['id', 'name', 'email', 'created_at'],
                    'version'
                ])
                ->assertJson(['version' => 'v1']);
    }

    /** @test */
    public function v1_does_not_include_profile_data()
    {
        $user = UserModel::factory()->create();

        $response = $this->getJson("/api/v1/users/{$user->id}");

        $response->assertStatus(200)
                ->assertJsonMissing(['profile', 'settings']);
    }
}
```

## Navegación

[⬅️ Implementación Infrastructure Layer](./implementacion-infrastructure-layer.md)
| [🏠 README Principal](../../README.md) |
[Autenticación Laravel Sanctum ➡️](./autenticacion-laravel-sanctum.md)
