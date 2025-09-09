# Sincronización Estado Redux

## ¿Qué es?

Es la implementación de Redux Toolkit en la aplicación móvil, compartiendo la
lógica de estado con la aplicación web y adaptándola a las necesidades
específicas de móvil. Incluye la configuración de Redux Persist para estado
offline, sincronización de datos y manejo de estado específico para móvil.

## ¿Por qué es importante?

- **Estado Compartido**: Consistencia entre web y móvil
- **Offline First**: Funcionamiento sin conexión a internet
- **Performance**: Estado optimizado para móvil
- **Sincronización**: Datos actualizados entre dispositivos
- **Predictabilidad**: Estado predecible y debuggeable
- **Escalabilidad**: Arquitectura que crece con la aplicación

## ¿Qué debe incluir?

### Redux Toolkit Setup

- Store configurado para móvil
- Slices compartidos con web
- Middleware específico para móvil
- DevTools configurado

### Persistencia de Estado

- Redux Persist configurado
- Estrategias de sincronización
- Manejo de migraciones de estado
- Cleanup de estado obsoleto

### Estado Específico de Móvil

- Network status
- Device information
- App state (background/foreground)
- Biometric authentication

### Optimizaciones

- Estado mínimo persistido
- Lazy loading de slices
- Performance optimizations
- Memory management

## ¿Qué debo hacer?

### 1. Instalación de Dependencias

```bash
# Redux Toolkit
npm install @reduxjs/toolkit react-redux

# Redux Persist
npm install redux-persist
npm install @react-native-async-storage/async-storage

# Network state
npm install @react-native-netinfo/netinfo

# Additional tools
npm install redux-flipper # Para debugging con Flipper
```

### 2. Configuración del Store

**store/index.ts**:

```typescript
import { combineReducers, configureStore } from "@reduxjs/toolkit";
import {
   FLUSH,
   PAUSE,
   PERSIST,
   persistReducer,
   persistStore,
   PURGE,
   REGISTER,
   REHYDRATE,
} from "redux-persist";
import AsyncStorage from "@react-native-async-storage/async-storage";
import { setupListeners } from "@reduxjs/toolkit/query";
import { authSlice } from "./slices/authSlice";
import { userSlice } from "./slices/userSlice";
import { appSlice } from "./slices/appSlice";
import { networkSlice } from "./slices/networkSlice";

// Configuración de persistencia
const persistConfig = {
   key: "root",
   storage: AsyncStorage,
   whitelist: ["auth", "user"], // Solo persistir slices específicos
   blacklist: ["network", "app"], // No persistir estado temporal
};

// Root reducer
const rootReducer = combineReducers({
   auth: authSlice.reducer,
   user: userSlice.reducer,
   app: appSlice.reducer,
   network: networkSlice.reducer,
});

// Reducer persistido
const persistedReducer = persistReducer(persistConfig, rootReducer);

// Store configurado
export const store = configureStore({
   reducer: persistedReducer,
   middleware: (getDefaultMiddleware) =>
      getDefaultMiddleware({
         serializableCheck: {
            ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
         },
      }).concat(
         // Middleware adicional para desarrollo
         __DEV__ ? [require("redux-flipper").default()] : [],
      ),
   devTools: __DEV__,
});

// Persistor
export const persistor = persistStore(store);

// Types
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;

// Setup listeners para auto-rehydration
setupListeners(store.dispatch);
```

### 3. Hooks Tipados

**store/hooks.ts**:

```typescript
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import type { AppDispatch, RootState } from "./index";

// Hooks tipados para usar en toda la aplicación
export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### 4. Slice de Network Status

**store/slices/networkSlice.ts**:

```typescript
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
import NetInfo, { NetInfoState } from "@react-native-netinfo/netinfo";

interface NetworkState {
   isConnected: boolean;
   connectionType: string | null;
   isInternetReachable: boolean | null;
   details: any;
}

const initialState: NetworkState = {
   isConnected: false,
   connectionType: null,
   isInternetReachable: null,
   details: null,
};

export const networkSlice = createSlice({
   name: "network",
   initialState,
   reducers: {
      updateNetworkState: (state, action: PayloadAction<NetInfoState>) => {
         state.isConnected = action.payload.isConnected ?? false;
         state.connectionType = action.payload.type;
         state.isInternetReachable = action.payload.isInternetReachable;
         state.details = action.payload.details;
      },
   },
});

export const { updateNetworkState } = networkSlice.actions;

// Thunk para inicializar network listener
export const initializeNetworkListener = () => (dispatch: any) => {
   return NetInfo.addEventListener((state) => {
      dispatch(updateNetworkState(state));
   });
};
```

### 5. Slice de App State

**store/slices/appSlice.ts**:

```typescript
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
import { AppState } from "react-native";

interface AppStateInterface {
   currentState: string;
   isActive: boolean;
   isBackground: boolean;
   lastActiveTime: number;
   biometricAvailable: boolean;
   biometricEnabled: boolean;
}

const initialState: AppStateInterface = {
   currentState: "active",
   isActive: true,
   isBackground: false,
   lastActiveTime: Date.now(),
   biometricAvailable: false,
   biometricEnabled: false,
};

export const appSlice = createSlice({
   name: "app",
   initialState,
   reducers: {
      updateAppState: (state, action: PayloadAction<string>) => {
         state.currentState = action.payload;
         state.isActive = action.payload === "active";
         state.isBackground = action.payload === "background";

         if (action.payload === "active") {
            state.lastActiveTime = Date.now();
         }
      },
      setBiometricAvailable: (state, action: PayloadAction<boolean>) => {
         state.biometricAvailable = action.payload;
      },
      setBiometricEnabled: (state, action: PayloadAction<boolean>) => {
         state.biometricEnabled = action.payload;
      },
   },
});

export const {
   updateAppState,
   setBiometricAvailable,
   setBiometricEnabled,
} = appSlice.actions;

// Thunk para inicializar app state listener
export const initializeAppStateListener = () => (dispatch: any) => {
   return AppState.addEventListener("change", (nextAppState) => {
      dispatch(updateAppState(nextAppState));
   });
};
```

### 6. Provider Configuration

**App.tsx**:

```typescript
import React, { useEffect } from "react";
import { Provider } from "react-redux";
import { PersistGate } from "redux-persist/integration/react";
import { NavigationContainer } from "@react-navigation/native";
import { persistor, store } from "@/store";
import { RootNavigator } from "@/navigation/RootNavigator";
import { NetworkProvider } from "@/providers/NetworkProvider";
import { AppStateProvider } from "@/providers/AppStateProvider";
import { LoadingScreen } from "@/components/common/LoadingScreen";

const App: React.FC = () => {
   return (
      <Provider store={store}>
         <PersistGate loading={<LoadingScreen />} persistor={persistor}>
            <NetworkProvider>
               <AppStateProvider>
                  <NavigationContainer>
                     <RootNavigator />
                  </NavigationContainer>
               </AppStateProvider>
            </NetworkProvider>
         </PersistGate>
      </Provider>
   );
};

export default App;
```

### 7. Network Provider

**providers/NetworkProvider.tsx**:

```typescript
import React, { useEffect } from "react";
import { useAppDispatch } from "@/store/hooks";
import { initializeNetworkListener } from "@/store/slices/networkSlice";

interface NetworkProviderProps {
   children: React.ReactNode;
}

export const NetworkProvider: React.FC<NetworkProviderProps> = ({
   children,
}) => {
   const dispatch = useAppDispatch();

   useEffect(() => {
      const unsubscribe = dispatch(initializeNetworkListener());

      return () => {
         if (unsubscribe && typeof unsubscribe === "function") {
            unsubscribe();
         }
      };
   }, [dispatch]);

   return <>{children}</>;
};
```

### 8. Componente con Estado Sincronizado

**components/UserProfile.tsx**:

```typescript
import React, { useEffect } from "react";
import { StyleSheet, Text, View } from "react-native";
import { useAppDispatch, useAppSelector } from "@/store/hooks";
import { fetchUserProfile } from "@/store/slices/userSlice";
import { Button } from "@/components/common/Button";

export const UserProfile: React.FC = () => {
   const dispatch = useAppDispatch();
   const { user, loading, error } = useAppSelector((state) => state.user);
   const { isConnected } = useAppSelector((state) => state.network);
   const { isActive } = useAppSelector((state) => state.app);

   useEffect(() => {
      // Solo cargar datos si hay conexión y la app está activa
      if (isConnected && isActive && !user) {
         dispatch(fetchUserProfile());
      }
   }, [dispatch, isConnected, isActive, user]);

   const handleRefresh = (): void => {
      if (isConnected) {
         dispatch(fetchUserProfile());
      }
   };

   if (loading) {
      return (
         <View style={styles.container}>
            <Text>Cargando perfil...</Text>
         </View>
      );
   }

   if (error) {
      return (
         <View style={styles.container}>
            <Text style={styles.error}>Error: {error}</Text>
            {isConnected && (
               <Button title="Reintentar" onPress={handleRefresh} />
            )}
         </View>
      );
   }

   if (!user) {
      return (
         <View style={styles.container}>
            <Text>No hay datos de usuario</Text>
            {isConnected && <Button title="Cargar" onPress={handleRefresh} />}
         </View>
      );
   }

   return (
      <View style={styles.container}>
         <Text style={styles.name}>{user.name}</Text>
         <Text style={styles.email}>{user.email}</Text>
         {isConnected && <Button title="Actualizar" onPress={handleRefresh} />}
         {!isConnected && <Text style={styles.offline}>Sin conexión</Text>}
      </View>
   );
};

const styles = StyleSheet.create({
   container: {
      padding: 20,
   },
   name: {
      fontSize: 24,
      fontWeight: "bold",
      marginBottom: 8,
   },
   email: {
      fontSize: 16,
      color: "#666",
      marginBottom: 20,
   },
   error: {
      color: "red",
      marginBottom: 10,
   },
   offline: {
      color: "orange",
      textAlign: "center",
      marginTop: 10,
   },
});
```

## Tips

### Performance

- **Memoization**: Usa React.memo para componentes que no cambian
- **Selective Subscription**: Suscríbete solo a las partes necesarias del estado
- **Normalized State**: Mantén el estado normalizado para mejor performance
- **Cleanup**: Limpia listeners y subscriptions correctamente

### Offline Handling

- **Queue Actions**: Encola acciones para ejecutar cuando vuelva la conexión
- **Optimistic Updates**: Actualiza la UI inmediatamente, revierte si falla
- **Conflict Resolution**: Maneja conflictos cuando se sincroniza
- **Cache Strategy**: Implementa estrategias de cache apropiadas

### Security

- **Sensitive Data**: No persistas datos sensibles
- **Encryption**: Encripta datos importantes antes de persistir
- **Cleanup**: Limpia el estado al hacer logout
- **Biometric**: Usa autenticación biométrica para acceso al estado

### Development

- **Redux DevTools**: Usa Redux DevTools para debugging
- **Flipper**: Integra con Flipper para debugging avanzado
- **Time Travel**: Usa time travel debugging en desarrollo
- **State Snapshots**: Captura snapshots del estado para testing

## Ejemplos

### Slice con Async Thunks

```typescript
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

export const fetchPosts = createAsyncThunk(
   "posts/fetchPosts",
   async (_, { getState, rejectWithValue }) => {
      const state = getState() as RootState;

      if (!state.network.isConnected) {
         return rejectWithValue("No internet connection");
      }

      try {
         const response = await fetch("/api/posts");
         return await response.json();
      } catch (error) {
         return rejectWithValue(error.message);
      }
   },
);

const postsSlice = createSlice({
   name: "posts",
   initialState: {
      items: [],
      loading: false,
      error: null,
   },
   reducers: {},
   extraReducers: (builder) => {
      builder
         .addCase(fetchPosts.pending, (state) => {
            state.loading = true;
            state.error = null;
         })
         .addCase(fetchPosts.fulfilled, (state, action) => {
            state.loading = false;
            state.items = action.payload;
         })
         .addCase(fetchPosts.rejected, (state, action) => {
            state.loading = false;
            state.error = action.payload;
         });
   },
});
```

### Middleware Personalizado

```typescript
import { Middleware } from "@reduxjs/toolkit";

const offlineMiddleware: Middleware = (store) => (next) => (action) => {
   const state = store.getState();

   // Si no hay conexión, encolar acciones de red
   if (!state.network.isConnected && action.type.includes("fetch")) {
      // Lógica para encolar acción
      return;
   }

   return next(action);
};
```

### Custom Hook para Estado

```typescript
import { useAppSelector } from "@/store/hooks";

export const useNetworkStatus = () => {
   const network = useAppSelector((state) => state.network);

   return {
      isOnline: network.isConnected,
      connectionType: network.connectionType,
      isWifi: network.connectionType === "wifi",
      isCellular: network.connectionType === "cellular",
   };
};
```

---

## Navegación

**Progreso en Desarrollo de Aplicación Móvil (React Native):**

- ✅ [Desarrollo de Aplicación Móvil](./desarrollo-movil.md)
- ✅
  [Configuración React Native + TypeScript](./configuracion-react-native-typescript.md)
- ✅ [Pantallas y React Navigation](./pantallas-react-navigation.md)
- ✅ **Sincronización Estado Redux** ← Estás aquí
- ⏭️ [Integración APIs Móvil](./integracion-apis-movil.md)
- ⏭️ [Testing Dispositivos y Simuladores](./testing-dispositivos-simuladores.md)
- ⏭️ [Preparación App Stores](./preparacion-app-stores.md)

---

### Siguiente Paso

Continúa con [**Integración APIs Móvil**](./integracion-apis-movil.md)

[⬅️ Pantallas y React Navigation](./pantallas-react-navigation.md) |
[🏠 README Principal](../../README.md) |
[➡️ Integración APIs Móvil](./integracion-apis-movil.md)
