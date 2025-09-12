# Configuración de React + TypeScript + Vite

## ¿Qué es?

La configuración de React + TypeScript + Vite es el proceso de establecer un
entorno de desarrollo moderno y eficiente para el frontend de la aplicación.
Vite actúa como bundler y servidor de desarrollo, React 18+ como framework de
interfaz de usuario, y TypeScript 5+ para proporcionar tipado estático y mejor
experiencia de desarrollo.

## ¿Por qué es importante?

- **Desarrollo rápido**: Vite ofrece hot reload instantáneo y builds optimizados
- **Tipado fuerte**: TypeScript previene errores en tiempo de compilación
- **Ecosistema moderno**: React 18+ con las últimas características y patrones
- **Productividad**: Herramientas integradas para linting, testing y formateo
- **Mantenibilidad**: Código más predecible y fácil de refactorizar
- **Escalabilidad**: Arquitectura preparada para proyectos grandes

## ¿Qué debe incluir?

### Configuración base

- **Vite**: Configuración optimizada para desarrollo y producción
- **TypeScript**: Configuración estricta con reglas de tipado fuerte
- **React 18+**: Configuración con StrictMode y nuevas características
- **Redux Toolkit**: Estado global con tipado completo
- **React Router**: Navegación tipada entre páginas

### Herramientas de calidad

- **ESLint**: Reglas de código consistentes
- **Prettier**: Formateo automático de código
- **Jest + React Testing Library**: Testing unitario y de integración
- **Husky**: Git hooks para mantener calidad del código

### Librerías adicionales

- **Axios**: Cliente HTTP tipado para APIs
- **React Hook Form**: Manejo de formularios con validación
- **Zod**: Validación de esquemas TypeScript-first

## ¿Qué debo hacer?

### 1. Crear el proyecto base

```bash
# Crear proyecto con Vite + React + TypeScript
npm create vite@latest frontend -- --template react-ts

# Navegar al directorio
cd frontend

# Instalar dependencias
npm install
```

### 2. Configurar TypeScript estricto

**`tsconfig.json`**:

```json
{
   "compilerOptions": {
      "target": "ES2020",
      "useDefineForClassFields": true,
      "lib": ["ES2020", "DOM", "DOM.Iterable"],
      "module": "ESNext",
      "skipLibCheck": true,
      "moduleResolution": "bundler",
      "allowImportingTsExtensions": true,
      "resolveJsonModule": true,
      "isolatedModules": true,
      "noEmit": true,
      "jsx": "react-jsx",
      "strict": true,
      "noUnusedLocals": true,
      "noUnusedParameters": true,
      "noFallthroughCasesInSwitch": true,
      "baseUrl": ".",
      "paths": {
         "@/*": ["./src/*"]
      }
   },
   "include": ["src"],
   "references": [{ "path": "./tsconfig.node.json" }]
}
```

### 3. Configurar Vite

**`vite.config.ts`**:

```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
   plugins: [react()],
   resolve: {
      alias: {
         "@": path.resolve(__dirname, "./src"),
      },
   },
   server: {
      port: 3000,
      open: true,
   },
   build: {
      outDir: "dist",
      sourcemap: true,
   },
});
```

### 4. Instalar dependencias principales

```bash
# Estado y navegación
npm install @reduxjs/toolkit react-redux react-router-dom

# HTTP y validación
npm install axios zod react-hook-form @hookform/resolvers

# Tipos para desarrollo
npm install -D @types/node

# Testing
npm install -D jest @testing-library/react @testing-library/jest-dom
npm install -D @testing-library/user-event jest-environment-jsdom

# Herramientas de calidad
npm install -D eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser
npm install -D prettier eslint-config-prettier eslint-plugin-prettier
npm install -D eslint-plugin-react eslint-plugin-react-hooks
npm install -D husky lint-staged
```

### 5. Configurar ESLint

**`.eslintrc.cjs`**:

```javascript
module.exports = {
   root: true,
   env: { browser: true, es2020: true },
   extends: [
      "eslint:recommended",
      "@typescript-eslint/recommended",
      "plugin:react-hooks/recommended",
      "plugin:react/recommended",
      "plugin:react/jsx-runtime",
      "prettier",
   ],
   ignorePatterns: ["dist", ".eslintrc.cjs"],
   parser: "@typescript-eslint/parser",
   plugins: ["react-refresh", "@typescript-eslint"],
   rules: {
      "react-refresh/only-export-components": [
         "warn",
         { allowConstantExport: true },
      ],
      "@typescript-eslint/no-unused-vars": "error",
      "@typescript-eslint/explicit-function-return-type": "warn",
      "react/prop-types": "off",
   },
   settings: {
      react: {
         version: "detect",
      },
   },
};
```

### 6. Configurar Prettier

**`.prettierrc`**:

```json
{
   "semi": false,
   "trailingComma": "es5",
   "singleQuote": true,
   "printWidth": 80,
   "tabWidth": 2,
   "useTabs": false
}
```

### 7. Configurar Jest

**`jest.config.js`**:

```javascript
export default {
   preset: "ts-jest",
   testEnvironment: "jsdom",
   setupFilesAfterEnv: ["<rootDir>/src/setupTests.ts"],
   moduleNameMapping: {
      "^@/(.*)$": "<rootDir>/src/$1",
   },
   transform: {
      "^.+\\.tsx?$": "ts-jest",
   },
   moduleFileExtensions: ["ts", "tsx", "js", "jsx"],
   collectCoverageFrom: [
      "src/**/*.{ts,tsx}",
      "!src/**/*.d.ts",
      "!src/main.tsx",
      "!src/vite-env.d.ts",
   ],
};
```

### 8. Crear estructura de carpetas

```
src/
├── components/        # Componentes reutilizables
│   ├── ui/           # Componentes básicos (Button, Input, etc.)
│   └── common/       # Componentes comunes (Header, Footer, etc.)
├── hooks/            # Custom hooks
├── pages/            # Páginas principales de la aplicación
├── services/         # API calls y servicios externos
├── store/            # Redux store y slices
│   ├── slices/       # Redux slices por dominio
│   └── index.ts      # Configuración del store
├── types/            # Definiciones TypeScript globales
├── utils/            # Utilidades y helpers
├── assets/           # Imágenes, iconos, etc.
└── styles/           # Estilos globales
```

### 9. Configurar Redux Toolkit

**`src/store/index.ts`**:

```typescript
import { configureStore } from "@reduxjs/toolkit";
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";

export const store = configureStore({
   reducer: {
      // Aquí irán los slices
   },
   middleware: (getDefaultMiddleware) =>
      getDefaultMiddleware({
         serializableCheck: {
            ignoredActions: ["persist/PERSIST"],
         },
      }),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

export const useAppDispatch = (): AppDispatch => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### 10. Configurar rutas principales

**`src/App.tsx`**:

```typescript
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import { Provider } from "react-redux";
import { store } from "@/store";

// Páginas
import HomePage from "@/pages/HomePage";
import NotFoundPage from "@/pages/NotFoundPage";

const App = (): JSX.Element => {
   return (
      <Provider store={store}>
         <Router>
            <div className="App">
               <Routes>
                  <Route path="/" element={<HomePage />} />
                  <Route path="*" element={<NotFoundPage />} />
               </Routes>
            </div>
         </Router>
      </Provider>
   );
};

export default App;
```

### 11. Configurar scripts en package.json

```json
{
   "scripts": {
      "dev": "vite",
      "build": "tsc && vite build",
      "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
      "lint:fix": "eslint . --ext ts,tsx --fix",
      "preview": "vite preview",
      "test": "jest",
      "test:watch": "jest --watch",
      "test:coverage": "jest --coverage",
      "format": "prettier --write \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
      "type-check": "tsc --noEmit"
   }
}
```

## Tips

### Desarrollo

- **Usa TypeScript estricto**: Configura `strict: true` desde el inicio
- **Aliases de importación**: Usa `@/` para imports más limpios
- **Tipado fuerte**: Define interfaces para todas las props y estados
- **Custom hooks**: Extrae lógica reutilizable en hooks personalizados
- **Componentes tipados**: Usa `React.FC` o define props explícitamente

### Performance

- **Lazy loading**: Usa `React.lazy()` para rutas y componentes grandes
- **Memorización**: Aplica `React.memo`, `useMemo` y `useCallback` cuando sea
  necesario
- **Bundle splitting**: Configura chunks específicos en Vite
- **Tree shaking**: Importa solo las funciones necesarias de librerías

### Testing

- **Testing pyramid**: Más tests unitarios, menos de integración, pocos E2E
- **Custom render**: Crea un helper para renderizar con providers
- **Mock services**: Mockea llamadas a APIs en tests unitarios
- **User-centric testing**: Usa selectores por rol y texto visible

### Calidad de código

- **Pre-commit hooks**: Configura Husky para lint y format automático
- **Convenciones de nombres**: PascalCase para componentes, camelCase para
  variables
- **Documentación**: Usa JSDoc para funciones complejas
- **Error boundaries**: Implementa manejo de errores global

## Ejemplos

### Componente base tipado

```typescript
// src/components/ui/Button.tsx
import { ButtonHTMLAttributes, ReactNode } from "react";

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
   variant?: "primary" | "secondary" | "danger";
   size?: "sm" | "md" | "lg";
   isLoading?: boolean;
   children: ReactNode;
}

const Button = ({
   variant = "primary",
   size = "md",
   isLoading = false,
   children,
   className = "",
   disabled,
   ...props
}: ButtonProps): JSX.Element => {
   const baseClasses = "font-medium rounded focus:outline-none focus:ring-2";
   const variantClasses = {
      primary: "bg-blue-600 text-white hover:bg-blue-700",
      secondary: "bg-gray-200 text-gray-900 hover:bg-gray-300",
      danger: "bg-red-600 text-white hover:bg-red-700",
   };
   const sizeClasses = {
      sm: "px-3 py-1.5 text-sm",
      md: "px-4 py-2",
      lg: "px-6 py-3 text-lg",
   };

   return (
      <button
         className={`${baseClasses} ${variantClasses[variant]} ${
            sizeClasses[size]
         } ${className}`}
         disabled={disabled || isLoading}
         {...props}
      >
         {isLoading ? "Cargando..." : children}
      </button>
   );
};

export default Button;
```

### Service con Axios tipado

```typescript
// src/services/userService.ts
import axios, { AxiosResponse } from "axios";
import { CreateUserRequest, UpdateUserRequest, User } from "@/types/user";

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL ||
   "http://localhost:8000/api";

const api = axios.create({
   baseURL: API_BASE_URL,
   timeout: 10000,
   headers: {
      "Content-Type": "application/json",
   },
});

// Interceptor para agregar token
api.interceptors.request.use((config) => {
   const token = localStorage.getItem("authToken");
   if (token) {
      config.headers.Authorization = `Bearer ${token}`;
   }
   return config;
});

export const userService = {
   async getUsers(): Promise<User[]> {
      const response: AxiosResponse<User[]> = await api.get("/users");
      return response.data;
   },

   async getUserById(id: string): Promise<User> {
      const response: AxiosResponse<User> = await api.get(`/users/${id}`);
      return response.data;
   },

   async createUser(userData: CreateUserRequest): Promise<User> {
      const response: AxiosResponse<User> = await api.post("/users", userData);
      return response.data;
   },

   async updateUser(id: string, userData: UpdateUserRequest): Promise<User> {
      const response: AxiosResponse<User> = await api.put(
         `/users/${id}`,
         userData,
      );
      return response.data;
   },

   async deleteUser(id: string): Promise<void> {
      await api.delete(`/users/${id}`);
   },
};
```

### Redux slice tipado

```typescript
// src/store/slices/userSlice.ts
import { createAsyncThunk, createSlice, PayloadAction } from "@reduxjs/toolkit";
import { User } from "@/types/user";
import { userService } from "@/services/userService";

interface UserState {
   users: User[];
   currentUser: User | null;
   loading: boolean;
   error: string | null;
}

const initialState: UserState = {
   users: [],
   currentUser: null,
   loading: false,
   error: null,
};

// Async thunks
export const fetchUsers = createAsyncThunk("user/fetchUsers", async () => {
   return await userService.getUsers();
});

export const fetchUserById = createAsyncThunk(
   "user/fetchUserById",
   async (userId: string) => {
      return await userService.getUserById(userId);
   },
);

const userSlice = createSlice({
   name: "user",
   initialState,
   reducers: {
      clearError: (state) => {
         state.error = null;
      },
      setCurrentUser: (state, action: PayloadAction<User | null>) => {
         state.currentUser = action.payload;
      },
   },
   extraReducers: (builder) => {
      builder
         // Fetch users
         .addCase(fetchUsers.pending, (state) => {
            state.loading = true;
            state.error = null;
         })
         .addCase(fetchUsers.fulfilled, (state, action) => {
            state.loading = false;
            state.users = action.payload;
         })
         .addCase(fetchUsers.rejected, (state, action) => {
            state.loading = false;
            state.error = action.error.message || "Error al cargar usuarios";
         })
         // Fetch user by ID
         .addCase(fetchUserById.fulfilled, (state, action) => {
            state.currentUser = action.payload;
         });
   },
});

export const { clearError, setCurrentUser } = userSlice.actions;
export default userSlice.reducer;
```

### Test ejemplo

```typescript
// src/components/ui/__tests__/Button.test.tsx
import { fireEvent, render, screen } from "@testing-library/react";
import "@testing-library/jest-dom";
import Button from "../Button";

describe("Button Component", () => {
   it("renders with correct text", () => {
      render(<Button>Click me</Button>);
      expect(
         screen.getByRole("button", { name: /click me/i }),
      ).toBeInTheDocument();
   });

   it("handles click events", () => {
      const handleClick = jest.fn();
      render(<Button onClick={handleClick}>Click me</Button>);

      fireEvent.click(screen.getByRole("button"));
      expect(handleClick).toHaveBeenCalledTimes(1);
   });

   it("shows loading state", () => {
      render(<Button isLoading>Submit</Button>);
      expect(screen.getByText("Cargando...")).toBeInTheDocument();
      expect(screen.getByRole("button")).toBeDisabled();
   });

   it("applies correct variant classes", () => {
      render(<Button variant="danger">Delete</Button>);
      expect(screen.getByRole("button")).toHaveClass("bg-red-600");
   });
});
```

## Navegación

[⬅️ Setup de Laravel con Clean Architecture](./setup-laravel-clean-architecture.md)
| [🏠 README Principal](../../README.md) |
[Etapa 4: Desarrollo del backend ➡️](../step_04/desarrollo-backend.md)
