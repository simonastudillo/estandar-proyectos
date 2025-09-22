# Integración APIs con Axios

## Conocimientos involucrados
- Axios
- Integración frontend-backend
- Manejo de errores HTTP
- Autenticación y seguridad

## Responsable
- Equipo de desarrollo frontend

## ¿Qué es?

La integración de APIs con Axios es el proceso de configurar y utilizar Axios
como cliente HTTP para comunicar el frontend React con el backend Laravel. Esta
integración incluye la configuración de interceptors, manejo de errores,
autenticación automática, y la creación de servicios tipados que encapsulan
todas las llamadas a la API.

## ¿Por qué es importante?

- **Comunicación eficiente**: Facilita la comunicación entre frontend y backend
  de manera estructurada
- **Manejo de errores**: Centraliza el manejo de errores HTTP y respuestas de la
  API
- **Autenticación automática**: Incluye automáticamente tokens de autenticación
  en las requests
- **Tipado fuerte**: Proporciona tipos TypeScript para requests y responses
- **Interceptors**: Permite modificar requests y responses de forma global
- **Reutilización**: Servicios reutilizables que encapsulan la lógica de API

## ¿Qué debe incluir?

### Estructura de Servicios

```
src/services/
├── api.ts               # Configuración base de Axios
├── types/              # Tipos para APIs
│   ├── common.ts       # Tipos comunes (ApiResponse, PaginatedResponse)
│   ├── auth.ts         # Tipos de autenticación
│   └── user.ts         # Tipos de usuario
├── auth/               # Servicios de autenticación
│   ├── authService.ts  # Login, register, logout
│   └── tokenService.ts # Manejo de tokens
├── user/               # Servicios de usuario
│   ├── userService.ts  # CRUD de usuarios
│   └── profileService.ts # Perfil de usuario
├── common/             # Servicios comunes
│   ├── uploadService.ts # Subida de archivos
│   └── searchService.ts # Búsquedas
└── hooks/              # Hooks para servicios
    ├── useApi.ts       # Hook genérico para APIs
    ├── useAuth.ts      # Hook de autenticación
    └── useUsers.ts     # Hook de usuarios
```

### Componentes Principales

- **Cliente Axios configurado**: Instancia base con interceptors
- **Servicios tipados**: Funciones que encapsulan llamadas a APIs específicas
- **Manejo de errores**: Sistema centralizado de manejo de errores
- **Hooks personalizados**: React hooks que integran servicios con componentes
- **Types y interfaces**: Definiciones TypeScript para todas las APIs

## ¿Qué debo hacer?

### 1. Instalar y Configurar Axios

```bash
npm install axios
npm install --save-dev @types/node
```

### 2. Crear Cliente Base de Axios

```typescript
// src/services/api.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from "axios";
import { env } from "@/config/env";

// Tipos para respuestas de la API
export interface ApiResponse<T = any> {
   data: T;
   message?: string;
   status: number;
}

export interface PaginatedResponse<T> {
   data: T[];
   meta: {
      current_page: number;
      last_page: number;
      per_page: number;
      total: number;
      from: number;
      to: number;
   };
   links: {
      first: string;
      last: string;
      prev: string | null;
      next: string | null;
   };
}

export interface ApiError {
   message: string;
   errors?: Record<string, string[]>;
   status: number;
}

// Configuración base del cliente Axios
const createApiClient = (): AxiosInstance => {
   const client = axios.create({
      baseURL: env.apiBaseUrl,
      timeout: 10000,
      headers: {
         "Content-Type": "application/json",
         "Accept": "application/json",
      },
   });

   // Interceptor de request - Añadir token de autenticación
   client.interceptors.request.use(
      (config) => {
         const token = localStorage.getItem("auth_token");
         if (token) {
            config.headers.Authorization = `Bearer ${token}`;
         }

         // Log de requests en desarrollo
         if (env.isDevelopment) {
            console.log(
               `🚀 API Request: ${config.method?.toUpperCase()} ${config.url}`,
               config.data,
            );
         }

         return config;
      },
      (error) => {
         console.error("❌ Request Error:", error);
         return Promise.reject(error);
      },
   );

   // Interceptor de response - Manejo de errores global
   client.interceptors.response.use(
      (response: AxiosResponse) => {
         // Log de responses en desarrollo
         if (env.isDevelopment) {
            console.log(
               `✅ API Response: ${response.config.method?.toUpperCase()} ${response.config.url}`,
               response.data,
            );
         }

         return response;
      },
      (error) => {
         console.error("❌ Response Error:", error);

         // Manejar diferentes tipos de errores
         if (error.response) {
            const { status, data } = error.response;

            switch (status) {
               case 401:
                  // Token expirado o inválido
                  localStorage.removeItem("auth_token");
                  window.location.href = "/login";
                  break;
               case 403:
                  // Sin permisos
                  console.error("Sin permisos para esta acción");
                  break;
               case 422:
                  // Errores de validación
                  console.error("Errores de validación:", data.errors);
                  break;
               case 500:
                  // Error del servidor
                  console.error("Error interno del servidor");
                  break;
               default:
                  console.error(
                     `Error ${status}:`,
                     data.message || "Error desconocido",
                  );
            }

            // Crear error consistente
            const apiError: ApiError = {
               message: data.message || `Error ${status}`,
               errors: data.errors,
               status,
            };

            return Promise.reject(apiError);
         } else if (error.request) {
            // Error de red
            const networkError: ApiError = {
               message: "Error de conexión. Verifica tu conexión a internet.",
               status: 0,
            };
            return Promise.reject(networkError);
         } else {
            // Error de configuración
            const configError: ApiError = {
               message: "Error de configuración de la petición",
               status: 0,
            };
            return Promise.reject(configError);
         }
      },
   );

   return client;
};

export const apiClient = createApiClient();

// Función helper para hacer requests tipadas
export const apiRequest = async <T>(
   config: AxiosRequestConfig,
): Promise<T> => {
   const response = await apiClient.request<ApiResponse<T>>(config);
   return response.data.data;
};
```

### 3. Crear Servicio de Autenticación

```typescript
// src/services/auth/authService.ts
import { apiClient, apiRequest } from "../api";
import {
   AuthResponse,
   LoginCredentials,
   RegisterCredentials,
   User,
} from "@types/auth";

export const authService = {
   // Login de usuario
   login: async (credentials: LoginCredentials): Promise<AuthResponse> => {
      return apiRequest<AuthResponse>({
         method: "POST",
         url: "/auth/login",
         data: credentials,
      });
   },

   // Registro de usuario
   register: async (userData: RegisterCredentials): Promise<AuthResponse> => {
      return apiRequest<AuthResponse>({
         method: "POST",
         url: "/auth/register",
         data: userData,
      });
   },

   // Logout
   logout: async (): Promise<void> => {
      await apiClient.post("/auth/logout");
   },

   // Obtener usuario actual
   getCurrentUser: async (): Promise<User> => {
      return apiRequest<User>({
         method: "GET",
         url: "/auth/me",
      });
   },

   // Refresh token
   refreshToken: async (): Promise<{ token: string }> => {
      return apiRequest<{ token: string }>({
         method: "POST",
         url: "/auth/refresh",
      });
   },

   // Solicitar reset de contraseña
   requestPasswordReset: async (
      email: string,
   ): Promise<{ message: string }> => {
      return apiRequest<{ message: string }>({
         method: "POST",
         url: "/auth/password/reset",
         data: { email },
      });
   },

   // Reset de contraseña
   resetPassword: async (data: {
      token: string;
      email: string;
      password: string;
      password_confirmation: string;
   }): Promise<{ message: string }> => {
      return apiRequest<{ message: string }>({
         method: "POST",
         url: "/auth/password/reset/confirm",
         data,
      });
   },
};
```

### 4. Crear Servicio de Usuarios

```typescript
// src/services/user/userService.ts
import { apiRequest, PaginatedResponse } from "../api";
import {
   CreateUserRequest,
   UpdateUserRequest,
   User,
   UserFilters,
} from "@types/user";

export const userService = {
   // Obtener usuarios con paginación y filtros
   getUsers: async (params?: {
      page?: number;
      per_page?: number;
      filters?: UserFilters;
   }): Promise<PaginatedResponse<User>> => {
      return apiRequest<PaginatedResponse<User>>({
         method: "GET",
         url: "/users",
         params,
      });
   },

   // Obtener usuario por ID
   getUserById: async (id: string): Promise<User> => {
      return apiRequest<User>({
         method: "GET",
         url: `/users/${id}`,
      });
   },

   // Crear nuevo usuario
   createUser: async (userData: CreateUserRequest): Promise<User> => {
      return apiRequest<User>({
         method: "POST",
         url: "/users",
         data: userData,
      });
   },

   // Actualizar usuario
   updateUser: async (
      id: string,
      userData: UpdateUserRequest,
   ): Promise<User> => {
      return apiRequest<User>({
         method: "PUT",
         url: `/users/${id}`,
         data: userData,
      });
   },

   // Actualizar parcialmente usuario
   patchUser: async (
      id: string,
      userData: Partial<UpdateUserRequest>,
   ): Promise<User> => {
      return apiRequest<User>({
         method: "PATCH",
         url: `/users/${id}`,
         data: userData,
      });
   },

   // Eliminar usuario (soft delete)
   deleteUser: async (id: string): Promise<void> => {
      await apiRequest<void>({
         method: "DELETE",
         url: `/users/${id}`,
      });
   },

   // Buscar usuarios
   searchUsers: async (query: string): Promise<User[]> => {
      return apiRequest<User[]>({
         method: "GET",
         url: "/users/search",
         params: { q: query },
      });
   },
};
```

### 5. Crear Hook Genérico para APIs

```typescript
// src/hooks/useApi.ts
import { useCallback, useEffect, useState } from "react";
import { ApiError } from "@services/api";

interface UseApiState<T> {
   data: T | null;
   loading: boolean;
   error: ApiError | null;
}

interface UseApiOptions {
   immediate?: boolean;
   onSuccess?: (data: any) => void;
   onError?: (error: ApiError) => void;
}

export const useApi = <T>(
   apiFunction: (...args: any[]) => Promise<T>,
   options: UseApiOptions = {},
) => {
   const { immediate = false, onSuccess, onError } = options;

   const [state, setState] = useState<UseApiState<T>>({
      data: null,
      loading: false,
      error: null,
   });

   const execute = useCallback(async (...args: any[]) => {
      setState((prev) => ({ ...prev, loading: true, error: null }));

      try {
         const data = await apiFunction(...args);
         setState({ data, loading: false, error: null });
         onSuccess?.(data);
         return data;
      } catch (error) {
         const apiError = error as ApiError;
         setState((prev) => ({ ...prev, loading: false, error: apiError }));
         onError?.(apiError);
         throw error;
      }
   }, [apiFunction, onSuccess, onError]);

   const reset = useCallback(() => {
      setState({ data: null, loading: false, error: null });
   }, []);

   useEffect(() => {
      if (immediate) {
         execute();
      }
   }, [immediate, execute]);

   return {
      ...state,
      execute,
      reset,
   };
};
```

### 6. Crear Hook Específico para Usuarios

```typescript
// src/hooks/useUsers.ts
import { useCallback, useState } from "react";
import { userService } from "@services/user/userService";
import { User, UserFilters } from "@types/user";
import { useApi } from "./useApi";
import { PaginatedResponse } from "@services/api";

export const useUsers = () => {
   const [filters, setFilters] = useState<UserFilters>({});
   const [page, setPage] = useState(1);
   const [perPage, setPerPage] = useState(10);

   // Hook para obtener usuarios con paginación
   const {
      data: usersData,
      loading: usersLoading,
      error: usersError,
      execute: fetchUsers,
   } = useApi<PaginatedResponse<User>>(
      () => userService.getUsers({ page, per_page: perPage, filters }),
   );

   // Hook para crear usuario
   const {
      loading: createLoading,
      error: createError,
      execute: createUser,
   } = useApi(userService.createUser, {
      onSuccess: () => {
         fetchUsers(); // Recargar lista después de crear
      },
   });

   // Hook para actualizar usuario
   const {
      loading: updateLoading,
      error: updateError,
      execute: updateUser,
   } = useApi(userService.updateUser, {
      onSuccess: () => {
         fetchUsers(); // Recargar lista después de actualizar
      },
   });

   // Hook para eliminar usuario
   const {
      loading: deleteLoading,
      error: deleteError,
      execute: deleteUser,
   } = useApi(userService.deleteUser, {
      onSuccess: () => {
         fetchUsers(); // Recargar lista después de eliminar
      },
   });

   // Funciones de conveniencia
   const applyFilters = useCallback((newFilters: UserFilters) => {
      setFilters(newFilters);
      setPage(1); // Reset a la primera página
   }, []);

   const changePage = useCallback((newPage: number) => {
      setPage(newPage);
   }, []);

   const changePerPage = useCallback((newPerPage: number) => {
      setPerPage(newPerPage);
      setPage(1); // Reset a la primera página
   }, []);

   const refreshUsers = useCallback(() => {
      fetchUsers();
   }, [fetchUsers]);

   return {
      // Estado
      users: usersData?.data || [],
      meta: usersData?.meta,
      links: usersData?.links,
      filters,
      page,
      perPage,

      // Loading states
      usersLoading,
      createLoading,
      updateLoading,
      deleteLoading,

      // Errors
      usersError,
      createError,
      updateError,
      deleteError,

      // Actions
      fetchUsers,
      createUser,
      updateUser,
      deleteUser,
      applyFilters,
      changePage,
      changePerPage,
      refreshUsers,
   };
};
```

## Tips

### Optimización y Mejores Prácticas

1. **Cache y React Query**: Para aplicaciones complejas, considera usar React
   Query

```typescript
// Alternativa con React Query
import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";

export const useUsersQuery = (filters: UserFilters) => {
   return useQuery({
      queryKey: ["users", filters],
      queryFn: () => userService.getUsers({ filters }),
      staleTime: 5 * 60 * 1000, // 5 minutos
   });
};
```

2. **Retry automático**:

```typescript
// En la configuración de Axios
const client = axios.create({
   // ... otras opciones
});

// Interceptor para retry automático
client.interceptors.response.use(
   (response) => response,
   async (error) => {
      const config = error.config;

      if (error.response?.status >= 500 && config && !config._retry) {
         config._retry = true;
         config._retryCount = (config._retryCount || 0) + 1;

         if (config._retryCount <= 3) {
            await new Promise((resolve) =>
               setTimeout(resolve, 1000 * config._retryCount)
            );
            return client(config);
         }
      }

      return Promise.reject(error);
   },
);
```

3. **Cancelación de requests**:

```typescript
export const useApi = <T>(apiFunction: (...args: any[]) => Promise<T>) => {
   const [abortController, setAbortController] = useState<
      AbortController | null
   >(null);

   const execute = useCallback(async (...args: any[]) => {
      // Cancelar request anterior si existe
      abortController?.abort();

      const newController = new AbortController();
      setAbortController(newController);

      try {
         const data = await apiFunction(...args, {
            signal: newController.signal,
         });
         return data;
      } catch (error) {
         if (axios.isCancel(error)) {
            console.log("Request cancelado");
            return;
         }
         throw error;
      }
   }, [apiFunction, abortController]);

   useEffect(() => {
      return () => {
         abortController?.abort();
      };
   }, [abortController]);

   return { execute };
};
```

### Tipos TypeScript

```typescript
// src/types/auth.ts
export interface LoginCredentials {
   email: string;
   password: string;
   remember?: boolean;
}

export interface RegisterCredentials {
   firstName: string;
   lastName: string;
   email: string;
   password: string;
   password_confirmation: string;
}

export interface User {
   id: string;
   firstName: string;
   lastName: string;
   email: string;
   role: "admin" | "user" | "moderator";
   permissions: string[];
   avatar?: string;
   emailVerifiedAt?: string;
   createdAt: string;
   updatedAt: string;
}

export interface AuthResponse {
   user: User;
   token: string;
   expiresAt: string;
}
```

## Ejemplos

### Ejemplo: Componente con Integración de API

```typescript
// src/components/UserList.tsx
import React, { useEffect, useState } from "react";
import { useUsers } from "@hooks/useUsers";
import { Button } from "@components/common/Button";
import { Input } from "@components/common/Input";
import { Modal } from "@components/common/Modal";
import { UserForm } from "./UserForm";

export const UserList: React.FC = () => {
   const {
      users,
      meta,
      usersLoading,
      createLoading,
      fetchUsers,
      createUser,
      deleteUser,
      applyFilters,
      changePage,
   } = useUsers();

   const [showCreateModal, setShowCreateModal] = useState(false);
   const [searchTerm, setSearchTerm] = useState("");

   useEffect(() => {
      fetchUsers();
   }, [fetchUsers]);

   const handleSearch = (e: React.FormEvent) => {
      e.preventDefault();
      applyFilters({ search: searchTerm });
   };

   const handleCreateUser = async (userData: CreateUserRequest) => {
      try {
         await createUser(userData);
         setShowCreateModal(false);
      } catch (error) {
         console.error("Error al crear usuario:", error);
      }
   };

   const handleDeleteUser = async (userId: string) => {
      if (window.confirm("¿Estás seguro de eliminar este usuario?")) {
         try {
            await deleteUser(userId);
         } catch (error) {
            console.error("Error al eliminar usuario:", error);
         }
      }
   };

   if (usersLoading && users.length === 0) {
      return <div>Cargando usuarios...</div>;
   }

   return (
      <div className="user-list">
         <div className="user-list-header">
            <h2>Lista de Usuarios</h2>
            <Button onClick={() => setShowCreateModal(true)}>
               Crear Usuario
            </Button>
         </div>

         <form onSubmit={handleSearch} className="search-form">
            <Input
               type="text"
               placeholder="Buscar usuarios..."
               value={searchTerm}
               onChange={(e) => setSearchTerm(e.target.value)}
            />
            <Button type="submit">Buscar</Button>
         </form>

         <div className="user-grid">
            {users.map((user) => (
               <div key={user.id} className="user-card">
                  <h3>{user.firstName} {user.lastName}</h3>
                  <p>{user.email}</p>
                  <p>Rol: {user.role}</p>
                  <div className="user-actions">
                     <Button variant="secondary" size="small">
                        Editar
                     </Button>
                     <Button
                        variant="danger"
                        size="small"
                        onClick={() => handleDeleteUser(user.id)}
                     >
                        Eliminar
                     </Button>
                  </div>
               </div>
            ))}
         </div>

         {meta && (
            <div className="pagination">
               <Button
                  disabled={meta.current_page === 1}
                  onClick={() => changePage(meta.current_page - 1)}
               >
                  Anterior
               </Button>
               <span>
                  Página {meta.current_page} de {meta.last_page}
               </span>
               <Button
                  disabled={meta.current_page === meta.last_page}
                  onClick={() => changePage(meta.current_page + 1)}
               >
                  Siguiente
               </Button>
            </div>
         )}

         <Modal
            isOpen={showCreateModal}
            onClose={() => setShowCreateModal(false)}
            title="Crear Nuevo Usuario"
         >
            <UserForm
               onSubmit={handleCreateUser}
               loading={createLoading}
               onCancel={() => setShowCreateModal(false)}
            />
         </Modal>
      </div>
   );
};
```

### Ejemplo: Servicio de Upload

```typescript
// src/services/common/uploadService.ts
import { apiClient } from "../api";

export interface UploadProgress {
   loaded: number;
   total: number;
   percentage: number;
}

export interface UploadOptions {
   onProgress?: (progress: UploadProgress) => void;
   maxSize?: number; // en bytes
   allowedTypes?: string[];
}

export const uploadService = {
   uploadFile: async (
      file: File,
      endpoint: string = "/upload",
      options: UploadOptions = {},
   ): Promise<{ url: string; filename: string }> => {
      const { onProgress, maxSize = 10 * 1024 * 1024, allowedTypes } = options;

      // Validaciones
      if (file.size > maxSize) {
         throw new Error(
            `El archivo es muy grande. Máximo ${maxSize / 1024 / 1024}MB`,
         );
      }

      if (allowedTypes && !allowedTypes.includes(file.type)) {
         throw new Error(
            `Tipo de archivo no permitido. Permitidos: ${
               allowedTypes.join(", ")
            }`,
         );
      }

      const formData = new FormData();
      formData.append("file", file);

      const response = await apiClient.post(endpoint, formData, {
         headers: {
            "Content-Type": "multipart/form-data",
         },
         onUploadProgress: (progressEvent) => {
            if (onProgress && progressEvent.total) {
               const progress: UploadProgress = {
                  loaded: progressEvent.loaded,
                  total: progressEvent.total,
                  percentage: Math.round(
                     (progressEvent.loaded * 100) / progressEvent.total,
                  ),
               };
               onProgress(progress);
            }
         },
      });

      return response.data.data;
   },

   uploadMultipleFiles: async (
      files: File[],
      endpoint: string = "/upload/multiple",
      options: UploadOptions = {},
   ): Promise<Array<{ url: string; filename: string }>> => {
      const formData = new FormData();
      files.forEach((file, index) => {
         formData.append(`files[${index}]`, file);
      });

      const response = await apiClient.post(endpoint, formData, {
         headers: {
            "Content-Type": "multipart/form-data",
         },
         onUploadProgress: options.onProgress
            ? (progressEvent) => {
               if (progressEvent.total) {
                  const progress: UploadProgress = {
                     loaded: progressEvent.loaded,
                     total: progressEvent.total,
                     percentage: Math.round(
                        (progressEvent.loaded * 100) / progressEvent.total,
                     ),
                  };
                  options.onProgress!(progress);
               }
            }
            : undefined,
      });

      return response.data.data;
   },
};
```

## Navegación

[⬅️ Redux Toolkit - Estado Global](./redux-toolkit-estado-global.md) |
[🏠 README Principal](../../README.md) |
[React Router - Navegación ➡️](./react-router-navegacion.md)
