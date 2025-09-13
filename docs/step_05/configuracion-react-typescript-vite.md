# Configuración React + TypeScript + Vite (Estándar Único)

> **IMPORTANTE**: Esta configuración representa el **estándar único NO
> NEGOCIABLE** para proyectos frontend. Vite es el único bundler permitido. No
> se permiten alternativas como Create React App, Webpack, Parcel o Next.js para
> SPAs.

## ¿Qué es?

La configuración inicial de React con TypeScript y Vite es el **único
procedimiento autorizado** para establecer proyectos frontend. Vite es nuestro
bundler estándar que proporciona un entorno de desarrollo ultra-rápido con Hot
Module Replacement (HMR) instantáneo, mientras que TypeScript garantiza tipado
fuerte obligatorio que previene errores en tiempo de compilación.

## ¿Por qué es importante?

- **Vite como estándar único**: Elimina ambigüedad y garantiza consistencia en
  todos los proyectos
- **Desarrollo ultra-rápido**: Vite ofrece el mejor DX con tiempo de inicio
  <100ms y HMR instantáneo
- **Tipado fuerte obligatorio**: TypeScript previene errores y mejora la
  mantenibilidad del código
- **ESM nativo**: Integración nativa con ES Modules y herramientas modernas de
  JavaScript
- **Optimización automática**: Vite optimiza automáticamente para producción con
  Rollup
- **Configuración estándar**: Setup predefinido que elimina decisiones de
  configuración
- **Ecosystem compatibility**: 100% compatible con React 18+, Redux Toolkit y
  nuestro stack

## ¿Qué debe incluir?

> **OBLIGATORIO**: Todas las dependencias listadas son **NO NEGOCIABLES** y
> representan el stack estándar completo.

### Dependencias Principales (Versiones Mínimas Obligatorias)

```json
{
   "dependencies": {
      "react": "^18.2.0",
      "react-dom": "^18.2.0",
      "@reduxjs/toolkit": "^1.9.5",
      "react-redux": "^8.1.1",
      "react-router-dom": "^6.14.1",
      "axios": "^1.4.0"
   },
   "devDependencies": {
      "@types/react": "^18.2.15",
      "@types/react-dom": "^18.2.7",
      "@types/node": "^20.4.2",
      "@typescript-eslint/eslint-plugin": "^6.0.0",
      "@typescript-eslint/parser": "^6.0.0",
      "@vitejs/plugin-react": "^4.0.3",
      "eslint": "^8.45.0",
      "eslint-plugin-react-hooks": "^4.6.0",
      "eslint-plugin-react-refresh": "^0.4.3",
      "eslint-config-prettier": "^9.0.0",
      "prettier": "^3.0.0",
      "typescript": "^5.0.2",
      "vite": "^4.4.5",
      "vitest": "^0.34.1",
      "@testing-library/react": "^13.4.0",
      "@testing-library/jest-dom": "^5.17.0",
      "@testing-library/user-event": "^14.4.3",
      "jsdom": "^22.1.0"
   }
}
```

### Archivos de Configuración (Estándar Obligatorio)

- `vite.config.ts` - Configuración de Vite (única herramienta de build
  permitida)
- `tsconfig.json` - TypeScript con configuración estricta obligatoria
- `tsconfig.node.json` - TypeScript para Node.js en Vite
- `.eslintrc.cjs` - ESLint con reglas estándar no negociables
- `.prettierrc` - Prettier con formato estándar obligatorio
- `vitest.config.ts` - Testing con Vitest (único framework permitido para este
  stack)

## ¿Qué debo hacer?

### 1. Crear el Proyecto Base

```bash
# Crear proyecto con template React + TypeScript
npm create vite@latest frontend -- --template react-ts
cd frontend

# Instalar dependencias base
npm install

# Instalar dependencias adicionales para el stack completo
npm install @reduxjs/toolkit react-redux react-router-dom axios

# Instalar dependencias de desarrollo
npm install --save-dev @types/node vitest jsdom
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event
npm install --save-dev eslint-config-prettier prettier
```

### 2. Configurar Vite

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
   plugins: [react()],
   resolve: {
      alias: {
         "@": path.resolve(__dirname, "./src"),
         "@components": path.resolve(__dirname, "./src/components"),
         "@pages": path.resolve(__dirname, "./src/pages"),
         "@hooks": path.resolve(__dirname, "./src/hooks"),
         "@services": path.resolve(__dirname, "./src/services"),
         "@store": path.resolve(__dirname, "./src/store"),
         "@types": path.resolve(__dirname, "./src/types"),
         "@utils": path.resolve(__dirname, "./src/utils"),
      },
   },
   server: {
      port: 3000,
      host: true,
      proxy: {
         "/api": {
            target: "http://localhost:8000",
            changeOrigin: true,
            secure: false,
         },
      },
   },
   build: {
      outDir: "dist",
      sourcemap: true,
      rollupOptions: {
         output: {
            manualChunks: {
               vendor: ["react", "react-dom"],
               redux: ["@reduxjs/toolkit", "react-redux"],
               router: ["react-router-dom"],
            },
         },
      },
   },
   test: {
      globals: true,
      environment: "jsdom",
      setupFiles: ["./src/test/setup.ts"],
      css: true,
   },
});
```

### 3. Configurar TypeScript Estricto

```json
// tsconfig.json
{
   "compilerOptions": {
      "target": "ES2020",
      "useDefineForClassFields": true,
      "lib": ["ES2020", "DOM", "DOM.Iterable"],
      "module": "ESNext",
      "skipLibCheck": true,

      /* Bundler mode */
      "moduleResolution": "bundler",
      "allowImportingTsExtensions": true,
      "resolveJsonModule": true,
      "isolatedModules": true,
      "noEmit": true,
      "jsx": "react-jsx",

      /* Linting */
      "strict": true,
      "noUnusedLocals": true,
      "noUnusedParameters": true,
      "noFallthroughCasesInSwitch": true,
      "noImplicitReturns": true,
      "noUncheckedIndexedAccess": true,

      /* Path mapping */
      "baseUrl": ".",
      "paths": {
         "@/*": ["./src/*"],
         "@components/*": ["./src/components/*"],
         "@pages/*": ["./src/pages/*"],
         "@hooks/*": ["./src/hooks/*"],
         "@services/*": ["./src/services/*"],
         "@store/*": ["./src/store/*"],
         "@types/*": ["./src/types/*"],
         "@utils/*": ["./src/utils/*"]
      }
   },
   "include": ["src"],
   "references": [{ "path": "./tsconfig.node.json" }]
}
```

### 4. Configurar ESLint

```javascript
// .eslintrc.cjs
module.exports = {
   root: true,
   env: { browser: true, es2020: true },
   extends: [
      "eslint:recommended",
      "@typescript-eslint/recommended",
      "plugin:react-hooks/recommended",
      "prettier",
   ],
   ignorePatterns: ["dist", ".eslintrc.cjs"],
   parser: "@typescript-eslint/parser",
   plugins: ["react-refresh"],
   rules: {
      "react-refresh/only-export-components": [
         "warn",
         { allowConstantExport: true },
      ],
      "@typescript-eslint/no-unused-vars": ["error", {
         argsIgnorePattern: "^_",
      }],
      "@typescript-eslint/explicit-function-return-type": "warn",
      "@typescript-eslint/no-explicit-any": "error",
      "prefer-const": "error",
      "no-var": "error",
   },
};
```

### 5. Configurar Prettier

```json
// .prettierrc
{
   "semi": true,
   "trailingComma": "es5",
   "singleQuote": true,
   "printWidth": 80,
   "tabWidth": 2,
   "useTabs": false,
   "bracketSpacing": true,
   "arrowParens": "avoid",
   "endOfLine": "lf"
}
```

### 6. Configurar Testing

```typescript
// src/test/setup.ts
import "@testing-library/jest-dom";

// Mock para APIs que podrían no estar disponibles en el entorno de test
Object.defineProperty(window, "matchMedia", {
   writable: true,
   value: jest.fn().mockImplementation((query) => ({
      matches: false,
      media: query,
      onchange: null,
      addListener: jest.fn(), // deprecated
      removeListener: jest.fn(), // deprecated
      addEventListener: jest.fn(),
      removeEventListener: jest.fn(),
      dispatchEvent: jest.fn(),
   })),
});
```

### 7. Configurar Variables de Entorno

```bash
# .env.development
VITE_API_BASE_URL=http://localhost:8000/api/v1
VITE_APP_NAME=Mi Aplicación
VITE_APP_VERSION=1.0.0
```

```bash
# .env.production
VITE_API_BASE_URL=https://api.midominio.com/v1
VITE_APP_NAME=Mi Aplicación
VITE_APP_VERSION=1.0.0
```

```typescript
// src/config/env.ts
interface EnvConfig {
   apiBaseUrl: string;
   appName: string;
   appVersion: string;
   isDevelopment: boolean;
   isProduction: boolean;
}

export const env: EnvConfig = {
   apiBaseUrl: import.meta.env.VITE_API_BASE_URL ||
      "http://localhost:8000/api/v1",
   appName: import.meta.env.VITE_APP_NAME || "Mi Aplicación",
   appVersion: import.meta.env.VITE_APP_VERSION || "1.0.0",
   isDevelopment: import.meta.env.DEV,
   isProduction: import.meta.env.PROD,
};
```

## ⚠️ Herramientas y Configuraciones PROHIBIDAS

Para mantener el estándar de Vite como bundler único:

### ❌ Bundlers y Build Tools Alternativos

- **Create React App (CRA)**: Deprecado por Meta → Usar **Vite únicamente**
- **Webpack configs manuales**: Complejidad innecesaria → Usar **Vite
  únicamente**
- **Parcel**: No alineado con stack → Usar **Vite únicamente**
- **Snowpack**: Descontinuado → Usar **Vite únicamente**
- **ESBuild directo**: Vite ya lo incluye → Usar **Vite únicamente**

### ❌ Testing Frameworks Alternativos (Para este Stack)

- **Jest directo**: Para proyectos Vite usar **Vitest** (mejor integración)
- **Karma**: Obsoleto → Usar **Vitest**
- **Cypress para unit tests**: Usar **@testing-library/react** + Vitest

### ❌ Package Managers Alternativos

- **Yarn**: Usar **npm únicamente** (viene con Node.js)
- **pnpm**: Usar **npm únicamente** para consistencia
- **Bun**: Demasiado nuevo → Usar **npm únicamente**

### ❌ TypeScript Alternativo o Configuraciones Relajadas

- **JavaScript puro**: **TypeScript es obligatorio**
- **PropTypes**: **TypeScript interfaces son obligatorias**
- **Tipado laxo**: **Configuración strict obligatoria**

### ❌ Frameworks Meta-Framework para SPAs

- **Next.js**: Para SPAs usar **React + Vite únicamente**
- **Remix**: Para SPAs usar **React + Vite únicamente**
- **Gatsby**: Para SPAs usar **React + Vite únicamente**

### ✅ Configuración Correcta (Única Permitida)

```bash
# Comando estándar obligatorio
npm create vite@latest frontend -- --template react-ts

# Dependencias estándar obligatorias
npm install @reduxjs/toolkit react-redux react-router-dom axios
npm install --save-dev @types/node vitest jsdom @testing-library/react
```

## Tips para Implementación Estándar

### Optimización de Desarrollo con Vite

1. **Hot Module Replacement**: Vite incluye HMR por defecto optimizado, mantén
   los componentes pequeños para máximo aprovechamiento

2. **Alias de Path Obligatorios**: Usa únicamente los alias configurados en el
   estándar:

```typescript
// ✅ Estándar correcto
import { Button } from "@components/common/Button";
import { userService } from "@services/userService";
import { RootState } from "@store/index";

// ❌ Prohibido - imports relativos profundos
import { Button } from "../../../components/common/Button";
import { userService } from "../../services/userService";
```

3. **TypeScript Estricto Obligatorio**: Aprovecha al máximo la configuración
   estándar:

```typescript
// ✅ Estándar correcto: Tipado explícito obligatorio
interface User {
   id: string;
   name: string;
   email: string;
   createdAt: Date;
}

const getUser = async (id: string): Promise<User> => {
   const response = await userService.fetchUser(id);
   return response.data;
};

// ❌ Prohibido: Tipado implícito o any
const getUser = async (id: any) => {
   const response = await userService.fetchUser(id);
   return response.data;
};
```

4. **Vite Dev Server Optimization**: Configuración estándar para desarrollo:

```typescript
// ✅ Configuración estándar en vite.config.ts
server: {
   port: 3000,
   host: true,
   open: true, // Abre browser automáticamente
   cors: true,
},
```

### Scripts de Package.json (Estándar Obligatorio)

```json
{
   "scripts": {
      "dev": "vite",
      "build": "tsc && vite build",
      "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
      "lint:fix": "eslint . --ext ts,tsx --fix",
      "format": "prettier --write \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
      "format:check": "prettier --check \"src/**/*.{ts,tsx,js,jsx,json,css,md}\"",
      "preview": "vite preview",
      "test": "vitest",
      "test:ui": "vitest --ui",
      "test:coverage": "vitest --coverage",
      "test:watch": "vitest --watch",
      "type-check": "tsc --noEmit",
      "type-check:watch": "tsc --noEmit --watch",
      "build:analyze": "vite build && npx rollup-plugin-visualizer dist/stats.html"
   }
}
```

> **NOTA**: Estos scripts son **NO NEGOCIABLES** y deben estar presentes en
> todos los proyectos para mantener consistencia en comandos de desarrollo.

## Ejemplos

### Ejemplo: Estructura de Carpetas Inicial

```
src/
├── components/
│   ├── common/
│   │   ├── Button/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.test.tsx
│   │   │   └── index.ts
│   │   └── Input/
│   └── layout/
│       ├── Header/
│       ├── Footer/
│       └── Sidebar/
├── hooks/
│   ├── useApi.ts
│   ├── useAuth.ts
│   └── index.ts
├── pages/
│   ├── auth/
│   │   ├── LoginPage.tsx
│   │   └── RegisterPage.tsx
│   └── dashboard/
│       └── DashboardPage.tsx
├── services/
│   ├── api.ts
│   ├── authService.ts
│   └── userService.ts
├── store/
│   ├── index.ts
│   ├── authSlice.ts
│   └── userSlice.ts
├── types/
│   ├── api.ts
│   ├── auth.ts
│   └── user.ts
├── utils/
│   ├── constants.ts
│   ├── formatters.ts
│   └── validators.ts
├── test/
│   └── setup.ts
├── App.tsx
├── main.tsx
└── vite-env.d.ts
```

### Ejemplo: Componente Base con TypeScript

```typescript
// src/components/common/Button/Button.tsx
import React from "react";

interface ButtonProps {
   children: React.ReactNode;
   variant?: "primary" | "secondary" | "danger";
   size?: "small" | "medium" | "large";
   disabled?: boolean;
   loading?: boolean;
   onClick?: () => void;
   type?: "button" | "submit" | "reset";
   className?: string;
}

export const Button: React.FC<ButtonProps> = ({
   children,
   variant = "primary",
   size = "medium",
   disabled = false,
   loading = false,
   onClick,
   type = "button",
   className = "",
}) => {
   const baseClasses = "btn";
   const variantClasses = `btn--${variant}`;
   const sizeClasses = `btn--${size}`;
   const stateClasses = disabled || loading ? "btn--disabled" : "";

   const classes = [
      baseClasses,
      variantClasses,
      sizeClasses,
      stateClasses,
      className,
   ]
      .filter(Boolean)
      .join(" ");

   return (
      <button
         type={type}
         className={classes}
         disabled={disabled || loading}
         onClick={onClick}
      >
         {loading ? "Cargando..." : children}
      </button>
   );
};
```

```typescript
// src/components/common/Button/index.ts
export { Button } from "./Button";
export type { ButtonProps } from "./Button";
```

### Ejemplo: Test del Componente

```typescript
// src/components/common/Button/Button.test.tsx
import { fireEvent, render, screen } from "@testing-library/react";
import { Button } from "./Button";

describe("Button Component", () => {
   it("renders children correctly", () => {
      render(<Button>Click me</Button>);
      expect(screen.getByText("Click me")).toBeInTheDocument();
   });

   it("applies correct variant class", () => {
      render(<Button variant="danger">Delete</Button>);
      const button = screen.getByRole("button");
      expect(button).toHaveClass("btn--danger");
   });

   it("calls onClick when clicked", () => {
      const mockOnClick = jest.fn();
      render(<Button onClick={mockOnClick}>Click me</Button>);

      fireEvent.click(screen.getByText("Click me"));
      expect(mockOnClick).toHaveBeenCalledTimes(1);
   });

   it("shows loading state correctly", () => {
      render(<Button loading>Submit</Button>);
      expect(screen.getByText("Cargando...")).toBeInTheDocument();
      expect(screen.getByRole("button")).toBeDisabled();
   });
});
```

## Navegación

[⬅️ Desarrollo del Frontend-web](./desarrollo-frontend-web.md) |
[🏠 README Principal](../../README.md) |
[Componentes Reutilizables con Tipado ➡️](./componentes-reutilizables-tipado.md)
