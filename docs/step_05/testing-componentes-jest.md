# Testing Componentes React + TypeScript con Jest

## Conocimientos involucrados
- Testing automatizado
- Jest
- React Testing Library
- Validación de interfaces

## Responsable
- Equipo de QA y desarrollo frontend

## ¿Qué es?

Testing de componentes con **Jest + React Testing Library** es la práctica de
escribir pruebas automatizadas para validar el comportamiento y funcionalidad de
componentes **React + TypeScript** utilizando **Jest** como framework de testing
y **React Testing Library** como utilidad para interactuar con los componentes.
Este enfoque está específicamente optimizado para nuestro stack **Shadcn/ui →
Atomic Design** y garantiza que los componentes funcionen correctamente de forma
aislada y en integración con **coverage mínimo del 80%**.

## ¿Por qué es importante para nuestro Stack?

- **Confianza en evolución UI**: Las pruebas permiten refactorizar de Shadcn/ui
  hacia Atomic Design con seguridad
- **TypeScript safety**: Validación de tipos + behavior testing para máxima
  seguridad
- **Component design system**: Tests como documentación viva para atoms,
  molecules, organisms
- **Performance regression**: Detección temprana de impacto en bundle size y
  rendering
- **Shadcn/ui compliance**: Validación de customización de componentes base sin
  romper funcionalidad
- **CI/CD integration**: Quality gates automáticos con coverage >80% y
  performance thresholds

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

- **Unit Testing**: Pruebas de componentes individuales con TypeScript strict
- **Integration Testing**: Pruebas de componentes trabajando juntos (molecules +
  atoms)
- **Snapshot Testing**: Comparación de salida renderizada (deprecated en favor
  de visual regression)
- **Accessibility Testing**: Validación WCAG compliance automática
- **Performance Testing**: Bundle size impact y rendering performance
- **Atomic Design Testing**: Validación de design system evolution (Shadcn →
  Atomic)

## ¿Qué debo hacer según nuestro Stack?

### 1. Configurar Testing Environment Stack-Específico

```bash
# Dependencias de testing específicas para nuestro stack
npm install --save-dev vitest jsdom @types/node
npm install --save-dev @testing-library/react @testing-library/jest-dom
npm install --save-dev @testing-library/user-event @testing-library/react-hooks
npm install --save-dev @vitest/ui @vitest/coverage-v8
npm install --save-dev msw                    # API mocking
npm install --save-dev @storybook/test-runner # Storybook integration
npm install --save-dev axe-core @axe-core/react # Accessibility testing
```

### 2. Configurar Vitest con TypeScript Strict

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
      coverage: {
         provider: "v8",
         reporter: ["text", "json", "html"],
         exclude: [
            "node_modules/",
            "src/__tests__/",
            "**/*.d.ts",
            "**/*.config.*",
            "**/index.ts",
         ],
         statements: 80, // Coverage mínimo 80%
         branches: 80,
         functions: 80,
         lines: 80,
         thresholds: {
            statements: 80,
            branches: 80,
            functions: 80,
            lines: 80,
         },
      },
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

```
