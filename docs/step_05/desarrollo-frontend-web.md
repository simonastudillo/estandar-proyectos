# Desarrollo del Frontend Web (React + TypeScript)

## ¿Qué es?

El desarrollo del frontend web es la fase donde implementamos la interfaz de
usuario utilizando React con TypeScript, siguiendo principios de Clean
Architecture y manteniendo un tipado fuerte. Esta etapa se centra en crear una
aplicación web moderna, escalable y mantenible que se integre perfectamente con
el backend desarrollado en Laravel.

## ¿Por qué es importante?

- **Experiencia de usuario**: Proporciona una interfaz intuitiva y responsiva
  para los usuarios finales
- **Tipado fuerte**: TypeScript previene errores en tiempo de compilación y
  mejora la mantenibilidad
- **Arquitectura escalable**: Estructura modular que facilita el crecimiento y
  mantenimiento
- **Estado predecible**: Redux Toolkit garantiza un manejo consistente del
  estado global
- **Testing robusto**: Jest y React Testing Library aseguran la calidad del
  código
- **Integración backend**: Comunicación eficiente con las APIs desarrolladas en
  Laravel

## ¿Qué debe incluir?

### Stack Tecnológico Frontend

```
Frontend Stack:
├── React 18+              # Framework principal
├── TypeScript 5+          # Tipado fuerte
├── Vite                   # Bundler y herramientas de desarrollo
├── Redux Toolkit          # Gestión de estado global
├── React Router           # Navegación del lado cliente
├── Axios                  # Cliente HTTP para APIs
├── Jest                   # Framework de testing
├── React Testing Library  # Utilidades de testing para React
├── ESLint + Prettier      # Calidad y formato de código
└── Material-UI/Tailwind   # Componentes UI (opcional)
```

### Estructura de Carpetas

```
src/
├── components/         # Componentes reutilizables
│   ├── common/        # Componentes genéricos (Button, Input, etc.)
│   ├── forms/         # Componentes de formularios específicos
│   └── layout/        # Componentes de layout (Header, Footer, etc.)
├── hooks/             # Custom hooks reutilizables
│   ├── useApi.ts      # Hook para llamadas a API
│   ├── useAuth.ts     # Hook para autenticación
│   └── useLocalStorage.ts
├── pages/             # Páginas principales de la aplicación
│   ├── auth/          # Páginas de autenticación
│   ├── dashboard/     # Dashboard principal
│   └── profile/       # Páginas de perfil
├── services/          # Servicios para comunicación con APIs
│   ├── api.ts         # Configuración base de Axios
│   ├── authService.ts # Servicios de autenticación
│   └── userService.ts # Servicios de usuario
├── store/             # Redux store y slices
│   ├── index.ts       # Configuración del store
│   ├── authSlice.ts   # Estado de autenticación
│   └── userSlice.ts   # Estado de usuarios
├── types/             # Definiciones TypeScript
│   ├── api.ts         # Tipos para respuestas de API
│   ├── auth.ts        # Tipos de autenticación
│   └── user.ts        # Tipos de usuario
├── utils/             # Utilidades y helpers
│   ├── constants.ts   # Constantes de la aplicación
│   ├── formatters.ts  # Funciones de formateo
│   └── validators.ts  # Validaciones custom
├── styles/            # Estilos globales y temas
└── __tests__/         # Tests organizados por funcionalidad
```

## ¿Qué debo hacer?

### 1. Configuración Inicial del Proyecto

```bash
# Crear el proyecto con Vite
npm create vite@latest frontend -- --template react-ts
cd frontend

# Instalar dependencias principales
npm install @reduxjs/toolkit react-redux react-router-dom axios
npm install @types/node

# Instalar dependencias de desarrollo
npm install --save-dev @testing-library/react @testing-library/jest-dom
npm install --save-dev @testing-library/user-event vitest jsdom
npm install --save-dev eslint prettier eslint-config-prettier
```

### 2. Configuración de TypeScript Estricta

```json
// tsconfig.json
{
   "compilerOptions": {
      "strict": true,
      "noImplicitAny": true,
      "noImplicitReturns": true,
      "noFallthroughCasesInSwitch": true,
      "noUncheckedIndexedAccess": true
   }
}
```

### 3. Estructura de Desarrollo

1. **Configurar React + TypeScript + Vite** →
   [Ver guía detallada](./configuracion-react-typescript-vite.md)
2. **Implementar componentes reutilizables** →
   [Ver guía detallada](./componentes-reutilizables-tipado.md)
3. **Configurar Redux Toolkit** →
   [Ver guía detallada](./redux-toolkit-estado-global.md)
4. **Integrar con APIs del backend** →
   [Ver guía detallada](./integracion-apis-axios.md)
5. **Implementar navegación** →
   [Ver guía detallada](./react-router-navegacion.md)
6. **Configurar testing** → [Ver guía detallada](./testing-componentes-jest.md)

### 4. Integración con Backend Laravel

```typescript
// src/services/api.ts
import axios from "axios";

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL ||
   "http://localhost:8000/api/v1";

export const apiClient = axios.create({
   baseURL: API_BASE_URL,
   headers: {
      "Content-Type": "application/json",
      "Accept": "application/json",
   },
});

// Interceptor para autenticación
apiClient.interceptors.request.use((config) => {
   const token = localStorage.getItem("auth_token");
   if (token) {
      config.headers.Authorization = `Bearer ${token}`;
   }
   return config;
});
```

## Tips

### Desarrollo Eficiente

1. **Tipado Fuerte**: Siempre define interfaces TypeScript para props y estados
2. **Componentes Funcionales**: Usa hooks en lugar de componentes de clase
3. **Custom Hooks**: Abstrae lógica reutilizable en custom hooks
4. **Error Boundaries**: Implementa manejo de errores a nivel de componente
5. **Code Splitting**: Usa React.lazy() para cargar componentes dinámicamente
6. **Memoización**: Usa React.memo() y useMemo() para optimizar rendimiento

### Best Practices

```typescript
// ✅ Bueno: Interface bien definida
interface UserProps {
   id: string;
   name: string;
   email: string;
   onEdit: (id: string) => void;
}

// ✅ Bueno: Componente funcional con tipado
const UserCard: React.FC<UserProps> = ({ id, name, email, onEdit }) => {
   const handleEdit = useCallback(() => {
      onEdit(id);
   }, [id, onEdit]);

   return (
      <div className="user-card">
         <h3>{name}</h3>
         <p>{email}</p>
         <button onClick={handleEdit}>Editar</button>
      </div>
   );
};

// ✅ Bueno: Hook personalizado
const useUser = (userId: string) => {
   const [user, setUser] = useState<User | null>(null);
   const [loading, setLoading] = useState(true);
   const [error, setError] = useState<string | null>(null);

   useEffect(() => {
      const fetchUser = async () => {
         try {
            setLoading(true);
            const userData = await userService.getById(userId);
            setUser(userData);
         } catch (err) {
            setError(err instanceof Error ? err.message : "Error desconocido");
         } finally {
            setLoading(false);
         }
      };

      fetchUser();
   }, [userId]);

   return { user, loading, error };
};
```

### Testing Strategy

```typescript
// ✅ Bueno: Test de componente
import { fireEvent, render, screen } from "@testing-library/react";
import { UserCard } from "./UserCard";

describe("UserCard", () => {
   const mockUser = {
      id: "1",
      name: "Juan Pérez",
      email: "juan@example.com",
   };

   it("should render user information", () => {
      const mockOnEdit = jest.fn();

      render(<UserCard {...mockUser} onEdit={mockOnEdit} />);

      expect(screen.getByText("Juan Pérez")).toBeInTheDocument();
      expect(screen.getByText("juan@example.com")).toBeInTheDocument();
   });

   it("should call onEdit when edit button is clicked", () => {
      const mockOnEdit = jest.fn();

      render(<UserCard {...mockUser} onEdit={mockOnEdit} />);

      fireEvent.click(screen.getByText("Editar"));

      expect(mockOnEdit).toHaveBeenCalledWith("1");
   });
});
```

## Ejemplos

### Ejemplo: Página de Login con TypeScript

```typescript
// src/pages/auth/LoginPage.tsx
import React, { useState } from "react";
import { useDispatch } from "react-redux";
import { useNavigate } from "react-router-dom";
import { loginUser } from "../../store/authSlice";
import { LoginCredentials } from "../../types/auth";

export const LoginPage: React.FC = () => {
   const [credentials, setCredentials] = useState<LoginCredentials>({
      email: "",
      password: "",
   });
   const [loading, setLoading] = useState(false);
   const [error, setError] = useState<string | null>(null);

   const dispatch = useDispatch();
   const navigate = useNavigate();

   const handleSubmit = async (e: React.FormEvent) => {
      e.preventDefault();
      setLoading(true);
      setError(null);

      try {
         await dispatch(loginUser(credentials)).unwrap();
         navigate("/dashboard");
      } catch (err) {
         setError(
            err instanceof Error ? err.message : "Error de autenticación",
         );
      } finally {
         setLoading(false);
      }
   };

   const handleInputChange = (e: React.ChangeEvent<HTMLInputElement>) => {
      const { name, value } = e.target;
      setCredentials((prev) => ({
         ...prev,
         [name]: value,
      }));
   };

   return (
      <div className="login-page">
         <form onSubmit={handleSubmit} className="login-form">
            <h2>Iniciar Sesión</h2>

            {error && <div className="error-message">{error}</div>}

            <div className="form-group">
               <label htmlFor="email">Email:</label>
               <input
                  type="email"
                  id="email"
                  name="email"
                  value={credentials.email}
                  onChange={handleInputChange}
                  required
                  disabled={loading}
               />
            </div>

            <div className="form-group">
               <label htmlFor="password">Contraseña:</label>
               <input
                  type="password"
                  id="password"
                  name="password"
                  value={credentials.password}
                  onChange={handleInputChange}
                  required
                  disabled={loading}
               />
            </div>

            <button type="submit" disabled={loading}>
               {loading ? "Iniciando sesión..." : "Iniciar Sesión"}
            </button>
         </form>
      </div>
   );
};
```

### Ejemplo: Service para API

```typescript
// src/services/userService.ts
import { apiClient } from "./api";
import { CreateUserRequest, UpdateUserRequest, User } from "../types/user";

export const userService = {
   // Obtener todos los usuarios
   getAll: async (): Promise<User[]> => {
      const response = await apiClient.get<{ data: User[] }>("/users");
      return response.data.data;
   },

   // Obtener usuario por ID
   getById: async (id: string): Promise<User> => {
      const response = await apiClient.get<{ data: User }>(`/users/${id}`);
      return response.data.data;
   },

   // Crear nuevo usuario
   create: async (userData: CreateUserRequest): Promise<User> => {
      const response = await apiClient.post<{ data: User }>("/users", userData);
      return response.data.data;
   },

   // Actualizar usuario
   update: async (id: string, userData: UpdateUserRequest): Promise<User> => {
      const response = await apiClient.put<{ data: User }>(
         `/users/${id}`,
         userData,
      );
      return response.data.data;
   },

   // Eliminar usuario
   delete: async (id: string): Promise<void> => {
      await apiClient.delete(`/users/${id}`);
   },
};
```

---

## Navegación

**Progreso en Desarrollo del Backend:**

- ✅ **Desarrollo del Frontend-web** ← Estás aquí
- ⏭️
  [Configuración React + TypeScript + Vite](./configuracion-react-typescript-vite.md)
- ⏭️
  [Componentes Reutilizables con Tipado](./componentes-reutilizables-tipado.md)
- ⏭️ [Redux Toolkit - Estado Global](./redux-toolkit-estado-global.md)
- ⏭️ [Integración APIs con Axios](./integracion-apis-axios.md)
- ⏭️ [React Router - Navegación](./react-router-navegacion.md)
- ⏭️ [Testing Componentes con Jest](./testing-componentes-jest.md)

---

### Siguiente Paso

Continúa con
[**Configuración React + TypeScript + Vite**](./configuracion-react-typescript-vite.md)

[⬅️ Etapa 4: Testing TDD Backend](../step_04/testing-tdd-backend.md) |
[🏠 README Principal](../../README.md) |
[➡️ Configuración React + TypeScript + Vite](./configuracion-react-typescript-vite.md)
