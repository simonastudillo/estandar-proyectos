# Testing TDD Backend

## ¿Qué es?

Test-Driven Development (TDD) para backend es una metodología de desarrollo donde se escriben las pruebas antes que el código de implementación. En el contexto de Laravel con Clean Architecture, esto significa crear tests para cada capa (Domain, Application, Infrastructure) antes de implementar la funcionalidad, asegurando que el código cumple con los requisitos y mantiene la calidad a lo largo del tiempo.

## ¿Por qué es importante?

- **Calidad del código**: Garantiza que cada funcionalidad está correctamente implementada
- **Refactoring seguro**: Permite cambios en el código con confianza
- **Documentación viva**: Los tests sirven como documentación del comportamiento esperado
- **Detección temprana de errores**: Identifica problemas antes de que lleguen a producción
- **Diseño mejorado**: Fuerza a pensar en el diseño de APIs y interfaces
- **Cobertura completa**: Asegura que todas las capas están probadas

## ¿Qué debe incluir?

### Tipos de Testing por Capa

```
tests/
├── Unit/              # Tests unitarios para lógica de negocio
│   ├── Domain/
│   │   ├── Entities/
│   │   ├── ValueObjects/
│   │   └── Services/
│   └── Application/
│       ├── UseCases/
│       └── DTOs/
├── Integration/       # Tests de integración entre capas
│   ├── Repositories/
│   ├── Services/
│   └── UseCases/
├── Feature/          # Tests de funcionalidades completas
│   ├── Api/
│   │   ├── V1/
│   │   └── V2/
│   ├── Auth/
│   └── Commands/
└── Performance/      # Tests de rendimiento
    ├── LoadTests/
    └── StressTests/
```

### Componentes de Testing

1. **Unit Tests**: Lógica de negocio pura
2. **Integration Tests**: Interacciones entre componentes
3. **Feature Tests**: Funcionalidades end-to-end
4. **Database Tests**: Operaciones de persistencia
5. **API Tests**: Endpoints HTTP
6. **Authentication Tests**: Flujos de autenticación
7. **Performance Tests**: Rendimiento y carga

## ¿Qué debo hacer?

### 1. Configurar el Entorno de Testing

```php
<?php
// phpunit.xml

<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
        <testsuite name="Integration">
            <directory suffix="Test.php">./tests/Integration</directory>
        </testsuite>
    </testsuites>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./app</directory>
        </include>
        <exclude>
            <directory suffix=".php">./app/Infrastructure/Persistence/Eloquent/Models</directory>
        </exclude>
    </coverage>
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="DB_CONNECTION" value="sqlite"/>
        <env name="DB_DATABASE" value=":memory:"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="SESSION_DRIVER" value="array"/>
        <env name="QUEUE_CONNECTION" value="sync"/>
    </php>
</phpunit>
```

### 2. Tests Unitarios para Domain Layer

```php
<?php
// tests/Unit/Domain/Entities/UserTest.php

namespace Tests\Unit\Domain\Entities;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase
{
    /** @test */
    public function can_create_user_with_valid_data()
    {
        $userId = UserId::generate();
        $email = new Email('john@example.com');
        $name = 'John Doe';

        $user = new User($userId, $name, $email);

        $this->assertEquals($userId, $user->getId());
        $this->assertEquals($name, $user->getName());
        $this->assertEquals($email, $user->getEmail());
        $this->assertFalse($user->isEmailVerified());
    }

    /** @test */
    public function can_change_user_name()
    {
        $user = $this->createUser();
        $newName = 'Jane Doe';

        $user->changeName($newName);

        $this->assertEquals($newName, $user->getName());
    }

    /** @test */
    public function cannot_change_name_to_empty_string()
    {
        $user = $this->createUser();

        $this->expectException(\InvalidArgumentException::class);
        $this->expectExceptionMessage('Name cannot be empty');

        $user->changeName('');
    }

    /** @test */
    public function can_verify_email()
    {
        $user = $this->createUser();

        $user->markEmailAsVerified();

        $this->assertTrue($user->isEmailVerified());
    }

    /** @test */
    public function changing_email_resets_verification()
    {
        $user = $this->createUser();
        $user->markEmailAsVerified();
        $newEmail = new Email('newemail@example.com');

        $user->changeEmail($newEmail);

        $this->assertEquals($newEmail, $user->getEmail());
        $this->assertFalse($user->isEmailVerified());
    }

    private function createUser(): User
    {
        return new User(
            UserId::generate(),
            'John Doe',
            new Email('john@example.com')
        );
    }
}
```

```php
<?php
// tests/Unit/Domain/ValueObjects/EmailTest.php

namespace Tests\Unit\Domain\ValueObjects;

use App\Domain\ValueObjects\Email;
use App\Domain\Exceptions\InvalidEmailException;
use PHPUnit\Framework\TestCase;

class EmailTest extends TestCase
{
    /** @test */
    public function can_create_email_with_valid_format()
    {
        $emailString = 'test@example.com';

        $email = new Email($emailString);

        $this->assertEquals($emailString, $email->getValue());
    }

    /** @test */
    public function normalizes_email_to_lowercase()
    {
        $email = new Email('TEST@EXAMPLE.COM');

        $this->assertEquals('test@example.com', $email->getValue());
    }

    /** @test */
    public function throws_exception_for_invalid_email()
    {
        $this->expectException(InvalidEmailException::class);

        new Email('invalid-email');
    }

    /** @test */
    public function can_compare_emails_for_equality()
    {
        $email1 = new Email('test@example.com');
        $email2 = new Email('TEST@EXAMPLE.COM');
        $email3 = new Email('other@example.com');

        $this->assertTrue($email1->equals($email2));
        $this->assertFalse($email1->equals($email3));
    }
}
```

### 3. Tests Unitarios para Application Layer

```php
<?php
// tests/Unit/Application/UseCases/CreateUserUseCaseTest.php

namespace Tests\Unit\Application\UseCases;

use App\Application\UseCases\User\CreateUserUseCase;
use App\Application\DTOs\User\CreateUserDTO;
use App\Application\Contracts\EmailServiceContract;
use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use App\Domain\Services\UserService;
use App\Domain\ValueObjects\Email;
use App\Domain\Exceptions\UserAlreadyExistsException;
use PHPUnit\Framework\TestCase;
use Mockery;

class CreateUserUseCaseTest extends TestCase
{
    private UserRepository $userRepository;
    private UserService $userService;
    private EmailServiceContract $emailService;
    private CreateUserUseCase $useCase;

    protected function setUp(): void
    {
        parent::setUp();

        $this->userRepository = Mockery::mock(UserRepository::class);
        $this->userService = Mockery::mock(UserService::class);
        $this->emailService = Mockery::mock(EmailServiceContract::class);

        $this->useCase = new CreateUserUseCase(
            $this->userRepository,
            $this->userService,
            $this->emailService
        );
    }

    /** @test */
    public function can_create_user_with_valid_data()
    {
        $dto = new CreateUserDTO('John Doe', 'john@example.com', 'password123');
        $user = $this->createMockUser();

        $this->userService->shouldReceive('isEmailAvailable')
            ->with('john@example.com')
            ->once()
            ->andReturn(true);

        $this->userRepository->shouldReceive('save')
            ->once()
            ->andReturn($user);

        $this->emailService->shouldReceive('sendWelcomeEmail')
            ->with('john@example.com')
            ->once();

        $result = $this->useCase->execute($dto);

        $this->assertInstanceOf(\App\Application\DTOs\User\UserResponseDTO::class, $result);
        $this->assertEquals('john@example.com', $result->email);
    }

    /** @test */
    public function throws_exception_when_email_already_exists()
    {
        $dto = new CreateUserDTO('John Doe', 'existing@example.com', 'password123');

        $this->userService->shouldReceive('isEmailAvailable')
            ->with('existing@example.com')
            ->once()
            ->andReturn(false);

        $this->expectException(UserAlreadyExistsException::class);

        $this->useCase->execute($dto);
    }

    private function createMockUser(): User
    {
        return Mockery::mock(User::class, [
            'getId->getValue' => 'user-id',
            'getName' => 'John Doe',
            'getEmail->getValue' => 'john@example.com',
            'isEmailVerified' => false,
            'getCreatedAt->format' => '2024-01-01 12:00:00'
        ]);
    }

    protected function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }
}
```

### 4. Tests de Integración para Repositories

```php
<?php
// tests/Integration/Repositories/EloquentUserRepositoryTest.php

namespace Tests\Integration\Repositories;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use App\Infrastructure\Persistence\Eloquent\Repositories\EloquentUserRepository;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class EloquentUserRepositoryTest extends TestCase
{
    use RefreshDatabase;

    private EloquentUserRepository $repository;

    protected function setUp(): void
    {
        parent::setUp();
        $this->repository = new EloquentUserRepository();
    }

    /** @test */
    public function can_save_and_retrieve_user()
    {
        $user = new User(
            UserId::generate(),
            'John Doe',
            new Email('john@example.com')
        );

        $savedUser = $this->repository->save($user);

        $this->assertEquals($user->getId()->getValue(), $savedUser->getId()->getValue());
        $this->assertEquals($user->getName(), $savedUser->getName());
        $this->assertEquals($user->getEmail()->getValue(), $savedUser->getEmail()->getValue());

        // Verify it was actually saved to database
        $this->assertDatabaseHas('users', [
            'id' => $user->getId()->getValue(),
            'email' => 'john@example.com'
        ]);
    }

    /** @test */
    public function can_find_user_by_id()
    {
        $userId = UserId::generate();
        UserModel::factory()->create([
            'id' => $userId->getValue(),
            'name' => 'John Doe',
            'email' => 'john@example.com'
        ]);

        $user = $this->repository->findById($userId);

        $this->assertNotNull($user);
        $this->assertEquals($userId->getValue(), $user->getId()->getValue());
        $this->assertEquals('John Doe', $user->getName());
    }

    /** @test */
    public function returns_null_when_user_not_found()
    {
        $userId = UserId::generate();

        $user = $this->repository->findById($userId);

        $this->assertNull($user);
    }

    /** @test */
    public function can_find_user_by_email()
    {
        $email = new Email('john@example.com');
        UserModel::factory()->create([
            'id' => UserId::generate()->getValue(),
            'email' => 'john@example.com'
        ]);

        $user = $this->repository->findByEmail($email);

        $this->assertNotNull($user);
        $this->assertEquals('john@example.com', $user->getEmail()->getValue());
    }

    /** @test */
    public function can_check_if_email_exists()
    {
        $email = new Email('existing@example.com');
        UserModel::factory()->create([
            'email' => 'existing@example.com'
        ]);

        $exists = $this->repository->existsByEmail($email);

        $this->assertTrue($exists);
    }

    /** @test */
    public function can_delete_user()
    {
        $userId = UserId::generate();
        UserModel::factory()->create([
            'id' => $userId->getValue()
        ]);

        $this->repository->delete($userId);

        $this->assertDatabaseMissing('users', [
            'id' => $userId->getValue(),
            'deleted_at' => null
        ]);
    }
}
```

### 5. Tests Feature para APIs

```php
<?php
// tests/Feature/Api/V1/UserApiTest.php

namespace Tests\Feature\Api\V1;

use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Laravel\Sanctum\Sanctum;
use Tests\TestCase;

class UserApiTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function can_register_new_user()
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
                    'message',
                    'data' => [
                        'user' => ['id', 'name', 'email'],
                        'token',
                        'token_type'
                    ]
                ]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com'
        ]);
    }

    /** @test */
    public function cannot_register_with_existing_email()
    {
        UserModel::factory()->create(['email' => 'existing@example.com']);

        $userData = [
            'name' => 'John Doe',
            'email' => 'existing@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        $response = $this->postJson('/api/v1/register', $userData);

        $response->assertStatus(422)
                ->assertJsonValidationErrors(['email']);
    }

    /** @test */
    public function authenticated_user_can_get_their_profile()
    {
        $user = UserModel::factory()->create();
        Sanctum::actingAs($user);

        $response = $this->getJson('/api/v1/me');

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'data' => ['id', 'name', 'email']
                ])
                ->assertJson([
                    'data' => [
                        'id' => $user->id,
                        'email' => $user->email
                    ]
                ]);
    }

    /** @test */
    public function unauthenticated_user_cannot_access_protected_routes()
    {
        $response = $this->getJson('/api/v1/me');

        $response->assertStatus(401);
    }

    /** @test */
    public function can_update_user_profile()
    {
        $user = UserModel::factory()->create();
        Sanctum::actingAs($user);

        $updateData = [
            'name' => 'Updated Name'
        ];

        $response = $this->patchJson("/api/v1/users/{$user->id}", $updateData);

        $response->assertStatus(200)
                ->assertJson([
                    'data' => [
                        'name' => 'Updated Name'
                    ]
                ]);

        $this->assertDatabaseHas('users', [
            'id' => $user->id,
            'name' => 'Updated Name'
        ]);
    }
}
```

### 6. Tests de Autenticación

```php
<?php
// tests/Feature/Auth/AuthenticationTest.php

namespace Tests\Feature\Auth;

use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class AuthenticationTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function user_can_login_with_valid_credentials()
    {
        $user = UserModel::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password123')
        ]);

        $credentials = [
            'email' => 'test@example.com',
            'password' => 'password123'
        ];

        $response = $this->postJson('/api/v1/login', $credentials);

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'message',
                    'data' => [
                        'user',
                        'token',
                        'token_type',
                        'expires_at'
                    ]
                ]);
    }

    /** @test */
    public function user_cannot_login_with_invalid_credentials()
    {
        $user = UserModel::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password123')
        ]);

        $credentials = [
            'email' => 'test@example.com',
            'password' => 'wrongpassword'
        ];

        $response = $this->postJson('/api/v1/login', $credentials);

        $response->assertStatus(401)
                ->assertJson([
                    'message' => 'Login failed'
                ]);
    }

    /** @test */
    public function user_can_logout()
    {
        $user = UserModel::factory()->create();
        $token = $user->createToken('test-token')->plainTextToken;

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token
        ])->postJson('/api/v1/logout');

        $response->assertStatus(200);

        // Verify token was revoked
        $this->assertDatabaseMissing('personal_access_tokens', [
            'tokenable_id' => $user->id,
            'name' => 'test-token'
        ]);
    }

    /** @test */
    public function rate_limiting_applies_to_login_attempts()
    {
        $credentials = [
            'email' => 'test@example.com',
            'password' => 'wrongpassword'
        ];

        // Make multiple failed attempts
        for ($i = 0; $i < 6; $i++) {
            $response = $this->postJson('/api/v1/login', $credentials);
        }

        // Should be rate limited
        $response->assertStatus(429);
    }
}
```

### 7. Tests de Performance

```php
<?php
// tests/Performance/UserCreationPerformanceTest.php

namespace Tests\Performance;

use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserCreationPerformanceTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function user_creation_endpoint_performs_within_acceptable_time()
    {
        $userData = [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        $startTime = microtime(true);

        $response = $this->postJson('/api/v1/register', $userData);

        $endTime = microtime(true);
        $executionTime = ($endTime - $startTime) * 1000; // Convert to milliseconds

        $response->assertStatus(201);
        $this->assertLessThan(500, $executionTime, 'User creation took too long: ' . $executionTime . 'ms');
    }

    /** @test */
    public function can_handle_concurrent_user_registrations()
    {
        $users = [];
        for ($i = 0; $i < 10; $i++) {
            $users[] = [
                'name' => "User {$i}",
                'email' => "user{$i}@example.com",
                'password' => 'password123',
                'password_confirmation' => 'password123'
            ];
        }

        $startTime = microtime(true);

        foreach ($users as $userData) {
            $response = $this->postJson('/api/v1/register', $userData);
            $response->assertStatus(201);
        }

        $endTime = microtime(true);
        $totalTime = ($endTime - $startTime) * 1000;

        $this->assertLessThan(5000, $totalTime, 'Concurrent registrations took too long: ' . $totalTime . 'ms');
        $this->assertEquals(10, UserModel::count());
    }
}
```

## Tips

### Mejores Prácticas de TDD

1. **Ciclo Red-Green-Refactor**:

   ```php
   // 1. Red: Escribir test que falle
   /** @test */
   public function can_create_user() {
       $result = $this->userService->createUser('John', 'john@example.com');
       $this->assertInstanceOf(User::class, $result);
   }

   // 2. Green: Implementar código mínimo para que pase
   // 3. Refactor: Mejorar el código manteniendo los tests verdes
   ```

2. **Arrange-Act-Assert Pattern**:

   ```php
   /** @test */
   public function can_update_user_name() {
       // Arrange
       $user = $this->createUser();
       $newName = 'Updated Name';

       // Act
       $user->changeName($newName);

       // Assert
       $this->assertEquals($newName, $user->getName());
   }
   ```

3. **Test Factories y Builders**:

   ```php
   // tests/Support/UserBuilder.php
   class UserBuilder {
       public static function create(): User {
           return new User(
               UserId::generate(),
               'Default Name',
               new Email('default@example.com')
           );
       }

       public function withName(string $name): self {
           $this->name = $name;
           return $this;
       }
   }
   ```

### Configuración de CI/CD para Tests

```yaml
# .github/workflows/tests.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: "8.2"
          extensions: mbstring, xml, ctype, iconv, intl, pdo_sqlite
          coverage: xdebug

      - name: Install dependencies
        run: composer install --prefer-dist --no-progress

      - name: Run unit tests
        run: php artisan test --testsuite=Unit --coverage

      - name: Run feature tests
        run: php artisan test --testsuite=Feature

      - name: Run integration tests
        run: php artisan test --testsuite=Integration
```

### Evitar Estos Errores

- ❌ No escribir tests después del código
- ❌ No separar tests por capas de arquitectura
- ❌ No usar mocks para dependencias externas
- ❌ No mantener los tests actualizados
- ❌ No ejecutar tests antes de commits

---

## Navegación

**Progreso en Desarrollo del Backend:**

- ✅ [Desarrollo del Backend](./desarrollo-backend.md)
- ✅ [Implementación Domain Layer](./implementacion-domain-layer.md)
- ✅ [Implementación Application Layer](./implementacion-application-layer.md)
- ✅ [Implementación Infrastructure Layer](./implementacion-infrastructure-layer.md)
- ✅ [APIs RESTful Versionadas](./apis-restful-versionadas.md)
- ✅ [Autenticación Laravel Sanctum](./autenticacion-laravel-sanctum.md)
- ✅ **Testing TDD Backend** ← Estás aquí

---

### � Etapa Completada

¡Felicitaciones! Has completado la **Etapa 4: Desarrollo del Backend (Laravel + Clean Architecture)**.

**Próximo paso**: Continúa con la [**Etapa 5: Desarrollo del Frontend Web (React + TypeScript)**](../step_05/desarrollo-frontend-web.md).

[⬅️ Autenticación Laravel Sanctum](./autenticacion-laravel-sanctum.md)
| [🏠 README Principal](../../README.md) |
[➡️ Desarrollo del frontend-web](../step_05/desarrollo-frontend-web.md)
