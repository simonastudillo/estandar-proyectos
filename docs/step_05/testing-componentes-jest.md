# Testing Componentes con Jest

## ¿Qué es?

Testing de componentes con Jest es la práctica de escribir pruebas automatizadas
para validar el comportamiento y la funcionalidad de componentes React
utilizando Jest como framework de testing y React Testing Library como utilidad
para interactuar con los componentes. Este enfoque garantiza que los componentes
funcionen correctamente de forma aislada y en integración.

## ¿Por qué es importante?

- **Confianza en el código**: Las pruebas permiten refactorizar y modificar
  código con seguridad
- **Detección temprana de bugs**: Identifica errores antes de que lleguen a
  producción
- **Documentación viva**: Los tests sirven como documentación del comportamiento
  esperado
- **Integración continua**: Facilita la implementación de pipelines de CI/CD
- **Mejor diseño**: Escribir tests fuerza a pensar en el diseño de componentes
- **Regresiones**: Previene que funcionalidades que funcionaban dejen de hacerlo

## ¿Qué debe incluir?

### Estructura de Testing

```
src/
├── __tests__/          # Tests globales y configuración
│   ├── setup.ts        # Configuración de testing
│   └── utils.tsx       # Utilidades para tests
├── components/
│   ├── common/
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── Button.stories.tsx
│   │   └── Input/
│   │       ├── Input.tsx
│   │       └── Input.test.tsx
├── hooks/
│   ├── useAuth.ts
│   ├── useAuth.test.ts
│   └── __mocks__/
│       └── useApi.ts
├── services/
│   ├── api.ts
│   ├── api.test.ts
│   └── __mocks__/
│       └── axios.ts
└── pages/
    ├── LoginPage.tsx
    └── LoginPage.test.tsx
```

### Tipos de Testing

- **Unit Testing**: Pruebas de componentes individuales
- **Integration Testing**: Pruebas de componentes trabajando juntos
- **Snapshot Testing**: Comparación de salida renderizada
- **Accessibility Testing**: Validación de accesibilidad
- **Performance Testing**: Pruebas de rendimiento básicas

## ¿Qué debo hacer?

### 1. Configurar Testing Environment

```bash
# Instalar dependencias de testing
npm install --save-dev vitest jsdom
npm install --save-dev @testing-library/react @testing-library/jest-dom
npm install --save-dev @testing-library/user-event
npm install --save-dev @vitest/ui @vitest/coverage-v8
```

### 2. Configurar Vitest

```typescript
// vitest.config.ts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
   plugins: [react()],
   test: {
      globals: true,
      environment: "jsdom",
      setupFiles: ["./src/__tests__/setup.ts"],
      css: true,
      coverage: {
         provider: "v8",
         reporter: ["text", "json", "html"],
         exclude: [
            "node_modules/",
            "src/__tests__/",
            "**/*.d.ts",
            "**/*.config.{js,ts}",
            "**/index.ts",
         ],
      },
   },
   resolve: {
      alias: {
         "@": path.resolve(__dirname, "./src"),
         "@components": path.resolve(__dirname, "./src/components"),
         "@hooks": path.resolve(__dirname, "./src/hooks"),
         "@services": path.resolve(__dirname, "./src/services"),
         "@types": path.resolve(__dirname, "./src/types"),
         "@utils": path.resolve(__dirname, "./src/utils"),
      },
   },
});
```

### 3. Configurar Setup de Testing

```typescript
// src/__tests__/setup.ts
import "@testing-library/jest-dom";
import { vi } from "vitest";

// Mock de localStorage
const localStorageMock = {
   getItem: vi.fn(),
   setItem: vi.fn(),
   removeItem: vi.fn(),
   clear: vi.fn(),
};
Object.defineProperty(window, "localStorage", {
   value: localStorageMock,
});

// Mock de window.matchMedia
Object.defineProperty(window, "matchMedia", {
   writable: true,
   value: vi.fn().mockImplementation((query) => ({
      matches: false,
      media: query,
      onchange: null,
      addListener: vi.fn(), // deprecated
      removeListener: vi.fn(), // deprecated
      addEventListener: vi.fn(),
      removeEventListener: vi.fn(),
      dispatchEvent: vi.fn(),
   })),
});

// Mock de IntersectionObserver
global.IntersectionObserver = vi.fn().mockImplementation(() => ({
   observe: vi.fn(),
   unobserve: vi.fn(),
   disconnect: vi.fn(),
}));

// Mock de ResizeObserver
global.ResizeObserver = vi.fn().mockImplementation(() => ({
   observe: vi.fn(),
   unobserve: vi.fn(),
   disconnect: vi.fn(),
}));
```

### 4. Crear Utilidades de Testing

```typescript
// src/__tests__/utils.tsx
import React, { ReactElement } from "react";
import { render, RenderOptions } from "@testing-library/react";
import { Provider } from "react-redux";
import { BrowserRouter } from "react-router-dom";
import { configureStore } from "@reduxjs/toolkit";
import { authSlice } from "@store/slices/authSlice";
import { uiSlice } from "@store/slices/uiSlice";

// Crear store de testing
const createTestStore = (initialState?: any) => {
   return configureStore({
      reducer: {
         auth: authSlice.reducer,
         ui: uiSlice.reducer,
      },
      preloadedState: initialState,
   });
};

// Wrapper personalizado para tests
interface CustomRenderOptions extends Omit<RenderOptions, "wrapper"> {
   initialState?: any;
   store?: ReturnType<typeof createTestStore>;
   route?: string;
}

const AllTheProviders: React.FC<{
   children: React.ReactNode;
   store: ReturnType<typeof createTestStore>;
   route?: string;
}> = ({ children, store, route = "/" }) => {
   // Navegar a la ruta especificada si se proporciona
   if (route !== "/") {
      window.history.pushState({}, "Test page", route);
   }

   return (
      <Provider store={store}>
         <BrowserRouter>
            {children}
         </BrowserRouter>
      </Provider>
   );
};

const customRender = (
   ui: ReactElement,
   {
      initialState,
      store = createTestStore(initialState),
      route,
      ...renderOptions
   }: CustomRenderOptions = {},
) => {
   const Wrapper: React.FC<{ children: React.ReactNode }> = ({ children }) => (
      <AllTheProviders store={store} route={route}>
         {children}
      </AllTheProviders>
   );

   return {
      store,
      ...render(ui, { wrapper: Wrapper, ...renderOptions }),
   };
};

// Mock para usuario autenticado
export const mockAuthenticatedUser = {
   id: "1",
   firstName: "John",
   lastName: "Doe",
   email: "john@example.com",
   role: "user" as const,
   permissions: ["read:profile"],
};

// Mock para admin
export const mockAdminUser = {
   ...mockAuthenticatedUser,
   role: "admin" as const,
   permissions: ["admin", "read:users", "write:users"],
};

// Estado inicial autenticado
export const authenticatedState = {
   auth: {
      user: mockAuthenticatedUser,
      token: "mock-token",
      isAuthenticated: true,
      loading: false,
      error: null,
   },
};

export * from "@testing-library/react";
export { createTestStore, customRender as render };
```

### 5. Testing de Componentes Básicos

```typescript
// src/components/common/Button/Button.test.tsx
import { fireEvent, render, screen } from "@/__tests__/utils";
import { Button } from "./Button";

describe("Button Component", () => {
   describe("Renderizado básico", () => {
      it("debe renderizar children correctamente", () => {
         render(<Button>Click me</Button>);
         expect(screen.getByRole("button", { name: /click me/i }))
            .toBeInTheDocument();
      });

      it("debe aplicar className personalizado", () => {
         render(<Button className="custom-class">Button</Button>);
         const button = screen.getByRole("button");
         expect(button).toHaveClass("custom-class");
      });

      it("debe mostrar test id cuando se proporciona", () => {
         render(<Button testId="test-button">Button</Button>);
         expect(screen.getByTestId("test-button")).toBeInTheDocument();
      });
   });

   describe("Variantes y estados", () => {
      it("debe aplicar la clase de variante correcta", () => {
         render(<Button variant="danger">Delete</Button>);
         const button = screen.getByRole("button");
         expect(button).toHaveClass("btn--danger");
      });

      it("debe aplicar la clase de tamaño correcta", () => {
         render(<Button size="large">Large Button</Button>);
         const button = screen.getByRole("button");
         expect(button).toHaveClass("btn--large");
      });

      it("debe estar deshabilitado cuando disabled=true", () => {
         render(<Button disabled>Disabled</Button>);
         const button = screen.getByRole("button");
         expect(button).toBeDisabled();
         expect(button).toHaveClass("btn--disabled");
      });

      it("debe mostrar estado de carga", () => {
         render(<Button loading>Submit</Button>);
         const button = screen.getByRole("button");
         expect(button).toBeDisabled();
         expect(button).toHaveClass("btn--loading");
         expect(screen.getByText("Cargando...")).toBeInTheDocument();
      });
   });

   describe("Interacciones", () => {
      it("debe llamar onClick cuando se hace click", () => {
         const mockOnClick = vi.fn();
         render(<Button onClick={mockOnClick}>Click me</Button>);

         fireEvent.click(screen.getByRole("button"));
         expect(mockOnClick).toHaveBeenCalledTimes(1);
      });

      it("no debe llamar onClick cuando está deshabilitado", () => {
         const mockOnClick = vi.fn();
         render(<Button onClick={mockOnClick} disabled>Disabled</Button>);

         fireEvent.click(screen.getByRole("button"));
         expect(mockOnClick).not.toHaveBeenCalled();
      });

      it("no debe llamar onClick cuando está en loading", () => {
         const mockOnClick = vi.fn();
         render(<Button onClick={mockOnClick} loading>Loading</Button>);

         fireEvent.click(screen.getByRole("button"));
         expect(mockOnClick).not.toHaveBeenCalled();
      });
   });

   describe("Iconos", () => {
      it("debe mostrar icono a la izquierda por defecto", () => {
         render(
            <Button icon={<span data-testid="icon">🔍</span>}>
               Search
            </Button>,
         );

         const button = screen.getByRole("button");
         const icon = screen.getByTestId("icon");
         const text = screen.getByText("Search");

         expect(button).toContainElement(icon);
         expect(button).toContainElement(text);
      });

      it('debe mostrar icono a la derecha cuando iconPosition="right"', () => {
         render(
            <Button
               icon={<span data-testid="icon">→</span>}
               iconPosition="right"
            >
               Next
            </Button>,
         );

         expect(screen.getByTestId("icon")).toBeInTheDocument();
         expect(screen.getByText("Next")).toBeInTheDocument();
      });
   });

   describe("Tipos de botón", () => {
      it('debe tener type="button" por defecto', () => {
         render(<Button>Default</Button>);
         expect(screen.getByRole("button")).toHaveAttribute("type", "button");
      });

      it('debe permitir type="submit"', () => {
         render(<Button type="submit">Submit</Button>);
         expect(screen.getByRole("button")).toHaveAttribute("type", "submit");
      });
   });

   describe("Accesibilidad", () => {
      it("debe ser accesible por teclado", () => {
         const mockOnClick = vi.fn();
         render(<Button onClick={mockOnClick}>Accessible Button</Button>);

         const button = screen.getByRole("button");
         button.focus();

         fireEvent.keyDown(button, { key: "Enter" });
         expect(mockOnClick).toHaveBeenCalledTimes(1);
      });

      it("debe mantener el foco después del click", () => {
         render(<Button>Focus Test</Button>);

         const button = screen.getByRole("button");
         fireEvent.click(button);

         expect(document.activeElement).toBe(button);
      });
   });
});
```

### 6. Testing de Hooks Personalizados

```typescript
// src/hooks/__tests__/useAuth.test.ts
import { renderHook, waitFor } from "@testing-library/react";
import { Provider } from "react-redux";
import { vi } from "vitest";
import { useAuth } from "../useAuth";
import { createTestStore } from "@/__tests__/utils";
import { authService } from "@services/auth/authService";

// Mock del servicio de autenticación
vi.mock("@services/auth/authService", () => ({
   authService: {
      getCurrentUser: vi.fn(),
      logout: vi.fn(),
   },
}));

const mockAuthService = authService as any;

describe("useAuth Hook", () => {
   let store: ReturnType<typeof createTestStore>;

   beforeEach(() => {
      store = createTestStore();
      vi.clearAllMocks();
   });

   const wrapper = ({ children }: { children: React.ReactNode }) => (
      <Provider store={store}>{children}</Provider>
   );

   describe("Estado inicial", () => {
      it("debe retornar estado inicial cuando no está autenticado", () => {
         const { result } = renderHook(() => useAuth(), { wrapper });

         expect(result.current.isAuthenticated).toBe(false);
         expect(result.current.user).toBeNull();
         expect(result.current.loading).toBe(false);
         expect(result.current.error).toBeNull();
      });

      it("debe retornar usuario cuando está autenticado", () => {
         store = createTestStore({
            auth: {
               user: { id: "1", name: "John Doe", email: "john@example.com" },
               token: "token",
               isAuthenticated: true,
               loading: false,
               error: null,
            },
         });

         const { result } = renderHook(() => useAuth(), { wrapper });

         expect(result.current.isAuthenticated).toBe(true);
         expect(result.current.user).toEqual({
            id: "1",
            name: "John Doe",
            email: "john@example.com",
         });
      });
   });

   describe("Funcionalidad de logout", () => {
      it("debe llamar al servicio de logout", async () => {
         mockAuthService.logout.mockResolvedValue(undefined);

         const { result } = renderHook(() => useAuth(), { wrapper });

         await result.current.logout();

         expect(mockAuthService.logout).toHaveBeenCalledTimes(1);
      });

      it("debe manejar errores en logout", async () => {
         const error = new Error("Logout failed");
         mockAuthService.logout.mockRejectedValue(error);

         const { result } = renderHook(() => useAuth(), { wrapper });

         await expect(result.current.logout()).rejects.toThrow("Logout failed");
      });
   });

   describe("Obtención de usuario actual", () => {
      it("debe obtener usuario actual cuando está autenticado sin user", async () => {
         store = createTestStore({
            auth: {
               user: null,
               token: "token",
               isAuthenticated: true,
               loading: false,
               error: null,
            },
         });

         const mockUser = { id: "1", name: "John", email: "john@example.com" };
         mockAuthService.getCurrentUser.mockResolvedValue(mockUser);

         renderHook(() => useAuth(), { wrapper });

         await waitFor(() => {
            expect(mockAuthService.getCurrentUser).toHaveBeenCalledTimes(1);
         });
      });
   });
});
```

### 7. Testing de Páginas Completas

```typescript
// src/pages/__tests__/LoginPage.test.tsx
import { fireEvent, render, screen, waitFor } from "@/__tests__/utils";
import userEvent from "@testing-library/user-event";
import { vi } from "vitest";
import { LoginPage } from "../LoginPage";
import { authService } from "@services/auth/authService";

// Mock del servicio
vi.mock("@services/auth/authService");
const mockAuthService = authService as any;

// Mock de react-router-dom
const mockNavigate = vi.fn();
vi.mock("react-router-dom", async () => {
   const actual = await vi.importActual("react-router-dom");
   return {
      ...actual,
      useNavigate: () => mockNavigate,
   };
});

describe("LoginPage", () => {
   beforeEach(() => {
      vi.clearAllMocks();
   });

   describe("Renderizado", () => {
      it("debe renderizar formulario de login", () => {
         render(<LoginPage />);

         expect(screen.getByRole("heading", { name: /iniciar sesión/i }))
            .toBeInTheDocument();
         expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
         expect(screen.getByLabelText(/contraseña/i)).toBeInTheDocument();
         expect(screen.getByRole("button", { name: /iniciar sesión/i }))
            .toBeInTheDocument();
      });

      it("debe renderizar link de registro", () => {
         render(<LoginPage />);

         expect(screen.getByRole("link", { name: /registrarse/i }))
            .toBeInTheDocument();
      });
   });

   describe("Validación de formulario", () => {
      it("debe mostrar errores de validación para campos vacíos", async () => {
         const user = userEvent.setup();
         render(<LoginPage />);

         const submitButton = screen.getByRole("button", {
            name: /iniciar sesión/i,
         });
         await user.click(submitButton);

         expect(screen.getByText(/email es requerido/i)).toBeInTheDocument();
         expect(screen.getByText(/contraseña es requerida/i))
            .toBeInTheDocument();
      });

      it("debe validar formato de email", async () => {
         const user = userEvent.setup();
         render(<LoginPage />);

         const emailInput = screen.getByLabelText(/email/i);
         await user.type(emailInput, "email-invalido");

         const submitButton = screen.getByRole("button", {
            name: /iniciar sesión/i,
         });
         await user.click(submitButton);

         expect(screen.getByText(/email no válido/i)).toBeInTheDocument();
      });
   });

   describe("Proceso de login", () => {
      it("debe enviar datos de login correctamente", async () => {
         const user = userEvent.setup();
         const mockResponse = {
            user: { id: "1", email: "test@example.com" },
            token: "mock-token",
         };

         mockAuthService.login.mockResolvedValue(mockResponse);

         render(<LoginPage />);

         // Llenar formulario
         await user.type(screen.getByLabelText(/email/i), "test@example.com");
         await user.type(screen.getByLabelText(/contraseña/i), "password123");

         // Enviar
         await user.click(
            screen.getByRole("button", { name: /iniciar sesión/i }),
         );

         await waitFor(() => {
            expect(mockAuthService.login).toHaveBeenCalledWith({
               email: "test@example.com",
               password: "password123",
            });
         });

         expect(mockNavigate).toHaveBeenCalledWith("/dashboard");
      });

      it("debe mostrar estado de carga durante login", async () => {
         const user = userEvent.setup();
         let resolveLogin: (value: any) => void;

         const loginPromise = new Promise((resolve) => {
            resolveLogin = resolve;
         });

         mockAuthService.login.mockReturnValue(loginPromise);

         render(<LoginPage />);

         await user.type(screen.getByLabelText(/email/i), "test@example.com");
         await user.type(screen.getByLabelText(/contraseña/i), "password123");
         await user.click(
            screen.getByRole("button", { name: /iniciar sesión/i }),
         );

         expect(screen.getByRole("button", { name: /iniciando sesión/i }))
            .toBeInTheDocument();
         expect(screen.getByRole("button")).toBeDisabled();

         // Resolver la promesa
         resolveLogin!({ user: {}, token: "token" });
      });

      it("debe mostrar error cuando el login falla", async () => {
         const user = userEvent.setup();
         const errorMessage = "Credenciales inválidas";

         mockAuthService.login.mockRejectedValue(new Error(errorMessage));

         render(<LoginPage />);

         await user.type(screen.getByLabelText(/email/i), "test@example.com");
         await user.type(screen.getByLabelText(/contraseña/i), "wrongpassword");
         await user.click(
            screen.getByRole("button", { name: /iniciar sesión/i }),
         );

         await waitFor(() => {
            expect(screen.getByText(errorMessage)).toBeInTheDocument();
         });

         expect(mockNavigate).not.toHaveBeenCalled();
      });
   });

   describe("Accesibilidad", () => {
      it("debe ser navegable por teclado", async () => {
         const user = userEvent.setup();
         render(<LoginPage />);

         const emailInput = screen.getByLabelText(/email/i);
         const passwordInput = screen.getByLabelText(/contraseña/i);
         const submitButton = screen.getByRole("button", {
            name: /iniciar sesión/i,
         });

         // Navegación con Tab
         await user.tab();
         expect(emailInput).toHaveFocus();

         await user.tab();
         expect(passwordInput).toHaveFocus();

         await user.tab();
         expect(submitButton).toHaveFocus();
      });

      it("debe tener labels asociados correctamente", () => {
         render(<LoginPage />);

         const emailInput = screen.getByLabelText(/email/i);
         const passwordInput = screen.getByLabelText(/contraseña/i);

         expect(emailInput).toHaveAttribute("id");
         expect(passwordInput).toHaveAttribute("id");
      });
   });
});
```

## Tips

### Mejores Prácticas

1. **Organizar tests por comportamiento**:

```typescript
describe("Button Component", () => {
   describe("cuando está en estado normal", () => {
      it("debe ser clickeable", () => {});
      it("debe mostrar el texto correcto", () => {});
   });

   describe("cuando está disabled", () => {
      it("no debe ser clickeable", () => {});
      it("debe tener atributo disabled", () => {});
   });
});
```

2. **Usar data-testid solo cuando sea necesario**:

```typescript
// ✅ Bueno: Usar roles y texto cuando sea posible
screen.getByRole("button", { name: /submit/i });
screen.getByText("Error message");

// ⚠️ Usar data-testid solo cuando no hay otra opción
screen.getByTestId("complex-component");
```

3. **Mock solo lo necesario**:

```typescript
// ✅ Bueno: Mock específico
vi.mock("@services/api", () => ({
   apiClient: {
      get: vi.fn(),
      post: vi.fn(),
   },
}));

// ❌ Evitar: Mock completo del módulo
vi.mock("@services/api");
```

4. **Testing de error boundaries**:

```typescript
it("debe capturar errores y mostrar fallback", () => {
   const ThrowError = () => {
      throw new Error("Test error");
   };

   render(
      <ErrorBoundary fallback={<div>Error occurred</div>}>
         <ThrowError />
      </ErrorBoundary>,
   );

   expect(screen.getByText("Error occurred")).toBeInTheDocument();
});
```

## Ejemplos

### Ejemplo: Test de Integración

```typescript
// src/components/__tests__/UserManagement.integration.test.tsx
import { render, screen, waitFor } from "@/__tests__/utils";
import userEvent from "@testing-library/user-event";
import { vi } from "vitest";
import { UserManagement } from "../UserManagement";
import { userService } from "@services/user/userService";

vi.mock("@services/user/userService");
const mockUserService = userService as any;

describe("UserManagement Integration", () => {
   const mockUsers = [
      {
         id: "1",
         firstName: "John",
         lastName: "Doe",
         email: "john@example.com",
      },
      {
         id: "2",
         firstName: "Jane",
         lastName: "Smith",
         email: "jane@example.com",
      },
   ];

   beforeEach(() => {
      mockUserService.getUsers.mockResolvedValue({
         data: mockUsers,
         meta: { current_page: 1, last_page: 1, total: 2 },
      });
   });

   it("debe cargar y mostrar lista de usuarios", async () => {
      render(<UserManagement />);

      await waitFor(() => {
         expect(screen.getByText("John Doe")).toBeInTheDocument();
         expect(screen.getByText("Jane Smith")).toBeInTheDocument();
      });
   });

   it("debe permitir crear nuevo usuario", async () => {
      const user = userEvent.setup();
      const newUser = {
         id: "3",
         firstName: "Bob",
         lastName: "Wilson",
         email: "bob@example.com",
      };

      mockUserService.createUser.mockResolvedValue(newUser);
      mockUserService.getUsers.mockResolvedValueOnce({
         data: [...mockUsers, newUser],
         meta: { current_page: 1, last_page: 1, total: 3 },
      });

      render(<UserManagement />);

      // Abrir modal de creación
      await user.click(screen.getByRole("button", { name: /crear usuario/i }));

      // Llenar formulario
      await user.type(screen.getByLabelText(/nombre/i), "Bob");
      await user.type(screen.getByLabelText(/apellido/i), "Wilson");
      await user.type(screen.getByLabelText(/email/i), "bob@example.com");

      // Enviar formulario
      await user.click(screen.getByRole("button", { name: /guardar/i }));

      await waitFor(() => {
         expect(mockUserService.createUser).toHaveBeenCalledWith({
            firstName: "Bob",
            lastName: "Wilson",
            email: "bob@example.com",
         });
      });

      // Verificar que se actualiza la lista
      await waitFor(() => {
         expect(screen.getByText("Bob Wilson")).toBeInTheDocument();
      });
   });

   it("debe manejar errores de creación", async () => {
      const user = userEvent.setup();
      const errorMessage = "Email ya existe";

      mockUserService.createUser.mockRejectedValue(new Error(errorMessage));

      render(<UserManagement />);

      await user.click(screen.getByRole("button", { name: /crear usuario/i }));
      await user.type(screen.getByLabelText(/nombre/i), "Test");
      await user.type(screen.getByLabelText(/apellido/i), "User");
      await user.type(screen.getByLabelText(/email/i), "existing@example.com");
      await user.click(screen.getByRole("button", { name: /guardar/i }));

      await waitFor(() => {
         expect(screen.getByText(errorMessage)).toBeInTheDocument();
      });
   });
});
```

### Ejemplo: Test de Performance

```typescript
// src/components/__tests__/HeavyComponent.performance.test.tsx
import { render } from "@/__tests__/utils";
import { vi } from "vitest";
import { HeavyComponent } from "../HeavyComponent";

describe("HeavyComponent Performance", () => {
   it("debe renderizar en menos de 100ms", () => {
      const start = performance.now();

      render(
         <HeavyComponent
            data={Array.from(
               { length: 1000 },
               (_, i) => ({ id: i, name: `Item ${i}` }),
            )}
         />,
      );

      const end = performance.now();
      const renderTime = end - start;

      expect(renderTime).toBeLessThan(100);
   });

   it("debe memoizar resultados costosos", () => {
      const expensiveCalculation = vi.fn(() => "resultado");

      const { rerender } = render(
         <HeavyComponent
            data={[]}
            expensiveCalculation={expensiveCalculation}
         />,
      );

      expect(expensiveCalculation).toHaveBeenCalledTimes(1);

      // Re-render con los mismos props
      rerender(
         <HeavyComponent
            data={[]}
            expensiveCalculation={expensiveCalculation}
         />,
      );

      // No debe llamar de nuevo la función
      expect(expensiveCalculation).toHaveBeenCalledTimes(1);
   });
});
```

## Navegación

[⬅️ React Router - Navegación](./react-router-navegacion.md) |
[🏠 README Principal](../../README.md) |
[Etapa 6:Desarrollo movil ➡️](../step_06/desarrollo-movil.md)
