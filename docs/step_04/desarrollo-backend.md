# Desarrollo del Backend (Laravel + Clean Architecture)

## ¿Qué es?

El desarrollo del backend es la fase donde implementamos toda la lógica de negocio y servicios del lado servidor utilizando Laravel con una arquitectura limpia basada en Domain-Driven Design (DDD). Esta etapa se centra en crear un sistema robusto, mantenible y escalable que separe claramente las responsabilidades entre las diferentes capas de la aplicación.

## ¿Por qué es importante?

- **Separación de responsabilidades**: Permite mantener las reglas de negocio independientes del framework Laravel
- **Mantenibilidad**: Facilita el mantenimiento y evolución del código a largo plazo
- **Testabilidad**: Mejora la capacidad de realizar testing unitario y de integración
- **Escalabilidad**: Permite que el sistema crezca de manera ordenada y controlada
- **Flexibilidad**: Facilita el cambio de tecnologías sin afectar las reglas de negocio
- **Consistencia**: Garantiza que todo el equipo desarrolle siguiendo los mismos patrones

## ¿Qué debe incluir?

### Estructura de Capas

```
app/
├── Domain/           # Capa de Dominio (reglas de negocio puras)
├── Application/      # Capa de Aplicación (casos de uso)
├── Infrastructure/   # Capa de Infraestructura (implementaciones técnicas)
└── Shared/          # Código compartido entre capas
```

### Componentes Principales

1. **Entidades de Dominio**: Objetos que encapsulan las reglas de negocio
2. **Value Objects**: Objetos inmutables que representan valores del dominio
3. **Repositorios**: Interfaces para la persistencia de datos
4. **Casos de Uso**: Orquestadores de la lógica de aplicación
5. **DTOs**: Objetos para transferencia de datos entre capas
6. **APIs RESTful**: Endpoints versionados y documentados
7. **Autenticación**: Sistema de seguridad con Laravel Sanctum
8. **Testing**: Suite completa de pruebas automatizadas

## ¿Qué debo hacer?

### 1. Preparación del Entorno

```bash
# Verificar instalación de Laravel
php artisan --version

# Instalar dependencias adicionales
composer require laravel/sanctum
composer require --dev phpunit/phpunit
```

### 2. Implementar las Capas en Orden

1. **Capa de Dominio** (`Domain/`)

   - Crear entidades principales
   - Definir value objects
   - Establecer interfaces de repositorios
   - Implementar servicios de dominio

2. **Capa de Aplicación** (`Application/`)

   - Desarrollar casos de uso
   - Crear DTOs para transferencia de datos
   - Implementar commands y queries
   - Definir contratos de servicios

3. **Capa de Infraestructura** (`Infrastructure/`)
   - Implementar repositorios concretos con Eloquent
   - Crear controladores API
   - Configurar middleware de seguridad
   - Establecer providers de servicios

### 3. Configurar APIs RESTful

```php
// routes/api/v1/users.php
Route::prefix('v1')->group(function () {
    Route::apiResource('users', UserController::class);
});
```

### 4. Implementar Autenticación

```php
// config/sanctum.php
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', sprintf(
    '%s%s',
    'localhost,localhost:3000,127.0.0.1,127.0.0.1:8000,::1',
    env('APP_URL') ? ','.parse_url(env('APP_URL'), PHP_URL_HOST) : ''
))),
```

### 5. Crear Suite de Testing

```bash
# Crear tests unitarios
php artisan make:test Unit/UserServiceTest --unit

# Crear tests de feature
php artisan make:test Feature/UserApiTest
```

## Tips

### Mejores Prácticas

1. **Naming Conventions**:

   - Entidades: `User`, `Order`, `Product`
   - Casos de uso: `CreateUserUseCase`, `UpdateOrderUseCase`
   - DTOs: `UserDTO`, `CreateUserDTO`
   - Repositorios: `UserRepository`, `OrderRepository`

2. **Inyección de Dependencias**:

   ```php
   // Usar constructor injection
   public function __construct(
       private UserRepository $userRepository,
       private EmailService $emailService
   ) {}
   ```

3. **Manejo de Errores**:

   ```php
   // Usar excepciones específicas del dominio
   throw new UserNotFoundException("User with ID {$id} not found");
   ```

4. **Validación de Datos**:
   ```php
   // Usar Form Requests para validación
   class CreateUserRequest extends FormRequest
   {
       public function rules(): array
       {
           return [
               'email' => 'required|email|unique:users',
               'name' => 'required|string|max:255',
           ];
       }
   }
   ```

### Evitar Estos Errores

- ❌ No usar Eloquent directamente en las capas Domain o Application
- ❌ No mezclar lógica de negocio en los controladores
- ❌ No crear dependencias circulares entre capas
- ❌ No hacer llamadas directas a servicios externos desde el dominio

## Ejemplos

### Estructura de Archivo de Entidad

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
        private ?\DateTime $emailVerifiedAt = null
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

    public function markEmailAsVerified(): void
    {
        $this->emailVerifiedAt = new \DateTime();
    }

    public function isEmailVerified(): bool
    {
        return $this->emailVerifiedAt !== null;
    }
}
```

### Estructura de Caso de Uso

```php
<?php

namespace App\Application\UseCases;

use App\Application\DTOs\CreateUserDTO;
use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;

class CreateUserUseCase
{
    public function __construct(
        private UserRepository $userRepository
    ) {}

    public function execute(CreateUserDTO $dto): User
    {
        $user = new User(
            id: UserId::generate(),
            name: $dto->name,
            email: new Email($dto->email)
        );

        return $this->userRepository->save($user);
    }
}
```

---

## Navegación

**Progreso en Desarrollo del Backend:**

- ✅ **Desarrollo del Backend** ← Estás aquí
- ⏭️ [Implementación Domain Layer](implementacion-domain-layer.md)
- ⏭️ [Implementación Application Layer](implementacion-application-layer.md)
- ⏭️ [Implementación Infrastructure Layer](implementacion-infrastructure-layer.md)
- ⏭️ [APIs RESTful Versionadas](apis-restful-versionadas.md)
- ⏭️ [Autenticación Laravel Sanctum](autenticacion-laravel-sanctum.md)
- ⏭️ [Testing TDD Backend](testing-tdd-backend.md)

---

### Siguiente Paso

Continúa con
[**Configuración de repositorios Git y GitHub**](./configuracion-repositorios-git.md).

[⬅️ Etapa 3: Configuración de React + TypeScript + Vite](../step_03/configuracion-react-typescript-vite.md)
| [🏠 README Principal](../../README.md) |
[➡️ Implementación Domain Layer](implementacion-domain-layer.md)
