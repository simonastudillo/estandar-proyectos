# Setup de Laravel con Clean Architecture

## ¿Qué es?

El setup de Laravel con Clean Architecture es la configuración e implementación
de un backend Laravel siguiendo los principios de Clean Architecture y
Domain-Driven Design (DDD). Esta arquitectura separa completamente las reglas de
negocio del framework, creando un código más mantenible, testeable y escalable.

## ¿Por qué es importante?

- **Separación de responsabilidades**: Las reglas de negocio están completamente
  desacopladas del framework Laravel
- **Testabilidad**: Permite testing unitario sin dependencias externas
- **Escalabilidad**: Facilita el crecimiento y mantenimiento del proyecto
- **Flexibilidad**: Permite cambiar implementaciones sin afectar la lógica de
  negocio
- **Claridad**: La arquitectura es explícita y fácil de entender para nuevos
  desarrolladores

## ¿Qué debe incluir?

### 1. Estructura de carpetas

- **Domain**: Entidades, Value Objects, Repositorios (interfaces), Servicios de
  dominio
- **Application**: Casos de uso, DTOs, Commands, Queries
- **Infrastructure**: Implementaciones concretas (Eloquent, HTTP, Console)
- **Shared**: Código compartido entre capas

### 2. Configuración base

- Service Providers para inyección de dependencias
- Binding de interfaces con implementaciones
- Configuración de rutas versionadas
- Middlewares personalizados

### 3. Convenciones establecidas

- Nomenclatura consistente
- Tipado fuerte en PHP 8+
- Validaciones con Form Requests
- Respuestas API estandarizadas

## ¿Qué debo hacer?

### Paso 1: Instalación de Laravel

```bash
# Crear nuevo proyecto Laravel
composer create-project laravel/laravel backend

# Instalar dependencias adicionales
cd backend
composer require --dev phpunit/phpunit
composer require --dev pestphp/pest
composer require --dev pestphp/pest-plugin-laravel
```

### Paso 2: Configurar estructura de carpetas

Crear la estructura completa en `app/`:

```bash
mkdir -p app/Domain/{Entities,ValueObjects,Repositories,Services,Events,Exceptions}
mkdir -p app/Application/{UseCases,DTOs,Commands,Queries}
mkdir -p app/Infrastructure/{Persistence/Eloquent,Http/{Controllers/Api/V1,Requests,Resources,Middleware},Events,Console,Providers}
mkdir -p app/Shared/{Utils,Traits}
```

### Paso 3: Configurar Service Providers

Crear `app/Infrastructure/Providers/DomainServiceProvider.php`:

```php
<?php

namespace App\Infrastructure\Providers;

use Illuminate\Support\ServiceProvider;

class DomainServiceProvider extends ServiceProvider
{
    public function register()
    {
        // Bind repositories
        $this->app->bind(
            \App\Domain\Repositories\UserRepositoryInterface::class,
            \App\Infrastructure\Persistence\Eloquent\UserRepository::class
        );
    }

    public function boot()
    {
        //
    }
}
```

### Paso 4: Configurar rutas versionadas

Crear estructura de rutas:

```bash
mkdir -p routes/api/{v1,v2}
```

Configurar en `routes/api.php`:

```php
<?php

Route::prefix('v1')->group(function () {
    require __DIR__ . '/api/v1/users.php';
    require __DIR__ . '/api/v1/auth.php';
});
```

### Paso 5: Crear ejemplos base

#### Entidad de Dominio

`app/Domain/Entities/User.php`:

```php
<?php

namespace App\Domain\Entities;

use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;

class User
{
    public function __construct(
        private UserId $id,
        private string $name,
        private Email $email,
        private \DateTimeImmutable $createdAt
    ) {}

    public function getId(): UserId
    {
        return $this->id;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function getEmail(): Email
    {
        return $this->email;
    }

    public function changeEmail(Email $newEmail): void
    {
        $this->email = $newEmail;
    }
}
```

#### Value Object

`app/Domain/ValueObjects/Email.php`:

```php
<?php

namespace App\Domain\ValueObjects;

use App\Domain\Exceptions\InvalidEmailException;

final class Email
{
    private string $value;

    public function __construct(string $email)
    {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidEmailException("Invalid email format: {$email}");
        }

        $this->value = $email;
    }

    public function getValue(): string
    {
        return $this->value;
    }

    public function equals(Email $other): bool
    {
        return $this->value === $other->value;
    }
}
```

#### Repository Interface

`app/Domain/Repositories/UserRepositoryInterface.php`:

```php
<?php

namespace App\Domain\Repositories;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\UserId;

interface UserRepositoryInterface
{
    public function findById(UserId $id): ?User;
    public function save(User $user): void;
    public function delete(UserId $id): void;
}
```

#### Caso de Uso

`app/Application/UseCases/CreateUserUseCase.php`:

```php
<?php

namespace App\Application\UseCases;

use App\Application\DTOs\UserDTO;
use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepositoryInterface;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;

class CreateUserUseCase
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function execute(UserDTO $userDTO): User
    {
        $user = new User(
            UserId::generate(),
            $userDTO->name,
            new Email($userDTO->email),
            new \DateTimeImmutable()
        );

        $this->userRepository->save($user);

        return $user;
    }
}
```

#### DTO

`app/Application/DTOs/UserDTO.php`:

```php
<?php

namespace App\Application\DTOs;

class UserDTO
{
    public function __construct(
        public readonly string $name,
        public readonly string $email
    ) {}
}
```

#### Controlador

`app/Infrastructure/Http/Controllers/Api/V1/UserController.php`:

```php
<?php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\DTOs\UserDTO;
use App\Application\UseCases\CreateUserUseCase;
use App\Infrastructure\Http\Controllers\Controller;
use App\Infrastructure\Http\Requests\CreateUserRequest;
use App\Infrastructure\Http\Resources\UserResource;
use Illuminate\Http\JsonResponse;

class UserController extends Controller
{
    public function __construct(
        private CreateUserUseCase $createUserUseCase
    ) {}

    public function store(CreateUserRequest $request): JsonResponse
    {
        $userDTO = new UserDTO(
            name: $request->validated('name'),
            email: $request->validated('email')
        );

        $user = $this->createUserUseCase->execute($userDTO);

        return response()->json(
            new UserResource($user),
            201
        );
    }
}
```

## Tips

1. **Mantén las capas separadas**: Nunca uses Eloquent en Domain o Application
2. **Usa inyección de dependencias**: Evita llamadas estáticas y new directos
3. **Valida en el borde**: Las validaciones van en Form Requests
4. **Exceptions específicas**: Crea excepciones de dominio específicas
5. **Tests unitarios**: Testea los casos de uso independientemente del framework
6. **UUIDs públicos**: Usa UUIDs para identificadores públicos, IDs
   incrementales internamente

## Ejemplos

### Configuración completa en AppServiceProvider

```php
public function register()
{
    // Registrar Service Provider de dominio
    $this->app->register(DomainServiceProvider::class);
}
```

### Middleware personalizado para API

```php
<?php

namespace App\Infrastructure\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class ApiVersionMiddleware
{
    public function handle(Request $request, Closure $next, string $version)
    {
        $request->headers->set('API-Version', $version);
        return $next($request);
    }
}
```

### Comando de consola

```php
<?php

namespace App\Infrastructure\Console\Commands;

use App\Application\UseCases\SyncUsersUseCase;
use Illuminate\Console\Command;

class SyncUsersCommand extends Command
{
    protected $signature = 'users:sync';
    protected $description = 'Synchronize users data';

    public function __construct(
        private SyncUsersUseCase $syncUsersUseCase
    ) {
        parent::__construct();
    }

    public function handle(): int
    {
        $this->info('Starting user synchronization...');

        $result = $this->syncUsersUseCase->execute();

        $this->info("Synchronized {$result->count} users");

        return 0;
    }
}
```

---

## Navegación

**Progreso en Configuración del Entorno de Desarrollo:**

- ✅
  [Configuración del Entorno - Introducción](./configuracion-entorno-desarrollo.md)
- ✅ [Requisitos mínimos del sistema](./requisitos-sistema.md)
- ✅
  [Configuración de repositorios Git y GitHub](./configuracion-repositorios-git.md)
- ✅
  [Setup de entornos de desarrollo local con Docker](./setup-entorno-local-docker.md)
- ✅
  [Configuración de herramientas de CI/CD con GitHub Actions](./configuracion-cicd-github-actions.md)
- ✅
  [Setup de ambientes (desarrollo, testing, staging, producción)](./setup-ambientes-multiples.md)
- ✅
  [Configuración de herramientas de calidad](./configuracion-herramientas-calidad.md)
- ⏭️ **Setup de Laravel con Clean Architecture** ← Estás aquí
- ⏭️
  [Configuración de React + TypeScript + Vite](./configuracion-react-typescript-vite.md)

---

### Siguiente Paso

Continúa con
[**Configuración de React + TypeScript + Vite**](./configuracion-react-typescript-vite.md)

[⬅️ Configuración de herramientas de calidad](./configuracion-herramientas-calidad.md)
| [🏠 README Principal](../../README.md) |
[➡️ Configuración de React + TypeScript + Vite](./configuracion-react-typescript-vite.md)
