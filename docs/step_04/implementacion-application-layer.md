# Implementación Application Layer (Capa de Aplicación)

## Conocimientos involucrados
- Casos de uso
- DTOs (Data Transfer Objects)
- Orquestación de servicios
- Laravel

## Responsable
- Equipo de desarrollo backend

## ¿Qué es?

La capa de aplicación es responsable de orquestar los casos de uso del sistema,
coordinando las interacciones entre la capa de dominio y el mundo exterior. Esta
capa contiene los casos de uso, DTOs (Data Transfer Objects), commands, queries
y contratos de servicios externos. No contiene lógica de negocio, sino que
delega en la capa de dominio.

## ¿Por qué es importante?

- **Orquestación**: Coordina las operaciones entre múltiples entidades y
  servicios
- **Aislamiento**: Mantiene la capa de dominio pura y libre de detalles de
  aplicación
- **Casos de uso explícitos**: Cada funcionalidad está claramente definida
- **Transaccionalidad**: Maneja las transacciones y la persistencia
- **Transformación de datos**: Convierte entre formatos externos e internos
- **Punto de entrada**: Define los puntos de acceso para las operaciones del
  sistema

## ¿Qué debe incluir?

### Estructura de la Capa de Aplicación

```
app/Application/
├── UseCases/         # Casos de uso del sistema
│   ├── User/
│   │   ├── CreateUserUseCase.php
│   │   ├── UpdateUserUseCase.php
│   │   ├── DeleteUserUseCase.php
│   │   └── GetUserUseCase.php
│   ├── Order/
│   │   ├── CreateOrderUseCase.php
│   │   ├── ConfirmOrderUseCase.php
│   │   └── CancelOrderUseCase.php
│   └── Auth/
│       ├── LoginUseCase.php
│       ├── RegisterUseCase.php
│       └── LogoutUseCase.php
├── DTOs/            # Data Transfer Objects
│   ├── User/
│   │   ├── CreateUserDTO.php
│   │   ├── UpdateUserDTO.php
│   │   └── UserResponseDTO.php
│   └── Order/
│       ├── CreateOrderDTO.php
│       └── OrderItemDTO.php
├── Commands/        # Commands para operaciones
│   ├── CreateUserCommand.php
│   ├── UpdateUserCommand.php
│   └── DeleteUserCommand.php
├── Queries/         # Queries para consultas
│   ├── GetUserQuery.php
│   ├── GetUsersQuery.php
│   └── GetOrdersQuery.php
└── Contracts/       # Interfaces para servicios externos
    ├── EmailServiceContract.php
    ├── PaymentServiceContract.php
    └── NotificationServiceContract.php
```

### Componentes Principales

1. **Casos de Uso**: Orquestadores de las operaciones del sistema
2. **DTOs**: Objetos para transferir datos entre capas
3. **Commands**: Operaciones que modifican el estado
4. **Queries**: Operaciones de consulta sin efectos secundarios
5. **Contratos**: Interfaces para servicios externos

## ¿Qué debo hacer?

### 1. Crear DTOs para Transferencia de Datos

```php
<?php
// app/Application/DTOs/User/CreateUserDTO.php

namespace App\Application\DTOs\User;

class CreateUserDTO
{
    public function __construct(
        public readonly string $name,
        public readonly string $email,
        public readonly string $password,
        public readonly ?string $phone = null
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            name: $data['name'],
            email: $data['email'],
            password: $data['password'],
            phone: $data['phone'] ?? null
        );
    }

    public function toArray(): array
    {
        return [
            'name' => $this->name,
            'email' => $this->email,
            'password' => $this->password,
            'phone' => $this->phone,
        ];
    }
}
```

```php
<?php
// app/Application/DTOs/User/UserResponseDTO.php

namespace App\Application\DTOs\User;

use App\Domain\Entities\User;

class UserResponseDTO
{
    public function __construct(
        public readonly string $id,
        public readonly string $name,
        public readonly string $email,
        public readonly bool $emailVerified,
        public readonly string $createdAt
    ) {}

    public static function fromEntity(User $user): self
    {
        return new self(
            id: $user->getId()->getValue(),
            name: $user->getName(),
            email: $user->getEmail()->getValue(),
            emailVerified: $user->isEmailVerified(),
            createdAt: $user->getCreatedAt()->format('Y-m-d H:i:s')
        );
    }

    public function toArray(): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'email_verified' => $this->emailVerified,
            'created_at' => $this->createdAt,
        ];
    }
}
```

### 2. Implementar Casos de Uso

```php
<?php
// app/Application/UseCases/User/CreateUserUseCase.php

namespace App\Application\UseCases\User;

use App\Application\DTOs\User\CreateUserDTO;
use App\Application\DTOs\User\UserResponseDTO;
use App\Application\Contracts\EmailServiceContract;
use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use App\Domain\Services\UserService;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserAlreadyExistsException;

class CreateUserUseCase
{
    public function __construct(
        private UserRepository $userRepository,
        private UserService $userService,
        private EmailServiceContract $emailService
    ) {}

    public function execute(CreateUserDTO $dto): UserResponseDTO
    {
        // Validate business rules
        if (!$this->userService->isEmailAvailable($dto->email)) {
            throw new UserAlreadyExistsException("User with email {$dto->email} already exists");
        }

        // Create domain entity
        $user = new User(
            id: UserId::generate(),
            name: $dto->name,
            email: new Email($dto->email)
        );

        // Persist user
        $savedUser = $this->userRepository->save($user);

        // Handle side effects (send welcome email)
        $this->emailService->sendWelcomeEmail($savedUser->getEmail()->getValue());

        // Return response DTO
        return UserResponseDTO::fromEntity($savedUser);
    }
}
```

```php
<?php
// app/Application/UseCases/User/UpdateUserUseCase.php

namespace App\Application\UseCases\User;

use App\Application\DTOs\User\UpdateUserDTO;
use App\Application\DTOs\User\UserResponseDTO;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserNotFoundException;

class UpdateUserUseCase
{
    public function __construct(
        private UserRepository $userRepository
    ) {}

    public function execute(UpdateUserDTO $dto): UserResponseDTO
    {
        // Find existing user
        $user = $this->userRepository->findById(UserId::fromString($dto->id));

        if (!$user) {
            throw new UserNotFoundException(UserId::fromString($dto->id));
        }

        // Update user data
        if ($dto->name !== null) {
            $user->changeName($dto->name);
        }

        if ($dto->email !== null) {
            $user->changeEmail(new Email($dto->email));
        }

        // Save changes
        $updatedUser = $this->userRepository->save($user);

        return UserResponseDTO::fromEntity($updatedUser);
    }
}
```

### 3. Crear Commands y Queries

```php
<?php
// app/Application/Commands/CreateUserCommand.php

namespace App\Application\Commands;

class CreateUserCommand
{
    public function __construct(
        public readonly string $name,
        public readonly string $email,
        public readonly string $password
    ) {}
}
```

```php
<?php
// app/Application/Queries/GetUserQuery.php

namespace App\Application\Queries;

class GetUserQuery
{
    public function __construct(
        public readonly string $userId
    ) {}
}
```

### 4. Implementar Query Handlers

```php
<?php
// app/Application/UseCases/User/GetUserUseCase.php

namespace App\Application\UseCases\User;

use App\Application\DTOs\User\UserResponseDTO;
use App\Application\Queries\GetUserQuery;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserNotFoundException;

class GetUserUseCase
{
    public function __construct(
        private UserRepository $userRepository
    ) {}

    public function execute(GetUserQuery $query): UserResponseDTO
    {
        $userId = UserId::fromString($query->userId);
        $user = $this->userRepository->findById($userId);

        if (!$user) {
            throw new UserNotFoundException($userId);
        }

        return UserResponseDTO::fromEntity($user);
    }
}
```

### 5. Definir Contratos de Servicios Externos

```php
<?php
// app/Application/Contracts/EmailServiceContract.php

namespace App\Application\Contracts;

interface EmailServiceContract
{
    public function sendWelcomeEmail(string $email): void;
    public function sendPasswordResetEmail(string $email, string $token): void;
    public function sendEmailVerification(string $email, string $token): void;
}
```

```php
<?php
// app/Application/Contracts/PaymentServiceContract.php

namespace App\Application\Contracts;

use App\Domain\ValueObjects\Money;

interface PaymentServiceContract
{
    public function processPayment(string $orderId, Money $amount, array $paymentData): bool;
    public function refundPayment(string $transactionId, Money $amount): bool;
    public function getPaymentStatus(string $transactionId): string;
}
```

### 6. Crear DTOs Complejos

```php
<?php
// app/Application/DTOs/Order/CreateOrderDTO.php

namespace App\Application\DTOs\Order;

class CreateOrderDTO
{
    /**
     * @param OrderItemDTO[] $items
     */
    public function __construct(
        public readonly string $userId,
        public readonly array $items,
        public readonly ?string $notes = null
    ) {}

    public static function fromArray(array $data): self
    {
        $items = array_map(
            fn($item) => OrderItemDTO::fromArray($item),
            $data['items'] ?? []
        );

        return new self(
            userId: $data['user_id'],
            items: $items,
            notes: $data['notes'] ?? null
        );
    }
}
```

```php
<?php
// app/Application/DTOs/Order/OrderItemDTO.php

namespace App\Application\DTOs\Order;

class OrderItemDTO
{
    public function __construct(
        public readonly string $productId,
        public readonly int $quantity,
        public readonly float $price
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            productId: $data['product_id'],
            quantity: (int) $data['quantity'],
            price: (float) $data['price']
        );
    }

    public function toArray(): array
    {
        return [
            'product_id' => $this->productId,
            'quantity' => $this->quantity,
            'price' => $this->price,
        ];
    }
}
```

## Tips

### Mejores Prácticas

1. **Casos de Uso**:

   - Un caso de uso por operación específica
   - Nombres descriptivos que expresen la intención
   - Manejo de transacciones en esta capa
   - Validaciones de negocio delegadas al dominio

2. **DTOs**:

   - Immutable (readonly properties)
   - Métodos factory para crear desde arrays
   - Métodos para conversión a arrays
   - Validación de tipos en el constructor

3. **Commands vs Queries**:

   - Commands modifican estado
   - Queries solo consultan datos
   - Separación clara de responsabilidades
   - Commands no devuelven datos de negocio

4. **Contratos**:
   - Interfaces simples y focalizadas
   - Sin dependencias a frameworks
   - Documentación clara de comportamiento
   - Fáciles de mockear para testing

### Evitar Estos Errores

- ❌ No incluir lógica de negocio en los casos de uso
- ❌ No hacer queries directas desde casos de uso
- ❌ No depender de detalles de infraestructura
- ❌ No acoplar casos de uso entre sí
- ❌ No devolver entidades directamente desde casos de uso

## Ejemplos

### Caso de Uso Complejo con Transacciones

```php
<?php
// app/Application/UseCases/Order/CreateOrderUseCase.php

namespace App\Application\UseCases\Order;

use App\Application\DTOs\Order\CreateOrderDTO;
use App\Application\DTOs\Order\OrderResponseDTO;
use App\Application\Contracts\PaymentServiceContract;
use App\Application\Contracts\EmailServiceContract;
use App\Domain\Entities\Order;
use App\Domain\Repositories\OrderRepository;
use App\Domain\Repositories\ProductRepository;
use App\Domain\Repositories\UserRepository;
use App\Domain\Services\OrderService;
use App\Domain\ValueObjects\OrderId;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserNotFoundException;
use App\Domain\Exceptions\ProductNotFoundException;

class CreateOrderUseCase
{
    public function __construct(
        private OrderRepository $orderRepository,
        private UserRepository $userRepository,
        private ProductRepository $productRepository,
        private OrderService $orderService,
        private PaymentServiceContract $paymentService,
        private EmailServiceContract $emailService
    ) {}

    public function execute(CreateOrderDTO $dto): OrderResponseDTO
    {
        // Start transaction (handled by infrastructure layer)

        // Validate user exists
        $userId = UserId::fromString($dto->userId);
        $user = $this->userRepository->findById($userId);
        if (!$user) {
            throw new UserNotFoundException($userId);
        }

        // Create order
        $order = new Order(
            id: OrderId::generate(),
            userId: $userId
        );

        // Add items to order
        foreach ($dto->items as $itemDTO) {
            $product = $this->productRepository->findById($itemDTO->productId);
            if (!$product) {
                throw new ProductNotFoundException($itemDTO->productId);
            }

            $order->addItem($product, $itemDTO->quantity);
        }

        // Apply business rules
        $this->orderService->validateOrder($order);

        // Save order
        $savedOrder = $this->orderRepository->save($order);

        // Process payment
        $paymentSuccessful = $this->paymentService->processPayment(
            $savedOrder->getId()->getValue(),
            $savedOrder->calculateTotal(),
            [] // payment data would come from DTO
        );

        if ($paymentSuccessful) {
            $savedOrder->confirm();
            $this->orderRepository->save($savedOrder);

            // Send confirmation email
            $this->emailService->sendOrderConfirmation(
                $user->getEmail()->getValue(),
                $savedOrder->getId()->getValue()
            );
        }

        // Commit transaction (handled by infrastructure layer)

        return OrderResponseDTO::fromEntity($savedOrder);
    }
}
```

### Command Handler con Validation

```php
<?php
// app/Application/UseCases/User/HandleCreateUserCommand.php

namespace App\Application\UseCases\User;

use App\Application\Commands\CreateUserCommand;
use App\Application\DTOs\User\UserResponseDTO;
use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserAlreadyExistsException;

class HandleCreateUserCommand
{
    public function __construct(
        private UserRepository $userRepository
    ) {}

    public function handle(CreateUserCommand $command): UserResponseDTO
    {
        $email = new Email($command->email);

        // Check if user already exists
        if ($this->userRepository->existsByEmail($email)) {
            throw new UserAlreadyExistsException("User with email {$command->email} already exists");
        }

        // Create and save user
        $user = new User(
            id: UserId::generate(),
            name: $command->name,
            email: $email
        );

        $savedUser = $this->userRepository->save($user);

        return UserResponseDTO::fromEntity($savedUser);
    }
}
```

## Navegación

[⬅️ Implementación Domain Layer](./implementacion-domain-layer.md) |
[🏠 README Principal](../../README.md) |
[Implementación Infrastructure Layer ➡️](implementacion-infrastructure-layer.md)
