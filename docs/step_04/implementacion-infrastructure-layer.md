# Implementación Infrastructure Layer (Capa de Infraestructura)

## Conocimientos involucrados
- Integración con bases de datos
- APIs externas
- Laravel
- Configuración de entornos

## Responsable
- Equipo de desarrollo backend

## ¿Qué es?

La capa de infraestructura es la capa más externa que implementa todos los
detalles técnicos y se conecta con tecnologías específicas como bases de datos,
APIs externas, frameworks web y servicios de terceros. Esta capa implementa las
interfaces definidas en las capas superiores y maneja toda la comunicación con
el mundo exterior.

## ¿Por qué es importante?

- **Implementación concreta**: Provee las implementaciones reales de los
  contratos definidos
- **Integración tecnológica**: Conecta el sistema con Laravel, bases de datos y
  servicios externos
- **Adaptación**: Adapta las tecnologías externas a las necesidades del dominio
- **Aislamiento**: Permite cambiar tecnologías sin afectar las capas superiores
- **Configuración**: Maneja toda la configuración específica del entorno
- **Performance**: Optimiza las consultas y operaciones técnicas

## ¿Qué debe incluir?

### Estructura de la Capa de Infraestructura

```
app/Infrastructure/
├── Persistence/
│   └── Eloquent/
│       ├── Models/
│       │   ├── UserModel.php
│       │   ├── OrderModel.php
│       │   └── ProductModel.php
│       └── Repositories/
│           ├── EloquentUserRepository.php
│           ├── EloquentOrderRepository.php
│           └── EloquentProductRepository.php
├── Http/
│   ├── Controllers/
│   │   └── Api/V1/
│   │       ├── UserController.php
│   │       ├── OrderController.php
│   │       └── AuthController.php
│   ├── Requests/
│   │   ├── CreateUserRequest.php
│   │   ├── UpdateUserRequest.php
│   │   └── CreateOrderRequest.php
│   ├── Resources/
│   │   ├── UserResource.php
│   │   ├── OrderResource.php
│   │   └── OrderCollection.php
│   └── Middleware/
│       ├── AuthenticateApi.php
│       └── ValidateApiVersion.php
├── Services/
│   ├── EmailService.php
│   ├── PaymentService.php
│   └── NotificationService.php
├── Events/
│   ├── UserCreatedHandler.php
│   └── OrderConfirmedHandler.php
└── Providers/
    ├── RepositoryServiceProvider.php
    ├── UseCaseServiceProvider.php
    └── EventServiceProvider.php
```

### Componentes Principales

1. **Modelos Eloquent**: Mapeo objeto-relacional con la base de datos
2. **Repositorios Concretos**: Implementaciones de las interfaces de repositorio
3. **Controladores API**: Endpoints HTTP para la aplicación
4. **Form Requests**: Validación de datos de entrada
5. **API Resources**: Formateo de respuestas JSON
6. **Servicios Externos**: Implementaciones de contratos para servicios de
   terceros
7. **Service Providers**: Configuración de inyección de dependencias

## ¿Qué debo hacer?

### 1. Crear Modelos Eloquent

```php
<?php
// app/Infrastructure/Persistence/Eloquent/Models/UserModel.php

namespace App\Infrastructure\Persistence\Eloquent\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class UserModel extends Model
{
    use HasFactory, SoftDeletes;

    protected $table = 'users';

    protected $fillable = [
        'id',
        'name',
        'email',
        'email_verified_at',
        'password',
    ];

    protected $hidden = [
        'password',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'created_at' => 'datetime',
        'updated_at' => 'datetime',
        'deleted_at' => 'datetime',
    ];

    public $incrementing = false;
    protected $keyType = 'string';

    // Relationships
    public function orders()
    {
        return $this->hasMany(OrderModel::class, 'user_id');
    }
}
```

### 2. Implementar Repositorios Concretos

```php
<?php
// app/Infrastructure/Persistence/Eloquent/Repositories/EloquentUserRepository.php

namespace App\Infrastructure\Persistence\Eloquent\Repositories;

use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;

class EloquentUserRepository implements UserRepository
{
    public function save(User $user): User
    {
        $model = UserModel::updateOrCreate(
            ['id' => $user->getId()->getValue()],
            [
                'name' => $user->getName(),
                'email' => $user->getEmail()->getValue(),
                'email_verified_at' => $user->isEmailVerified()
                    ? $user->getEmailVerifiedAt()
                    : null,
            ]
        );

        return $this->toDomainEntity($model);
    }

    public function findById(UserId $id): ?User
    {
        $model = UserModel::find($id->getValue());

        return $model ? $this->toDomainEntity($model) : null;
    }

    public function findByEmail(Email $email): ?User
    {
        $model = UserModel::where('email', $email->getValue())->first();

        return $model ? $this->toDomainEntity($model) : null;
    }

    public function delete(UserId $id): void
    {
        UserModel::where('id', $id->getValue())->delete();
    }

    public function existsByEmail(Email $email): bool
    {
        return UserModel::where('email', $email->getValue())->exists();
    }

    private function toDomainEntity(UserModel $model): User
    {
        return new User(
            id: UserId::fromString($model->id),
            name: $model->name,
            email: new Email($model->email),
            emailVerifiedAt: $model->email_verified_at,
            createdAt: $model->created_at
        );
    }
}
```

### 3. Crear Controladores API

```php
<?php
// app/Infrastructure/Http/Controllers/Api/V1/UserController.php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\DTOs\User\CreateUserDTO;
use App\Application\DTOs\User\UpdateUserDTO;
use App\Application\UseCases\User\CreateUserUseCase;
use App\Application\UseCases\User\GetUserUseCase;
use App\Application\UseCases\User\UpdateUserUseCase;
use App\Application\UseCases\User\DeleteUserUseCase;
use App\Application\Queries\GetUserQuery;
use App\Infrastructure\Http\Controllers\Controller;
use App\Infrastructure\Http\Requests\CreateUserRequest;
use App\Infrastructure\Http\Requests\UpdateUserRequest;
use App\Infrastructure\Http\Resources\UserResource;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Response;

class UserController extends Controller
{
    public function __construct(
        private CreateUserUseCase $createUserUseCase,
        private GetUserUseCase $getUserUseCase,
        private UpdateUserUseCase $updateUserUseCase,
        private DeleteUserUseCase $deleteUserUseCase
    ) {}

    public function store(CreateUserRequest $request): JsonResponse
    {
        try {
            $dto = CreateUserDTO::fromArray($request->validated());
            $userResponse = $this->createUserUseCase->execute($dto);

            return response()->json([
                'message' => 'User created successfully',
                'data' => new UserResource($userResponse)
            ], Response::HTTP_CREATED);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'Error creating user',
                'error' => $e->getMessage()
            ], Response::HTTP_BAD_REQUEST);
        }
    }

    public function show(string $id): JsonResponse
    {
        try {
            $query = new GetUserQuery($id);
            $userResponse = $this->getUserUseCase->execute($query);

            return response()->json([
                'data' => new UserResource($userResponse)
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'User not found',
                'error' => $e->getMessage()
            ], Response::HTTP_NOT_FOUND);
        }
    }

    public function update(UpdateUserRequest $request, string $id): JsonResponse
    {
        try {
            $data = array_merge($request->validated(), ['id' => $id]);
            $dto = UpdateUserDTO::fromArray($data);
            $userResponse = $this->updateUserUseCase->execute($dto);

            return response()->json([
                'message' => 'User updated successfully',
                'data' => new UserResource($userResponse)
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'Error updating user',
                'error' => $e->getMessage()
            ], Response::HTTP_BAD_REQUEST);
        }
    }

    public function destroy(string $id): JsonResponse
    {
        try {
            $this->deleteUserUseCase->execute($id);

            return response()->json([
                'message' => 'User deleted successfully'
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'message' => 'Error deleting user',
                'error' => $e->getMessage()
            ], Response::HTTP_BAD_REQUEST);
        }
    }
}
```

### 4. Implementar Form Requests

```php
<?php
// app/Infrastructure/Http/Requests/CreateUserRequest.php

namespace App\Infrastructure\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

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
            'email.unique' => 'Este email ya está registrado',
            'password.required' => 'La contraseña es obligatoria',
            'password.min' => 'La contraseña debe tener al menos 8 caracteres',
            'password.confirmed' => 'Las contraseñas no coinciden',
        ];
    }
}
```

### 5. Crear API Resources

```php
<?php
// app/Infrastructure/Http/Resources/UserResource.php

namespace App\Infrastructure\Http\Resources;

use App\Application\DTOs\User\UserResponseDTO;
use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        /** @var UserResponseDTO $this->resource */
        return [
            'id' => $this->resource->id,
            'name' => $this->resource->name,
            'email' => $this->resource->email,
            'email_verified' => $this->resource->emailVerified,
            'created_at' => $this->resource->createdAt,
        ];
    }
}
```

### 6. Implementar Servicios Externos

```php
<?php
// app/Infrastructure/Services/EmailService.php

namespace App\Infrastructure\Services;

use App\Application\Contracts\EmailServiceContract;
use Illuminate\Support\Facades\Mail;
use App\Infrastructure\Mail\WelcomeEmail;
use App\Infrastructure\Mail\OrderConfirmationEmail;

class EmailService implements EmailServiceContract
{
    public function sendWelcomeEmail(string $email): void
    {
        Mail::to($email)->send(new WelcomeEmail());
    }

    public function sendPasswordResetEmail(string $email, string $token): void
    {
        // Implementation for password reset email
    }

    public function sendEmailVerification(string $email, string $token): void
    {
        // Implementation for email verification
    }

    public function sendOrderConfirmation(string $email, string $orderId): void
    {
        Mail::to($email)->send(new OrderConfirmationEmail($orderId));
    }
}
```

### 7. Configurar Service Providers

```php
<?php
// app/Infrastructure/Providers/RepositoryServiceProvider.php

namespace App\Infrastructure\Providers;

use App\Domain\Repositories\UserRepository;
use App\Domain\Repositories\OrderRepository;
use App\Infrastructure\Persistence\Eloquent\Repositories\EloquentUserRepository;
use App\Infrastructure\Persistence\Eloquent\Repositories\EloquentOrderRepository;
use Illuminate\Support\ServiceProvider;

class RepositoryServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->app->bind(UserRepository::class, EloquentUserRepository::class);
        $this->app->bind(OrderRepository::class, EloquentOrderRepository::class);
    }
}
```

```php
<?php
// app/Infrastructure/Providers/UseCaseServiceProvider.php

namespace App\Infrastructure\Providers;

use App\Application\Contracts\EmailServiceContract;
use App\Application\Contracts\PaymentServiceContract;
use App\Infrastructure\Services\EmailService;
use App\Infrastructure\Services\PaymentService;
use Illuminate\Support\ServiceProvider;

class UseCaseServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->app->bind(EmailServiceContract::class, EmailService::class);
        $this->app->bind(PaymentServiceContract::class, PaymentService::class);
    }
}
```

### 8. Configurar Rutas API

```php
<?php
// routes/api/v1/users.php

use App\Infrastructure\Http\Controllers\Api\V1\UserController;
use Illuminate\Support\Facades\Route;

Route::prefix('v1')->middleware(['api', 'auth:sanctum'])->group(function () {
    Route::apiResource('users', UserController::class);
});
```

## Tips

### Mejores Prácticas

1. **Separación de Responsabilidades**:

   - Los modelos Eloquent solo manejan persistencia
   - Los controladores solo orquestan requests/responses
   - Los repositories manejan la conversión dominio ↔ persistencia

2. **Naming Conventions**:

   - Modelos: `UserModel`, `OrderModel`
   - Repositorios: `EloquentUserRepository`
   - Controladores: `UserController`
   - Resources: `UserResource`

3. **Manejo de Errores**:

   ```php
   try {
       $result = $this->useCase->execute($dto);
       return response()->json(['data' => $result], 200);
   } catch (DomainException $e) {
       return response()->json(['error' => $e->getMessage()], 400);
   } catch (\Exception $e) {
       return response()->json(['error' => 'Internal server error'], 500);
   }
   ```

4. **Transacciones**:
   ```php
   DB::transaction(function () use ($dto) {
       return $this->useCase->execute($dto);
   });
   ```

### Evitar Estos Errores

- ❌ No exponer modelos Eloquent en las respuestas de API
- ❌ No incluir lógica de negocio en controladores
- ❌ No hacer queries directas desde controladores
- ❌ No acoplar la implementación con la interfaz
- ❌ No manejar excepciones apropiadamente

## Ejemplos

### Repositorio Completo con Queries Complejas

```php
<?php

namespace App\Infrastructure\Persistence\Eloquent\Repositories;

use App\Domain\Entities\Order;
use App\Domain\Repositories\OrderRepository;
use App\Domain\ValueObjects\OrderId;
use App\Domain\ValueObjects\UserId;
use App\Infrastructure\Persistence\Eloquent\Models\OrderModel;
use Illuminate\Database\Eloquent\Collection;

class EloquentOrderRepository implements OrderRepository
{
    public function save(Order $order): Order
    {
        $model = OrderModel::updateOrCreate(
            ['id' => $order->getId()->getValue()],
            [
                'user_id' => $order->getUserId()->getValue(),
                'status' => $order->getStatus(),
                'total' => $order->calculateTotal()->toPesos(),
                'notes' => $order->getNotes(),
            ]
        );

        // Save order items
        $this->saveOrderItems($model, $order);

        return $this->toDomainEntity($model->fresh('items'));
    }

    public function findById(OrderId $id): ?Order
    {
        $model = OrderModel::with('items.product')->find($id->getValue());

        return $model ? $this->toDomainEntity($model) : null;
    }

    public function findByUserId(UserId $userId): array
    {
        $models = OrderModel::with('items.product')
            ->where('user_id', $userId->getValue())
            ->orderBy('created_at', 'desc')
            ->get();

        return $models->map(fn($model) => $this->toDomainEntity($model))->toArray();
    }

    public function findPendingOrders(): array
    {
        $models = OrderModel::with('items.product')
            ->where('status', 'pending')
            ->where('created_at', '>', now()->subDays(1))
            ->get();

        return $models->map(fn($model) => $this->toDomainEntity($model))->toArray();
    }

    private function toDomainEntity(OrderModel $model): Order
    {
        $order = new Order(
            id: OrderId::fromString($model->id),
            userId: UserId::fromString($model->user_id),
            createdAt: $model->created_at
        );

        // Add items to order
        foreach ($model->items as $item) {
            $product = $this->convertToProduct($item->product);
            $order->addItem($product, $item->quantity);
        }

        return $order;
    }

    private function saveOrderItems(OrderModel $orderModel, Order $order): void
    {
        // Clear existing items
        $orderModel->items()->delete();

        // Add new items
        foreach ($order->getItems() as $item) {
            $orderModel->items()->create([
                'product_id' => $item->getProduct()->getId()->getValue(),
                'quantity' => $item->getQuantity(),
                'price' => $item->getPrice()->toPesos(),
            ]);
        }
    }
}
```

### Controlador con Paginación y Filtros

```php
<?php

namespace App\Infrastructure\Http\Controllers\Api\V1;

use App\Application\Queries\GetOrdersQuery;
use App\Application\UseCases\Order\GetOrdersUseCase;
use App\Infrastructure\Http\Resources\OrderCollection;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;

class OrderController extends Controller
{
    public function __construct(
        private GetOrdersUseCase $getOrdersUseCase
    ) {}

    public function index(Request $request): JsonResponse
    {
        $query = new GetOrdersQuery(
            userId: $request->input('user_id'),
            status: $request->input('status'),
            page: (int) $request->input('page', 1),
            perPage: (int) $request->input('per_page', 15),
            sortBy: $request->input('sort_by', 'created_at'),
            sortDirection: $request->input('sort_direction', 'desc')
        );

        $result = $this->getOrdersUseCase->execute($query);

        return response()->json([
            'data' => new OrderCollection($result->orders),
            'meta' => [
                'current_page' => $result->currentPage,
                'per_page' => $result->perPage,
                'total' => $result->total,
                'total_pages' => $result->totalPages,
            ]
        ]);
    }
}
```

## Navegación

[⬅️ Implementación Application Layer](./implementacion-application-layer.md) |
[🏠 README Principal](../../README.md) |
[APIs RESTful Versionadas ➡️](./apis-restful-versionadas.md)
