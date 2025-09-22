# Testing Funcional Automatizado

## Conocimientos involucrados
- Estrategias de testing automatizado
- Herramientas como Jest, PHPUnit, Cypress
- Buenas prácticas de QA

## Responsable
- Equipo de QA y desarrollo

# Testing Funcional Automatizado

## ¿Qué es?

El testing funcional automatizado es el proceso de verificar que el software
cumple con los requerimientos funcionales especificados mediante la ejecución
automática de pruebas. Incluye pruebas unitarias, de integración, end-to-end y
de API que validan que cada funcionalidad del sistema trabaje correctamente
según las especificaciones.

## ¿Por qué es importante?

- **Detección temprana**: Identifica problemas antes de que lleguen a producción
- **Regresión**: Evita que nuevos cambios rompan funcionalidades existentes
- **Confianza en el código**: Permite refactorizar y mejorar sin miedo
- **Documentación viva**: Los tests sirven como documentación de comportamiento
- **Velocidad de desarrollo**: Acelera el ciclo de desarrollo con feedback
  inmediato
- **Reducción de costos**: Es más barato arreglar bugs en desarrollo que en
  producción
- **Calidad consistente**: Mantiene estándares de calidad en todo el equipo

## ¿Qué debe incluir?

### Pruebas Unitarias

- Tests para cada caso de uso del dominio
- Validación de entidades y value objects
- Pruebas de servicios de dominio
- Tests de repositorios (con mocks)
- Validación de reglas de negocio

### Pruebas de Integración

- Tests de APIs REST completas
- Integración entre capas de la aplicación
- Conexión con base de datos real
- Validación de flujos entre componentes
- Tests de middleware y autenticación

### Pruebas End-to-End

- Flujos completos de usuario
- Navegación entre páginas
- Formularios y validaciones
- Interacciones con APIs
- Estados de la aplicación

### Pruebas de Componentes

- Renderizado de componentes React
- Interacciones del usuario
- Estados y props
- Hooks personalizados
- Manejo de errores

## ¿Qué debo hacer?

### 1. Configurar el entorno de testing

#### Backend (Laravel + PHPUnit)

```bash
# Instalar dependencias
composer require --dev phpunit/phpunit
composer require --dev mockery/mockery
composer require --dev fakerphp/faker

# Configurar base de datos de testing
cp .env .env.testing
```

#### Frontend (React + Jest + Testing Library)

```bash
# Instalar dependencias
npm install --save-dev jest @testing-library/react
npm install --save-dev @testing-library/jest-dom
npm install --save-dev @testing-library/user-event
npm install --save-dev jest-environment-jsdom
```

### 2. Estructura de archivos de testing

#### Backend

```
tests/
├── Feature/                    # Tests de integración
│   ├── Api/
│   │   └── V1/
│   │       ├── AuthTest.php
│   │       ├── UserTest.php
│   │       └── ProductTest.php
│   └── Web/
│       └── HomeTest.php
├── Unit/                       # Tests unitarios
│   ├── Domain/
│   │   ├── Entities/
│   │   │   ├── UserTest.php
│   │   │   └── ProductTest.php
│   │   ├── ValueObjects/
│   │   │   ├── EmailTest.php
│   │   │   └── MoneyTest.php
│   │   └── Services/
│   │       └── UserServiceTest.php
│   └── Application/
│       └── UseCases/
│           ├── CreateUserUseCaseTest.php
│           └── UpdateUserUseCaseTest.php
└── TestCase.php
```

#### Frontend

```
src/
├── __tests__/
│   ├── App.test.tsx
│   └── setupTests.ts
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   └── Button.test.tsx
│   └── Form/
│       ├── LoginForm.tsx
│       └── LoginForm.test.tsx
├── hooks/
│   ├── useAuth.ts
│   └── useAuth.test.ts
├── services/
│   ├── api.ts
│   └── api.test.ts
└── utils/
    ├── validation.ts
    └── validation.test.ts
```

### 3. Implementar pruebas unitarias

#### Ejemplo para Backend (UseCase)

```php
<?php

namespace Tests\Unit\Application\UseCases;

use Tests\TestCase;
use Mockery;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Application\UseCases\CreateUserUseCase;
use App\Domain\Repositories\UserRepository;
use App\Application\DTOs\UserDTO;

class CreateUserUseCaseTest extends TestCase
{
    private $userRepository;
    private $createUserUseCase;

    protected function setUp(): void
    {
        parent::setUp();
        $this->userRepository = Mockery::mock(UserRepository::class);
        $this->createUserUseCase = new CreateUserUseCase($this->userRepository);
    }

    public function test_should_create_user_successfully()
    {
        // Arrange
        $userData = new UserDTO([
            'name' => 'Juan Pérez',
            'email' => 'juan@example.com',
            'password' => 'password123'
        ]);

        $expectedUser = new User(
            'Juan Pérez',
            new Email('juan@example.com'),
            'hashedPassword'
        );

        $this->userRepository
            ->shouldReceive('findByEmail')
            ->with($userData->email)
            ->once()
            ->andReturn(null);

        $this->userRepository
            ->shouldReceive('save')
            ->once()
            ->andReturn($expectedUser);

        // Act
        $result = $this->createUserUseCase->execute($userData);

        // Assert
        $this->assertInstanceOf(User::class, $result);
        $this->assertEquals('Juan Pérez', $result->getName());
        $this->assertEquals('juan@example.com', $result->getEmail()->getValue());
    }

    public function test_should_throw_exception_when_email_already_exists()
    {
        // Arrange
        $userData = new UserDTO([
            'name' => 'Juan Pérez',
            'email' => 'juan@example.com',
            'password' => 'password123'
        ]);

        $existingUser = new User(
            'Juan Existente',
            new Email('juan@example.com'),
            'hashedPassword'
        );

        $this->userRepository
            ->shouldReceive('findByEmail')
            ->with($userData->email)
            ->once()
            ->andReturn($existingUser);

        // Act & Assert
        $this->expectException(\App\Domain\Exceptions\UserAlreadyExistsException::class);
        $this->createUserUseCase->execute($userData);
    }

    protected function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }
}
```

#### Ejemplo para Frontend (Componente)

```typescript
// src/components/LoginForm/LoginForm.test.tsx
import React from "react";
import { fireEvent, render, screen, waitFor } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { Provider } from "react-redux";
import { BrowserRouter } from "react-router-dom";
import { configureStore } from "@reduxjs/toolkit";
import LoginForm from "./LoginForm";
import authSlice from "../../store/slices/authSlice";

// Mock del servicio de API
jest.mock("../../services/authService", () => ({
   login: jest.fn(),
}));

import { login as mockLogin } from "../../services/authService";
const mockLoginFn = mockLogin as jest.MockedFunction<typeof mockLogin>;

describe("LoginForm", () => {
   let store: any;
   let user: any;

   beforeEach(() => {
      store = configureStore({
         reducer: {
            auth: authSlice,
         },
      });

      user = userEvent.setup();
      jest.clearAllMocks();
   });

   const renderWithProviders = (component: React.ReactElement) => {
      return render(
         <Provider store={store}>
            <BrowserRouter>{component}</BrowserRouter>
         </Provider>,
      );
   };

   it("should render login form fields", () => {
      renderWithProviders(<LoginForm />);

      expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
      expect(screen.getByLabelText(/contraseña/i)).toBeInTheDocument();
      expect(
         screen.getByRole("button", { name: /iniciar sesión/i }),
      ).toBeInTheDocument();
   });

   it("should show validation errors for empty fields", async () => {
      renderWithProviders(<LoginForm />);

      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });
      await user.click(submitButton);

      expect(await screen.findByText(/email es requerido/i))
         .toBeInTheDocument();
      expect(
         await screen.findByText(/contraseña es requerida/i),
      ).toBeInTheDocument();
   });

   it("should show error for invalid email format", async () => {
      renderWithProviders(<LoginForm />);

      const emailInput = screen.getByLabelText(/email/i);
      await user.type(emailInput, "email-invalido");

      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });
      await user.click(submitButton);

      expect(
         await screen.findByText(/email debe ser válido/i),
      ).toBeInTheDocument();
   });

   it("should submit form with valid credentials", async () => {
      const mockUser = {
         id: 1,
         name: "Juan Pérez",
         email: "juan@example.com",
         token: "mock-token",
      };

      mockLoginFn.mockResolvedValue(mockUser);

      renderWithProviders(<LoginForm />);

      const emailInput = screen.getByLabelText(/email/i);
      const passwordInput = screen.getByLabelText(/contraseña/i);
      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });

      await user.type(emailInput, "juan@example.com");
      await user.type(passwordInput, "password123");
      await user.click(submitButton);

      await waitFor(() => {
         expect(mockLoginFn).toHaveBeenCalledWith({
            email: "juan@example.com",
            password: "password123",
         });
      });
   });

   it("should show error message on login failure", async () => {
      mockLoginFn.mockRejectedValue(new Error("Credenciales inválidas"));

      renderWithProviders(<LoginForm />);

      const emailInput = screen.getByLabelText(/email/i);
      const passwordInput = screen.getByLabelText(/contraseña/i);
      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });

      await user.type(emailInput, "juan@example.com");
      await user.type(passwordInput, "password-incorrecto");
      await user.click(submitButton);

      expect(
         await screen.findByText(/credenciales inválidas/i),
      ).toBeInTheDocument();
   });

   it("should disable submit button while loading", async () => {
      mockLoginFn.mockImplementation(
         () => new Promise((resolve) => setTimeout(resolve, 1000)),
      );

      renderWithProviders(<LoginForm />);

      const emailInput = screen.getByLabelText(/email/i);
      const passwordInput = screen.getByLabelText(/contraseña/i);
      const submitButton = screen.getByRole("button", {
         name: /iniciar sesión/i,
      });

      await user.type(emailInput, "juan@example.com");
      await user.type(passwordInput, "password123");
      await user.click(submitButton);

      expect(submitButton).toBeDisabled();
      expect(screen.getByText(/iniciando sesión.../i)).toBeInTheDocument();
   });
});
```

### 4. Configurar pruebas de integración

#### Ejemplo para Backend (API Test)

```php
<?php

namespace Tests\Feature\Api\V1;

use Tests\TestCase;
use App\Domain\Entities\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;

class UserApiTest extends TestCase
{
    use RefreshDatabase, WithFaker;

    public function test_can_create_user_via_api()
    {
        // Arrange
        $userData = [
            'name' => 'Juan Pérez',
            'email' => 'juan@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        // Act
        $response = $this->postJson('/api/v1/users', $userData);

        // Assert
        $response->assertStatus(201)
                ->assertJsonStructure([
                    'data' => [
                        'id',
                        'name',
                        'email',
                        'created_at'
                    ]
                ]);

        $this->assertDatabaseHas('users', [
            'name' => 'Juan Pérez',
            'email' => 'juan@example.com'
        ]);
    }

    public function test_cannot_create_user_with_existing_email()
    {
        // Arrange
        $existingUser = User::factory()->create([
            'email' => 'juan@example.com'
        ]);

        $userData = [
            'name' => 'Juan Nuevo',
            'email' => 'juan@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ];

        // Act
        $response = $this->postJson('/api/v1/users', $userData);

        // Assert
        $response->assertStatus(422)
                ->assertJsonValidationErrors(['email']);
    }

    public function test_can_get_user_list_with_pagination()
    {
        // Arrange
        User::factory()->count(15)->create();

        // Act
        $response = $this->getJson('/api/v1/users?page=1&per_page=10');

        // Assert
        $response->assertStatus(200)
                ->assertJsonStructure([
                    'data' => [
                        '*' => ['id', 'name', 'email', 'created_at']
                    ],
                    'meta' => [
                        'current_page',
                        'per_page',
                        'total'
                    ]
                ]);

        $this->assertCount(10, $response->json('data'));
    }

    public function test_requires_authentication_for_protected_endpoints()
    {
        // Act
        $response = $this->getJson('/api/v1/users/profile');

        // Assert
        $response->assertStatus(401);
    }

    public function test_authenticated_user_can_access_profile()
    {
        // Arrange
        $user = User::factory()->create();

        // Act
        $response = $this->actingAs($user, 'sanctum')
                        ->getJson('/api/v1/users/profile');

        // Assert
        $response->assertStatus(200)
                ->assertJson([
                    'data' => [
                        'id' => $user->id,
                        'name' => $user->name,
                        'email' => $user->email
                    ]
                ]);
    }
}
```

### 5. Implementar pruebas end-to-end

#### Configuración de Cypress

```bash
# Instalar Cypress
npm install --save-dev cypress

# Configurar Cypress
npx cypress open
```

#### Ejemplo de test E2E

```typescript
// cypress/e2e/user-registration.cy.ts
describe("User Registration Flow", () => {
   beforeEach(() => {
      cy.visit("/register");
   });

   it("should complete user registration successfully", () => {
      // Llenar formulario
      cy.get("[data-testid=name-input]").type("Juan Pérez");
      cy.get("[data-testid=email-input]").type("juan@example.com");
      cy.get("[data-testid=password-input]").type("password123");
      cy.get("[data-testid=password-confirmation-input]").type("password123");

      // Enviar formulario
      cy.get("[data-testid=submit-button]").click();

      // Verificar redirección y mensaje de éxito
      cy.url().should("include", "/dashboard");
      cy.get("[data-testid=welcome-message]").should(
         "contain",
         "Bienvenido, Juan Pérez",
      );
   });

   it("should show validation errors for invalid input", () => {
      cy.get("[data-testid=email-input]").type("email-invalido");
      cy.get("[data-testid=password-input]").type("123");
      cy.get("[data-testid=submit-button]").click();

      cy.get("[data-testid=email-error]").should(
         "contain",
         "Email debe ser válido",
      );
      cy.get("[data-testid=password-error]").should(
         "contain",
         "Contraseña debe tener al menos 8 caracteres",
      );
   });

   it("should handle server errors gracefully", () => {
      // Interceptar request y simular error
      cy.intercept("POST", "/api/v1/users", {
         statusCode: 500,
         body: { message: "Error interno del servidor" },
      }).as("createUser");

      // Llenar formulario válido
      cy.get("[data-testid=name-input]").type("Juan Pérez");
      cy.get("[data-testid=email-input]").type("juan@example.com");
      cy.get("[data-testid=password-input]").type("password123");
      cy.get("[data-testid=password-confirmation-input]").type("password123");
      cy.get("[data-testid=submit-button]").click();

      // Verificar manejo de error
      cy.wait("@createUser");
      cy.get("[data-testid=error-message]").should(
         "contain",
         "Error interno del servidor",
      );
   });
});
```

### 6. Configurar scripts de testing

#### package.json

```json
{
   "scripts": {
      "test": "jest",
      "test:watch": "jest --watch",
      "test:coverage": "jest --coverage",
      "test:e2e": "cypress run",
      "test:e2e:open": "cypress open"
   },
   "jest": {
      "testEnvironment": "jsdom",
      "setupFilesAfterEnv": ["<rootDir>/src/__tests__/setupTests.ts"],
      "collectCoverageFrom": [
         "src/**/*.{ts,tsx}",
         "!src/**/*.d.ts",
         "!src/index.tsx",
         "!src/reportWebVitals.ts"
      ],
      "coverageThreshold": {
         "global": {
            "branches": 80,
            "functions": 80,
            "lines": 80,
            "statements": 80
         }
      }
   }
}
```

#### composer.json (Backend)

```json
{
   "scripts": {
      "test": "phpunit",
      "test:unit": "phpunit --testsuite=Unit",
      "test:feature": "phpunit --testsuite=Feature",
      "test:coverage": "phpunit --coverage-html coverage"
   }
}
```

## Tips

- **AAA Pattern**: Organiza tests en Arrange, Act, Assert
- **Nombres descriptivos**: Usa nombres que expliquen qué se está probando
- **Un assert por test**: Cada test debe verificar una sola cosa
- **Tests independientes**: Cada test debe poder ejecutarse por separado
- **Datos de prueba**: Usa factories y seeders para datos consistentes
- **Mocks y stubs**: Simula dependencias externas para pruebas unitarias
- **Test-Driven Development**: Escribe tests antes que el código cuando sea
  posible
- **Cobertura significativa**: Enfócate en cobertura de casos de uso, no líneas

## Ejemplos

### Custom Hook Testing

```typescript
// src/hooks/useAuth.test.ts
import { act, renderHook } from "@testing-library/react";
import { Provider } from "react-redux";
import { store } from "../store/store";
import useAuth from "./useAuth";

const wrapper = ({ children }: { children: React.ReactNode }) => (
   <Provider store={store}>{children}</Provider>
);

describe("useAuth", () => {
   it("should return initial auth state", () => {
      const { result } = renderHook(() => useAuth(), { wrapper });

      expect(result.current.user).toBeNull();
      expect(result.current.isAuthenticated).toBe(false);
      expect(result.current.isLoading).toBe(false);
   });

   it("should login user successfully", async () => {
      const { result } = renderHook(() => useAuth(), { wrapper });

      await act(async () => {
         await result.current.login("juan@example.com", "password123");
      });

      expect(result.current.isAuthenticated).toBe(true);
      expect(result.current.user).toEqual(
         expect.objectContaining({
            email: "juan@example.com",
         }),
      );
   });
});
```

### Service Testing con MSW

```typescript
// src/services/api.test.ts
import { setupServer } from "msw/node";
import { rest } from "msw";
import { userService } from "./userService";

const server = setupServer(
   rest.get("/api/v1/users", (req, res, ctx) => {
      return res(
         ctx.json({
            data: [{ id: 1, name: "Juan", email: "juan@example.com" }],
         }),
      );
   }),
   rest.post("/api/v1/users", (req, res, ctx) => {
      return res(
         ctx.status(201),
         ctx.json({
            data: { id: 2, name: "Maria", email: "maria@example.com" },
         }),
      );
   }),
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe("UserService", () => {
   it("should fetch users successfully", async () => {
      const users = await userService.getUsers();

      expect(users).toHaveLength(1);
      expect(users[0]).toEqual(
         expect.objectContaining({
            name: "Juan",
            email: "juan@example.com",
         }),
      );
   });

   it("should create user successfully", async () => {
      const userData = {
         name: "Maria",
         email: "maria@example.com",
         password: "password123",
      };

      const user = await userService.createUser(userData);

      expect(user).toEqual(
         expect.objectContaining({
            id: 2,
            name: "Maria",
            email: "maria@example.com",
         }),
      );
   });
});
```

## Navegación

[⬅️ Checklist de Testing por Tipo de Proyecto](./checklist-testing-proyectos.md)
| [🏠 README Principal](../../README.md) |
[Testing de Regresión ➡️](./testing-regresion.md)
