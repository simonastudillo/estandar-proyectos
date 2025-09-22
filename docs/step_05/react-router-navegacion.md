# React Router - Navegación

## Conocimientos involucrados
- React Router
- Navegación SPA
- Protección de rutas
- Tipado en TypeScript

## Responsable
- Equipo de desarrollo frontend

## ¿Qué es?

React Router es la biblioteca estándar para manejo de rutas en aplicaciones
React. Permite crear aplicaciones de una sola página (SPA) con navegación del
lado cliente, rutas anidadas, protección de rutas, navegación programática y
manejo del historial del navegador. En aplicaciones TypeScript, React Router
proporciona un sistema de navegación robusto y tipado.

## ¿Por qué es importante?

- **Navegación SPA**: Permite navegación sin recargar la página completa
- **URLs semánticas**: Crea URLs legibles y amigables para SEO
- **Protección de rutas**: Implementa control de acceso basado en autenticación
  y permisos
- **Navegación anidada**: Soporta layouts complejos con rutas anidadas
- **Gestión del historial**: Maneja correctamente el botón "atrás" del navegador
- **Code splitting**: Facilita la carga lazy de componentes por ruta
- **Tipado fuerte**: Integración completa con TypeScript para rutas tipadas

## ¿Qué debe incluir?

### Estructura de Routing

```
src/
├── router/
│   ├── index.tsx           # Configuración principal del router
│   ├── routes.tsx          # Definición de rutas
│   ├── ProtectedRoute.tsx  # Componente para rutas protegidas
│   ├── AdminRoute.tsx      # Componente para rutas de admin
│   └── routeGuards.ts      # Guards y validaciones de rutas
├── pages/
│   ├── auth/
│   │   ├── LoginPage.tsx
│   │   ├── RegisterPage.tsx
│   │   └── ForgotPasswordPage.tsx
│   ├── dashboard/
│   │   ├── DashboardPage.tsx
│   │   └── AnalyticsPage.tsx
│   ├── users/
│   │   ├── UsersListPage.tsx
│   │   ├── UserDetailPage.tsx
│   │   └── UserEditPage.tsx
│   ├── profile/
│   │   ├── ProfilePage.tsx
│   │   └── SettingsPage.tsx
│   ├── errors/
│   │   ├── NotFoundPage.tsx
│   │   ├── UnauthorizedPage.tsx
│   │   └── ServerErrorPage.tsx
│   └── HomePage.tsx
└── layouts/
    ├── MainLayout.tsx      # Layout principal con sidebar
    ├── AuthLayout.tsx      # Layout para páginas de autenticación
    └── DashboardLayout.tsx # Layout específico del dashboard
```

### Conceptos Principales

- **BrowserRouter**: Router principal que usa HTML5 History API
- **Routes y Route**: Definición de rutas y componentes asociados
- **Outlet**: Punto donde se renderizan rutas anidadas
- **Link y NavLink**: Componentes para navegación
- **useNavigate**: Hook para navegación programática
- **useParams**: Hook para acceder a parámetros de URL
- **useLocation**: Hook para acceder a información de la ubicación actual

## ¿Qué debo hacer?

### 1. Instalar React Router

```bash
npm install react-router-dom
npm install --save-dev @types/react-router-dom
```

### 2. Configurar el Router Principal

```typescript
// src/router/index.tsx
import React from "react";
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import { routes } from "./routes";
import { ErrorBoundary } from "@components/common/ErrorBoundary";

const router = createBrowserRouter(routes, {
   future: {
      // Habilitar funcionalidades futuras
      v7_normalizeFormMethod: true,
   },
});

export const AppRouter: React.FC = () => {
   return (
      <ErrorBoundary>
         <RouterProvider router={router} />
      </ErrorBoundary>
   );
};

export default AppRouter;
```

### 3. Definir Rutas con Tipado

```typescript
// src/router/routes.tsx
import React from "react";
import { RouteObject } from "react-router-dom";
import { MainLayout } from "@layouts/MainLayout";
import { AuthLayout } from "@layouts/AuthLayout";
import { DashboardLayout } from "@layouts/DashboardLayout";
import { ProtectedRoute } from "./ProtectedRoute";
import { AdminRoute } from "./AdminRoute";

// Lazy loading de páginas
const HomePage = React.lazy(() => import("@pages/HomePage"));
const LoginPage = React.lazy(() => import("@pages/auth/LoginPage"));
const RegisterPage = React.lazy(() => import("@pages/auth/RegisterPage"));
const DashboardPage = React.lazy(() =>
   import("@pages/dashboard/DashboardPage")
);
const UsersListPage = React.lazy(() => import("@pages/users/UsersListPage"));
const UserDetailPage = React.lazy(() => import("@pages/users/UserDetailPage"));
const ProfilePage = React.lazy(() => import("@pages/profile/ProfilePage"));
const NotFoundPage = React.lazy(() => import("@pages/errors/NotFoundPage"));

export const routes: RouteObject[] = [
   {
      path: "/",
      element: <MainLayout />,
      children: [
         {
            index: true,
            element: (
               <React.Suspense fallback={<div>Cargando...</div>}>
                  <HomePage />
               </React.Suspense>
            ),
         },
      ],
   },
   {
      path: "/auth",
      element: <AuthLayout />,
      children: [
         {
            path: "login",
            element: (
               <React.Suspense fallback={<div>Cargando...</div>}>
                  <LoginPage />
               </React.Suspense>
            ),
         },
         {
            path: "register",
            element: (
               <React.Suspense fallback={<div>Cargando...</div>}>
                  <RegisterPage />
               </React.Suspense>
            ),
         },
      ],
   },
   {
      path: "/dashboard",
      element: (
         <ProtectedRoute>
            <DashboardLayout />
         </ProtectedRoute>
      ),
      children: [
         {
            index: true,
            element: (
               <React.Suspense fallback={<div>Cargando...</div>}>
                  <DashboardPage />
               </React.Suspense>
            ),
         },
         {
            path: "users",
            children: [
               {
                  index: true,
                  element: (
                     <AdminRoute>
                        <React.Suspense fallback={<div>Cargando...</div>}>
                           <UsersListPage />
                        </React.Suspense>
                     </AdminRoute>
                  ),
               },
               {
                  path: ":userId",
                  element: (
                     <AdminRoute>
                        <React.Suspense fallback={<div>Cargando...</div>}>
                           <UserDetailPage />
                        </React.Suspense>
                     </AdminRoute>
                  ),
               },
            ],
         },
         {
            path: "profile",
            element: (
               <React.Suspense fallback={<div>Cargando...</div>}>
                  <ProfilePage />
               </React.Suspense>
            ),
         },
      ],
   },
   {
      path: "*",
      element: (
         <React.Suspense fallback={<div>Cargando...</div>}>
            <NotFoundPage />
         </React.Suspense>
      ),
   },
];
```

### 4. Crear Componente de Ruta Protegida

```typescript
// src/router/ProtectedRoute.tsx
import React from "react";
import { Navigate, useLocation } from "react-router-dom";
import { useAuth } from "@hooks/useAuth";
import { LoadingSpinner } from "@components/common/LoadingSpinner";

interface ProtectedRouteProps {
   children: React.ReactNode;
   redirectTo?: string;
}

export const ProtectedRoute: React.FC<ProtectedRouteProps> = ({
   children,
   redirectTo = "/auth/login",
}) => {
   const { isAuthenticated, loading } = useAuth();
   const location = useLocation();

   if (loading) {
      return <LoadingSpinner />;
   }

   if (!isAuthenticated) {
      // Guardar la ubicación actual para redireccionar después del login
      return <Navigate to={redirectTo} state={{ from: location }} replace />;
   }

   return <>{children}</>;
};
```

### 5. Crear Componente de Ruta de Admin

```typescript
// src/router/AdminRoute.tsx
import React from "react";
import { Navigate } from "react-router-dom";
import { useAuth } from "@hooks/useAuth";
import { UnauthorizedPage } from "@pages/errors/UnauthorizedPage";

interface AdminRouteProps {
   children: React.ReactNode;
   requiredPermissions?: string[];
}

export const AdminRoute: React.FC<AdminRouteProps> = ({
   children,
   requiredPermissions = ["admin"],
}) => {
   const { user, isAuthenticated } = useAuth();

   if (!isAuthenticated) {
      return <Navigate to="/auth/login" replace />;
   }

   const hasRequiredPermissions = requiredPermissions.every((permission) =>
      user?.permissions?.includes(permission) || user?.role === "admin"
   );

   if (!hasRequiredPermissions) {
      return <UnauthorizedPage />;
   }

   return <>{children}</>;
};
```

### 6. Crear Layouts

```typescript
// src/layouts/MainLayout.tsx
import React from "react";
import { Outlet } from "react-router-dom";
import { Header } from "@components/layout/Header";
import { Footer } from "@components/layout/Footer";

export const MainLayout: React.FC = () => {
   return (
      <div className="main-layout">
         <Header />
         <main className="main-content">
            <Outlet />
         </main>
         <Footer />
      </div>
   );
};
```

```typescript
// src/layouts/DashboardLayout.tsx
import React from "react";
import { Outlet } from "react-router-dom";
import { DashboardHeader } from "@components/layout/DashboardHeader";
import { Sidebar } from "@components/layout/Sidebar";
import { useSidebar } from "@hooks/useSidebar";

export const DashboardLayout: React.FC = () => {
   const { isOpen, toggle } = useSidebar();

   return (
      <div className="dashboard-layout">
         <DashboardHeader onToggleSidebar={toggle} />
         <div className="dashboard-content">
            <Sidebar isOpen={isOpen} />
            <main
               className={`main-content ${
                  isOpen ? "sidebar-open" : "sidebar-closed"
               }`}
            >
               <Outlet />
            </main>
         </div>
      </div>
   );
};
```

### 7. Crear Componentes de Navegación

```typescript
// src/components/common/Navigation.tsx
import React from "react";
import { NavLink, useNavigate } from "react-router-dom";
import { useAuth } from "@hooks/useAuth";
import { Button } from "@components/common/Button";

interface NavigationItem {
   to: string;
   label: string;
   requiredPermissions?: string[];
}

const navigationItems: NavigationItem[] = [
   { to: "/", label: "Inicio" },
   { to: "/dashboard", label: "Dashboard" },
   {
      to: "/dashboard/users",
      label: "Usuarios",
      requiredPermissions: ["admin"],
   },
   { to: "/dashboard/profile", label: "Perfil" },
];

export const Navigation: React.FC = () => {
   const { user, isAuthenticated, logout } = useAuth();
   const navigate = useNavigate();

   const handleLogout = async () => {
      await logout();
      navigate("/");
   };

   const hasPermission = (permissions?: string[]): boolean => {
      if (!permissions || permissions.length === 0) return true;
      if (!user) return false;

      return permissions.some((permission) =>
         user.permissions?.includes(permission) || user.role === "admin"
      );
   };

   return (
      <nav className="navigation">
         <ul className="nav-list">
            {navigationItems.map((item) => {
               if (!hasPermission(item.requiredPermissions)) {
                  return null;
               }

               return (
                  <li key={item.to} className="nav-item">
                     <NavLink
                        to={item.to}
                        className={({ isActive }) =>
                           `nav-link ${isActive ? "nav-link--active" : ""}`}
                     >
                        {item.label}
                     </NavLink>
                  </li>
               );
            })}
         </ul>

         <div className="nav-actions">
            {isAuthenticated
               ? (
                  <div className="user-menu">
                     <span className="user-name">
                        Hola, {user?.firstName || "Usuario"}
                     </span>
                     <Button variant="secondary" onClick={handleLogout}>
                        Cerrar Sesión
                     </Button>
                  </div>
               )
               : (
                  <div className="auth-links">
                     <NavLink to="/auth/login" className="nav-link">
                        Iniciar Sesión
                     </NavLink>
                     <NavLink to="/auth/register" className="nav-link">
                        Registrarse
                     </NavLink>
                  </div>
               )}
         </div>
      </nav>
   );
};
```

### 8. Hooks Personalizados para Navegación

```typescript
// src/hooks/useNavigationParams.ts
import { useParams, useSearchParams } from "react-router-dom";

// Hook tipado para parámetros de URL
export const useTypedParams = <T extends Record<string, string>>(): T => {
   return useParams() as T;
};

// Hook para query parameters con validación
export const useQueryParams = <T extends Record<string, any>>(
   defaultValues: T,
): [T, (newParams: Partial<T>) => void] => {
   const [searchParams, setSearchParams] = useSearchParams();

   const params = Object.keys(defaultValues).reduce((acc, key) => {
      const value = searchParams.get(key);
      if (value !== null) {
         // Intentar parsear valores
         try {
            acc[key] = JSON.parse(value);
         } catch {
            acc[key] = value;
         }
      } else {
         acc[key] = defaultValues[key];
      }
      return acc;
   }, {} as T);

   const setParams = (newParams: Partial<T>) => {
      const updatedParams = { ...params, ...newParams };
      const newSearchParams = new URLSearchParams();

      Object.entries(updatedParams).forEach(([key, value]) => {
         if (value !== undefined && value !== null && value !== "") {
            newSearchParams.set(
               key,
               typeof value === "string" ? value : JSON.stringify(value),
            );
         }
      });

      setSearchParams(newSearchParams);
   };

   return [params, setParams];
};
```

## Tips

### Mejores Prácticas

1. **Lazy Loading**: Usa lazy loading para todas las páginas

```typescript
const LazyPage = React.lazy(() => import("@pages/SomePage"));

// En la ruta
element: <React.Suspense fallback={<PageSkeleton />}>
   <LazyPage />
</React.Suspense>;
```

2. **Rutas tipadas**: Define tipos para tus rutas

```typescript
// src/types/routes.ts
export interface RouteParams {
   userId: string;
   productId: string;
}

export type AppRoutes =
   | "/"
   | "/auth/login"
   | "/dashboard"
   | `/users/${string}`
   | `/products/${string}`;
```

3. **Breadcrumbs automáticos**:

```typescript
// src/hooks/useBreadcrumbs.ts
import { useLocation } from "react-router-dom";

interface Breadcrumb {
   label: string;
   path: string;
}

export const useBreadcrumbs = (): Breadcrumb[] => {
   const location = useLocation();

   const pathSegments = location.pathname.split("/").filter(Boolean);

   return pathSegments.map((segment, index) => {
      const path = "/" + pathSegments.slice(0, index + 1).join("/");
      const label = segment.charAt(0).toUpperCase() + segment.slice(1);

      return { label, path };
   });
};
```

4. **Navegación con confirmación**:

```typescript
// src/hooks/useConfirmNavigation.ts
import { useEffect } from "react";
import { useBeforeUnload } from "react-router-dom";

export const useConfirmNavigation = (
   shouldConfirm: boolean,
   message?: string,
) => {
   useBeforeUnload(
      shouldConfirm ? () => message || "¿Estás seguro de salir?" : undefined,
   );

   useEffect(() => {
      const handleBeforeUnload = (e: BeforeUnloadEvent) => {
         if (shouldConfirm) {
            e.preventDefault();
            e.returnValue = message || "¿Estás seguro de salir?";
         }
      };

      window.addEventListener("beforeunload", handleBeforeUnload);
      return () =>
         window.removeEventListener("beforeunload", handleBeforeUnload);
   }, [shouldConfirm, message]);
};
```

## Ejemplos

### Ejemplo: Página con Parámetros y Query Strings

```typescript
// src/pages/users/UserDetailPage.tsx
import React, { useEffect } from "react";
import { useNavigate, useParams } from "react-router-dom";
import { useUser } from "@hooks/useUser";
import { useQueryParams } from "@hooks/useNavigationParams";
import { Button } from "@components/common/Button";
import { Tabs } from "@components/common/Tabs";

interface UserDetailParams {
   userId: string;
}

interface QueryParams {
   tab: "profile" | "settings" | "activity";
   edit: boolean;
}

export const UserDetailPage: React.FC = () => {
   const { userId } = useParams<UserDetailParams>();
   const navigate = useNavigate();
   const [queryParams, setQueryParams] = useQueryParams<QueryParams>({
      tab: "profile",
      edit: false,
   });

   const { user, loading, error, fetchUser } = useUser(userId!);

   useEffect(() => {
      if (userId) {
         fetchUser();
      }
   }, [userId, fetchUser]);

   const handleTabChange = (tab: QueryParams["tab"]) => {
      setQueryParams({ tab });
   };

   const toggleEditMode = () => {
      setQueryParams({ edit: !queryParams.edit });
   };

   const handleBack = () => {
      navigate("/dashboard/users");
   };

   if (loading) return <div>Cargando usuario...</div>;
   if (error) return <div>Error: {error}</div>;
   if (!user) return <div>Usuario no encontrado</div>;

   return (
      <div className="user-detail-page">
         <div className="page-header">
            <Button variant="secondary" onClick={handleBack}>
               ← Volver
            </Button>
            <h1>{user.firstName} {user.lastName}</h1>
            <Button onClick={toggleEditMode}>
               {queryParams.edit ? "Cancelar" : "Editar"}
            </Button>
         </div>

         <Tabs
            activeTab={queryParams.tab}
            onTabChange={handleTabChange}
         >
            <Tabs.TabList>
               <Tabs.Tab value="profile">Perfil</Tabs.Tab>
               <Tabs.Tab value="settings">Configuración</Tabs.Tab>
               <Tabs.Tab value="activity">Actividad</Tabs.Tab>
            </Tabs.TabList>

            <Tabs.TabPanels>
               <Tabs.TabPanel value="profile">
                  <UserProfile user={user} editMode={queryParams.edit} />
               </Tabs.TabPanel>
               <Tabs.TabPanel value="settings">
                  <UserSettings user={user} editMode={queryParams.edit} />
               </Tabs.TabPanel>
               <Tabs.TabPanel value="activity">
                  <UserActivity userId={user.id} />
               </Tabs.TabPanel>
            </Tabs.TabPanels>
         </Tabs>
      </div>
   );
};

export default UserDetailPage;
```

### Ejemplo: Navegación Programática

```typescript
// src/hooks/useAppNavigation.ts
import { useLocation, useNavigate } from "react-router-dom";
import { useAuth } from "./useAuth";

export const useAppNavigation = () => {
   const navigate = useNavigate();
   const location = useLocation();
   const { user } = useAuth();

   const goToHome = () => navigate("/");

   const goToLogin = (returnUrl?: string) => {
      navigate("/auth/login", {
         state: { from: returnUrl || location.pathname },
      });
   };

   const goToDashboard = () => navigate("/dashboard");

   const goToUserProfile = (userId?: string) => {
      const targetUserId = userId || user?.id;
      if (targetUserId) {
         navigate(`/dashboard/users/${targetUserId}`);
      }
   };

   const goToUserList = (filters?: Record<string, any>) => {
      const searchParams = new URLSearchParams();
      if (filters) {
         Object.entries(filters).forEach(([key, value]) => {
            if (value) searchParams.set(key, value.toString());
         });
      }

      const queryString = searchParams.toString();
      navigate(`/dashboard/users${queryString ? `?${queryString}` : ""}`);
   };

   const goBack = () => navigate(-1);

   const replaceCurrentRoute = (path: string) => {
      navigate(path, { replace: true });
   };

   return {
      goToHome,
      goToLogin,
      goToDashboard,
      goToUserProfile,
      goToUserList,
      goBack,
      replaceCurrentRoute,
      currentPath: location.pathname,
   };
};
```

### Ejemplo: Error Boundary para Rutas

```typescript
// src/components/common/RouteErrorBoundary.tsx
import React from "react";
import { isRouteErrorResponse, useRouteError } from "react-router-dom";
import { Button } from "@components/common/Button";

export const RouteErrorBoundary: React.FC = () => {
   const error = useRouteError();

   let errorMessage: string;
   let errorStatus: number | undefined;

   if (isRouteErrorResponse(error)) {
      errorMessage = error.data?.message ||
         `${error.status} ${error.statusText}`;
      errorStatus = error.status;
   } else if (error instanceof Error) {
      errorMessage = error.message;
   } else if (typeof error === "string") {
      errorMessage = error;
   } else {
      errorMessage = "Error desconocido";
   }

   const handleReload = () => {
      window.location.reload();
   };

   const handleGoHome = () => {
      window.location.href = "/";
   };

   return (
      <div className="route-error-boundary">
         <div className="error-content">
            <h1>¡Oops! Algo salió mal</h1>
            {errorStatus && (
               <div className="error-status">
                  Error {errorStatus}
               </div>
            )}
            <p className="error-message">{errorMessage}</p>

            <div className="error-actions">
               <Button onClick={handleReload}>
                  Recargar Página
               </Button>
               <Button variant="secondary" onClick={handleGoHome}>
                  Ir a Inicio
               </Button>
            </div>
         </div>
      </div>
   );
};
```

## Navegación

[⬅️ Integración APIs con Axios](./integracion-apis-axios.md) |
[🏠 README Principal](../../README.md) |
[Testing Componentes con Jest ➡️](./testing-componentes-jest.md)
