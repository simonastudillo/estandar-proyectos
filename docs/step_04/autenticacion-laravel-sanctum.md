# Autenticación Laravel Sanctum

## ¿Qué es?

Laravel Sanctum es el sistema de autenticación oficial de Laravel para APIs, que
proporciona una solución simple y liviana para autenticar SPAs (Single Page
Applications), aplicaciones móviles y APIs simples mediante tokens. Sanctum
ofrece tanto autenticación basada en sesiones para SPAs como tokens de API para
aplicaciones móviles y servicios de terceros.

## ¿Por qué es importante?

- **Simplicidad**: Más simple que Laravel Passport para casos de uso básicos
- **Flexibilidad**: Soporta tanto autenticación por sesión como por tokens
- **Seguridad**: Implementa mejores prácticas de seguridad por defecto
- **Performance**: Menor overhead que OAuth2 para APIs simples
- **Integración**: Se integra perfectamente con el ecosistema Laravel
- **Escalabilidad**: Funciona bien tanto para aplicaciones pequeñas como grandes

## ¿Qué debe incluir?

### Componentes de Autenticación

1. **Configuración de Sanctum**: Instalación y configuración inicial
2. **Modelos de Usuario**: Preparación del modelo para Sanctum
3. **Middleware de Autenticación**: Protección de rutas API
4. **Endpoints de Autenticación**: Login, logout, registro, refresh
5. **Abilities y Permisos**: Control granular de acceso
6. **Rate Limiting**: Protección contra ataques de fuerza bruta
7. **Token Management**: Creación, renovación y revocación de tokens

### Flujos de Autenticación

```
1. SPA Authentication Flow:
   Client → POST /sanctum/csrf-cookie → Get CSRF Token
   Client → POST /api/login → Get Session Cookie
   Client → API Requests with Session Cookie

2. Mobile App Token Flow:
   Client → POST /api/login → Get API Token
   Client → API Requests with Bearer Token
   Client → POST /api/logout → Revoke Token
```

## ¿Qué debo hacer?

### 1. Instalar y Configurar Sanctum

```bash
# Install Sanctum
composer require laravel/sanctum

# Publish configuration
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"

# Run migrations
php artisan migrate
```

```php
<?php
// config/sanctum.php

return [
    'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', sprintf(
        '%s%s',
        'localhost,localhost:3000,127.0.0.1,127.0.0.1:8000,::1',
        env('APP_URL') ? ','.parse_url(env('APP_URL'), PHP_URL_HOST) : ''
    ))),

    'guard' => ['web'],

    'expiration' => null, // Tokens never expire by default

    'middleware' => [
        'verify_csrf_token' => App\Http\Middleware\VerifyCsrfToken::class,
        'encrypt_cookies' => App\Http\Middleware\EncryptCookies::class,
    ],
];
```

### 2. Preparar el Modelo de Usuario

```php
<?php
// app/Infrastructure/Persistence/Eloquent/Models/UserModel.php

namespace App\Infrastructure\Persistence\Eloquent\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class UserModel extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;

    protected $table = 'users';

    protected $fillable = [
        'id',
        'name',
        'email',
        'password',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed', // Laravel 10+
    ];

    public $incrementing = false;
    protected $keyType = 'string';
}
```

### 3. Crear Controlador de Autenticación

```php
<?php
// app/Infrastructure/Http/Controllers/Api/V1/AuthController.php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\UseCases\Auth\LoginUseCase;
use App\Application\UseCases\Auth\RegisterUseCase;
use App\Application\UseCases\Auth\LogoutUseCase;
use App\Application\DTOs\Auth\LoginDTO;
use App\Application\DTOs\Auth\RegisterDTO;
use App\Infrastructure\Http\Controllers\Controller;
use App\Infrastructure\Http\Requests\V1\LoginRequest;
use App\Infrastructure\Http\Requests\V1\RegisterRequest;
use App\Infrastructure\Http\Resources\V1\UserResource;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class AuthController extends Controller
{
    public function __construct(
        private LoginUseCase $loginUseCase,
        private RegisterUseCase $registerUseCase,
        private LogoutUseCase $logoutUseCase
    ) {}

    public function register(RegisterRequest $request): JsonResponse
    {
        try {
            $dto = RegisterDTO::fromArray($request->validated());
            $result = $this->registerUseCase->execute($dto);

            return response()->json([
                'message' => 'User registered successfully',
                'data' => [
                    'user' => new UserResource($result->user),
                    'token' => $result->token,
                    'token_type' => 'Bearer'
                ]
            ], 201);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'Registration failed',
                'error' => $e->getMessage()
            ], 400);
        }
    }

    public function login(LoginRequest $request): JsonResponse
    {
        try {
            $dto = LoginDTO::fromArray($request->validated());
            $result = $this->loginUseCase->execute($dto);

            return response()->json([
                'message' => 'Login successful',
                'data' => [
                    'user' => new UserResource($result->user),
                    'token' => $result->token,
                    'token_type' => 'Bearer',
                    'expires_at' => $result->expiresAt
                ]
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'Login failed',
                'error' => 'Invalid credentials'
            ], 401);
        }
    }

    public function logout(Request $request): JsonResponse
    {
        try {
            $this->logoutUseCase->execute($request->user());

            return response()->json([
                'message' => 'Logout successful'
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'Logout failed',
                'error' => $e->getMessage()
            ], 400);
        }
    }

    public function me(Request $request): JsonResponse
    {
        return response()->json([
            'data' => new UserResource($request->user())
        ]);
    }

    public function refreshToken(Request $request): JsonResponse
    {
        try {
            // Revoke current token
            $request->user()->currentAccessToken()->delete();

            // Create new token
            $token = $request->user()->createToken(
                'api-token',
                ['*'],
                now()->addDays(7)
            );

            return response()->json([
                'message' => 'Token refreshed successfully',
                'data' => [
                    'token' => $token->plainTextToken,
                    'token_type' => 'Bearer',
                    'expires_at' => $token->accessToken->expires_at
                ]
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'Token refresh failed',
                'error' => $e->getMessage()
            ], 400);
        }
    }
}
```

### 4. Implementar Casos de Uso de Autenticación

```php
<?php
// app/Application/UseCases/Auth/LoginUseCase.php

namespace App\Application\UseCases\Auth;

use App\Application\DTOs\Auth\LoginDTO;
use App\Application\DTOs\Auth\AuthResponseDTO;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\Email;
use App\Domain\Exceptions\InvalidCredentialsException;
use Illuminate\Support\Facades\Hash;

class LoginUseCase
{
    public function __construct(
        private UserRepository $userRepository
    ) {}

    public function execute(LoginDTO $dto): AuthResponseDTO
    {
        $email = new Email($dto->email);
        $user = $this->userRepository->findByEmail($email);

        if (!$user || !Hash::check($dto->password, $user->getPassword())) {
            throw new InvalidCredentialsException('Invalid email or password');
        }

        // Create token with abilities
        $abilities = $this->getUserAbilities($user);
        $expiresAt = now()->addDays(7);

        $token = $user->createToken(
            name: $dto->deviceName ?? 'api-token',
            abilities: $abilities,
            expiresAt: $expiresAt
        );

        return new AuthResponseDTO(
            user: $user,
            token: $token->plainTextToken,
            expiresAt: $expiresAt->toISOString()
        );
    }

    private function getUserAbilities($user): array
    {
        // Define abilities based on user role or permissions
        $abilities = ['*']; // Default: all abilities

        // Example role-based abilities
        if ($user->hasRole('admin')) {
            $abilities = ['*'];
        } elseif ($user->hasRole('user')) {
            $abilities = ['read', 'update-profile'];
        }

        return $abilities;
    }
}
```

```php
<?php
// app/Application/UseCases/Auth/RegisterUseCase.php

namespace App\Application\UseCases\Auth;

use App\Application\DTOs\Auth\RegisterDTO;
use App\Application\DTOs\Auth\AuthResponseDTO;
use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserAlreadyExistsException;
use Illuminate\Support\Facades\Hash;

class RegisterUseCase
{
    public function __construct(
        private UserRepository $userRepository
    ) {}

    public function execute(RegisterDTO $dto): AuthResponseDTO
    {
        $email = new Email($dto->email);

        if ($this->userRepository->existsByEmail($email)) {
            throw new UserAlreadyExistsException('User already exists with this email');
        }

        // Create user
        $user = new User(
            id: UserId::generate(),
            name: $dto->name,
            email: $email,
            password: Hash::make($dto->password)
        );

        $savedUser = $this->userRepository->save($user);

        // Create token
        $expiresAt = now()->addDays(7);
        $token = $savedUser->createToken(
            name: 'registration-token',
            abilities: ['read', 'update-profile'],
            expiresAt: $expiresAt
        );

        return new AuthResponseDTO(
            user: $savedUser,
            token: $token->plainTextToken,
            expiresAt: $expiresAt->toISOString()
        );
    }
}
```

### 5. Crear DTOs para Autenticación

```php
<?php
// app/Application/DTOs/Auth/LoginDTO.php

namespace App\Application\DTOs\Auth;

class LoginDTO
{
    public function __construct(
        public readonly string $email,
        public readonly string $password,
        public readonly ?string $deviceName = null
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            email: $data['email'],
            password: $data['password'],
            deviceName: $data['device_name'] ?? null
        );
    }
}
```

```php
<?php
// app/Application/DTOs/Auth/AuthResponseDTO.php

namespace App\Application\DTOs\Auth;

use App\Domain\Entities\User;

class AuthResponseDTO
{
    public function __construct(
        public readonly User $user,
        public readonly string $token,
        public readonly string $expiresAt
    ) {}
}
```

### 6. Implementar Form Requests

```php
<?php
// app/Infrastructure/Http/Requests/V1/LoginRequest.php

namespace App\Infrastructure\Http\Requests\V1;

use Illuminate\Foundation\Http\FormRequest;

class LoginRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'email' => 'required|email',
            'password' => 'required|string|min:8',
            'device_name' => 'nullable|string|max:255',
        ];
    }

    public function messages(): array
    {
        return [
            'email.required' => 'El email es obligatorio',
            'email.email' => 'El email debe tener un formato válido',
            'password.required' => 'La contraseña es obligatoria',
            'password.min' => 'La contraseña debe tener al menos 8 caracteres',
        ];
    }
}
```

### 7. Configurar Rutas de Autenticación

```php
<?php
// routes/api/v1/auth.php

use App\Infrastructure\Http\Controllers\Api\V1\AuthController;
use Illuminate\Support\Facades\Route;

// Public routes
Route::post('register', [AuthController::class, 'register']);
Route::post('login', [AuthController::class, 'login']);

// Protected routes
Route::middleware('auth:sanctum')->group(function () {
    Route::post('logout', [AuthController::class, 'logout']);
    Route::get('me', [AuthController::class, 'me']);
    Route::post('refresh-token', [AuthController::class, 'refreshToken']);
});
```

### 8. Implementar Middleware Personalizado

```php
<?php
// app/Infrastructure/Http/Middleware/EnsureTokenHasAbility.php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

class EnsureTokenHasAbility
{
    public function handle(Request $request, Closure $next, string $ability): Response
    {
        if (EnsureFrontendRequestsAreStateful::fromFrontend($request)) {
            return $next($request);
        }

        if (!$request->user() || !$request->user()->tokenCan($ability)) {
            return response()->json([
                'message' => 'Insufficient permissions',
                'required_ability' => $ability
            ], 403);
        }

        return $next($request);
    }
}
```

### 9. Configurar Rate Limiting

```php
<?php
// app/Providers/RouteServiceProvider.php

use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

public function boot(): void
{
    // Configure rate limiting for auth endpoints
    RateLimiter::for('auth', function (Request $request) {
        return Limit::perMinute(5)->by($request->ip());
    });

    RateLimiter::for('api', function (Request $request) {
        return Limit::perMinute(60)->by(optional($request->user())->id ?: $request->ip());
    });
}
```

```php
<?php
// Apply rate limiting to routes
Route::middleware(['throttle:auth'])->group(function () {
    Route::post('login', [AuthController::class, 'login']);
    Route::post('register', [AuthController::class, 'register']);
});
```

## Tips

### Mejores Prácticas de Seguridad

1. **Token Management**:

   ```php
   // Always set expiration for tokens
   $token = $user->createToken('api-token', ['*'], now()->addDays(7));

   // Revoke specific tokens
   $user->tokens()->where('name', 'old-token')->delete();

   // Revoke all tokens
   $user->tokens()->delete();
   ```

2. **Abilities y Permisos**:

   ```php
   // Check token abilities
   if ($request->user()->tokenCan('admin')) {
       // Admin functionality
   }

   // Create tokens with specific abilities
   $token = $user->createToken('limited-token', ['read', 'write']);
   ```

3. **CORS Configuration**:
   ```php
   // config/cors.php
   'paths' => ['api/*', 'sanctum/csrf-cookie'],
   'allowed_methods' => ['*'],
   'allowed_origins' => [env('FRONTEND_URL', 'http://localhost:3000')],
   'allowed_headers' => ['*'],
   'supports_credentials' => true,
   ```

### Configuración para SPAs

```javascript
// Frontend SPA configuration
axios.defaults.withCredentials = true;
axios.defaults.baseURL = "http://localhost:8000";

// Get CSRF cookie first
await axios.get("/sanctum/csrf-cookie");

// Then make authenticated requests
const response = await axios.post("/api/login", credentials);
```

### Evitar Estos Errores

- ❌ No configurar correctamente SANCTUM_STATEFUL_DOMAINS
- ❌ No usar HTTPS en producción
- ❌ No establecer expiración para tokens de larga duración
- ❌ No revocar tokens al hacer logout
- ❌ No implementar rate limiting en endpoints de autenticación

## Ejemplos

### Autenticación con Roles y Permisos

```php
<?php
// app/Infrastructure/Http/Middleware/CheckRole.php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckRole
{
    public function handle(Request $request, Closure $next, string $role): Response
    {
        if (!$request->user() || !$request->user()->hasRole($role)) {
            return response()->json([
                'message' => 'Access denied. Required role: ' . $role
            ], 403);
        }

        return $next($request);
    }
}
```

### Token con Metadata

```php
<?php
// Create token with additional metadata
$token = $user->createToken('api-token', ['*'], now()->addDays(7), [
    'device_type' => 'mobile',
    'app_version' => '1.0.0',
    'platform' => 'ios'
]);

// Access token metadata
$tokenModel = $request->user()->currentAccessToken();
$metadata = $tokenModel->metadata;
```

### Logout de Todos los Dispositivos

```php
<?php
public function logoutAllDevices(Request $request): JsonResponse
{
    // Revoke all user tokens except current
    $currentToken = $request->user()->currentAccessToken();
    $request->user()->tokens()->where('id', '!=', $currentToken->id)->delete();

    return response()->json([
        'message' => 'Logged out from all other devices'
    ]);
}
```

### Testing de Autenticación

```php
<?php
// tests/Feature/AuthTest.php

namespace Tests\Feature;

use Tests\TestCase;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;

class AuthTest extends TestCase
{
    /** @test */
    public function user_can_register_and_get_token()
    {
        $userData = [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        $response = $this->postJson('/api/v1/register', $userData);

        $response->assertStatus(201)
                ->assertJsonStructure([
                    'data' => ['user', 'token', 'token_type']
                ]);
    }

    /** @test */
    public function user_can_login_with_valid_credentials()
    {
        $user = UserModel::factory()->create([
            'password' => bcrypt('password123')
        ]);

        $credentials = [
            'email' => $user->email,
            'password' => 'password123'
        ];

        $response = $this->postJson('/api/v1/login', $credentials);

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'data' => ['user', 'token']
                ]);
    }

    /** @test */
    public function authenticated_user_can_access_protected_routes()
    {
        $user = UserModel::factory()->create();
        $token = $user->createToken('test-token')->plainTextToken;

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token
        ])->getJson('/api/v1/me');

        $response->assertStatus(200);
    }
}
```

## Navegación

[⬅️ Validaciones de Seguridad](./validaciones-seguridad.md) |
[🏠 README Principal](../../README.md) |
[Migraciones y Seeders ➡️](./migraciones-seeders.md)
