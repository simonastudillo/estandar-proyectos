# Ejemplos de Testing por Lenguaje

## ¿Qué es?

Esta documentación proporciona ejemplos prácticos y específicos de
implementación de tests para cada lenguaje y framework del stack tecnológico.
Incluye patrones de testing, casos de uso reales, mejores prácticas de
implementación y ejemplos de código que pueden ser utilizados como plantillas
para escribir tests efectivos en cada tecnología.

## ¿Por qué es importante?

- **Ejemplos concretos**: Proporciona plantillas reales de testing para cada
  tecnología
- **Mejores prácticas**: Demuestra patrones correctos de testing por lenguaje
- **Consistencia**: Establece un estándar de calidad en la escritura de tests
- **Aprendizaje**: Facilita el onboarding de nuevos desarrolladores
- **Referencia rápida**: Permite consultar ejemplos específicos sin buscar en
  múltiples fuentes
- **Patterns**: Muestra patrones de testing específicos para cada arquitectura
- **Troubleshooting**: Incluye soluciones a problemas comunes de testing

## ¿Qué debe incluir?

### Testing Frontend (React + TypeScript + Jest)

#### Tests Unitarios de Componentes

```typescript
// src/components/common/Button/Button.test.tsx
import React from "react";
import { fireEvent, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { Button } from "./Button";

describe("Button Component", () => {
   it("should render with correct text", () => {
      render(<Button>Click me</Button>);

      expect(screen.getByRole("button", { name: /click me/i }))
         .toBeInTheDocument();
   });

   it("should handle click events", async () => {
      const handleClick = jest.fn();
      const user = userEvent.setup();

      render(<Button onClick={handleClick}>Click me</Button>);

      await user.click(screen.getByRole("button"));

      expect(handleClick).toHaveBeenCalledTimes(1);
   });

   it("should be disabled when loading", () => {
      render(<Button loading>Submit</Button>);

      expect(screen.getByRole("button")).toBeDisabled();
      expect(screen.getByTestId("loading-spinner")).toBeInTheDocument();
   });

   it("should apply correct variant styles", () => {
      render(<Button variant="danger">Delete</Button>);

      expect(screen.getByRole("button")).toHaveClass("btn-danger");
   });

   it("should handle async operations", async () => {
      const asyncHandler = jest.fn().mockResolvedValue("success");
      const user = userEvent.setup();

      render(<Button onClick={asyncHandler}>Async Action</Button>);

      await user.click(screen.getByRole("button"));

      expect(asyncHandler).toHaveBeenCalled();
      await expect(asyncHandler).resolves.toBe("success");
   });
});
```

#### Tests de Custom Hooks

```typescript
// src/hooks/useAuth.test.ts
import { act, renderHook } from "@testing-library/react";
import { Provider } from "react-redux";
import { configureStore } from "@reduxjs/toolkit";
import { useAuth } from "./useAuth";
import { authSlice } from "@/store/slices/authSlice";

const createWrapper = (initialState = {}) => {
   const store = configureStore({
      reducer: { auth: authSlice.reducer },
      preloadedState: { auth: initialState },
   });

   return ({ children }: { children: React.ReactNode }) => (
      <Provider store={store}>{children}</Provider>
   );
};

describe("useAuth Hook", () => {
   it("should return initial auth state", () => {
      const { result } = renderHook(() => useAuth(), {
         wrapper: createWrapper(),
      });

      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.user).toBeNull();
      expect(result.current.loading).toBe(false);
   });

   it("should handle login successfully", async () => {
      const { result } = renderHook(() => useAuth(), {
         wrapper: createWrapper(),
      });

      await act(async () => {
         await result.current.login("test@example.com", "password123");
      });

      expect(result.current.isAuthenticated).toBe(true);
      expect(result.current.user).toEqual({
         id: 1,
         email: "test@example.com",
         name: "Test User",
      });
   });

   it("should handle login errors", async () => {
      const { result } = renderHook(() => useAuth(), {
         wrapper: createWrapper(),
      });

      await act(async () => {
         try {
            await result.current.login("invalid@example.com", "wrongpass");
         } catch (error) {
            expect(error.message).toBe("Invalid credentials");
         }
      });

      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.error).toBeTruthy();
   });

   it("should logout user", async () => {
      const initialState = {
         isAuthenticated: true,
         user: { id: 1, email: "test@example.com", name: "Test User" },
         token: "fake-token",
      };

      const { result } = renderHook(() => useAuth(), {
         wrapper: createWrapper(initialState),
      });

      await act(async () => {
         result.current.logout();
      });

      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.user).toBeNull();
   });
});
```

#### Tests de Redux Slices

```typescript
// src/store/slices/userSlice.test.ts
import { configureStore } from "@reduxjs/toolkit";
import userSlice, {
   createUser,
   deleteUser,
   fetchUsers,
   updateUser,
   UserState,
} from "./userSlice";

describe("userSlice", () => {
   let store: ReturnType<typeof configureStore>;

   beforeEach(() => {
      store = configureStore({
         reducer: { users: userSlice.reducer },
      });
   });

   it("should return initial state", () => {
      const state = store.getState().users;

      expect(state).toEqual({
         users: [],
         loading: false,
         error: null,
         currentPage: 1,
         totalPages: 0,
      });
   });

   it("should handle fetchUsers.pending", () => {
      store.dispatch(fetchUsers.pending("requestId", { page: 1 }));

      const state = store.getState().users;
      expect(state.loading).toBe(true);
      expect(state.error).toBeNull();
   });

   it("should handle fetchUsers.fulfilled", () => {
      const mockUsers = [
         { id: 1, name: "John Doe", email: "john@example.com" },
         { id: 2, name: "Jane Smith", email: "jane@example.com" },
      ];

      store.dispatch(fetchUsers.fulfilled(
         {
            users: mockUsers,
            totalPages: 5,
            currentPage: 1,
         },
         "requestId",
         { page: 1 },
      ));

      const state = store.getState().users;
      expect(state.loading).toBe(false);
      expect(state.users).toEqual(mockUsers);
      expect(state.totalPages).toBe(5);
      expect(state.currentPage).toBe(1);
   });

   it("should handle fetchUsers.rejected", () => {
      const errorMessage = "Failed to fetch users";

      store.dispatch(fetchUsers.rejected(
         new Error(errorMessage),
         "requestId",
         { page: 1 },
      ));

      const state = store.getState().users;
      expect(state.loading).toBe(false);
      expect(state.error).toBe(errorMessage);
   });

   it("should handle createUser.fulfilled", () => {
      const newUser = { id: 3, name: "New User", email: "new@example.com" };

      store.dispatch(createUser.fulfilled(newUser, "requestId", {
         name: "New User",
         email: "new@example.com",
      }));

      const state = store.getState().users;
      expect(state.users).toContain(newUser);
   });
});
```

#### Tests de Integración con APIs

```typescript
// src/services/userService.test.ts
import { rest } from "msw";
import { setupServer } from "msw/node";
import { userService } from "./userService";

const server = setupServer(
   rest.get("/api/v1/users", (req, res, ctx) => {
      const page = req.url.searchParams.get("page") || "1";

      return res(
         ctx.status(200),
         ctx.json({
            data: [
               { id: 1, name: "John Doe", email: "john@example.com" },
               { id: 2, name: "Jane Smith", email: "jane@example.com" },
            ],
            meta: {
               current_page: parseInt(page),
               total_pages: 5,
               total: 50,
            },
         }),
      );
   }),
   rest.post("/api/v1/users", (req, res, ctx) => {
      return res(
         ctx.status(201),
         ctx.json({
            data: { id: 3, name: "New User", email: "new@example.com" },
         }),
      );
   }),
   rest.get("/api/v1/users/:id", (req, res, ctx) => {
      const { id } = req.params;

      if (id === "999") {
         return res(
            ctx.status(404),
            ctx.json({ message: "User not found" }),
         );
      }

      return res(
         ctx.status(200),
         ctx.json({
            data: { id: Number(id), name: "User", email: "user@example.com" },
         }),
      );
   }),
);

beforeAll(() => server.listen({ onUnhandledRequest: "error" }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe("UserService", () => {
   it("should fetch users with pagination", async () => {
      const result = await userService.getUsers({ page: 1, limit: 10 });

      expect(result.data).toHaveLength(2);
      expect(result.meta.current_page).toBe(1);
      expect(result.meta.total_pages).toBe(5);
   });

   it("should create user successfully", async () => {
      const userData = { name: "New User", email: "new@example.com" };
      const result = await userService.createUser(userData);

      expect(result.data).toEqual({
         id: 3,
         name: "New User",
         email: "new@example.com",
      });
   });

   it("should handle API errors", async () => {
      await expect(userService.getUser(999)).rejects.toThrow("User not found");
   });

   it("should handle network errors", async () => {
      server.use(
         rest.get("/api/v1/users", (req, res, ctx) => {
            return res.networkError("Failed to connect");
         }),
      );

      await expect(userService.getUsers()).rejects.toThrow();
   });
});
```

### Testing Backend (Laravel + PHPUnit + Clean Architecture)

#### Tests Unitarios de Domain Layer

```php
<?php
// tests/Unit/Domain/Entities/UserTest.php
namespace Tests\Unit\Domain\Entities;

use Tests\Unit\UnitTestCase;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\InvalidEmailException;

class UserTest extends UnitTestCase
{
    public function test_can_create_user_with_valid_data(): void
    {
        $userId = UserId::generate();
        $email = new Email('test@example.com');
        $name = 'John Doe';

        $user = new User($userId, $email, $name);

        $this->assertEquals($userId, $user->getId());
        $this->assertEquals($email, $user->getEmail());
        $this->assertEquals($name, $user->getName());
        $this->assertFalse($user->isEmailVerified());
    }

    public function test_user_can_verify_email(): void
    {
        $user = $this->createUser();

        $user->verifyEmail();

        $this->assertTrue($user->isEmailVerified());
    }

    public function test_user_can_change_email(): void
    {
        $user = $this->createUser();
        $newEmail = new Email('newemail@example.com');

        $user->changeEmail($newEmail);

        $this->assertEquals($newEmail, $user->getEmail());
        $this->assertFalse($user->isEmailVerified());
    }

    public function test_cannot_create_user_with_invalid_email(): void
    {
        $this->expectException(InvalidEmailException::class);

        new Email('invalid-email');
    }

    public function test_user_can_be_activated_and_deactivated(): void
    {
        $user = $this->createUser();

        $this->assertTrue($user->isActive());

        $user->deactivate();
        $this->assertFalse($user->isActive());

        $user->activate();
        $this->assertTrue($user->isActive());
    }

    private function createUser(): User
    {
        return new User(
            UserId::generate(),
            new Email('test@example.com'),
            'John Doe'
        );
    }
}
```

#### Tests de Application Layer (Use Cases)

```php
<?php
// tests/Unit/Application/UseCases/CreateUserUseCaseTest.php
namespace Tests\Unit\Application\UseCases;

use Tests\Unit\UnitTestCase;
use App\Application\UseCases\CreateUserUseCase;
use App\Application\DTOs\CreateUserDTO;
use App\Domain\Repositories\UserRepositoryInterface;
use App\Domain\Services\EmailVerificationService;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Exceptions\UserAlreadyExistsException;
use Mockery;

class CreateUserUseCaseTest extends UnitTestCase
{
    private UserRepositoryInterface $userRepository;
    private EmailVerificationService $emailService;
    private CreateUserUseCase $useCase;

    protected function setUp(): void
    {
        parent::setUp();

        $this->userRepository = Mockery::mock(UserRepositoryInterface::class);
        $this->emailService = Mockery::mock(EmailVerificationService::class);
        
        $this->useCase = new CreateUserUseCase(
            $this->userRepository,
            $this->emailService
        );
    }

    public function test_can_create_user_successfully(): void
    {
        $dto = new CreateUserDTO(
            name: 'John Doe',
            email: 'john@example.com',
            password: 'securepassword123'
        );

        $this->userRepository
            ->shouldReceive('findByEmail')
            ->with(Mockery::type(Email::class))
            ->once()
            ->andReturn(null);

        $this->userRepository
            ->shouldReceive('save')
            ->with(Mockery::type(User::class))
            ->once()
            ->andReturn(true);

        $this->emailService
            ->shouldReceive('sendVerificationEmail')
            ->with(Mockery::type(User::class))
            ->once();

        $result = $this->useCase->execute($dto);

        $this->assertInstanceOf(User::class, $result);
        $this->assertEquals('John Doe', $result->getName());
        $this->assertEquals('john@example.com', $result->getEmail()->getValue());
    }

    public function test_throws_exception_when_user_already_exists(): void
    {
        $dto = new CreateUserDTO(
            name: 'John Doe',
            email: 'existing@example.com',
            password: 'password123'
        );

        $existingUser = new User(
            UserId::generate(),
            new Email('existing@example.com'),
            'Existing User'
        );

        $this->userRepository
            ->shouldReceive('findByEmail')
            ->with(Mockery::type(Email::class))
            ->once()
            ->andReturn($existingUser);

        $this->expectException(UserAlreadyExistsException::class);

        $this->useCase->execute($dto);
    }

    public function test_handles_repository_failure(): void
    {
        $dto = new CreateUserDTO(
            name: 'John Doe',
            email: 'john@example.com',
            password: 'password123'
        );

        $this->userRepository
            ->shouldReceive('findByEmail')
            ->once()
            ->andReturn(null);

        $this->userRepository
            ->shouldReceive('save')
            ->once()
            ->andReturn(false);

        $this->expectException(\RuntimeException::class);
        $this->expectExceptionMessage('Failed to save user');

        $this->useCase->execute($dto);
    }
}
```

#### Tests de Integration Layer

```php
<?php
// tests/Integration/Infrastructure/Persistence/UserRepositoryTest.php
namespace Tests\Integration\Infrastructure\Persistence;

use Tests\Integration\IntegrationTestCase;
use App\Infrastructure\Persistence\Eloquent\UserRepository;
use App\Infrastructure\Persistence\Eloquent\UserModel;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;

class UserRepositoryTest extends IntegrationTestCase
{
    private UserRepository $repository;

    protected function setUp(): void
    {
        parent::setUp();
        $this->repository = new UserRepository();
    }

    public function test_can_save_and_find_user(): void
    {
        $user = new User(
            UserId::generate(),
            new Email('test@example.com'),
            'Test User'
        );

        $this->repository->save($user);

        $foundUser = $this->repository->findByEmail(new Email('test@example.com'));

        $this->assertNotNull($foundUser);
        $this->assertEquals($user->getId()->getValue(), $foundUser->getId()->getValue());
        $this->assertEquals($user->getEmail()->getValue(), $foundUser->getEmail()->getValue());
        $this->assertEquals($user->getName(), $foundUser->getName());
    }

    public function test_can_update_existing_user(): void
    {
        $user = new User(
            UserId::generate(),
            new Email('original@example.com'),
            'Original Name'
        );

        $this->repository->save($user);

        $user->changeEmail(new Email('updated@example.com'));
        $this->repository->save($user);

        $updatedUser = $this->repository->findById($user->getId());
        
        $this->assertEquals('updated@example.com', $updatedUser->getEmail()->getValue());
    }

    public function test_can_delete_user(): void
    {
        $user = new User(
            UserId::generate(),
            new Email('delete@example.com'),
            'To Delete'
        );

        $this->repository->save($user);
        
        $this->assertNotNull($this->repository->findById($user->getId()));

        $this->repository->delete($user->getId());
        
        $this->assertNull($this->repository->findById($user->getId()));
    }

    public function test_returns_null_when_user_not_found(): void
    {
        $result = $this->repository->findByEmail(new Email('nonexistent@example.com'));
        
        $this->assertNull($result);
    }

    public function test_can_find_users_with_pagination(): void
    {
        // Create multiple users
        for ($i = 1; $i <= 15; $i++) {
            $user = new User(
                UserId::generate(),
                new Email("user{$i}@example.com"),
                "User {$i}"
            );
            $this->repository->save($user);
        }

        $result = $this->repository->findAll(page: 1, limit: 10);

        $this->assertCount(10, $result->getUsers());
        $this->assertEquals(1, $result->getCurrentPage());
        $this->assertEquals(2, $result->getTotalPages());
        $this->assertEquals(15, $result->getTotalCount());
    }
}
```

#### Tests de Feature (End-to-End API)

```php
<?php
// tests/Feature/Api/V1/UserControllerTest.php
namespace Tests\Feature\Api\V1;

use Tests\Feature\FeatureTestCase;
use App\Infrastructure\Persistence\Eloquent\UserModel;
use Laravel\Sanctum\Sanctum;

class UserControllerTest extends FeatureTestCase
{
    public function test_can_get_paginated_users(): void
    {
        $user = $this->createAuthenticatedUser();
        UserModel::factory()->count(15)->create();

        $response = $this->getJson('/api/v1/users?page=1&limit=10');

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'data' => [
                        '*' => ['id', 'name', 'email', 'created_at']
                    ],
                    'meta' => [
                        'current_page',
                        'total_pages',
                        'total_count',
                        'per_page'
                    ],
                    'message',
                    'status'
                ])
                ->assertJsonPath('meta.current_page', 1)
                ->assertJsonPath('meta.per_page', 10);
    }

    public function test_can_create_user(): void
    {
        $admin = $this->createAuthenticatedUser(['role' => 'admin']);

        $userData = [
            'name' => 'New User',
            'email' => 'newuser@example.com',
            'password' => 'securepassword123',
            'password_confirmation' => 'securepassword123'
        ];

        $response = $this->postJson('/api/v1/users', $userData);

        $response->assertStatus(201)
                ->assertJsonStructure([
                    'data' => ['id', 'name', 'email', 'created_at'],
                    'message',
                    'status'
                ])
                ->assertJsonPath('data.name', 'New User')
                ->assertJsonPath('data.email', 'newuser@example.com');

        $this->assertDatabaseHas('users', [
            'name' => 'New User',
            'email' => 'newuser@example.com'
        ]);
    }

    public function test_validates_user_creation_data(): void
    {
        $admin = $this->createAuthenticatedUser(['role' => 'admin']);

        $invalidData = [
            'name' => '',
            'email' => 'invalid-email',
            'password' => '123'
        ];

        $response = $this->postJson('/api/v1/users', $invalidData);

        $response->assertStatus(422)
                ->assertJsonValidationErrors(['name', 'email', 'password']);
    }

    public function test_cannot_create_user_with_existing_email(): void
    {
        $admin = $this->createAuthenticatedUser(['role' => 'admin']);
        
        UserModel::factory()->create(['email' => 'existing@example.com']);

        $userData = [
            'name' => 'Another User',
            'email' => 'existing@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        $response = $this->postJson('/api/v1/users', $userData);

        $response->assertStatus(422)
                ->assertJsonValidationErrors(['email']);
    }

    public function test_requires_authentication(): void
    {
        $response = $this->getJson('/api/v1/users');

        $response->assertStatus(401);
    }

    public function test_requires_admin_role_for_user_creation(): void
    {
        $user = $this->createAuthenticatedUser(['role' => 'user']);

        $userData = [
            'name' => 'New User',
            'email' => 'newuser@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        $response = $this->postJson('/api/v1/users', $userData);

        $response->assertStatus(403);
    }
}
```

### Testing Mobile (React Native + Jest + Detox)

#### Tests Unitarios de Componentes Móviles

```typescript
// src/components/UserProfile/UserProfile.test.tsx
import React from "react";
import { fireEvent, render } from "@testing-library/react-native";
import { Provider } from "react-redux";
import { UserProfile } from "./UserProfile";
import { createMockStore } from "../../__tests__/utils/mockStore";

const mockNavigation = {
   navigate: jest.fn(),
   goBack: jest.fn(),
   dispatch: jest.fn(),
};

describe("UserProfile Component", () => {
   it("should render user information correctly", () => {
      const mockUser = {
         id: 1,
         name: "John Doe",
         email: "john@example.com",
         avatar: "https://example.com/avatar.jpg",
      };

      const store = createMockStore({
         auth: { user: mockUser, isAuthenticated: true },
      });

      const { getByText, getByTestId } = render(
         <Provider store={store}>
            <UserProfile navigation={mockNavigation} />
         </Provider>,
      );

      expect(getByText("John Doe")).toBeTruthy();
      expect(getByText("john@example.com")).toBeTruthy();
      expect(getByTestId("user-avatar")).toBeTruthy();
   });

   it("should handle edit profile button press", () => {
      const store = createMockStore({
         auth: {
            user: { id: 1, name: "John", email: "john@example.com" },
            isAuthenticated: true,
         },
      });

      const { getByTestId } = render(
         <Provider store={store}>
            <UserProfile navigation={mockNavigation} />
         </Provider>,
      );

      const editButton = getByTestId("edit-profile-button");
      fireEvent.press(editButton);

      expect(mockNavigation.navigate).toHaveBeenCalledWith("EditProfile");
   });

   it("should handle logout", () => {
      const store = createMockStore({
         auth: {
            user: { id: 1, name: "John", email: "john@example.com" },
            isAuthenticated: true,
         },
      });

      const { getByTestId } = render(
         <Provider store={store}>
            <UserProfile navigation={mockNavigation} />
         </Provider>,
      );

      const logoutButton = getByTestId("logout-button");
      fireEvent.press(logoutButton);

      // Verify that logout action was dispatched
      const actions = store.getActions();
      expect(actions).toContainEqual(
         expect.objectContaining({ type: "auth/logout" }),
      );
   });
});
```

#### Tests E2E con Detox

```typescript
// e2e/user-authentication.e2e.ts
import { by, device, element, expect } from "detox";

describe("User Authentication Flow", () => {
   beforeAll(async () => {
      await device.launchApp();
   });

   beforeEach(async () => {
      await device.reloadReactNative();
   });

   it("should show login screen on app launch", async () => {
      await expect(element(by.testID("login-screen"))).toBeVisible();
      await expect(element(by.testID("email-input"))).toBeVisible();
      await expect(element(by.testID("password-input"))).toBeVisible();
      await expect(element(by.testID("login-button"))).toBeVisible();
   });

   it("should login with valid credentials", async () => {
      await element(by.testID("email-input")).typeText("test@example.com");
      await element(by.testID("password-input")).typeText("password123");
      await element(by.testID("login-button")).tap();

      // Wait for navigation to home screen
      await expect(element(by.testID("home-screen"))).toBeVisible();
      await expect(element(by.text("Welcome, Test User!"))).toBeVisible();
   });

   it("should show error for invalid credentials", async () => {
      await element(by.testID("email-input")).typeText("invalid@example.com");
      await element(by.testID("password-input")).typeText("wrongpassword");
      await element(by.testID("login-button")).tap();

      await expect(element(by.testID("error-message"))).toBeVisible();
      await expect(element(by.text("Invalid credentials"))).toBeVisible();
   });

   it("should navigate to signup screen", async () => {
      await element(by.testID("signup-link")).tap();

      await expect(element(by.testID("signup-screen"))).toBeVisible();
      await expect(element(by.testID("name-input"))).toBeVisible();
      await expect(element(by.testID("email-input"))).toBeVisible();
      await expect(element(by.testID("password-input"))).toBeVisible();
      await expect(element(by.testID("signup-button"))).toBeVisible();
   });

   it("should complete full user registration flow", async () => {
      // Navigate to signup
      await element(by.testID("signup-link")).tap();

      // Fill signup form
      await element(by.testID("name-input")).typeText("New User");
      await element(by.testID("email-input")).typeText("newuser@example.com");
      await element(by.testID("password-input")).typeText("securepassword123");
      await element(by.testID("confirm-password-input")).typeText(
         "securepassword123",
      );

      // Submit form
      await element(by.testID("signup-button")).tap();

      // Verify success
      await expect(element(by.testID("success-message"))).toBeVisible();
      await expect(element(by.text("Account created successfully!")))
         .toBeVisible();

      // Should navigate to login screen
      await expect(element(by.testID("login-screen"))).toBeVisible();
   });

   it("should maintain user session after app restart", async () => {
      // Login first
      await element(by.testID("email-input")).typeText("test@example.com");
      await element(by.testID("password-input")).typeText("password123");
      await element(by.testID("login-button")).tap();

      await expect(element(by.testID("home-screen"))).toBeVisible();

      // Restart app
      await device.terminateApp();
      await device.launchApp();

      // Should still be logged in
      await expect(element(by.testID("home-screen"))).toBeVisible();
   });
});
```

#### Tests de Performance Móvil

```typescript
// e2e/performance.e2e.ts
import { by, device, element, expect } from "detox";

describe("Performance Tests", () => {
   beforeAll(async () => {
      await device.launchApp();
   });

   it("should load home screen within acceptable time", async () => {
      const startTime = Date.now();

      await element(by.testID("email-input")).typeText("test@example.com");
      await element(by.testID("password-input")).typeText("password123");
      await element(by.testID("login-button")).tap();

      await expect(element(by.testID("home-screen"))).toBeVisible();

      const loadTime = Date.now() - startTime;
      expect(loadTime).toBeLessThan(3000); // 3 seconds max
   });

   it("should handle list scrolling smoothly", async () => {
      await element(by.testID("users-list")).scroll(1000, "down");
      await element(by.testID("users-list")).scroll(1000, "up");

      // Verify list is still functional
      await expect(element(by.testID("users-list"))).toBeVisible();
   });

   it("should handle network delays gracefully", async () => {
      // Simulate slow network
      await device.setNetworkConditions({
         name: "slow",
         speed: "100kb",
         latency: "1000ms",
      });

      await element(by.testID("refresh-button")).tap();

      // Should show loading indicator
      await expect(element(by.testID("loading-indicator"))).toBeVisible();

      // Eventually should load content
      await expect(element(by.testID("content-container"))).toBeVisible();

      // Reset network conditions
      await device.setNetworkConditions({ name: "default" });
   });
});
```

## ¿Qué debo hacer?

### 1. Implementar estructura de testing por proyecto

```bash
# Frontend
mkdir -p src/__tests__/{components,hooks,services,utils}
mkdir -p src/__tests__/__mocks__

# Backend
mkdir -p tests/{Unit,Integration,Feature}
mkdir -p tests/Unit/{Domain,Application}
mkdir -p tests/Integration/{Infrastructure,Services}

# Mobile
mkdir -p e2e/{tests,utils,config}
mkdir -p src/__tests__/{components,screens,navigation}
```

### 2. Configurar scripts de testing específicos

```json
// Frontend package.json
{
   "scripts": {
      "test:unit": "jest --testPathPattern=src/__tests__",
      "test:integration": "jest --testPathPattern=integration",
      "test:component": "jest --testPathPattern=components",
      "test:hooks": "jest --testPathPattern=hooks"
   }
}
```

```json
// Backend composer.json
{
   "scripts": {
      "test:domain": "phpunit --testsuite=Unit --filter=Domain",
      "test:application": "phpunit --testsuite=Unit --filter=Application",
      "test:infrastructure": "phpunit --testsuite=Integration",
      "test:api": "phpunit --testsuite=Feature"
   }
}
```

### 3. Establecer convenciones de nomenclatura

- **Frontend**: `ComponentName.test.tsx`, `hookName.test.ts`
- **Backend**: `ClassNameTest.php`, ubicados en estructura que refleje `app/`
- **Mobile**: `ScreenName.test.tsx`, `feature-name.e2e.ts`

### 4. Configurar coverage por tipo de test

```javascript
// jest.config.js - Frontend
module.exports = {
   collectCoverageFrom: [
      "src/components/**/*.{ts,tsx}",
      "src/hooks/**/*.ts",
      "!src/**/*.stories.{ts,tsx}",
   ],
   coverageThreshold: {
      "src/components/": { statements: 90 },
      "src/hooks/": { statements: 85 },
      "src/services/": { statements: 80 },
   },
};
```

### 5. Implementar utilidades de testing reutilizables

```typescript
// src/__tests__/utils/renderWithProviders.tsx
export const renderWithProviders = (
   ui: ReactElement,
   {
      preloadedState = {},
      store = setupStore(preloadedState),
      ...renderOptions
   } = {},
) => {
   function Wrapper({ children }: PropsWithChildren<{}>) {
      return (
         <Provider store={store}>
            <BrowserRouter>
               <ThemeProvider theme={defaultTheme}>
                  {children}
               </ThemeProvider>
            </BrowserRouter>
         </Provider>
      );
   }

   return { store, ...render(ui, { wrapper: Wrapper, ...renderOptions }) };
};
```

## Tips

- **Organización**: Mantén la estructura de tests reflejando la estructura del
  código
- **Nomenclatura**: Usa nombres descriptivos que expliquen qué se está probando
- **Isolation**: Cada test debe ser independiente y no depender de otros tests
- **Data Setup**: Usa factories y builders para crear datos de prueba
  consistentes
- **Mocking**: Mock dependencias externas, mantén mocks simples y específicos
- **Assertions**: Usa assertions específicas, evita assertions genéricas
- **Performance**: Agrupa tests relacionados, usa `describe` blocks para
  organizar

## Ejemplos de Patrones Avanzados

### Test Builder Pattern (Backend)

```php
<?php
// tests/Builders/UserBuilder.php
class UserBuilder
{
    private array $attributes = [];

    public static function create(): self
    {
        return new self();
    }

    public function withName(string $name): self
    {
        $this->attributes['name'] = $name;
        return $this;
    }

    public function withEmail(string $email): self
    {
        $this->attributes['email'] = $email;
        return $this;
    }

    public function verified(): self
    {
        $this->attributes['email_verified_at'] = now();
        return $this;
    }

    public function build(): User
    {
        return new User(
            UserId::generate(),
            new Email($this->attributes['email'] ?? 'test@example.com'),
            $this->attributes['name'] ?? 'Test User'
        );
    }
}

// Usage in tests
$user = UserBuilder::create()
    ->withName('John Doe')
    ->withEmail('john@example.com')
    ->verified()
    ->build();
```

### Mock Service Pattern (Frontend)

```typescript
// src/__tests__/mocks/userServiceMock.ts
export const createUserServiceMock = () => ({
   getUsers: jest.fn().mockResolvedValue({
      data: [
         { id: 1, name: "User 1", email: "user1@example.com" },
         { id: 2, name: "User 2", email: "user2@example.com" },
      ],
      meta: { total: 2, page: 1, totalPages: 1 },
   }),

   createUser: jest.fn().mockImplementation((userData) =>
      Promise.resolve({
         data: { id: Date.now(), ...userData },
      })
   ),

   updateUser: jest.fn().mockImplementation((id, userData) =>
      Promise.resolve({
         data: { id, ...userData },
      })
   ),

   deleteUser: jest.fn().mockResolvedValue(true),
});
```

## Navegación

[⬅️ Configuración de Herramientas de Testing](./configuracion-herramientas-testing.md)
| [Inicio](../../README.md) |
[Testing funcional automatizado ➡️](./testing-funcional-automatizado.md)
