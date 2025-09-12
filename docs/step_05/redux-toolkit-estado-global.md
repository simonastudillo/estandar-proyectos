# Redux Toolkit - Estado Global

## ¿Qué es?

Redux Toolkit es la forma oficial y recomendada de trabajar con Redux en
aplicaciones modernas. Proporciona herramientas y abstracciones que simplifican
la configuración y el uso de Redux, incluyendo la creación de stores, slices, y
la gestión del estado global de manera más eficiente y con menos código
boilerplate.

## ¿Por qué es importante?

- **Estado predecible**: Centraliza el estado de la aplicación en un store único
  y predecible
- **Debugging avanzado**: Redux DevTools permite inspeccionar y debuggear el
  estado fácilmente
- **Menos boilerplate**: Redux Toolkit reduce significativamente el código
  repetitivo
- **Inmutabilidad**: Usa Immer internamente para manejar actualizaciones
  inmutables de forma simple
- **TypeScript friendly**: Excelente integración con TypeScript para tipado
  fuerte
- **Performance**: Optimizaciones integradas y selectores memoizados

## ¿Qué debe incluir?

### Estructura del Store

```
src/store/
├── index.ts           # Configuración del store principal
├── hooks.ts          # Hooks tipados para useSelector y useDispatch
├── types.ts          # Tipos globales del store
├── slices/           # Slices organizados por dominio
│   ├── authSlice.ts  # Estado de autenticación
│   ├── userSlice.ts  # Estado de usuarios
│   ├── uiSlice.ts    # Estado de interfaz (modales, loading, etc.)
│   └── apiSlice.ts   # RTK Query para APIs (opcional)
├── middleware/       # Middleware personalizado
│   ├── authMiddleware.ts
│   └── errorMiddleware.ts
└── selectors/        # Selectores memoizados complejos
    ├── authSelectors.ts
    └── userSelectors.ts
```

### Conceptos Principales

- **Store**: Contenedor central del estado
- **Slices**: Fragmentos del estado con reducers y actions
- **Actions**: Eventos que describen cambios en el estado
- **Reducers**: Funciones que determinan cómo cambia el estado
- **Selectors**: Funciones para extraer datos del estado
- **Middleware**: Funciones que interceptan y pueden modificar actions

## ¿Qué debo hacer?

### 1. Instalar Dependencias

```bash
npm install @reduxjs/toolkit react-redux
npm install --save-dev @types/react-redux
```

### 2. Configurar el Store Principal

```typescript
// src/store/index.ts
import { configureStore } from "@reduxjs/toolkit";
import { authSlice } from "./slices/authSlice";
import { userSlice } from "./slices/userSlice";
import { uiSlice } from "./slices/uiSlice";

export const store = configureStore({
   reducer: {
      auth: authSlice.reducer,
      user: userSlice.reducer,
      ui: uiSlice.reducer,
   },
   middleware: (getDefaultMiddleware) =>
      getDefaultMiddleware({
         serializableCheck: {
            ignoredActions: ["persist/PERSIST", "persist/REHYDRATE"],
         },
      }),
   devTools: process.env.NODE_ENV !== "production",
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### 3. Crear Hooks Tipados

```typescript
// src/store/hooks.ts
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import type { AppDispatch, RootState } from "./index";

// Hooks tipados para usar en toda la aplicación
export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### 4. Crear Slice de Autenticación

```typescript
// src/store/slices/authSlice.ts
import { createAsyncThunk, createSlice, PayloadAction } from "@reduxjs/toolkit";
import { authService } from "@services/authService";
import { LoginCredentials, RegisterCredentials, User } from "@types/auth";

interface AuthState {
   user: User | null;
   token: string | null;
   isAuthenticated: boolean;
   loading: boolean;
   error: string | null;
}

const initialState: AuthState = {
   user: null,
   token: localStorage.getItem("auth_token"),
   isAuthenticated: Boolean(localStorage.getItem("auth_token")),
   loading: false,
   error: null,
};

// Async Thunks
export const loginUser = createAsyncThunk(
   "auth/login",
   async (credentials: LoginCredentials, { rejectWithValue }) => {
      try {
         const response = await authService.login(credentials);
         localStorage.setItem("auth_token", response.token);
         return response;
      } catch (error) {
         const message = error instanceof Error
            ? error.message
            : "Error de login";
         return rejectWithValue(message);
      }
   },
);

export const registerUser = createAsyncThunk(
   "auth/register",
   async (userData: RegisterCredentials, { rejectWithValue }) => {
      try {
         const response = await authService.register(userData);
         localStorage.setItem("auth_token", response.token);
         return response;
      } catch (error) {
         const message = error instanceof Error
            ? error.message
            : "Error de registro";
         return rejectWithValue(message);
      }
   },
);

export const logoutUser = createAsyncThunk(
   "auth/logout",
   async (_, { rejectWithValue }) => {
      try {
         await authService.logout();
         localStorage.removeItem("auth_token");
      } catch (error) {
         // Logout local aunque falle el servidor
         localStorage.removeItem("auth_token");
         const message = error instanceof Error
            ? error.message
            : "Error de logout";
         return rejectWithValue(message);
      }
   },
);

export const getCurrentUser = createAsyncThunk(
   "auth/getCurrentUser",
   async (_, { rejectWithValue }) => {
      try {
         const user = await authService.getCurrentUser();
         return user;
      } catch (error) {
         localStorage.removeItem("auth_token");
         const message = error instanceof Error
            ? error.message
            : "Usuario no válido";
         return rejectWithValue(message);
      }
   },
);

// Slice
export const authSlice = createSlice({
   name: "auth",
   initialState,
   reducers: {
      clearError: (state) => {
         state.error = null;
      },
      resetAuthState: () => initialState,
      updateUser: (state, action: PayloadAction<Partial<User>>) => {
         if (state.user) {
            state.user = { ...state.user, ...action.payload };
         }
      },
   },
   extraReducers: (builder) => {
      builder
         // Login
         .addCase(loginUser.pending, (state) => {
            state.loading = true;
            state.error = null;
         })
         .addCase(loginUser.fulfilled, (state, action) => {
            state.loading = false;
            state.user = action.payload.user;
            state.token = action.payload.token;
            state.isAuthenticated = true;
            state.error = null;
         })
         .addCase(loginUser.rejected, (state, action) => {
            state.loading = false;
            state.error = action.payload as string;
            state.isAuthenticated = false;
         })
         // Register
         .addCase(registerUser.pending, (state) => {
            state.loading = true;
            state.error = null;
         })
         .addCase(registerUser.fulfilled, (state, action) => {
            state.loading = false;
            state.user = action.payload.user;
            state.token = action.payload.token;
            state.isAuthenticated = true;
            state.error = null;
         })
         .addCase(registerUser.rejected, (state, action) => {
            state.loading = false;
            state.error = action.payload as string;
         })
         // Logout
         .addCase(logoutUser.fulfilled, (state) => {
            state.user = null;
            state.token = null;
            state.isAuthenticated = false;
            state.loading = false;
            state.error = null;
         })
         // Get Current User
         .addCase(getCurrentUser.pending, (state) => {
            state.loading = true;
         })
         .addCase(getCurrentUser.fulfilled, (state, action) => {
            state.loading = false;
            state.user = action.payload;
            state.isAuthenticated = true;
         })
         .addCase(getCurrentUser.rejected, (state) => {
            state.loading = false;
            state.user = null;
            state.token = null;
            state.isAuthenticated = false;
         });
   },
});

export const { clearError, resetAuthState, updateUser } = authSlice.actions;
```

### 5. Crear Slice de UI

```typescript
// src/store/slices/uiSlice.ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

interface UIState {
   theme: "light" | "dark";
   sidebarOpen: boolean;
   modals: {
      [key: string]: boolean;
   };
   notifications: Array<{
      id: string;
      type: "success" | "error" | "warning" | "info";
      message: string;
      duration?: number;
   }>;
   loading: {
      [key: string]: boolean;
   };
}

const initialState: UIState = {
   theme: (localStorage.getItem("theme") as "light" | "dark") || "light",
   sidebarOpen: false,
   modals: {},
   notifications: [],
   loading: {},
};

export const uiSlice = createSlice({
   name: "ui",
   initialState,
   reducers: {
      setTheme: (state, action: PayloadAction<"light" | "dark">) => {
         state.theme = action.payload;
         localStorage.setItem("theme", action.payload);
      },
      toggleSidebar: (state) => {
         state.sidebarOpen = !state.sidebarOpen;
      },
      setSidebarOpen: (state, action: PayloadAction<boolean>) => {
         state.sidebarOpen = action.payload;
      },
      openModal: (state, action: PayloadAction<string>) => {
         state.modals[action.payload] = true;
      },
      closeModal: (state, action: PayloadAction<string>) => {
         state.modals[action.payload] = false;
      },
      addNotification: (
         state,
         action: PayloadAction<Omit<UIState["notifications"][0], "id">>,
      ) => {
         const notification = {
            ...action.payload,
            id: Date.now().toString(),
         };
         state.notifications.push(notification);
      },
      removeNotification: (state, action: PayloadAction<string>) => {
         state.notifications = state.notifications.filter((n) =>
            n.id !== action.payload
         );
      },
      clearNotifications: (state) => {
         state.notifications = [];
      },
      setLoading: (
         state,
         action: PayloadAction<{ key: string; loading: boolean }>,
      ) => {
         state.loading[action.payload.key] = action.payload.loading;
      },
      clearLoading: (state, action: PayloadAction<string>) => {
         delete state.loading[action.payload];
      },
   },
});

export const {
   setTheme,
   toggleSidebar,
   setSidebarOpen,
   openModal,
   closeModal,
   addNotification,
   removeNotification,
   clearNotifications,
   setLoading,
   clearLoading,
} = uiSlice.actions;
```

### 6. Crear Selectores Memoizados

```typescript
// src/store/selectors/authSelectors.ts
import { createSelector } from "@reduxjs/toolkit";
import { RootState } from "../index";

// Selectores básicos
export const selectAuth = (state: RootState) => state.auth;
export const selectUser = (state: RootState) => state.auth.user;
export const selectAuthToken = (state: RootState) => state.auth.token;
export const selectIsAuthenticated = (state: RootState) =>
   state.auth.isAuthenticated;
export const selectAuthLoading = (state: RootState) => state.auth.loading;
export const selectAuthError = (state: RootState) => state.auth.error;

// Selectores memoizados complejos
export const selectUserName = createSelector(
   [selectUser],
   (user) => user ? `${user.firstName} ${user.lastName}` : "",
);

export const selectUserPermissions = createSelector(
   [selectUser],
   (user) => user?.permissions || [],
);

export const selectHasPermission = createSelector(
   [selectUserPermissions, (_, permission: string) => permission],
   (permissions, permission) => permissions.includes(permission),
);

export const selectIsAdmin = createSelector(
   [selectUser],
   (user) => user?.role === "admin",
);
```

### 7. Configurar el Provider

```typescript
// src/main.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { store } from "./store";
import App from "./App.tsx";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")!).render(
   <React.StrictMode>
      <Provider store={store}>
         <App />
      </Provider>
   </React.StrictMode>,
);
```

## Tips

### Mejores Prácticas

1. **Normalizar el estado**: Para estructuras complejas

```typescript
interface UserState {
   entities: { [id: string]: User };
   ids: string[];
   loading: boolean;
}
```

2. **Usar createSelector para computaciones complejas**:

```typescript
const selectExpensiveComputation = createSelector(
   [selectUsers, selectFilters],
   (users, filters) => {
      // Computación costosa solo se ejecuta si cambian users o filters
      return users.filter((user) =>
         filters.every((filter) => filter.apply(user))
      ).sort((a, b) => a.name.localeCompare(b.name));
   },
);
```

3. **Async Thunks con tipos genéricos**:

```typescript
export const fetchUsers = createAsyncThunk<
   User[], // Tipo de retorno
   { page: number; limit: number }, // Tipo de argumentos
   { rejectValue: string } // Tipo de error
>("users/fetchUsers", async (params, { rejectWithValue }) => {
   try {
      return await userService.getUsers(params);
   } catch (error) {
      return rejectWithValue(error.message);
   }
});
```

4. **Middleware personalizado**:

```typescript
// src/store/middleware/errorMiddleware.ts
import { isRejectedWithValue, Middleware } from "@reduxjs/toolkit";
import { addNotification } from "../slices/uiSlice";

export const errorMiddleware: Middleware = (store) => (next) => (action) => {
   if (isRejectedWithValue(action)) {
      store.dispatch(addNotification({
         type: "error",
         message: action.payload as string,
         duration: 5000,
      }));
   }
   return next(action);
};
```

### Patrones Avanzados

1. **RTK Query para APIs**:

```typescript
// src/store/api/userApi.ts
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";
import { User } from "@types/user";

export const userApi = createApi({
   reducerPath: "userApi",
   baseQuery: fetchBaseQuery({
      baseUrl: "/api/v1/users",
      prepareHeaders: (headers, { getState }) => {
         const token = (getState() as RootState).auth.token;
         if (token) {
            headers.set("authorization", `Bearer ${token}`);
         }
         return headers;
      },
   }),
   tagTypes: ["User"],
   endpoints: (builder) => ({
      getUsers: builder.query<User[], void>({
         query: () => "",
         providesTags: ["User"],
      }),
      getUserById: builder.query<User, string>({
         query: (id) => `/${id}`,
         providesTags: (result, error, id) => [{ type: "User", id }],
      }),
      createUser: builder.mutation<User, Partial<User>>({
         query: (userData) => ({
            url: "",
            method: "POST",
            body: userData,
         }),
         invalidatesTags: ["User"],
      }),
   }),
});

export const { useGetUsersQuery, useGetUserByIdQuery, useCreateUserMutation } =
   userApi;
```

## Ejemplos

### Ejemplo: Hook Personalizado para Autenticación

```typescript
// src/hooks/useAuth.ts
import { useEffect } from "react";
import { useAppDispatch, useAppSelector } from "@store/hooks";
import { getCurrentUser, logoutUser } from "@store/slices/authSlice";
import {
   selectAuthError,
   selectAuthLoading,
   selectIsAuthenticated,
   selectUser,
} from "@store/selectors/authSelectors";

export const useAuth = () => {
   const dispatch = useAppDispatch();
   const isAuthenticated = useAppSelector(selectIsAuthenticated);
   const user = useAppSelector(selectUser);
   const loading = useAppSelector(selectAuthLoading);
   const error = useAppSelector(selectAuthError);

   useEffect(() => {
      if (isAuthenticated && !user) {
         dispatch(getCurrentUser());
      }
   }, [isAuthenticated, user, dispatch]);

   const logout = () => {
      dispatch(logoutUser());
   };

   return {
      isAuthenticated,
      user,
      loading,
      error,
      logout,
   };
};
```

### Ejemplo: Componente con Redux

```typescript
// src/components/UserProfile.tsx
import React from "react";
import { useAppDispatch, useAppSelector } from "@store/hooks";
import { updateUser } from "@store/slices/authSlice";
import { selectUser, selectUserName } from "@store/selectors/authSelectors";
import { Button } from "@components/common/Button";

export const UserProfile: React.FC = () => {
   const dispatch = useAppDispatch();
   const user = useAppSelector(selectUser);
   const userName = useAppSelector(selectUserName);

   const handleUpdateProfile = () => {
      if (user) {
         dispatch(updateUser({
            firstName: "Nuevo",
            lastName: "Nombre",
         }));
      }
   };

   if (!user) {
      return <div>No hay usuario logueado</div>;
   }

   return (
      <div className="user-profile">
         <h2>Perfil de Usuario</h2>
         <p>Nombre completo: {userName}</p>
         <p>Email: {user.email}</p>
         <Button onClick={handleUpdateProfile}>
            Actualizar Perfil
         </Button>
      </div>
   );
};
```

### Ejemplo: Test de Slice

```typescript
// src/store/slices/__tests__/authSlice.test.ts
import { configureStore } from "@reduxjs/toolkit";
import { authSlice, clearError, loginUser } from "../authSlice";

describe("authSlice", () => {
   let store: ReturnType<typeof configureStore>;

   beforeEach(() => {
      store = configureStore({
         reducer: {
            auth: authSlice.reducer,
         },
      });
   });

   it("should handle clearError", () => {
      // Simular un estado con error
      store.dispatch({
         type: "auth/login/rejected",
         payload: "Error de login",
      });

      // Limpiar el error
      store.dispatch(clearError());

      const state = store.getState().auth;
      expect(state.error).toBeNull();
   });

   it("should handle loginUser.fulfilled", () => {
      const userData = {
         user: {
            id: "1",
            email: "test@example.com",
            firstName: "Test",
            lastName: "User",
         },
         token: "test-token",
      };

      store.dispatch(
         loginUser.fulfilled(userData, "", {
            email: "test@example.com",
            password: "password",
         }),
      );

      const state = store.getState().auth;
      expect(state.isAuthenticated).toBe(true);
      expect(state.user).toEqual(userData.user);
      expect(state.token).toBe(userData.token);
      expect(state.loading).toBe(false);
   });
});
```

## Navegación

[⬅️ Componentes Reutilizables con Tipado](./componentes-reutilizables-tipado.md)
| [🏠 README Principal](../../README.md) |
[Integración APIs con Axios ➡️](./integracion-apis-axios.md)
