# Implementación Domain Layer (Capa de Dominio)

## Conocimientos involucrados
- Reglas de negocio
- Entidades y objetos de valor
- Interfaces de persistencia
- Domain-Driven Design (DDD)

## Responsable
- Equipo de desarrollo backend

## ¿Qué es?

La capa de dominio es el núcleo de la aplicación que contiene las reglas de
negocio puras, independientes de cualquier framework o tecnología externa. Esta
capa incluye entidades, value objects, repositorios (interfaces), servicios de
dominio y eventos. Es la capa más estable y no debe tener dependencias hacia
otras capas.

## ¿Por qué es importante?

- **Independencia tecnológica**: Las reglas de negocio no dependen de Laravel ni
  de ninguna tecnología específica
- **Testabilidad**: Permite testing unitario puro sin dependencias externas
- **Mantenibilidad**: Las reglas de negocio están centralizadas y son fáciles de
  entender
- **Reutilización**: Puede ser reutilizada en diferentes contextos (web, API,
  CLI)
- **Evolución**: Permite cambiar tecnologías sin afectar la lógica de negocio
- **Claridad**: Expresa el lenguaje del negocio de forma explícita

## ¿Qué debe incluir?

### Estructura de la Capa de Dominio

```
app/Domain/
├── Entities/         # Objetos principales del negocio
│   ├── User.php
│   ├── Order.php
│   └── Product.php
├── ValueObjects/     # Objetos de valor inmutables
│   ├── Email.php
│   ├── Money.php
│   ├── UserId.php
│   └── Rut.php
├── Repositories/     # Interfaces de persistencia
│   ├── UserRepository.php
│   ├── OrderRepository.php
│   └── ProductRepository.php
├── Services/         # Servicios de dominio
│   ├── UserService.php
│   ├── OrderService.php
│   └── PaymentService.php
├── Events/          # Eventos de dominio
│   ├── UserCreated.php
│   ├── OrderPlaced.php
│   └── PaymentProcessed.php
└── Exceptions/      # Excepciones del dominio
    ├── UserNotFoundException.php
    ├── InvalidEmailException.php
    └── InsufficientFundsException.php
```

### Componentes Principales

1. **Entidades**: Objetos con identidad que encapsulan comportamiento
2. **Value Objects**: Objetos inmutables que representan valores
3. **Repositorios**: Contratos para la persistencia de agregados
4. **Servicios de Dominio**: Lógica que no pertenece a una entidad específica
5. **Eventos**: Notificaciones de cambios importantes en el dominio
6. **Excepciones**: Errores específicos del dominio

## ¿Qué debo hacer?

### 1. Crear Entidades Principales

```php
<?php
// app/Domain/Entities/User.php

namespace App\Domain\Entities;

use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Events\UserCreated;

class User
{
    private array $domainEvents = [];

    public function __construct(
        private UserId $id,
        private string $name,
        private Email $email,
        private ?\DateTime $emailVerifiedAt = null,
        private ?\DateTime $createdAt = null
    ) {
        $this->createdAt = $this->createdAt ?? new \DateTime();
        $this->recordEvent(new UserCreated($this->id));
    }

    // Getters
    public function getId(): UserId { return $this->id; }
    public function getName(): string { return $this->name; }
    public function getEmail(): Email { return $this->email; }
    public function getCreatedAt(): \DateTime { return $this->createdAt; }

    // Business methods
    public function changeName(string $newName): void
    {
        if (empty(trim($newName))) {
            throw new \InvalidArgumentException('Name cannot be empty');
        }
        $this->name = $newName;
    }

    public function changeEmail(Email $newEmail): void
    {
        $this->email = $newEmail;
        $this->emailVerifiedAt = null; // Reset verification
    }

    public function markEmailAsVerified(): void
    {
        $this->emailVerifiedAt = new \DateTime();
    }

    public function isEmailVerified(): bool
    {
        return $this->emailVerifiedAt !== null;
    }

    // Domain events
    private function recordEvent(object $event): void
    {
        $this->domainEvents[] = $event;
    }

    public function releaseEvents(): array
    {
        $events = $this->domainEvents;
        $this->domainEvents = [];
        return $events;
    }
}
```

### 2. Implementar Value Objects

```php
<?php
// app/Domain/ValueObjects/Email.php

namespace App\Domain\ValueObjects;

use App\Domain\Exceptions\InvalidEmailException;

final class Email
{
    private string $value;

    public function __construct(string $email)
    {
        $this->validate($email);
        $this->value = strtolower(trim($email));
    }

    private function validate(string $email): void
    {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidEmailException("Invalid email format: {$email}");
        }
    }

    public function getValue(): string
    {
        return $this->value;
    }

    public function equals(Email $other): bool
    {
        return $this->value === $other->value;
    }

    public function __toString(): string
    {
        return $this->value;
    }
}
```

```php
<?php
// app/Domain/ValueObjects/UserId.php

namespace App\Domain\ValueObjects;

final class UserId
{
    private string $value;

    public function __construct(string $id)
    {
        if (empty(trim($id))) {
            throw new \InvalidArgumentException('User ID cannot be empty');
        }
        $this->value = $id;
    }

    public static function generate(): self
    {
        return new self(bin2hex(random_bytes(16)));
    }

    public static function fromString(string $id): self
    {
        return new self($id);
    }

    public function getValue(): string
    {
        return $this->value;
    }

    public function equals(UserId $other): bool
    {
        return $this->value === $other->value;
    }

    public function __toString(): string
    {
        return $this->value;
    }
}
```

### 3. Definir Interfaces de Repositorios

```php
<?php
// app/Domain/Repositories/UserRepository.php

namespace App\Domain\Repositories;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;

interface UserRepository
{
    public function save(User $user): User;
    public function findById(UserId $id): ?User;
    public function findByEmail(Email $email): ?User;
    public function delete(UserId $id): void;
    public function existsByEmail(Email $email): bool;
}
```

### 4. Crear Servicios de Dominio

```php
<?php
// app/Domain/Services/UserService.php

namespace App\Domain\Services;

use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserAlreadyExistsException;

class UserService
{
    public function __construct(
        private UserRepository $userRepository
    ) {}

    public function createUser(string $name, string $email): User
    {
        $emailVO = new Email($email);

        if ($this->userRepository->existsByEmail($emailVO)) {
            throw new UserAlreadyExistsException("User with email {$email} already exists");
        }

        $user = new User(
            id: UserId::generate(),
            name: $name,
            email: $emailVO
        );

        return $this->userRepository->save($user);
    }

    public function isEmailAvailable(string $email): bool
    {
        $emailVO = new Email($email);
        return !$this->userRepository->existsByEmail($emailVO);
    }
}
```

### 5. Implementar Eventos de Dominio

```php
<?php
// app/Domain/Events/UserCreated.php

namespace App\Domain\Events;

use App\Domain\ValueObjects\UserId;

class UserCreated
{
    public function __construct(
        private UserId $userId,
        private \DateTime $occurredAt = new \DateTime()
    ) {}

    public function getUserId(): UserId
    {
        return $this->userId;
    }

    public function getOccurredAt(): \DateTime
    {
        return $this->occurredAt;
    }
}
```

### 6. Crear Excepciones Específicas

```php
<?php
// app/Domain/Exceptions/UserNotFoundException.php

namespace App\Domain\Exceptions;

use App\Domain\ValueObjects\UserId;

class UserNotFoundException extends \Exception
{
    public function __construct(UserId $userId)
    {
        parent::__construct("User with ID {$userId->getValue()} not found");
    }
}
```

## Tips

### Mejores Prácticas

1. **Entidades**:

   - Siempre tienen identidad única
   - Encapsulan comportamiento, no solo datos
   - Validan su propio estado
   - Pueden generar eventos de dominio

2. **Value Objects**:

   - Son inmutables
   - Se comparan por valor, no por referencia
   - Validan su formato en el constructor
   - Implementan método `equals()`

3. **Repositorios**:

   - Solo definen interfaces en esta capa
   - Trabajan con agregados completos
   - Usan value objects como parámetros
   - No exponen detalles de persistencia

4. **Servicios de Dominio**:
   - Contienen lógica que no pertenece a una entidad
   - Coordinan operaciones entre múltiples entidades
   - No tienen estado
   - Expresan conceptos del lenguaje del negocio

### Evitar Estos Errores

- ❌ No usar Eloquent models en la capa de dominio
- ❌ No hacer queries directas a base de datos
- ❌ No incluir dependencias a Laravel
- ❌ No crear entidades anémicas (solo getters/setters)
- ❌ No mutar value objects después de crearlos

## Ejemplos

### Entidad Completa con Comportamiento

```php
<?php

namespace App\Domain\Entities;

use App\Domain\ValueObjects\Money;
use App\Domain\ValueObjects\OrderId;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\InsufficientStockException;

class Order
{
    private array $items = [];
    private string $status = 'pending';

    public function __construct(
        private OrderId $id,
        private UserId $userId,
        private \DateTime $createdAt = new \DateTime()
    ) {}

    public function addItem(Product $product, int $quantity): void
    {
        if ($quantity <= 0) {
            throw new \InvalidArgumentException('Quantity must be positive');
        }

        if (!$product->hasStock($quantity)) {
            throw new InsufficientStockException(
                "Insufficient stock for product {$product->getId()}"
            );
        }

        $this->items[] = new OrderItem($product, $quantity);
    }

    public function calculateTotal(): Money
    {
        $total = Money::zero();
        foreach ($this->items as $item) {
            $total = $total->add($item->getSubtotal());
        }
        return $total;
    }

    public function confirm(): void
    {
        if (empty($this->items)) {
            throw new \DomainException('Cannot confirm empty order');
        }
        $this->status = 'confirmed';
    }

    public function isConfirmed(): bool
    {
        return $this->status === 'confirmed';
    }
}
```

### Value Object Complejo

```php
<?php

namespace App\Domain\ValueObjects;

final class Money
{
    public function __construct(
        private int $amount, // Amount in cents
        private string $currency = 'CLP'
    ) {
        if ($amount < 0) {
            throw new \InvalidArgumentException('Amount cannot be negative');
        }
    }

    public static function zero(string $currency = 'CLP'): self
    {
        return new self(0, $currency);
    }

    public static function fromPesos(float $pesos, string $currency = 'CLP'): self
    {
        return new self((int) round($pesos * 100), $currency);
    }

    public function add(Money $other): self
    {
        $this->ensureSameCurrency($other);
        return new self($this->amount + $other->amount, $this->currency);
    }

    public function subtract(Money $other): self
    {
        $this->ensureSameCurrency($other);
        return new self($this->amount - $other->amount, $this->currency);
    }

    public function multiply(float $factor): self
    {
        return new self((int) round($this->amount * $factor), $this->currency);
    }

    public function toPesos(): float
    {
        return $this->amount / 100;
    }

    public function equals(Money $other): bool
    {
        return $this->amount === $other->amount && $this->currency === $other->currency;
    }

    private function ensureSameCurrency(Money $other): void
    {
        if ($this->currency !== $other->currency) {
            throw new \InvalidArgumentException('Currency mismatch');
        }
    }
}
```

## Navegación

[⬅️ Desarrollo del Backend](desarrollo-backend.md) |
[🏠 README Principal](../../README.md) |
[Implementación Application Layer ➡️](implementacion-application-layer.md)
